# Proposal P012: Cognitive Load Manager

> **Status**: Draft
> **Proposer**: AI Assistant
> **Date**: 2026-05-16
> **Related**: ROUND-04 brainstorm, P010 (Trust Score Model), P011 (Dynamic Permission), VISION.md ("AI Proposes, Human Decides")

---

## Summary

This proposal presents a Cognitive Load Manager that controls the frequency, method, and timing of decision requests sent by AI to humans, preventing humans from being overwhelmed by too many decision requests. The system dynamically adjusts decision request presentation strategies by evaluating the human's current cognitive load in real-time: batch-merging related decisions, sorting by priority, intelligently delaying non-urgent requests, and auto-handling low-risk decisions when appropriate.

The core design consists of four parts: a cognitive load assessment model (calculating load value based on pending decision count, complexity, and human behavior signals), a decision batch merging engine (packaging related decisions into "decision packages"), a priority sorting and smart delay system (selecting optimal presentation timing based on urgency and human state), and an adaptive presentation engine (adjusting information density and interaction methods based on load level).

---

## Design

### Overall Architecture

```
┌──────────────────────────────────────────────────────────────────┐
│                   Cognitive Load Manager                          │
│                                                                  │
│  ┌──────────────┐  ┌──────────────┐  ┌───────────────────────┐  │
│  │  Cognitive    │  │  Decision    │  │  Priority &           │  │
│  │  Load         │  │  Batcher     │  │  Delay Scheduler      │  │
│  │  Evaluator    │  │  (Batch      │  │  (Priority +          │  │
│  │               │  │   Merging)   │  │   Delay)              │  │
│  └──────┬───────┘  └──────┬───────┘  └────────┬──────────────┘  │
│         │                 │                    │                 │
│  ┌──────┴─────────────────┴────────────────────┴──────────────┐  │
│  │              Adaptive Presentation Engine                   │  │
│  │   (adaptive presentation: info density, interaction,       │  │
│  │    explanation depth)                                      │  │
│  └──────────────────────────┬────────────────────────────────┘  │
│                             │                                    │
│              ┌──────────────┴──────────────┐                     │
│              │                             │                     │
│  ┌───────────┴──────────┐    ┌─────────────┴────────────┐      │
│  │  Human State         │    │  Decision Queue          │      │
│  │  Tracker             │    │  (pending decision queue) │      │
│  │  (human state        │    │                          │      │
│  │   tracking)          │    │                          │      │
│  └──────────────────────┘    └──────────────────────────┘      │
└──────────────────────────────────────────────────────────────────┘
```

### 1. TypeScript Interface Definitions

#### 1.1 Cognitive Load Assessment

```typescript
/** Cognitive load level */
type CognitiveLoadLevel = "low" | "moderate" | "high" | "overloaded";

/** Cognitive load assessment result */
interface CognitiveLoadAssessment {
  level: CognitiveLoadLevel;
  score: number;                // 0.0 - 1.0 (0 = no load, 1 = overloaded)
  components: {
    pendingDecisions: number;   // Number of pending decisions
    decisionComplexity: number; // Decision complexity (weighted average)
    timePressure: number;       // Time pressure 0-1
    contextSwitches: number;    // Recent context switch count
    humanCapacity: number;      // Estimated human attention capacity 0-1
  };
  recommendation: LoadRecommendation;
  lastUpdated: number;
}

interface LoadRecommendation {
  strategy: PresentationStrategy;
  maxDecisionsPerBatch: number;
  maxBatchesPerHour: number;
  autoApproveBelowRisk: RiskLevel;  // Auto-handle decisions below this risk level
  delayNonUrgentMs: number;         // Delay time for non-urgent decisions
  explanationDepth: "full" | "summary" | "minimal" | "none";
}

/** Presentation strategy */
type PresentationStrategy =
  | "detailed"        // Low load: show full information, encourage deep human engagement
  | "standard"        // Moderate load: standard presentation
  | "batched"         // High load: batch merge, show only key information
  | "streamlined"     // Overloaded: minimal presentation, show only highest priority decisions
  | "async_only";     // Extremely overloaded: all decisions converted to async, don't block workflow

/** Cognitive load evaluator */
interface CognitiveLoadEvaluator {
  /** Evaluate current cognitive load */
  evaluate(userId: string): CognitiveLoadAssessment;

  /** Update human behavior signal */
  updateBehaviorSignal(signal: HumanBehaviorSignal): void;

  /** Get cognitive load trend */
  getTrend(userId: string, periodMs: number): LoadTrend;

  /** Subscribe to load changes */
  onLoadChange(
    callback: (assessment: CognitiveLoadAssessment) => void
  ): UnsubscribeFn;
}

/** Human behavior signal */
interface HumanBehaviorSignal {
  type:
    | "response_received"       // Human responded to a decision request
    | "decision_skipped"         // Human skipped a decision
    | "quick_approval"           // Quick approve (< 3 seconds)
    | "deep_review"              // Deep review (> 60 seconds)
    | "batch_approved"           // Batch approved
    | "request_more_info"        // Requested more information
    | "session_start"            // Session started
    | "session_end"              // Session ended
    | "idle_detected"            // Idle detected
    | "switch_context";          // Context switch
  timestamp: number;
  userId: string;
  metadata?: {
    responseTimeMs?: number;
    decisionId?: string;
    batchSize?: number;
  };
}

interface LoadTrend {
  period: { start: number; end: number };
  avgScore: number;
  peakScore: number;
  direction: "increasing" | "stable" | "decreasing";
  overloadCount: number;         // Overload count
}
```

