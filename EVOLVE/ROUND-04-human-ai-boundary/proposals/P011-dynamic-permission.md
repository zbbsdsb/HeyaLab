# Proposal P011: Dynamic Permission System

> **Status**: Draft
> **Proposer**: AI Assistant
> **Date**: 2026-05-16
> **Related**: ROUND-04 brainstorm, P010 (Trust Score Model), VISION.md ("The AI never silently makes a high-stakes decision")

---

## Summary

This proposal presents a trust-based Dynamic Permission System where AI autonomy adjusts in real-time based on changes in human trust. The system defines five permission levels (L0 Locked to L4 Restricted Autonomous), each corresponding to different operation scopes, approval workflows, and error handling strategies. Permissions are not global, but finely controlled through a two-dimensional matrix of "operation type x task type."

The core design consists of four parts: permission level definitions and operation scope matrix, an upgrade/downgrade engine (slow upgrade, fast downgrade principle), an approval workflow simplification mechanism (reducing approval steps under high trust), and a permission change notification and audit system.

---

## Design

### Overall Architecture

```
┌──────────────────────────────────────────────────────────────┐
│                  Dynamic Permission System                    │
│                                                              │
│  ┌──────────────┐  ┌──────────────┐  ┌───────────────────┐  │
│  │  Permission   │  │  Level       │  │  Approval         │  │
│  │  Matrix       │  │  Transition  │  │  Simplifier       │  │
│  │  (Ops x Tasks)│  │  Engine      │  │  (Workflow        │  │
│  └──────┬───────┘  └──────┬───────┘  │   Simplification)  │  │
│         │                 │          └────────┬──────────┘  │
│  ┌──────┴─────────────────┴────────────────────┴──────────┐  │
│  │              Permission State Manager                   │  │
│  │   (current permission state, change history, audit log) │  │
│  └──────────────────────────┬────────────────────────────┘  │
│                             │                                │
│              ┌──────────────┴──────────────┐                 │
│              │                             │                 │
│  ┌───────────┴──────────┐    ┌─────────────┴────────────┐  │
│  │  Trust Score Input   │    │  Permission Enforcer     │  │
│  │  (from P010)         │    │  (runtime permission     │  │
│  └──────────────────────┘    │   checks)                │  │
│                              └──────────────────────────┘  │
└──────────────────────────────────────────────────────────────┘
```

### 1. TypeScript Interface Definitions

#### 1.1 Permission Levels and Operation Scope

```typescript
/** Permission level */
type PermissionLevel = 0 | 1 | 2 | 3 | 4;

/** Permission level description */
interface PermissionLevelDefinition {
  level: PermissionLevel;
  name: string;
  description: string;
  humanInvolvement: "every_step" | "key_checkpoints" | "anomalies_only" | "hard_boundaries_only" | "none";
  autoExecuteScope: AutoExecuteScope;
  approvalRequirements: ApprovalRequirements;
  errorHandlingScope: ErrorHandlingScope;
}

/** Auto-execute scope */
interface AutoExecuteScope {
  allowed: boolean;                  // Whether auto-execution is allowed
  maxCostPerAction: number;          // Max cost per single operation (USD)
  maxCostPerSession: number;         // Max cost per single session
  allowedRiskLevels: RiskLevel[];    // Allowed risk levels
  allowedOperations: OperationType[]; // Allowed operation types
  timeoutMs: number;                 // Auto-execute timeout
  requirePreCheck: boolean;          // Whether pre-execution check is required
  requirePostCheck: boolean;         // Whether post-execution verification is required
}

/** Approval requirements */
interface ApprovalRequirements {
  preApproval: boolean;              // Whether pre-execution approval is needed
  postApproval: boolean;             // Whether post-execution confirmation is needed
  batchApproval: boolean;            // Whether batch approval is supported
  approvalTimeoutMs: number;         // Approval timeout (default behavior after timeout)
  approvalTimeoutAction: "deny" | "approve_with_warning" | "auto_approve_low_risk";
  escalationAfterMs: number;         // Escalation notification after no response
}

/** Error handling scope */
interface ErrorHandlingScope {
  autoFixableErrorTypes: ErrorType[];
  autoRetryMaxAttempts: number;
  autoRetryBackoffMs: number;
  requireHumanOnFirstError: boolean;
  requireHumanOnConsecutiveErrors: number;  // Require human after N consecutive errors
}

/** Operation type */
type OperationType =
  | "read_file"
  | "write_file"
  | "delete_file"
  | "execute_command"
  | "call_external_api"
  | "deploy"
  | "database_write"
  | "database_delete"
  | "send_notification"
  | "modify_configuration"
  | "install_dependency";

/** Error type */
type ErrorType =
  | "format_error"        // Format error (indentation, naming, etc.)
  | "style_error"         // Style error
  | "logic_error"         // Logic error
  | "runtime_error"       // Runtime error
  | "design_error"        // Design error
  | "security_error"      // Security error
  | "data_error"          // Data error
  | "compliance_error";   // Compliance error

/** Risk level */
type RiskLevel = "critical" | "high" | "medium" | "low" | "minimal";
```

