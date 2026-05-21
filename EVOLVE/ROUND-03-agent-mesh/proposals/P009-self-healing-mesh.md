# Proposal P009: Self-Healing Mesh Mechanism

> **Status**: Draft
> **Proposed by**: AI Assistant
> **Date**: 2026-05-16
> **Related**: ROUND-003 brainstorm, P007 (Dynamic Topology), P008 (Communication Protocol), PAPERS/02-multi-agent-coordination.md

---

## Summary

This proposal designs a self-healing mechanism for the Agent Mesh, ensuring that single or multiple Agent failures do not cause overall system collapse or data loss. The self-healing mechanism comprises four core subsystems: Health Monitoring (real-time monitoring of Agent status), Fault Detection (distinguishing different types of failures), Task Migration (safely transferring failed Agent tasks to replacement Agents), and State Recovery (restoring task execution state from checkpoints).

The design follows Heya's core principle: automatically recover from routine failures, with human approval for critical decisions. Task takeover after an Agent crash is completed automatically in most cases, but recovery involving data consistency, cost overruns, or human approval nodes must go through human confirmation.

---

## Design

### Overall Architecture

```
┌──────────────────────────────────────────────────────────────────┐
│                    Self-Healing Control Plane                    │
│                                                                  │
│  ┌──────────────┐  ┌───────────────┐  ┌──────────────────────┐  │
│  │ Health       │  │ Fault         │  │ Task                 │  │
│  │ Monitor      │  │ Detector      │  │ Migrator             │  │
│  └──────┬───────┘  └──────┬────────┘  └──────────┬───────────┘  │
│         │                 │                       │              │
│  ┌──────┴─────────────────┴───────────────────────┴───────────┐  │
│  │                 Recovery Orchestrator                       │  │
│  │  ┌────────────────┐  ┌──────────────┐  ┌────────────────┐  │  │
│  │  │ State          │  │ Human        │  │ Recovery       │  │  │
│  │  │ Restorer       │  │ Escalator    │  │ Logger         │  │  │
│  │  └────────────────┘  └──────────────┘  └────────────────┘  │  │
│  └────────────────────────────────────────────────────────────┘  │
│                           │                                     │
│  ┌────────────────────────┴───────────────────────────────────┐  │
│  │              Checkpoint Store (persistent)                 │  │
│  │  Agent state snapshots | Task checkpoints | Message logs   │  │
│  │  | Recovery records                                         │  │
│  └────────────────────────────────────────────────────────────┘  │
└──────────────────────────────────────────────────────────────────┘
```

### 1. Health Monitoring (Health Monitor)

Continuously monitors the health status of all Agents.

```typescript
// ============ Health Status Definitions ============

type HealthStatus =
  | "healthy"       // operating normally
  | "degraded"      // performance degraded but functional
  | "overloaded"    // overloaded, needs load shedding
  | "suspect"       // suspected failure (heartbeat missed)
  | "confirmed_down" // confirmed failure
  | "maintenance";  // planned maintenance

interface HealthReport {
  agentId: string;
  status: HealthStatus;
  timestamp: number;
  metrics: HealthMetrics;
  activeTasks: string[];
  connections: string[];           // currently active connections
  lastHealthyAt: number;
  consecutiveFailures: number;
}

interface HealthMetrics {
  // System metrics
  cpuUsage: number;                // 0-1
  memoryUsage: number;             // 0-1
  diskUsage: number;               // 0-1

  // Application metrics
  queueLength: number;
  queueUtilization: number;        // 0-1
  processingRate: number;          // messages/second
  errorRate: number;               // errors/minute
  avgResponseTimeMs: number;
  p99ResponseTimeMs: number;

  // LLM metrics
  llmCallSuccessRate: number;      // 0-1
  llmAvgLatencyMs: number;
  tokenUsageRate: number;          // tokens/minute
}
```

**Health Monitoring Service Interface**:

```typescript
interface HealthMonitor {
  // Register an Agent for monitoring
  registerAgent(agentId: string, config?: HealthCheckConfig): void;

  // Unregister an Agent
  unregisterAgent(agentId: string): void;

  // Receive heartbeat
  receiveHeartbeat(agentId: string, report: Partial<HealthReport>): void;

  // Get Agent health status
  getHealth(agentId: string): HealthReport;

  // Get all Agent health statuses
  getAllHealth(): Map<string, HealthReport>;

  // Subscribe to health status changes
  onHealthChange(callback: (event: HealthChangeEvent) => void): UnsubscribeFn;

  // Get health statistics
  getHealthStats(): HealthStats;
}

interface HealthCheckConfig {
  heartbeatIntervalMs: number;     // heartbeat interval, default 10000ms
  missedHeartbeatThreshold: number; // missed heartbeat threshold, default 3
  degradedThreshold: {
    cpuUsage?: number;             // default 0.85
    memoryUsage?: number;          // default 0.85
    errorRate?: number;            // default 10/min
    responseTimeMs?: number;       // default 5000ms
  };
}

interface HealthChangeEvent {
  agentId: string;
  oldStatus: HealthStatus;
  newStatus: HealthStatus;
  timestamp: number;
  reason: string;
  metrics?: HealthMetrics;
}

interface HealthStats {
  totalAgents: number;
  healthy: number;
  degraded: number;
  overloaded: number;
  suspect: number;
  confirmedDown: number;
  avgResponseTimeMs: number;
  avgErrorRate: number;
}
```