#### 1.2 Cognitive Load Calculation Engine

```typescript
class CognitiveLoadEngine implements CognitiveLoadEvaluator {
  private behaviorBuffer: Map<string, HumanBehaviorSignal[]> = new Map();

  /**
   * Cognitive load calculation formula:
   *
   *   load = w1 * pending_factor
   *        + w2 * complexity_factor
   *        + w3 * time_pressure_factor
   *        + w4 * context_switch_factor
   *        - w5 * capacity_factor
   *
   * Factors:
   *   pending_factor = min(pending_decisions / max_pending, 1.0)
   *   complexity_factor = avg_decision_complexity
   *   time_pressure_factor = urgency based on deadline
   *   context_switch_factor = context switches in last 1 hour / 10
   *   capacity_factor = estimated current attention capacity based on historical behavior
   */
  evaluate(userId: string): CognitiveLoadAssessment {
    const pending = this.getPendingDecisionCount(userId);
    const complexity = this.getAverageComplexity(userId);
    const timePressure = this.getTimePressure(userId);
    const contextSwitches = this.getContextSwitchCount(userId);
    const capacity = this.estimateHumanCapacity(userId);

    const weights = { pending: 0.30, complexity: 0.20, timePressure: 0.15, contextSwitch: 0.15, capacity: 0.20 };

    const pendingFactor = Math.min(pending / 15, 1.0);     // 15 pending = full load
    const complexityFactor = complexity;                     // Already normalized 0-1
    const timePressureFactor = timePressure;                 // Already normalized 0-1
    const contextSwitchFactor = Math.min(contextSwitches / 10, 1.0);  // 10/hour = full load
    const capacityFactor = capacity;                         // Already normalized 0-1

    const rawScore =
      weights.pending * pendingFactor +
      weights.complexity * complexityFactor +
      weights.timePressure * timePressureFactor +
      weights.contextSwitch * contextSwitchFactor -
      weights.capacity * capacityFactor;

    const score = Math.max(0, Math.min(1, rawScore));

    return {
      level: this.scoreToLevel(score),
      score,
      components: {
        pendingDecisions: pending,
        decisionComplexity: complexity,
        timePressure: timePressure,
        contextSwitches,
        humanCapacity: capacity,
      },
      recommendation: this.generateRecommendation(score),
      lastUpdated: Date.now(),
    };
  }

  /** Estimate human's current attention capacity */
  private estimateHumanCapacity(userId: string): number {
    const signals = this.getRecentSignals(userId, 60 * 60 * 1000); // Last 1 hour
    if (signals.length === 0) return 0.5; // Default medium capacity

    // Estimate based on response speed and review depth
    const responseTimes = signals
      .filter(s => s.type === "response_received" && s.metadata?.responseTimeMs)
      .map(s => s.metadata!.responseTimeMs!);

    const deepReviews = signals.filter(s => s.type === "deep_review").length;
    const quickApprovals = signals.filter(s => s.type === "quick_approval").length;

    if (responseTimes.length === 0) return 0.5;

    const avgResponseTime = responseTimes.reduce((a, b) => a + b, 0) / responseTimes.length;

    // Fast response + lots of quick approves = high capacity (human trusts AI, doesn't want to be bothered)
    // Slow response + lots of deep reviews = low capacity (human is thinking carefully, needs more time)
    let capacity = 0.5;

    if (avgResponseTime < 10000) capacity += 0.2;       // Average < 10s
    if (avgResponseTime < 5000) capacity += 0.1;        // Average < 5s
    if (avgResponseTime > 60000) capacity -= 0.2;       // Average > 60s
    if (avgResponseTime > 120000) capacity -= 0.1;      // Average > 120s

    if (quickApprovals > deepReviews * 2) capacity += 0.1;  // Quick approves far outnumber deep reviews
    if (deepReviews > quickApprovals * 2) capacity -= 0.1;  // Deep reviews far outnumber quick approves

    return Math.max(0.1, Math.min(1.0, capacity));
  }

  /** Generate tiered recommendations based on load score */
  private generateRecommendation(score: number): LoadRecommendation {
    if (score < 0.25) {
      // Low load: show full information
      return {
        strategy: "detailed",
        maxDecisionsPerBatch: 3,
        maxBatchesPerHour: 20,
        autoApproveBelowRisk: "minimal",
        delayNonUrgentMs: 0,
        explanationDepth: "full",
      };
    }

    if (score < 0.50) {
      // Moderate load: standard presentation
      return {
        strategy: "standard",
        maxDecisionsPerBatch: 5,
        maxBatchesPerHour: 12,
        autoApproveBelowRisk: "minimal",
        delayNonUrgentMs: 5 * 60 * 1000,   // 5 minutes
        explanationDepth: "summary",
      };
    }

    if (score < 0.75) {
      // High load: batch merge
      return {
        strategy: "batched",
        maxDecisionsPerBatch: 10,
        maxBatchesPerHour: 6,
        autoApproveBelowRisk: "low",
        delayNonUrgentMs: 15 * 60 * 1000,  // 15 minutes
        explanationDepth: "summary",
      };
    }

    // Overloaded: minimal presentation
    return {
      strategy: "streamlined",
      maxDecisionsPerBatch: 20,
      maxBatchesPerHour: 3,
      autoApproveBelowRisk: "medium",
      delayNonUrgentMs: 60 * 60 * 1000,    // 1 hour
      explanationDepth: "minimal",
    };
  }

  private scoreToLevel(score: number): CognitiveLoadLevel {
    if (score < 0.25) return "low";
    if (score < 0.50) return "moderate";
    if (score < 0.75) return "high";
    return "overloaded";
  }

  // --- Helper methods (depend on external data sources) ---
  private getPendingDecisionCount(_userId: string): number { return 0; }
  private getAverageComplexity(_userId: string): number { return 0; }
  private getTimePressure(_userId: string): number { return 0; }
  private getContextSwitchCount(_userId: string): number { return 0; }
  private getRecentSignals(_userId: string, _windowMs: number): HumanBehaviorSignal[] { return []; }

  updateBehaviorSignal(signal: HumanBehaviorSignal): void {
    const buffer = this.behaviorBuffer.get(signal.userId) ?? [];
    buffer.push(signal);
    // Keep signals from the last 24 hours
    const cutoff = Date.now() - 24 * 60 * 60 * 1000;
    this.behaviorBuffer.set(
      signal.userId,
      buffer.filter(s => s.timestamp > cutoff)
    );
  }

  getTrend(_userId: string, _periodMs: number): LoadTrend {
    return { period: { start: 0, end: 0 }, avgScore: 0, peakScore: 0, direction: "stable", overloadCount: 0 };
  }

  onLoadChange(_callback: (assessment: CognitiveLoadAssessment) => void): () => void {
    return () => {};
  }
}
```