#### 1.2 Permission Matrix

```typescript
/** Permission matrix: operation type x task type -> max allowed permission level */
interface PermissionMatrix {
  /** Get the max permission level for a given operation and task type */
  getMaxPermissionLevel(
    operation: OperationType,
    taskType: TaskType
  ): PermissionLevel;

  /** Get the effective permission level considering trust score */
  getEffectivePermissionLevel(
    operation: OperationType,
    taskType: TaskType,
    trustScore: number
  ): PermissionLevel;

  /** Check if an operation is allowed */
  isOperationAllowed(
    operation: OperationType,
    taskType: TaskType,
    currentLevel: PermissionLevel
  ): PermissionCheckResult;
}

interface PermissionCheckResult {
  allowed: boolean;
  reason: string;
  requiredLevel: PermissionLevel;
  currentLevel: PermissionLevel;
  upgradePath?: string[];    // How to obtain higher permission
  safetyOverride?: boolean;  // Whether rejected due to safety floor
}

/**
 * Permission matrix definition (safety floor)
 *
 * Regardless of trust level, certain operations have a maximum permission ceiling:
 *
 * Operation Type     | Code Gen | Code Review | Arch Design | File Ops | External API | Data Ops | Deploy | Creative | Docs | Testing
 * -------------------|----------|------------|-------------|----------|--------------|----------|--------|----------|------|--------
 * read_file          | L4       | L4         | L4          | L4       | L4           | L4       | L4     | L4       | L4   | L4
 * write_file         | L3       | L2         | L2          | L3       | L1           | L1       | L1     | L3       | L3   | L3
 * delete_file        | L0       | L0         | L0          | L0       | L0           | L0       | L0     | L0       | L0   | L0
 * execute_command    | L2       | L1         | L1          | L2       | L1           | L1       | L1     | L2       | L2   | L2
 * call_external_api  | L1       | L1         | L1          | L1       | L1           | L1       | L1     | L1       | L1   | L1
 * deploy             | L1       | L0         | L0          | L0       | L0           | L0       | L1     | L0       | L0   | L0
 * database_write     | L1       | L1         | L1          | L1       | L1           | L1       | L1     | L1       | L1   | L1
 * database_delete    | L0       | L0         | L0          | L0       | L0           | L0       | L0     | L0       | L0   | L0
 * send_notification  | L3       | L2         | L2          | L2       | L2           | L2       | L2     | L3       | L3   | L3
 * modify_config      | L2       | L1         | L1          | L2       | L1           | L1       | L1     | L2       | L2   | L2
 * install_dep        | L2       | L2         | L2          | L2       | L2           | L2       | L2     | L2       | L2   | L2
 */
class DefaultPermissionMatrix implements PermissionMatrix {
  private matrix: Record<OperationType, Partial<Record<TaskType, PermissionLevel>>> = {
    read_file: {
      code_generation: 4, code_review: 4, architecture_design: 4,
      file_operation: 4, external_api_call: 4, data_operation: 4,
      deployment: 4, creative_design: 4, documentation: 4, testing: 4,
    },
    write_file: {
      code_generation: 3, code_review: 2, architecture_design: 2,
      file_operation: 3, external_api_call: 1, data_operation: 1,
      deployment: 1, creative_design: 3, documentation: 3, testing: 3,
    },
    delete_file: {
      // All scenarios are L0 (must be manually confirmed)
    },
    execute_command: {
      code_generation: 2, code_review: 1, architecture_design: 1,
      file_operation: 2, external_api_call: 1, data_operation: 1,
      deployment: 1, creative_design: 2, documentation: 2, testing: 2,
    },
    call_external_api: {
      // External API calls max L1
      code_generation: 1, code_review: 1, architecture_design: 1,
      file_operation: 1, external_api_call: 1, data_operation: 1,
      deployment: 1, creative_design: 1, documentation: 1, testing: 1,
    },
    deploy: {
      deployment: 1, // Only deployment scenario allows L1
    },
    database_write: {
      // Database writes max L1
      code_generation: 1, code_review: 1, architecture_design: 1,
      file_operation: 1, external_api_call: 1, data_operation: 1,
      deployment: 1, creative_design: 1, documentation: 1, testing: 1,
    },
    database_delete: {
      // Database deletes always L0
    },
    send_notification: {
      code_generation: 3, code_review: 2, architecture_design: 2,
      file_operation: 2, external_api_call: 2, data_operation: 2,
      deployment: 2, creative_design: 3, documentation: 3, testing: 3,
    },
    modify_configuration: {
      code_generation: 2, code_review: 1, architecture_design: 1,
      file_operation: 2, external_api_call: 1, data_operation: 1,
      deployment: 1, creative_design: 2, documentation: 2, testing: 2,
    },
    install_dependency: {
      code_generation: 2, code_review: 2, architecture_design: 2,
      file_operation: 2, external_api_call: 2, data_operation: 2,
      deployment: 2, creative_design: 2, documentation: 2, testing: 2,
    },
  };

  getMaxPermissionLevel(operation: OperationType, taskType: TaskType): PermissionLevel {
    return this.matrix[operation]?.[taskType] ?? 0;
  }

  getEffectivePermissionLevel(
    operation: OperationType,
    taskType: TaskType,
    trustScore: number
  ): PermissionLevel {
    const trustLevel = this.trustScoreToLevel(trustScore);
    const maxAllowed = this.getMaxPermissionLevel(operation, taskType);
    return Math.min(trustLevel, maxAllowed);
  }

  isOperationAllowed(
    operation: OperationType,
    taskType: TaskType,
    currentLevel: PermissionLevel
  ): PermissionCheckResult {
    const required = this.getMaxPermissionLevel(operation, taskType);
    const allowed = currentLevel >= required;

    return {
      allowed,
      reason: allowed
        ? "Operation is within current permission scope"
        : `Requires permission level L${required}, currently L${currentLevel}`,
      requiredLevel: required,
      currentLevel,
      safetyOverride: !allowed && required === 0,
    };
  }

  private trustScoreToLevel(score: number): PermissionLevel {
    if (score >= 0.90) return 4;
    if (score >= 0.80) return 3;
    if (score >= 0.60) return 2;
    if (score >= 0.40) return 1;
    return 0;
  }
}
```