**Health Check Pseudocode**:

```typescript
class HealthMonitorImpl implements HealthMonitor {
  private agents: Map<string, AgentHealthState> = new Map();
  private checkTimers: Map<string, NodeJS.Timer> = new Map();

  registerAgent(agentId: string, config?: HealthCheckConfig): void {
    const cfg = { ...DEFAULT_CONFIG, ...config };
    this.agents.set(agentId, {
      agentId,
      config: cfg,
      status: "healthy",
      lastHeartbeat: Date.now(),
      missedHeartbeats: 0,
      metrics: createEmptyMetrics(),
    });

    // Start periodic checks
    const timer = setInterval(() => this.checkAgent(agentId), cfg.heartbeatIntervalMs);
    this.checkTimers.set(agentId, timer);
  }

  private checkAgent(agentId: string): void {
    const state = this.agents.get(agentId);
    if (!state) return;

    const now = Date.now();
    const elapsed = now - state.lastHeartbeat;

    // Check heartbeat timeout
    if (elapsed > state.config.heartbeatIntervalMs * 1.5) {
      state.missedHeartbeats++;

      if (state.missedHeartbeats >= state.config.missedHeartbeatThreshold) {
        this.updateStatus(agentId, "confirmed_down", `missed ${state.missedHeartbeats} heartbeats`);
        this.triggerRecovery(agentId);
      } else if (state.missedHeartbeats >= Math.ceil(state.config.missedHeartbeatThreshold / 2)) {
        this.updateStatus(agentId, "suspect", `missed ${state.missedHeartbeats} heartbeats`);
      }
    }

    // Check performance metrics (based on last received heartbeat data)
    if (state.status === "healthy" || state.status === "degraded") {
      const m = state.metrics;
      const cfg = state.config.degradedThreshold;

      if (m.cpuUsage > cfg.cpuUsage! || m.memoryUsage > cfg.memoryUsage! || m.errorRate > cfg.errorRate!) {
        this.updateStatus(agentId, "degraded", "performance metrics exceeded threshold");
      } else if (state.status === "degraded") {
        this.updateStatus(agentId, "healthy", "performance metrics recovered");
      }
    }

    // Check overload
    if (state.metrics.queueUtilization > 0.9 && state.status !== "overloaded") {
      this.updateStatus(agentId, "overloaded", "queue utilization above 90%");
    } else if (state.metrics.queueUtilization < 0.7 && state.status === "overloaded") {
      this.updateStatus(agentId, "healthy", "queue utilization recovered");
    }
  }

  receiveHeartbeat(agentId: string, report: Partial<HealthReport>): void {
    const state = this.agents.get(agentId);
    if (!state) return;

    state.lastHeartbeat = Date.now();
    state.missedHeartbeats = 0;

    if (report.metrics) {
      state.metrics = { ...state.metrics, ...report.metrics };
    }

    // If previously in suspect status, recover upon receiving heartbeat
    if (state.status === "suspect") {
      this.updateStatus(agentId, "healthy", "heartbeat received after suspected failure");
    }
  }
}
```

### 2. Fault Detection (Fault Detector)

Distinguishes different types of failures and determines the recovery strategy.

```typescript
interface FaultDetector {
  // Analyze a fault
  analyzeFault(agentId: string, healthReport: HealthReport): FaultAnalysis;

  // Get fault history
  getFaultHistory(agentId: string): FaultRecord[];

  // Detect fault patterns
  detectPatterns(): FaultPattern[];
}

interface FaultAnalysis {
  agentId: string;
  faultType: FaultType;
  severity: "low" | "medium" | "high" | "critical";
  probableCause: string;
  recoveryStrategy: RecoveryStrategy;
  requiresHumanApproval: boolean;
  estimatedRecoveryTimeMs: number;
  affectedTasks: string[];
  affectedConnections: string[];
}

type FaultType =
  | "process_crash"       // process crash
  | "heartbeat_timeout"   // heartbeat timeout
  | "network_partition"   // network partition
  | "resource_exhaustion" // resource exhaustion (memory, disk)
  | "llm_failure"         // sustained LLM call failures
  | "logic_error"         // logic error (consistently producing incorrect results)
  | "performance_degradation" // performance degradation
  | "unknown";            // unknown fault

type RecoveryStrategy =
  | { type: "restart"; targetAgentId: string }
  | { type: "migrate"; tasks: string[]; targetAgentId: string }
  | { type: "wait_and_retry"; estimatedRecoveryMs: number }
  | { type: "escalate_to_human"; reason: string }
  | { type: "degrade_service"; reducedCapabilities: string[] }
  | { type: "no_action"; reason: string };

interface FaultRecord {
  id: string;
  agentId: string;
  faultType: FaultType;
  timestamp: number;
  recoveryDurationMs: number;
  recoveryStrategy: string;
  tasksLost: number;
  tasksRecovered: number;
  humanIntervention: boolean;
}

interface FaultPattern {
  pattern: string;                  // e.g., "agent_X_crashes_every_30min"
  frequency: number;                // occurrence frequency
  affectedAgents: string[];
  suggestedAction: string;
}
```