#### 1.3 Decision Batch Merging Engine

```typescript
/** Decision request */
interface DecisionRequest {
  id: string;
  type: DecisionType;
  taskType: TaskType;
  priority: DecisionPriority;
  riskLevel: RiskLevel;
  complexity: number;           // 0-1
  description: string;
  reasoning: string;            // AI's reasoning process
  options: DecisionOption[];    // Available options
  dependencies: string[];       // IDs of dependent decisions
  deadline?: number;            // Deadline
  aiConfidence: number;         // AI self-assessed confidence
  createdAt: number;
  context: {
    workOrderId: string;
    taskId: string;
    affectedFiles?: string[];
    affectedServices?: string[];
  };
}

type DecisionType =
  | "architecture"       // Architecture decision
  | "technology"         // Technology selection
  | "implementation"     // Implementation approach
  | "design"             // Design decision
  | "error_handling"     // Error handling approach
  | "optimization"       // Optimization strategy
  | "naming"             // Naming/formatting
  | "approval"           // Execution approval
  | "escalation";        // Anomaly escalation

type DecisionPriority = "critical" | "high" | "normal" | "low";

interface DecisionOption {
  id: string;
  label: string;
  description: string;
  pros: string[];
  cons: string[];
  estimatedImpact: {
    cost: number;
    time: number;
    risk: number;
  };
  recommended: boolean;
}

/** Decision batch */
interface DecisionBatch {
  id: string;
  title: string;
  theme: string;               // Theme (e.g. "User authentication module technology selection")
  decisions: DecisionRequest[];
  priority: DecisionPriority;  // Highest priority within the batch
  totalComplexity: number;     // Sum of decision complexities in the batch
  estimatedReviewTimeMs: number;
  createdAt: number;
  status: "pending" | "presented" | "approved" | "partially_approved" | "rejected";
  humanResponse?: BatchResponse;
}

interface BatchResponse {
  type: "approve_all" | "reject_all" | "selective" | "modify";
  timestamp: number;
  reviewDurationMs: number;
  selections?: Record<string, string>;  // decisionId -> optionId
  modifications?: Record<string, string>; // decisionId -> modification notes
  notes?: string;
}

/** Decision batch merger */
interface DecisionBatcher {
  /** Add decision to queue */
  enqueue(decision: DecisionRequest): void;

  /** Get the next batch to present */
  getNextBatch(
    userId: string,
    loadAssessment: CognitiveLoadAssessment
  ): DecisionBatch | null;

  /** Handle human response to a decision batch */
  handleBatchResponse(
    batchId: string,
    response: BatchResponse
  ): BatchProcessingResult;
}

interface BatchProcessingResult {
  success: boolean;
  approvedDecisions: string[];    // Approved decision IDs
  rejectedDecisions: string[];    // Rejected decision IDs
  modifiedDecisions: string[];    // Modified decision IDs
  autoProcessedDecisions: string[]; // Auto-processed decision IDs
}

/**
 * Batch merging strategy
 *
 * Merging rules:
 * 1. Decisions from the same task (taskId) are prioritized for merging
 * 2. Decisions with the same theme (e.g. "authentication", "database", "UI") are merged
 * 3. Decisions with dependencies must be ordered by dependency
 * 4. Batch size is limited by cognitive load
 * 5. Critical priority decisions do not participate in batching, presented immediately
 */
class SmartDecisionBatcher implements DecisionBatcher {
  private queue: DecisionRequest[] = [];
  private pendingBatches: DecisionBatch[] = [];

  enqueue(decision: DecisionRequest): void {
    this.queue.push(decision);
    this.tryCreateBatches();
  }

  getNextBatch(
    userId: string,
    loadAssessment: CognitiveLoadAssessment
  ): DecisionBatch | null {
    // 1. Check if there are Critical decisions that need immediate presentation
    const criticalDecision = this.queue.find(d => d.priority === "critical");
    if (criticalDecision) {
      this.queue = this.queue.filter(d => d.id !== criticalDecision.id);
      return this.createSingleBatch(criticalDecision);
    }

    // 2. Check if there are ready batches
    const readyBatch = this.pendingBatches.find(b => b.status === "pending");
    if (readyBatch) {
      readyBatch.status = "presented";
      return readyBatch;
    }

    // 3. Decide whether to create a new batch based on load
    if (loadAssessment.level === "overloaded") {
      // When overloaded, only show High and above priority decisions
      const highPriority = this.queue.filter(d => d.priority === "high");
      if (highPriority.length > 0) {
        return this.createBatchFromDecisions(highPriority.slice(0, 3));
      }
      return null; // Other decisions are delayed
    }

    // 4. Normal batching
    const maxBatchSize = loadAssessment.recommendation.maxDecisionsPerBatch;
    const batchable = this.queue.filter(d => d.priority !== "critical");

    if (batchable.length === 0) return null;

    // Group by theme
    const groups = this.groupByTheme(batchable);
    const bestGroup = this.selectBestGroup(groups, maxBatchSize);

    if (bestGroup.length === 0) return null;

    return this.createBatchFromDecisions(bestGroup.slice(0, maxBatchSize));
  }

  /** Group by theme */
  private groupByTheme(decisions: DecisionRequest[]): Map<string, DecisionRequest[]> {
    const groups = new Map<string, DecisionRequest[]>();

    for (const decision of decisions) {
      // Grouping strategy: by taskId + decisionType combination
      const theme = this.extractTheme(decision);
      const group = groups.get(theme) ?? [];
      group.push(decision);
      groups.set(theme, group);
    }

    return groups;
  }

  /** Extract decision theme */
  private extractTheme(decision: DecisionRequest): string {
    // Simple theme extraction: based on task ID and decision type
    return `${decision.context.taskId}:${decision.type}`;
  }

  /** Select the best group (prioritize the group with the most decisions) */
  private selectBestGroup(
    groups: Map<string, DecisionRequest[]>,
    maxSize: number
  ): DecisionRequest[] {
    let bestGroup: DecisionRequest[] = [];

    for (const [, decisions] of groups) {
      if (decisions.length > bestGroup.length && decisions.length <= maxSize) {
        bestGroup = decisions;
      }
    }

    // If no suitable group, take the first N decisions
    if (bestGroup.length === 0 && groups.size > 0) {
      const allDecisions = Array.from(groups.values()).flat();
      bestGroup = allDecisions.slice(0, maxSize);
    }

    // Sort by dependencies
    return this.sortByDependencies(bestGroup);
  }

  /** Sort by dependencies (topological sort) */
  private sortByDependencies(decisions: DecisionRequest[]): DecisionRequest[] {
    const idMap = new Map(decisions.map(d => [d.id, d]));
    const sorted: DecisionRequest[] = [];
    const visited = new Set<string>();

    const visit = (id: string) => {
      if (visited.has(id)) return;
      visited.add(id);
      const decision = idMap.get(id);
      if (decision) {
        for (const depId of decision.dependencies) {
          visit(depId);
        }
        sorted.push(decision);
      }
    };

    for (const d of decisions) {
      visit(d.id);
    }

    return sorted;
  }

  /** Create a single-decision batch (for Critical decisions) */
  private createSingleBatch(decision: DecisionRequest): DecisionBatch {
    return {
      id: `batch-${decision.id}`,
      title: `[Urgent] ${decision.description}`,
      theme: decision.context.taskId,
      decisions: [decision],
      priority: decision.priority,
      totalComplexity: decision.complexity,
      estimatedReviewTimeMs: this.estimateReviewTime([decision]),
      createdAt: Date.now(),
      status: "pending",
    };
  }

  /** Create a batch from multiple decisions */
  private createBatchFromDecisions(decisions: DecisionRequest[]): DecisionBatch {
    const theme = decisions.length > 1
      ? this.generateBatchTheme(decisions)
      : decisions[0].description;

    return {
      id: `batch-${crypto.randomUUID().slice(0, 8)}`,
      title: theme,
      theme: decisions[0].context.taskId,
      decisions: this.sortByDependencies(decisions),
      priority: this.getHighestPriority(decisions),
      totalComplexity: decisions.reduce((sum, d) => sum + d.complexity, 0),
      estimatedReviewTimeMs: this.estimateReviewTime(decisions),
      createdAt: Date.now(),
      status: "pending",
    };
  }

  private generateBatchTheme(decisions: DecisionRequest[]): string {
    const types = [...new Set(decisions.map(d => d.type))];
    const taskIds = [...new Set(decisions.map(d => d.context.taskId))];
    return `${taskIds[0]} - ${types.join(", ")} related decisions (${decisions.length} items)`;
  }

  private getHighestPriority(decisions: DecisionRequest[]): DecisionPriority {
    const order: Record<DecisionPriority, number> = { critical: 4, high: 3, normal: 2, low: 1 };
    return decisions.reduce((highest, d) =>
      order[d.priority] > order[highest] ? d.priority : highest,
      decisions[0].priority
    );
  }

  private estimateReviewTime(decisions: DecisionRequest[]): number {
    // Base time + per-decision review time
    const baseTime = 5000;  // 5 seconds base time (reading batch title, etc.)
    const perDecisionTime = decisions.reduce((sum, d) => {
      switch (d.complexity) {
        case x when x > 0.7: return sum + 60000;   // High complexity: ~60s
        case x when x > 0.4: return sum + 30000;   // Medium complexity: ~30s
        default: return sum + 10000;                // Low complexity: ~10s
      }
    }, 0);
    return baseTime + perDecisionTime;
  }

  handleBatchResponse(
    batchId: string,
    response: BatchResponse
  ): BatchProcessingResult {
    const batch = this.pendingBatches.find(b => b.id === batchId);
    if (!batch) {
      return { success: false, approvedDecisions: [], rejectedDecisions: [], modifiedDecisions: [], autoProcessedDecisions: [] };
    }

    batch.humanResponse = response;
    batch.status = response.type === "approve_all" ? "approved"
      : response.type === "reject_all" ? "rejected"
      : "partially_approved";

    // Remove processed decisions from queue
    const processedIds = new Set(batch.decisions.map(d => d.id));
    this.queue = this.queue.filter(d => !processedIds.has(d.id));

    const result: BatchProcessingResult = {
      success: true,
      approvedDecisions: [],
      rejectedDecisions: [],
      modifiedDecisions: [],
      autoProcessedDecisions: [],
    };

    switch (response.type) {
      case "approve_all":
        result.approvedDecisions = batch.decisions.map(d => d.id);
        break;
      case "reject_all":
        result.rejectedDecisions = batch.decisions.map(d => d.id);
        break;
      case "selective":
        if (response.selections) {
          for (const [decisionId, optionId] of Object.entries(response.selections)) {
            const decision = batch.decisions.find(d => d.id === decisionId);
            if (decision) {
              const selectedOption = decision.options.find(o => o.id === optionId);
              if (selectedOption) {
                result.approvedDecisions.push(decisionId);
              }
            }
          }
        }
        if (response.modifications) {
          result.modifiedDecisions = Object.keys(response.modifications);
        }
        break;
      case "modify":
        result.modifiedDecisions = batch.decisions.map(d => d.id);
        break;
    }

    return result;
  }

  private tryCreateBatches(): void {
    // Background batch creation logic (created on-demand when getNextBatch is called)
  }
}
```