#### 1.3 Upgrade/Downgrade Engine

```typescript
/** Level transition engine */
interface LevelTransitionEngine {
  /** Evaluate whether an upgrade should occur */
  evaluateUpgrade(
    userId: string,
    taskType: TaskType,
    currentLevel: PermissionLevel,
    trustHistory: TrustHistory
  ): TransitionEvaluation;

  /** Evaluate whether a downgrade should occur */
  evaluateDowngrade(
    userId: string,
    taskType: TaskType,
    currentLevel: PermissionLevel,
    recentEvents: TrustEvent[]
  ): TransitionEvaluation;

  /** Execute permission change */
  executeTransition(
    userId: string,
    taskType: TaskType,
    fromLevel: PermissionLevel,
    toLevel: PermissionLevel,
    reason: string
  ): TransitionResult;
}

interface TrustHistory {
  totalInteractions: number;
  approvalCount: number;
  rejectionCount: number;
  modificationCount: number;
  rollbackCount: number;
  consecutiveApprovals: number;
  consecutiveRejections: number;
  avgReviewDurationMs: number;
  daysSinceFirstUse: number;
  daysSinceLastError: number;
  currentTrustScore: number;
}

interface TransitionEvaluation {
  shouldTransition: boolean;
  targetLevel: PermissionLevel;
  confidence: number;           // Evaluation confidence
  reasons: string[];            // Upgrade/downgrade reasons
  unmetRequirements: string[];  // Unmet conditions
}

interface TransitionResult {
  success: boolean;
  previousLevel: PermissionLevel;
  newLevel: PermissionLevel;
  effectiveAt: number;
  trialPeriodEndsAt: number;    // Trial period end time
  auditLogId: string;
}

/**
 * Upgrade condition definitions
 *
 * Principle: slow upgrade, fast downgrade
 * - Upgrades require multiple conditions to be met simultaneously
 * - Downgrades require only one condition to trigger
 */
class DefaultTransitionEngine implements LevelTransitionEngine {
  /** Upgrade requirements table */
  private static readonly UPGRADE_REQUIREMENTS: Record<number, UpgradeRequirement> = {
    1: { // L0 -> L1
      minInteractions: 10,
      minApprovalRate: 0.70,
      maxConsecutiveRejections: 2,
      minDaysSinceFirstUse: 1,
      minTrustScore: 0.40,
      trialPeriodDays: 3,
    },
    2: { // L1 -> L2
      minInteractions: 30,
      minApprovalRate: 0.85,
      maxConsecutiveRejections: 1,
      minDaysSinceFirstUse: 7,
      minTrustScore: 0.60,
      maxAvgReviewDurationMs: 30000,
      trialPeriodDays: 7,
    },
    3: { // L2 -> L3
      minInteractions: 80,
      minApprovalRate: 0.92,
      maxConsecutiveRejections: 0,
      minDaysSinceFirstUse: 30,
      minTrustScore: 0.80,
      maxAvgReviewDurationMs: 15000,
      minDaysSinceLastError: 14,
      trialPeriodDays: 14,
    },
    4: { // L3 -> L4
      minInteractions: 200,
      minApprovalRate: 0.97,
      maxConsecutiveRejections: 0,
      minDaysSinceFirstUse: 90,
      minTrustScore: 0.90,
      maxAvgReviewDurationMs: 5000,
      minDaysSinceLastError: 30,
      requireHumanApproval: true,  // L4 upgrade must be proactively approved by human
      trialPeriodDays: 30,
    },
  };

  /** Downgrade triggers table */
  private static readonly DOWNGRADE_TRIGGERS: DowngradeTrigger[] = [
    { condition: "safety_violation", targetLevel: 0, immediate: true },
    { condition: "data_loss", targetLevel: 0, immediate: true },
    { condition: "compliance_violation", targetLevel: 0, immediate: true },
    { condition: "consecutive_rejections_5", targetLevel: -1, immediate: true },
    { condition: "consecutive_rejections_3", targetLevel: -1, immediate: false },
    { condition: "rollback_event", targetLevel: -1, immediate: false },
    { condition: "trust_score_below_0.3", targetLevel: 0, immediate: false },
    { condition: "trust_score_below_0.5", targetLevel: -1, immediate: false },
    { condition: "human_manual_downgrade", targetLevel: -2, immediate: true },
  ];

  evaluateUpgrade(
    userId: string,
    taskType: TaskType,
    currentLevel: PermissionLevel,
    history: TrustHistory
  ): TransitionEvaluation {
    if (currentLevel >= 4) {
      return {
        shouldTransition: false,
        targetLevel: currentLevel,
        confidence: 1,
        reasons: ["Already at maximum permission level"],
        unmetRequirements: [],
      };
    }

    const requirement = DefaultTransitionEngine.UPGRADE_REQUIREMENTS[currentLevel + 1];
    if (!requirement) {
      return {
        shouldTransition: false,
        targetLevel: currentLevel,
        confidence: 0,
        reasons: ["No upgrade path defined"],
        unmetRequirements: [],
      };
    }

    const checks: { met: boolean; description: string }[] = [
      { met: history.totalInteractions >= requirement.minInteractions, description: `Min interactions: ${history.totalInteractions}/${requirement.minInteractions}` },
      { met: (history.approvalCount / Math.max(1, history.approvalCount + history.rejectionCount)) >= requirement.minApprovalRate, description: `Approval rate: ${(history.approvalCount / Math.max(1, history.approvalCount + history.rejectionCount)).toFixed(2)}/${requirement.minApprovalRate}` },
      { met: history.consecutiveRejections <= requirement.maxConsecutiveRejections, description: `Consecutive rejections: ${history.consecutiveRejections}/${requirement.maxConsecutiveRejections}` },
      { met: history.daysSinceFirstUse >= requirement.minDaysSinceFirstUse, description: `Days since first use: ${history.daysSinceFirstUse}/${requirement.minDaysSinceFirstUse}` },
      { met: history.currentTrustScore >= requirement.minTrustScore, description: `Trust score: ${history.currentTrustScore.toFixed(2)}/${requirement.minTrustScore}` },
    ];

    if (requirement.maxAvgReviewDurationMs !== undefined) {
      checks.push({
        met: history.avgReviewDurationMs <= requirement.maxAvgReviewDurationMs,
        description: `Avg review time: ${history.avgReviewDurationMs}ms/${requirement.maxAvgReviewDurationMs}ms`,
      });
    }

    if (requirement.minDaysSinceLastError !== undefined) {
      checks.push({
        met: history.daysSinceLastError >= requirement.minDaysSinceLastError,
        description: `Days since last error: ${history.daysSinceLastError}/${requirement.minDaysSinceLastError}`,
      });
    }

    const allMet = checks.every(c => c.met);
    const unmet = checks.filter(c => !c.met).map(c => c.description);

    return {
      shouldTransition: allMet,
      targetLevel: (currentLevel + 1) as PermissionLevel,
      confidence: allMet ? 1 : checks.filter(c => c.met).length / checks.length,
      reasons: allMet ? ["All upgrade conditions met"] : [],
      unmetRequirements: unmet,
    };
  }

  evaluateDowngrade(
    userId: string,
    taskType: TaskType,
    currentLevel: PermissionLevel,
    recentEvents: TrustEvent[]
  ): TransitionEvaluation {
    for (const trigger of DefaultTransitionEngine.DOWNGRADE_TRIGGERS) {
      if (this.matchesTrigger(trigger.condition, recentEvents)) {
        const targetLevel = Math.max(
          0,
          (currentLevel + trigger.targetLevel) as PermissionLevel
        );

        if (targetLevel < currentLevel) {
          return {
            shouldTransition: true,
            targetLevel: targetLevel as PermissionLevel,
            confidence: trigger.immediate ? 1 : 0.8,
            reasons: [this.describeTrigger(trigger.condition)],
            unmetRequirements: [],
          };
        }
      }
    }

    return {
      shouldTransition: false,
      targetLevel: currentLevel,
      confidence: 1,
      reasons: ["No downgrade trigger conditions"],
      unmetRequirements: [],
    };
  }
}

interface UpgradeRequirement {
  minInteractions: number;
  minApprovalRate: number;
  maxConsecutiveRejections: number;
  minDaysSinceFirstUse: number;
  minTrustScore: number;
  maxAvgReviewDurationMs?: number;
  minDaysSinceLastError?: number;
  requireHumanApproval?: boolean;
  trialPeriodDays: number;
}

interface DowngradeTrigger {
  condition: string;
  targetLevel: number;   // Offset relative to current level (negative = downgrade)
  immediate: boolean;    // Whether to execute immediately
}
```