**Fault Analysis Pseudocode**:

```typescript
class FaultDetectorImpl implements FaultDetector {
  analyzeFault(agentId: string, report: HealthReport): FaultAnalysis {
    const faultType = this.classifyFault(report);
    const severity = this.assessSeverity(faultType, report);
    const probableCause = this.inferCause(faultType, report);
    const recoveryStrategy = this.determineRecovery(faultType, severity, report);
    const requiresHuman = this.needsHumanApproval(faultType, severity, report);

    return {
      agentId,
      faultType,
      severity,
      probableCause,
      recoveryStrategy,
      requiresHumanApproval: requiresHuman,
      estimatedRecoveryTimeMs: this.estimateRecoveryTime(recoveryStrategy),
      affectedTasks: report.activeTasks,
      affectedConnections: report.connections,
    };
  }

  private classifyFault(report: HealthReport): FaultType {
    if (report.status === "confirmed_down") {
      // Further distinguish between crash and network partition
      const history = this.getFaultHistory(report.agentId);
      const recentNetworkIssues = history.filter(
        f => f.faultType === "network_partition" && Date.now() - f.timestamp < 3600000
      );
      return recentNetworkIssues.length > 2 ? "network_partition" : "process_crash";
    }

    if (report.metrics.llmCallSuccessRate < 0.5) {
      return "llm_failure";
    }

    if (report.metrics.memoryUsage > 0.95 || report.metrics.diskUsage > 0.95) {
      return "resource_exhaustion";
    }

    if (report.metrics.errorRate > 20 && report.metrics.llmCallSuccessRate > 0.8) {
      return "logic_error";
    }

    if (report.metrics.p99ResponseTimeMs > 30000) {
      return "performance_degradation";
    }

    return "unknown";
  }

  private determineRecovery(
    faultType: FaultType,
    severity: FaultAnalysis["severity"],
    report: HealthReport
  ): RecoveryStrategy {
    switch (faultType) {
      case "process_crash":
        return { type: "migrate", tasks: report.activeTasks, targetAgentId: "" }; // targetAgentId filled by Recovery Orchestrator

      case "network_partition":
        return { type: "wait_and_retry", estimatedRecoveryMs: 60000 }; // wait 60 seconds

      case "resource_exhaustion":
        return { type: "restart", targetAgentId: report.agentId }; // restart itself

      case "llm_failure":
        // LLM failure may be global; tasks should not be migrated
        return { type: "wait_and_retry", estimatedRecoveryMs: 120000 };

      case "logic_error":
        return { type: "escalate_to_human", reason: "Agent consistently producing errors, requires human review" };

      case "performance_degradation":
        return { type: "degrade_service", reducedCapabilities: this.identifyNonCriticalCapabilities(report) };

      default:
        return { type: "escalate_to_human", reason: "Unknown fault type" };
    }
  }

  private needsHumanApproval(
    faultType: FaultType,
    severity: FaultAnalysis["severity"],
    report: HealthReport
  ): boolean {
    // The following situations require human approval
    if (severity === "critical") return true;
    if (faultType === "logic_error") return true;
    if (report.activeTasks.some(taskId => this.isHumanCheckpointTask(taskId))) return true;
    if (this.getRecentFaultCount(report.agentId, 3600000) > 3) return true; // frequent failures within 1 hour
    return false;
  }
}
```

### 3. Task Migration (Task Migrator)

Safely migrates failed Agent tasks to replacement Agents.