#### 1.4 Priority Sorting and Smart Delay

```typescript
/** Priority and delay scheduler */
interface PriorityDelayScheduler {
  /** Schedule the presentation timing of a decision */
  schedule(
    decision: DecisionRequest,
    userId: string,
    loadAssessment: CognitiveLoadAssessment
  ): ScheduleResult;

  /** Get decisions that should currently be presented */
  getDueDecisions(userId: string): DecisionRequest[];

  /** Cancel delay (e.g. human proactively requests to see all decisions) */
  cancelDelay(userId: string): void;
}

interface ScheduleResult {
  action: "present_now" | "delay" | "auto_approve" | "queue";
  delayUntil?: number;
  autoApproveReason?: string;
  queuePosition?: number;
}

/**
 * Smart delay strategy
 *
 * Whether a decision is delayed depends on:
 * 1. The decision's urgency (whether it has a deadline)
 * 2. The decision's risk level
 * 3. The human's current cognitive load
 * 4. Whether the decision has blocking dependencies (other decisions waiting for this decision's result)
 */
class SmartDelayScheduler implements PriorityDelayScheduler {
  private delayedDecisions: Map<string, Map<string, { decision: DecisionRequest; delayUntil: number }>> = new Map();

  schedule(
    decision: DecisionRequest,
    userId: string,
    loadAssessment: CognitiveLoadAssessment
  ): ScheduleResult {
    // Rule 1: Critical priority is always presented immediately
    if (decision.priority === "critical") {
      return { action: "present_now" };
    }

    // Rule 2: Decisions with deadlines are presented at an appropriate time before the deadline
    if (decision.deadline) {
      const timeUntilDeadline = decision.deadline - Date.now();
      const reviewTime = this.estimateReviewTime(decision);

      if (timeUntilDeadline <= reviewTime * 2) {
        return { action: "present_now" }; // Deadline is urgent, present immediately
      }
    }

    // Rule 3: High-risk decisions are not auto-approved, but can be delayed
    if (decision.riskLevel === "critical" || decision.riskLevel === "high") {
      if (loadAssessment.level === "overloaded") {
        // When overloaded, delay high-risk decisions but with a short delay
        const delayMs = Math.min(loadAssessment.recommendation.delayNonUrgentMs, 5 * 60 * 1000);
        return { action: "delay", delayUntil: Date.now() + delayMs };
      }
      return { action: "present_now" };
    }

    // Rule 4: Low-risk decisions are auto-handled under high load
    const autoApproveThreshold = loadAssessment.recommendation.autoApproveBelowRisk;
    if (this.riskLevelBelow(decision.riskLevel, autoApproveThreshold)) {
      if (decision.aiConfidence > 0.85) {
        return {
          action: "auto_approve",
          autoApproveReason: `Low risk (${decision.riskLevel}) + high confidence (${decision.aiConfidence.toFixed(2)}) + high load (${loadAssessment.level})`,
        };
      }
    }

    // Rule 5: Decisions with blocking dependencies are presented first
    if (decision.dependencies.length > 0) {
      // Check if other decisions are waiting for this decision
      const hasDependents = this.hasDependentDecisions(decision.id);
      if (hasDependents) {
        return { action: "present_now" };
      }
    }

    // Rule 6: Decide delay based on load
    if (loadAssessment.level === "high" || loadAssessment.level === "overloaded") {
      const delayMs = loadAssessment.recommendation.delayNonUrgentMs;
      return { action: "delay", delayUntil: Date.now() + delayMs };
    }

    // Default: queue for batch processing
    return { action: "queue" };
  }

  getDueDecisions(userId: string): DecisionRequest[] {
    const now = Date.now();
    const userDelays = this.delayedDecisions.get(userId);
    if (!userDelays) return [];

    const due: DecisionRequest[] = [];
    for (const [id, entry] of userDelays) {
      if (entry.delayUntil <= now) {
        due.push(entry.decision);
        userDelays.delete(id);
      }
    }

    return due;
  }

  cancelDelay(userId: string): void {
    this.delayedDecisions.delete(userId);
  }

  private riskLevelBelow(level: RiskLevel, threshold: RiskLevel): boolean {
    const order: Record<RiskLevel, number> = { minimal: 0, low: 1, medium: 2, high: 3, critical: 4 };
    return order[level] < order[threshold];
  }

  private hasDependentDecisions(_decisionId: string): boolean {
    // Check if there are decisions in the queue that depend on this decision
    return false;
  }

  private estimateReviewTime(decision: DecisionRequest): number {
    switch (decision.complexity) {
      case x when x > 0.7: return 60000;
      case x when x > 0.4: return 30000;
      default: return 10000;
    }
  }
}
```