#### 1.4 Approval Workflow Simplification

```typescript
/** Approval workflow simplifier */
interface ApprovalSimplifier {
  /**
   * Generate approval workflow based on permission level and operation risk
   *
   * L0: Full approval workflow (reason explanation + risk assessment + human confirmation + double confirmation)
   * L1: Standard approval workflow (reason explanation + human confirmation)
   * L2: Simplified approval workflow (only key operations need confirmation, batch approval available)
   * L3: Minimal approval workflow (only confirm on anomalies, auto-execute routine operations)
   * L4: No approval workflow (only hard boundary checks)
   */
  generateApprovalFlow(
    operation: PlannedOperation,
    currentLevel: PermissionLevel,
    trustScore: number
  ): ApprovalFlow;
}

interface PlannedOperation {
  id: string;
  type: OperationType;
  taskType: TaskType;
  riskLevel: RiskLevel;
  description: string;
  estimatedCost: number;
  affectedResources: string[];
  reversible: boolean;
  rollbackPlan?: string;
  aiConfidence: number;
}

interface ApprovalFlow {
  steps: ApprovalStep[];
  canBatch: boolean;
  canAutoApprove: boolean;
  estimatedHumanTimeMs: number;  // Estimated time required from human
}

interface ApprovalStep {
  type: "explain" | "confirm" | "risk_acknowledge" | "double_confirm" | "timeout_check";
  required: boolean;
  canSkip: boolean;
  skipCondition?: string;
  content: string;
  estimatedTimeMs: number;
}

class DefaultApprovalSimplifier implements ApprovalSimplifier {
  generateApprovalFlow(
    operation: PlannedOperation,
    currentLevel: PermissionLevel,
    trustScore: number
  ): ApprovalFlow {
    // Safety floor: certain operations always require approval regardless of permission level
    if (operation.type === "delete_file" || operation.type === "database_delete") {
      return this.fullApprovalFlow(operation);
    }

    if (operation.riskLevel === "critical") {
      return this.fullApprovalFlow(operation);
    }

    switch (currentLevel) {
      case 0: return this.fullApprovalFlow(operation);
      case 1: return this.standardApprovalFlow(operation);
      case 2: return this.simplifiedApprovalFlow(operation, trustScore);
      case 3: return this.minimalApprovalFlow(operation, trustScore);
      case 4: return this.noApprovalFlow(operation);
      default: return this.fullApprovalFlow(operation);
    }
  }

  /** L0: Full approval workflow */
  private fullApprovalFlow(op: PlannedOperation): ApprovalFlow {
    return {
      steps: [
        { type: "explain", required: true, canSkip: false, content: "Detailed explanation of operation purpose, impact scope, and risk assessment", estimatedTimeMs: 30000 },
        { type: "risk_acknowledge", required: true, canSkip: false, content: "Human confirms understanding of risks", estimatedTimeMs: 5000 },
        { type: "confirm", required: true, canSkip: false, content: "Human confirms execution", estimatedTimeMs: 3000 },
        { type: "double_confirm", required: !op.reversible, canSkip: op.reversible, content: "Double confirmation for irreversible operation", estimatedTimeMs: 5000 },
      ],
      canBatch: false,
      canAutoApprove: false,
      estimatedHumanTimeMs: 43000,
    };
  }

  /** L1: Standard approval workflow */
  private standardApprovalFlow(op: PlannedOperation): ApprovalFlow {
    return {
      steps: [
        { type: "explain", required: true, canSkip: false, content: "Brief explanation of operation purpose", estimatedTimeMs: 10000 },
        { type: "confirm", required: true, canSkip: false, content: "Human confirms execution", estimatedTimeMs: 3000 },
      ],
      canBatch: true,
      canAutoApprove: false,
      estimatedHumanTimeMs: 13000,
    };
  }

  /** L2: Simplified approval workflow */
  private simplifiedApprovalFlow(op: PlannedOperation, trustScore: number): ApprovalFlow {
    const isLowRisk = op.riskLevel === "low" || op.riskLevel === "minimal";
    const isHighTrust = trustScore > 0.70;

    if (isLowRisk && isHighTrust) {
      return {
        steps: [
          { type: "explain", required: true, canSkip: false, content: "One-sentence explanation", estimatedTimeMs: 2000 },
        ],
        canBatch: true,
        canAutoApprove: true,
        estimatedHumanTimeMs: 2000,
      };
    }

    return {
      steps: [
        { type: "explain", required: true, canSkip: false, content: "Brief explanation of operation purpose", estimatedTimeMs: 5000 },
        { type: "confirm", required: true, canSkip: isLowRisk, skipCondition: "Low-risk operations can be skipped", content: "Human confirms execution", estimatedTimeMs: 3000 },
      ],
      canBatch: true,
      canAutoApprove: isLowRisk,
      estimatedHumanTimeMs: 8000,
    };
  }

  /** L3: Minimal approval workflow */
  private minimalApprovalFlow(op: PlannedOperation, trustScore: number): ApprovalFlow {
    const isLowRisk = op.riskLevel === "low" || op.riskLevel === "minimal";

    if (isLowRisk) {
      return {
        steps: [],
        canBatch: true,
        canAutoApprove: true,
        estimatedHumanTimeMs: 0,
      };
    }

    return {
      steps: [
        { type: "confirm", required: true, canSkip: false, content: "Medium/high-risk operation confirmation", estimatedTimeMs: 3000 },
      ],
      canBatch: true,
      canAutoApprove: false,
      estimatedHumanTimeMs: 3000,
    };
  }

  /** L4: No approval workflow (only hard boundary checks) */
  private noApprovalFlow(op: PlannedOperation): ApprovalFlow {
    return {
      steps: [],
      canBatch: true,
      canAutoApprove: true,
      estimatedHumanTimeMs: 0,
    };
  }
}
```