```typescript
interface TaskMigrator {
  // Migrate a single task
  migrateTask(taskId: string, fromAgentId: string, toAgentId: string): Promise<MigrationResult>;

  // Batch migration (all tasks of an Agent)
  migrateAllTasks(fromAgentId: string): Promise<BatchMigrationResult>;

  // Find a replacement Agent
  findReplacement(agentId: string, taskId: string): Promise<ReplacementCandidate[]>;

  // Evaluate migration feasibility
  evaluateMigration(taskId: string, fromAgentId: string, toAgentId: string): MigrationFeasibility;

  // Get migration status
  getMigrationStatus(migrationId: string): MigrationStatus;
}

interface MigrationResult {
  migrationId: string;
  taskId: string;
  fromAgentId: string;
  toAgentId: string;
  status: "success" | "partial" | "failed";
  restoredFromCheckpoint: string;    // restored checkpoint ID
  skippedSteps: string[];            // skipped steps (already completed)
  durationMs: number;
  dataIntegrityVerified: boolean;
  error?: string;
}

interface BatchMigrationResult {
  agentId: string;
  migrations: MigrationResult[];
  summary: {
    total: number;
    succeeded: number;
    partial: number;
    failed: number;
    totalDurationMs: number;
  };
}

interface ReplacementCandidate {
  agentId: string;
  score: number;                     // replacement suitability 0-1
  reasons: string[];
  estimatedMigrationTimeMs: number;
  currentLoad: number;               // 0-1
}

interface MigrationFeasibility {
  feasible: boolean;
  score: number;                     // 0-1
  risks: string[];
  requirements: {
    needsStateTransfer: boolean;
    estimatedDataSize: number;       // bytes
    estimatedDowntimeMs: number;
    requiresHumanApproval: boolean;
  };
}

interface MigrationStatus {
  migrationId: string;
  phase: "preparing" | "transferring" | "restoring" | "verifying" | "completed" | "failed";
  progress: number;                  // 0-1
  startedAt: number;
  estimatedCompletionAt?: number;
  error?: string;
}
```

**Task Migration Pseudocode**:

```typescript
class TaskMigratorImpl implements TaskMigrator {
  async migrateTask(
    taskId: string,
    fromAgentId: string,
    toAgentId: string
  ): Promise<MigrationResult> {
    const migrationId = generateMigrationId();
    const startTime = Date.now();

    try {
      // === Phase 1: Preparation ===
      // 1.1 Get the source Agent's latest checkpoint
      const checkpoint = await this.checkpointStore.getLatestCheckpoint(fromAgentId, taskId);
      if (!checkpoint) {
        return {
          migrationId, taskId, fromAgentId, toAgentId,
          status: "failed",
          restoredFromCheckpoint: "",
          skippedSteps: [],
          durationMs: Date.now() - startTime,
          dataIntegrityVerified: false,
          error: "No checkpoint found for task",
        };
      }

      // 1.2 Evaluate the target Agent's feasibility
      const feasibility = this.evaluateMigration(taskId, fromAgentId, toAgentId);
      if (!feasibility.feasible) {
        return {
          migrationId, taskId, fromAgentId, toAgentId,
          status: "failed",
          restoredFromCheckpoint: checkpoint.id,
          skippedSteps: [],
          durationMs: Date.now() - startTime,
          dataIntegrityVerified: false,
          error: `Migration not feasible: ${feasibility.risks.join(", ")}`,
        };
      }

      // 1.3 Notify related Agents to pause sending messages to the source Agent
      await this.notifyDependents(taskId, fromAgentId, "pause");

      // === Phase 2: State Transfer ===
      // 2.1 Serialize task state
      const serializedState = await this.serializeTaskState(checkpoint);

      // 2.2 Send to target Agent
      await this.transferState(toAgentId, serializedState);

      // === Phase 3: Restoration ===
      // 3.1 Target Agent restores from checkpoint
      const restoreResult = await this.restoreOnTarget(toAgentId, checkpoint);

      // 3.2 Determine completed steps (to skip)
      const skippedSteps = checkpoint.completedSteps;

      // === Phase 4: Verification ===
      // 4.1 Verify data integrity
      const integrityVerified = await this.verifyDataIntegrity(checkpoint, restoreResult);

      // 4.2 Update message routing (redirect related messages to the new Agent)
      await this.updateRouting(taskId, fromAgentId, toAgentId);

      // 4.3 Notify related Agents to resume communication
      await this.notifyDependents(taskId, toAgentId, "resume");

      // 4.4 Log the migration
      await this.recoveryLogger.logMigration({
        migrationId, taskId, fromAgentId, toAgentId,
        checkpointId: checkpoint.id,
        durationMs: Date.now() - startTime,
        integrityVerified,
      });

      return {
        migrationId, taskId, fromAgentId, toAgentId,
        status: integrityVerified ? "success" : "partial",
        restoredFromCheckpoint: checkpoint.id,
        skippedSteps,
        durationMs: Date.now() - startTime,
        dataIntegrityVerified: integrityVerified,
      };
    } catch (error) {
      // Migration failed, attempt rollback
      await this.rollbackMigration(migrationId, taskId, fromAgentId, toAgentId);
      throw error;
    }
  }

  async findReplacement(agentId: string, taskId: string): Promise<ReplacementCandidate[]> {
    // Get the failed Agent's capability requirements
    const taskInfo = await this.getTaskInfo(taskId);
    const requiredCapabilities = taskInfo.requiredCapabilities;

    // Search for Agents with matching capabilities
    const candidates = await this.discovery.discoverByCapability(requiredCapabilities, {
      excludeAgents: [agentId],
      maxLoad: 0.8,  // don't select overloaded Agents
    });

    // Score and sort
    const scored = candidates.map(agent => ({
      agentId: agent.agentId,
      score: this.calculateReplacementScore(agent, taskInfo),
      reasons: this.generateScoreReasons(agent, taskInfo),
      estimatedMigrationTimeMs: this.estimateMigrationTime(taskInfo),
      currentLoad: agent.load.cpuUsage,
    }));

    return scored
      .filter(c => c.score > 0.3)
      .sort((a, b) => b.score - a.score);
  }

  private calculateReplacementScore(agent: AgentProfile, taskInfo: TaskInfo): number {
    let score = 0;

    // Capability match (weight 0.4)
    const capabilityMatch = this.matchCapabilities(agent.capabilities, taskInfo.requiredCapabilities);
    score += capabilityMatch * 0.4;

    // Historical performance (weight 0.2)
    const performance = agent.capabilities
      .filter(c => taskInfo.requiredCapabilities.includes(c.name))
      .reduce((sum, c) => sum + c.successRate, 0) / taskInfo.requiredCapabilities.length;
    score += performance * 0.2;

    // Available resources (weight 0.2)
    const availability = 1 - agent.load.cpuUsage;
    score += availability * 0.2;

    // Trust score (weight 0.1)
    const trust = this.trustStore.getTrustScore("system", agent.agentId);
    score += trust * 0.1;

    // Same Factory preference (weight 0.1)
    const sameFactory = agent.metadata.factoryId === taskInfo.factoryId ? 1 : 0.5;
    score += sameFactory * 0.1;

    return Math.min(score, 1);
  }
}
```