#### 1.5 Adaptive Presentation Engine

```typescript
/** Adaptive presentation engine */
interface AdaptivePresentationEngine {
  /** Generate presentation content based on load and decision content */
  generatePresentation(
    batch: DecisionBatch,
    loadAssessment: CognitiveLoadAssessment,
    trustScore: number
  ): Presentation;

  /** Generate decision summary (for async notifications) */
  generateSummary(
    batch: DecisionBatch,
    loadAssessment: CognitiveLoadAssessment
  ): string;
}

interface Presentation {
  title: string;
  summary: string;              // One-sentence summary
  details: DecisionDetail[];    // Detailed info for each decision
  actions: PresentationAction[];// Available actions
  metadata: {
    estimatedReviewTimeMs: number;
    urgency: "immediate" | "soon" | "flexible";
    canBatchApprove: boolean;
    canAutoApprove: boolean;
    explanationDepth: "full" | "summary" | "minimal";
  };
}

interface DecisionDetail {
  decisionId: string;
  title: string;
  description: string;
  reasoning: string;            // AI's reasoning (truncated based on explanationDepth)
  options: PresentationOption[];
  riskLevel: RiskLevel;
  aiConfidence: number;
  isRecommended: boolean;       // Whether AI recommends an option
  expandable: boolean;          // Whether expandable for more details
}

interface PresentationOption {
  id: string;
  label: string;
  summary: string;              // One-sentence summary
  details?: string;             // Detailed description (shown when expanded)
  impact: string;               // Impact description
  recommended: boolean;
}

interface PresentationAction {
  type: "approve" | "reject" | "modify" | "defer" | "approve_all" | "view_details" | "ask_ai";
  label: string;
  description: string;
  primary: boolean;             // Whether this is the primary action
  destructive: boolean;         // Whether this is a destructive action (needs confirmation)
}

class DefaultPresentationEngine implements AdaptivePresentationEngine {
  generatePresentation(
    batch: DecisionBatch,
    loadAssessment: CognitiveLoadAssessment,
    trustScore: number
  ): Presentation {
    const depth = loadAssessment.recommendation.explanationDepth;

    // Adjust presentation based on trust
    const trustAdjustedDepth = trustScore > 0.80 && depth === "summary" ? "minimal" : depth;

    return {
      title: batch.title,
      summary: this.generateBatchSummary(batch, trustAdjustedDepth),
      details: batch.decisions.map(d => this.formatDecision(d, trustAdjustedDepth)),
      actions: this.generateActions(batch, loadAssessment),
      metadata: {
        estimatedReviewTimeMs: batch.estimatedReviewTimeMs,
        urgency: batch.priority === "critical" ? "immediate"
          : batch.priority === "high" ? "soon"
          : "flexible",
        canBatchApprove: batch.decisions.length > 1 && loadAssessment.level !== "low",
        canAutoApprove: loadAssessment.recommendation.autoApproveBelowRisk !== "minimal",
        explanationDepth: trustAdjustedDepth,
      },
    };
  }

  private formatDecision(decision: DecisionRequest, depth: string): DecisionDetail {
    const isExpandable = depth !== "full";

    return {
      decisionId: decision.id,
      title: decision.description,
      description: depth === "minimal" ? this.truncate(decision.description, 50) : decision.description,
      reasoning: depth === "full" ? decision.reasoning
        : depth === "summary" ? this.truncate(decision.reasoning, 200)
        : this.truncate(decision.reasoning, 50),
      options: decision.options.map(o => ({
        id: o.id,
        label: o.label,
        summary: depth === "minimal" ? this.truncate(o.description, 30) : o.description,
        details: isExpandable ? o.description : undefined,
        impact: depth !== "minimal" ? this.formatImpact(o.estimatedImpact) : undefined,
        recommended: o.recommended,
      })),
      riskLevel: decision.riskLevel,
      aiConfidence: decision.aiConfidence,
      isRecommended: decision.options.some(o => o.recommended),
      expandable: isExpandable,
    };
  }

  private generateActions(batch: DecisionBatch, load: CognitiveLoadAssessment): PresentationAction[] {
    const actions: PresentationAction[] = [];

    if (batch.decisions.length === 1) {
      // Single decision
      actions.push(
        { type: "approve", label: "Approve", description: "Agree with AI's recommendation", primary: true, destructive: false },
        { type: "reject", label: "Reject", description: "Disagree, need a new proposal", primary: false, destructive: false },
        { type: "modify", label: "Modify", description: "Modify then approve", primary: false, destructive: false },
      );
    } else {
      // Batch decisions
      actions.push(
        { type: "approve_all", label: "Approve All", description: "Agree with all decisions", primary: true, destructive: false },
        { type: "reject", label: "Reject All", description: "Reject all decisions", primary: false, destructive: false },
        { type: "modify", label: "Select Individually", description: "Review and select each one", primary: false, destructive: false },
      );
    }

    // Non-urgent decisions can be deferred
    if (batch.priority !== "critical") {
      actions.push(
        { type: "defer", label: "Handle Later", description: "Delay until available", primary: false, destructive: false },
      );
    }

    // Under high load, provide "Let AI Decide" option
    if (load.level === "high" || load.level === "overloaded") {
      actions.push(
        { type: "ask_ai", label: "Let AI Decide", description: "AI auto-selects the recommended option", primary: false, destructive: true },
      );
    }

    return actions;
  }

  generateSummary(batch: DecisionBatch, load: CognitiveLoadAssessment): string {
    const decisionCount = batch.decisions.length;
    const highRiskCount = batch.decisions.filter(d => d.riskLevel === "high" || d.riskLevel === "critical").length;

    if (load.level === "overloaded") {
      return `[${batch.priority}] ${decisionCount} decisions pending${highRiskCount > 0 ? ` (${highRiskCount} high-risk)` : ""}`;
    }

    return `${batch.title} - ${decisionCount} decisions, estimated review ${Math.ceil(batch.estimatedReviewTimeMs / 1000)} seconds`;
  }

  private truncate(text: string, maxLen: number): string {
    if (text.length <= maxLen) return text;
    return text.slice(0, maxLen - 3) + "...";
  }

  private formatImpact(impact: { cost: number; time: number; risk: number }): string {
    return `Cost: ${impact.cost}, Time: ${impact.time}, Risk: ${impact.risk}`;
  }

  private generateBatchSummary(batch: DecisionBatch, depth: string): string {
    if (depth === "minimal") {
      return `${batch.decisions.length} decisions`;
    }
    return batch.title;
  }
}
```