#### 1.5 Permission Enforcer

```typescript
/** Runtime permission enforcer */
interface PermissionEnforcer {
  /** Check and execute operation */
  async executeWithPermission(
    operation: PlannedOperation,
    userId: string,
    currentLevel: PermissionLevel,
    trustScore: number
  ): Promise<ExecutionResult>;
}

interface ExecutionResult {
  success: boolean;
  action: "executed" | "pending_approval" | "denied" | "escalated";
  approvalFlow?: ApprovalFlow;
  denialReason?: string;
  executionResult?: unknown;
  auditEntry: AuditEntry;
}

interface AuditEntry {
  id: string;
  timestamp: number;
  userId: string;
  operation: PlannedOperation;
  permissionLevel: PermissionLevel;
  trustScore: number;
  action: "executed" | "pending_approval" | "denied" | "escalated";
  reason: string;
  durationMs: number;
}

class DefaultPermissionEnforcer implements PermissionEnforcer {
  constructor(
    private matrix: PermissionMatrix,
    private simplifier: ApprovalSimplifier
  ) {}

  async executeWithPermission(
    operation: PlannedOperation,
    userId: string,
    currentLevel: PermissionLevel,
    trustScore: number
  ): Promise<ExecutionResult> {
    // 1. Permission check
    const check = this.matrix.isOperationAllowed(
      operation.type,
      operation.taskType,
      currentLevel
    );

    if (!check.allowed) {
      return {
        success: false,
        action: "denied",
        denialReason: check.reason,
        auditEntry: this.createAuditEntry(userId, operation, currentLevel, trustScore, "denied", check.reason),
      };
    }

    // 2. Generate approval workflow
    const flow = this.simplifier.generateApprovalFlow(operation, currentLevel, trustScore);

    // 3. If approval is needed
    if (flow.steps.length > 0 && !flow.canAutoApprove) {
      return {
        success: false,
        action: "pending_approval",
        approvalFlow: flow,
        auditEntry: this.createAuditEntry(userId, operation, currentLevel, trustScore, "pending_approval", "Waiting for human approval"),
      };
    }

    // 4. Auto-execute (L2+ low risk or L3+)
    try {
      const result = await this.executeOperation(operation);
      return {
        success: true,
        action: "executed",
        executionResult: result,
        auditEntry: this.createAuditEntry(userId, operation, currentLevel, trustScore, "executed", "Auto-executed"),
      };
    } catch (error) {
      return {
        success: false,
        action: "escalated",
        denialReason: `Execution failed: ${error}`,
        auditEntry: this.createAuditEntry(userId, operation, currentLevel, trustScore, "escalated", `Execution error: ${error}`),
      };
    }
  }

  private async executeOperation(operation: PlannedOperation): Promise<unknown> {
    // Actual operation execution logic (implemented by specific OperationHandler)
    throw new Error("Not implemented - delegate to OperationHandler");
  }

  private createAuditEntry(
    userId: string,
    operation: PlannedOperation,
    level: PermissionLevel,
    score: number,
    action: string,
    reason: string
  ): AuditEntry {
    return {
      id: crypto.randomUUID(),
      timestamp: Date.now(),
      userId,
      operation,
      permissionLevel: level,
      trustScore: score,
      action: action as AuditEntry["action"],
      reason,
      durationMs: 0,
    };
  }
}
```