### 4. State Recovery (State Restorer)

Restores Agent and task execution state from checkpoints.

```typescript
interface StateRestorer {
  // Save a checkpoint
  saveCheckpoint(agentId: string, taskId: string, state: TaskState): Promise<Checkpoint>;

  // Restore from checkpoint
  restoreFromCheckpoint(checkpointId: string, targetAgentId: string): Promise<RestoreResult>;

  // Get the latest checkpoint
  getLatestCheckpoint(agentId: string, taskId: string): Promise<Checkpoint | null>;

  // Clean up old checkpoints
  cleanupOldCheckpoints(agentId: string, retentionMs: number): Promise<number>;

  // Verify checkpoint integrity
  verifyCheckpoint(checkpointId: string): Promise<VerificationResult>;
}

interface TaskState {
  taskId: string;
  agentId: string;
  phase: string;                     // current execution phase
  completedSteps: string[];          // completed steps
  currentStep: string;               // current step
  context: {
    inputs: Record<string, unknown>;
    intermediateResults: Record<string, unknown>;
    decisions: DecisionRecord[];      // decisions already made
    pendingApprovals: string[];      // pending approval items
  };
  messageHistory: MessageSummary[];  // related message summaries
  artifacts: ArtifactReference[];    // produced artifacts
  timestamp: number;
}

interface DecisionRecord {
  decisionId: string;
  description: string;
  madeBy: string;                    // "agent:xxx" or "human"
  reason: string;
  timestamp: number;
}

interface Checkpoint {
  id: string;
  agentId: string;
  taskId: string;
  state: TaskState;
  createdAt: number;
  sizeBytes: number;
  hash: string;                      // content hash for integrity verification
  version: number;                   // checkpoint version number
  previousCheckpointId?: string;     // previous checkpoint (chained)
}

interface RestoreResult {
  checkpointId: string;
  targetAgentId: string;
  status: "success" | "partial" | "failed";
  restoredPhase: string;
  restoredSteps: string[];
  pendingSteps: string[];
  integrityVerified: boolean;
  warnings: string[];
  durationMs: number;
}

interface VerificationResult {
  valid: boolean;
  hashMatches: boolean;
  stateComplete: boolean;
  artifactsAccessible: boolean;
  errors: string[];
}
```

**Checkpoint Strategy Pseudocode**:

```typescript
class StateRestorerImpl implements StateRestorer {
  // Checkpoint save strategy: auto-save after critical steps
  async saveCheckpoint(agentId: string, taskId: string, state: TaskState): Promise<Checkpoint> {
    // 1. Serialize state
    const serialized = this.serialize(state);

    // 2. Compute hash
    const hash = await this.computeHash(serialized);

    // 3. Get previous checkpoint
    const prevCheckpoint = await this.getLatestCheckpoint(agentId, taskId);

    // 4. Create checkpoint record
    const checkpoint: Checkpoint = {
      id: generateCheckpointId(agentId, taskId),
      agentId,
      taskId,
      state,
      createdAt: Date.now(),
      sizeBytes: Buffer.byteLength(serialized),
      hash,
      version: (prevCheckpoint?.version ?? 0) + 1,
      previousCheckpointId: prevCheckpoint?.id,
    };

    // 5. Persist to storage
    await this.checkpointStore.save(checkpoint);

    // 6. Clean up old checkpoints (retain the most recent N)
    await this.cleanupOldCheckpoints(agentId, DEFAULT_RETENTION_MS);

    return checkpoint;
  }

  async restoreFromCheckpoint(checkpointId: string, targetAgentId: string): Promise<RestoreResult> {
    const startTime = Date.now();
    const warnings: string[] = [];

    // 1. Load checkpoint
    const checkpoint = await this.checkpointStore.load(checkpointId);
    if (!checkpoint) {
      return {
        checkpointId, targetAgentId,
        status: "failed", restoredPhase: "", restoredSteps: [], pendingSteps: [],
        integrityVerified: false, warnings: ["Checkpoint not found"], durationMs: 0,
      };
    }

    // 2. Verify integrity
    const verification = await this.verifyCheckpoint(checkpointId);
    if (!verification.valid) {
      warnings.push(`Checkpoint integrity issues: ${verification.errors.join(", ")}`);
    }

    // 3. Check artifact accessibility
    for (const artifact of checkpoint.state.artifacts) {
      const accessible = await this.artifactStore.exists(artifact.id);
      if (!accessible) {
        warnings.push(`Artifact ${artifact.id} not accessible`);
      }
    }

    // 4. Send restore command to target Agent
    const restoreMessage: MeshMessage = {
      id: generateMessageId(),
      type: "control",
      priority: "high",
      source: { agentId: "recovery-orchestrator", factoryId: "system" },
      target: { mode: "unicast", target: { agentId: targetAgentId, factoryId: "" } },
      payload: {
        command: "restore",
        targetTaskId: checkpoint.taskId,
        parameters: {
          checkpointId: checkpoint.id,
          state: checkpoint.state,
          skipSteps: checkpoint.state.completedSteps,
        },
      } as ControlPayload,
      context: {
        workOrderId: "",
        correlationId: checkpoint.taskId,
        timestamp: Date.now(),
        ttl: 300000,
        traceId: `recovery-${checkpointId}`,
      },
      reliability: {
        deliveryGuarantee: "exactly_once",
        requiresAck: true,
        retryPolicy: { maxRetries: 5, backoffStrategy: "exponential", initialDelayMs: 1000, maxDelayMs: 30000, jitter: true },
      },
      metadata: { schemaVersion: "1.0", sizeBytes: 0, compression: "gzip", encrypted: false },
    };

    await this.messageLifecycle.send(restoreMessage);

    // 5. Determine pending steps
    const pipeline = await this.getPipelineDefinition(checkpoint.taskId);
    const allSteps = pipeline.steps.map(s => s.id);
    const pendingSteps = allSteps.filter(s => !checkpoint.state.completedSteps.includes(s));

    return {
      checkpointId,
      targetAgentId,
      status: warnings.length === 0 ? "success" : "partial",
      restoredPhase: checkpoint.state.phase,
      restoredSteps: checkpoint.state.completedSteps,
      pendingSteps,
      integrityVerified: verification.valid,
      warnings,
      durationMs: Date.now() - startTime,
    };
  }
}
```

### 5. Recovery Orchestrator

Top-level orchestration that coordinates all self-healing subsystems.

```typescript
interface RecoveryOrchestrator {
  // Handle fault events
  handleFault(event: HealthChangeEvent): Promise<RecoveryPlan>;

  // Execute a recovery plan
  executePlan(plan: RecoveryPlan): Promise<RecoveryResult>;

  // Get recovery status
  getRecoveryStatus(recoveryId: string): RecoveryStatus;

  // Get recovery history
  getRecoveryHistory(options?: { agentId?: string; limit?: number }): RecoveryRecord[];
}

interface RecoveryPlan {
  recoveryId: string;
  agentId: string;
  faultAnalysis: FaultAnalysis;
  steps: RecoveryStep[];
  estimatedDurationMs: number;
  requiresHumanApproval: boolean;
  humanApprovalPrompt?: string;
  createdAt: number;
}

interface RecoveryStep {
  id: string;
  order: number;
  action: string;                    // e.g., "find_replacement", "migrate_task", "restore_state"
  description: string;
  dependencies: string[];            // prerequisite steps
  status: "pending" | "in_progress" | "completed" | "failed" | "skipped";
  result?: unknown;
  durationMs?: number;
}

interface RecoveryResult {
  recoveryId: string;
  status: "success" | "partial" | "failed" | "awaiting_human_approval";
  stepsCompleted: number;
  stepsTotal: number;
  tasksRecovered: number;
  tasksFailed: number;
  durationMs: number;
  humanDecision?: {
    approved: boolean;
    decisionAt: number;
    comments?: string;
  };
}

interface RecoveryRecord {
  recoveryId: string;
  agentId: string;
  faultType: FaultType;
  recoveryStatus: RecoveryResult["status"];
  tasksRecovered: number;
  tasksFailed: number;
  durationMs: number;
  humanIntervention: boolean;
  timestamp: number;
}
```