### 2. Cognitive Load Management Flow

```
AI generates decision request:
  |
  v
PriorityDelayScheduler.schedule()
  ├── Critical -> present immediately
  ├── High risk -> decide based on load (short delay when overloaded)
  ├── Low risk + high load + high confidence -> auto-approve
  ├── Has blocking dependencies -> present immediately
  ├── High/overloaded load -> delay
  └── Normal load -> queue for batch processing
  |
  v
DecisionBatcher.getNextBatch()
  ├── Check for Critical decisions -> present individually
  ├── Group by theme -> select best group
  ├── Sort by dependencies
  └── Control batch size (limited by cognitive load)
  |
  v
CognitiveLoadEvaluator.evaluate()
  ├── Calculate current cognitive load score
  └── Generate presentation strategy recommendation
  |
  v
AdaptivePresentationEngine.generatePresentation()
  ├── Adjust information density based on load
  ├── Adjust explanation depth based on trust
  ├── Generate available actions
  └── Estimate review time
  |
  v
Present to human
  |
  ├── Human responds -> DecisionBatcher.handleBatchResponse()
  │   ├── Approve all -> execute all decisions
  │   ├── Select individually -> execute selected decisions
  │   ├── Reject -> notify AI to regenerate proposals
  │   └── Defer -> put back in delay queue
  |
  └── No response (timeout)
      ├── Critical/High -> escalate notification (email/push)
      ├── Normal -> delay longer
      └── Low -> auto-approve recommended option (log the action)
```