### 2. Permission Change Flow

```
Normal operation flow:
  1. AI plans to execute an operation
  2. PermissionEnforcer checks permissions
  3. If allowed and no approval needed -> auto-execute
  4. If approval needed -> generate ApprovalFlow -> wait for human
  5. If rejected -> record event -> may trigger downgrade evaluation

Upgrade flow:
  1. TrustScoreEngine calculates trust score
  2. LevelTransitionEngine evaluates upgrade conditions
  3. If conditions met -> generate upgrade suggestion
  4. L0->L1, L1->L2, L2->L3: auto-upgrade (enter trial period)
  5. L3->L4: requires human proactive approval
  6. If downgrade condition triggered during trial -> immediate downgrade

Downgrade flow:
  1. Downgrade trigger condition detected (rejection/rollback/safety incident)
  2. LevelTransitionEngine evaluates downgrade
  3. If confirmed -> immediate downgrade
  4. Notify human
  5. Record audit log
  6. After downgrade, must re-meet upgrade conditions to recover
```

---

## Advantages

1. **Safety floor**: The permission matrix ensures certain operations (delete, deploy, external API) always have a ceiling regardless of trust level
2. **Slow upgrade, fast downgrade**: Upgrades require long-term evidence accumulation, downgrades trigger on a single serious error, maximizing safety
3. **Fine-grained control**: The "operation type x task type" 2D matrix avoids "one-size-fits-all"
4. **Approval simplification**: Under high trust, approval workflows are significantly simplified, reducing human burden
5. **Trial period mechanism**: Each upgrade has a trial period, preventing "easy up, impossible down"
6. **Complete audit**: All permission changes and operation executions have audit logs, fully traceable
7. **Seamless integration with P010**: Trust scores directly map to permission levels