**Recovery Orchestration Pseudocode**:

```typescript
class RecoveryOrchestratorImpl implements RecoveryOrchestrator {
  async handleFault(event: HealthChangeEvent): Promise<RecoveryPlan> {
    const report = this.healthMonitor.getHealth(event.agentId);

    // 1. Analyze the fault
    const analysis = this.faultDetector.analyzeFault(event.agentId, report);

    // 2. Generate recovery plan
    const steps: RecoveryStep[] = [];

    // Step 1: Isolate the faulty Agent
    steps.push({
      id: "isolate",
      order: 1,
      action: "isolate_agent",
      description: `Isolate faulty Agent ${event.agentId}, stop receiving new tasks`,
      dependencies: [],
      status: "pending",
    });

    // Step 2: Find replacement Agent
    if (analysis.recoveryStrategy.type === "migrate") {
      steps.push({
        id: "find_replacement",
        order: 2,
        action: "find_replacement",
        description: `Find replacement Agent for ${analysis.affectedTasks.length} tasks`,
        dependencies: ["isolate"],
        status: "pending",
      });

      // Step 3: Migrate tasks
      for (const taskId of analysis.affectedTasks) {
        steps.push({
          id: `migrate_${taskId}`,
          order: 3,
          action: "migrate_task",
          description: `Migrate task ${taskId} to replacement Agent`,
          dependencies: ["find_replacement"],
          status: "pending",
        });
      }
    }

    // Step 4: Update topology
    steps.push({
      id: "update_topology",
      order: 4,
      action: "update_topology",
      description: "Update Mesh topology, remove faulty Agent's connections",
      dependencies: analysis.affectedTasks.length > 0 ? analysis.affectedTasks.map(t => `migrate_${t}`) : ["isolate"],
      status: "pending",
    });

    // Step 5: Notify human (if required)
    if (analysis.requiresHumanApproval) {
      steps.push({
        id: "notify_human",
        order: 5,
        action: "notify_human",
        description: "Notify human to approve recovery plan",
        dependencies: ["update_topology"],
        status: "pending",
      });
    }

    const plan: RecoveryPlan = {
      recoveryId: generateRecoveryId(),
      agentId: event.agentId,
      faultAnalysis: analysis,
      steps,
      estimatedDurationMs: this.estimateDuration(steps, analysis),
      requiresHumanApproval: analysis.requiresHumanApproval,
      humanApprovalPrompt: analysis.requiresHumanApproval
        ? this.generateHumanPrompt(analysis)
        : undefined,
      createdAt: Date.now(),
    };

    // Save recovery plan
    await this.store.savePlan(plan);

    return plan;
  }

  async executePlan(plan: RecoveryPlan): Promise<RecoveryResult> {
    // If human approval is required, wait first
    if (plan.requiresHumanApproval) {
      await this.waitForHumanApproval(plan.recoveryId);
    }

    const startTime = Date.now();
    let tasksRecovered = 0;
    let tasksFailed = 0;

    // Execute steps in order
    for (const step of plan.steps) {
      step.status = "in_progress";
      const stepStart = Date.now();

      try {
        switch (step.action) {
          case "isolate_agent":
            await this.isolateAgent(plan.agentId);
            break;

          case "find_replacement":
            const candidates = await this.taskMigrator.findReplacement(
              plan.agentId,
              plan.faultAnalysis.affectedTasks[0]
            );
            step.result = candidates;
            break;

          case "migrate_task":
            const taskId = step.id.replace("migrate_", "");
            const candidates = plan.steps.find(s => s.id === "find_replacement")?.result as ReplacementCandidate[];
            const target = candidates?.[0];
            if (target) {
              const result = await this.taskMigrator.migrateTask(taskId, plan.agentId, target.agentId);
              if (result.status === "success") tasksRecovered++;
              else tasksFailed++;
            } else {
              tasksFailed++;
            }
            break;

          case "update_topology":
            await this.updateMeshTopology(plan.agentId);
            break;

          case "notify_human":
            await this.humanEscalator.escalate(plan);
            break;
        }

        step.status = "completed";
        step.durationMs = Date.now() - stepStart;
      } catch (error) {
        step.status = "failed";
        step.durationMs = Date.now() - stepStart;
        // Log failure but do not abort the entire recovery process
        this.logger.warn(`Recovery step ${step.id} failed: ${error.message}`);
      }
    }

    const result: RecoveryResult = {
      recoveryId: plan.recoveryId,
      status: tasksFailed === 0 ? "success" : "partial",
      stepsCompleted: plan.steps.filter(s => s.status === "completed").length,
      stepsTotal: plan.steps.length,
      tasksRecovered,
      tasksFailed,
      durationMs: Date.now() - startTime,
    };

    await this.store.saveResult(result);
    return result;
  }

  private generateHumanPrompt(analysis: FaultAnalysis): string {
    return [
      `Agent ${analysis.agentId} experienced a ${analysis.faultType} fault.`,
      `\nSeverity: ${analysis.severity}`,
      `\nProbable cause: ${analysis.probableCause}`,
      `\nAffected tasks: ${analysis.affectedTasks.join(", ")}`,
      `\nRecommended recovery strategy: ${analysis.recoveryStrategy.type}`,
      `\n\nPlease choose an action:`,
      `- [Approve] Automatically execute the recovery plan`,
      `- [Modify] Adjust the recovery strategy`,
      `- [Manual] Handle the fault manually`,
      `- [Ignore] Do not handle for now`,
    ].join("\n");
  }
}
```