---

## Advantages

1. **Prevents decision fatigue**: Through batch merging and smart delay, significantly reduces the number of decisions humans need to handle
2. **Adaptive**: Dynamically adjusts presentation strategy based on human's current state, no "one-size-fits-all"
3. **Preserves sense of control**: Even under high load, high-risk decisions are still presented to humans
4. **Efficiency improvement**: Low-risk + high-confidence decisions can be auto-handled, not wasting human time
5. **Explainable**: Every auto-approved decision has a reason explanation; humans can review afterwards
6. **Synergy with P010/P011**: Higher trust enables more auto-approved decisions; permission levels affect approval workflows
7. **Progressive disclosure**: Information unfolds from summary to details layer by layer; humans access on demand

## Risks

1. **Auto-approval risk**: Auto-approving low-risk decisions under high load may cause errors
   - Mitigation: Only auto-approve when AI confidence > 0.85; auto-approved decisions are logged for post-hoc review
2. **Delay causing blocking**: Delayed decisions may block subsequent tasks that depend on them
   - Mitigation: Decisions with blocking dependencies are not delayed; delayed decisions auto-escalate after timeout
3. **Batch too large**: After batch merging, decision packages may be overly complex
   - Mitigation: Strictly control batch size; provide "review individually" option
4. **Human state misjudgment**: Cognitive load assessment may be inaccurate (e.g., human distraction misjudged as high load)
   - Mitigation: Use multi-signal cross-validation; humans can override at any time (cancel delay, view all decisions)
5. **Information loss**: Minimal presentation under high load may miss important information
   - Mitigation: Key information (risk level, impact scope) is always shown; provide "view details" entry point

---

## Pending Decisions

- [ ] Auto-approval threshold: Is AI confidence 0.85 appropriate? Should it be adjusted by task type?
- [ ] Post-delay-timeout behavior: auto-approve recommended option vs escalate notification vs mark as "needs human handling"?
- [ ] Batch merging granularity: group by task vs group by theme vs group by time window?
- [ ] Cognitive load evaluation frequency: evaluate on every decision request vs periodic evaluation (e.g., every minute)?
- [ ] Human state awareness data sources: only system-internal behavior signals vs integrate external signals (e.g., calendar, device status)?
- [ ] Post-hoc review mechanism: should auto-approved decisions be periodically summarized for human review?
- [ ] Integration with notification system: through what channel to notify humans when delayed decisions come due?
- [ ] Multi-user scenario: when multiple users share a Factory, how should cognitive load be managed separately?