## Risks

1. **Permission matrix maintenance cost**: 11 operation types x 10 task types = 110 configuration items
   - Mitigation: Provide reasonable defaults, support batch configuration by category
2. **Upgrades too slow**: Strict upgrade conditions may cause users to remain at low permission levels for extended periods
   - Mitigation: Shadow mode accelerates data accumulation; allow manual upgrade by human (but downgrade remains automatic)
3. **Downgrades too sensitive**: A single misoperation may trigger downgrade
   - Mitigation: Non-safety downgrades have a buffer period (e.g., trigger after 3 consecutive occurrences, not 1)
4. **Approval skip risk**: Auto-execution under high permission levels may cause errors
   - Mitigation: Post-execution verification is mandatory after auto-execution; verification failure triggers immediate downgrade
5. **Multi-user permission conflicts**: Different users of the same Factory have different permission levels
   - Mitigation: Use the lowest permission level as the operation ceiling; or execute based on the initiator's permission level

---

## Pending Decisions

- [ ] Should the permission matrix support user customization? Or only admin configuration?
- [ ] Should L4 upgrade require human proactive approval? Or auto-enter trial period when conditions are met?
- [ ] Post-downgrade recovery strategy: return to previous level or directly to L0?
- [ ] Default behavior on approval timeout: deny or approve?
- [ ] Multi-user scenario: use lowest permission or use initiator's permission?
- [ ] Permission change notification method: real-time push, periodic summary, or only notify on downgrade?
- [ ] Audit log retention period: 30 days, 90 days, or permanent?
- [ ] Should "temporary privilege escalation" be supported: human temporarily authorizes AI to execute one high-permission operation?