---

## Recovery Flow Examples

### Example 1: Agent Crash (Automatic Recovery)

```
T0: HealthMonitor detects Agent-BE-01 heartbeat loss (3/3)
    |
T1: Mark Agent-BE-01 as "confirmed_down"
    |
T2: FaultDetector analyzes: process_crash, severity=high
    |
T3: RecoveryOrchestrator generates recovery plan:
    - Isolate Agent-BE-01
    - Find replacement (Agent-BE-02 available)
    - Migrate task-001, task-002 to Agent-BE-02
    - Update topology
    |
T4: No human approval required (severity=high but not critical)
    |
T5: Automatic execution:
    - Isolation complete
    - Agent-BE-02 restores task-001 from checkpoint (success)
    - Agent-BE-02 restores task-002 from checkpoint (success)
    - Topology update complete
    |
T6: Recovery complete, took 12 seconds
```

### Example 2: Agent Logic Error (Human Approval)

```
T0: HealthMonitor detects Agent-QA-01 errorRate > 20/min
    |
T1: Mark Agent-QA-01 as "degraded"
    |
T2: FaultDetector analyzes: logic_error, severity=high
    |
T3: RecoveryOrchestrator generates recovery plan:
    - requiresHumanApproval = true
    |
T4: Send human_checkpoint message to human:
    "Agent-QA-01 consistently producing incorrect results, recommend replacing with Agent-QA-02"
    |
T5: Human selects [Approve]
    |
T6: Execute migration...
```

---

## Advantages

1. **High Availability**: Automatic detection and recovery minimize fault impact
2. **Data Safety**: Checkpoint mechanism ensures task state is not lost
3. **Tiered Response**: Minor faults handled automatically; severe faults require human approval
4. **Observability**: Complete recovery logs support post-incident analysis
5. **Progressive Recovery**: Step-by-step execution with verification at each step; failures can be rolled back

## Risks

1. **Checkpoint Overhead**: Frequent checkpoint saves consume storage and computing resources
   - Mitigation: Save only after critical steps; incremental checkpoints (save only changes); set retention periods for automatic cleanup
2. **Migration Latency**: Task migration takes time during which tasks are paused
   - Mitigation: Pre-warm replacement Agents (load necessary context in advance); parallel migration of multiple tasks
3. **State Inconsistency**: State inconsistency may occur after migration
   - Mitigation: Strict integrity verification; run verification tests after migration; support rollback
4. **False Positive Fault Detection**: Brief network jitter may be misidentified as a fault
   - Mitigation: Multi-level confirmation (suspect -> confirmed_down); progressive response (observe first, then act)
5. **Recovery Storm**: During large-scale failures, numerous recovery operations may exacerbate system load
   - Mitigation: Rate-limit recovery operations; prioritize high-priority task recovery; batch recovery strategy

---

## Open Decisions

- [ ] Checkpoint save frequency: save every step vs save at critical steps vs timed saves?
- [ ] Checkpoint storage location: local disk vs distributed storage vs hybrid?
- [ ] Specific values for heartbeat interval and missed heartbeat threshold?
- [ ] Weights for each factor in the replacement Agent selection strategy?
- [ ] Maximum concurrency for recovery operations?
- [ ] Human approval timeout? (Auto-approve or auto-reject on timeout?)
- [ ] Should "pre-warming" of replacement Agents be supported (loading checkpoints in advance)?
- [ ] Checkpoint encryption requirements?
