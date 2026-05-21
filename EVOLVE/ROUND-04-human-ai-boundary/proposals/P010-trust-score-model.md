# Proposal P010: Trust Score Model

> **Status**: Draft
> **Proposer**: AI Assistant
> **Date**: 2026-05-16
> **Related**: ROUND-04 brainstorm, VISION.md ("AI Proposes, Human Decides"), RESEARCH/SYNTHESIS.md (Reflexion, DPO)

---

## Summary

This proposal presents a multi-dimensional Trust Score Model for quantifying the level of human trust in AI across different capability dimensions. Trust is not a single 0-100 score, but a structured scoring system that includes multiple dimensions, supports time decay and recovery, and can be mapped to permission levels.

The core design consists of five parts: multi-dimensional trust scoring (accuracy/consistency/safety/creativity/efficiency), a signal collection system (explicit signals + implicit signals + context signals), an adaptive weight engine (dynamically adjusting dimension weights based on task type), a decay and recovery mechanism (time decay + event-driven recovery), and a permission mapping table (mapping rules from Trust Score to permission level).

---

## Design

### Overall Architecture

```
┌──────────────────────────────────────────────────────────────┐
│                    Trust Score Engine                         │
│                                                              │
│  ┌──────────────┐  ┌──────────────┐  ┌───────────────────┐  │
│  │   Signal      │  │   Multi-Dim  │  │   Adaptive        │  │
│  │   Collector   │──│   Scorer     │──│   Weight Engine   │  │
│  │               │  │              │  │                   │  │
│  │ Explicit/     │  │ Accuracy/    │  │ Dynamically adjust │  │
│  │ Implicit/     │  │ Consistency/ │  │ dimension weights  │  │
│  │ Context       │  │ Safety/      │  │ by task type       │  │
│  │ Signals       │  │ Creativity/  │  │                   │  │
│  │               │  │ Efficiency   │  │                   │  │
│  └──────────────┘  └──────┬───────┘  └────────┬──────────┘  │
│                           │                    │             │
│                    ┌──────┴────────────────────┴──────────┐  │
│                    │         Composite Trust Score         │  │
│                    │   (per dimension, per task type)      │  │
│                    └──────────────────┬───────────────────┘  │
│                                       │                      │
│                    ┌──────────────────┴───────────────────┐  │
│                    │         Decay & Recovery Engine       │  │
│                    │   (time decay + event-driven recovery) │  │
│                    └──────────────────┬───────────────────┘  │
│                                       │                      │
│                    ┌──────────────────┴───────────────────┐  │
│                    │         Permission Mapper             │  │
│                    │   (trust score -> permission level)   │  │
│                    └──────────────────────────────────────┘  │
└──────────────────────────────────────────────────────────────┘
```

### 1. TypeScript Interface Definitions

#### 1.1 Core Types

```typescript
/** Trust dimension */
type TrustDimension = "accuracy" | "consistency" | "safety" | "creativity" | "efficiency";

/** Trust level */
type TrustLevel = "very_low" | "low" | "medium" | "high" | "very_high";

/** Task type */
type TaskType =
  | "code_generation"
  | "code_review"
  | "architecture_design"
  | "file_operation"
  | "external_api_call"
  | "data_operation"
  | "deployment"
  | "creative_design"
  | "documentation"
  | "testing";

/** Operation risk level */
type RiskLevel = "critical" | "high" | "medium" | "low" | "minimal";

/** Single dimension trust score */
interface DimensionScore {
  dimension: TrustDimension;
  score: number;              // 0.0 - 1.0
  confidence: number;         // Score confidence 0.0 - 1.0 (low when insufficient data)
  sampleSize: number;         // Based on how many interactions
  lastUpdated: number;        // timestamp
  trend: "improving" | "stable" | "declining";  // Trend
  history: ScoreHistoryEntry[];  // Last N scores
}

interface ScoreHistoryEntry {
  timestamp: number;
  score: number;
  event: string;              // Event description that triggered the score change
}

/** Composite trust score */
interface CompositeTrustScore {
  userId: string;
  factoryId: string;

  // Per-dimension scores
  dimensions: Record<TrustDimension, DimensionScore>;

  // Per-task-type scores (derived from weighted dimension scores)
  taskTypeScores: Record<TaskType, number>;

  // Global overall score
  overallScore: number;       // 0.0 - 1.0

  // Current trust level
  trustLevel: TrustLevel;

  // Score metadata
  metadata: {
    firstCalculated: number;
    lastCalculated: number;
    totalInteractions: number;
    totalApprovals: number;
    totalRejections: number;
    totalModifications: number;
  };
}
```

#### 1.2 Signal Collection Interfaces

```typescript
/** Explicit signal: actively expressed by the human */
interface ExplicitSignal {
  type: "approval" | "rejection" | "modification" | "trust_override" | "feedback";
  timestamp: number;
  taskId: string;
  taskType: TaskType;

  // Approval details
  approvalDetail?: {
    action: "approved" | "approved_with_minor_changes" | "approved_with_major_changes";
    reviewDurationMs: number;    // Human review duration
    modificationRatio: number;   // Modification ratio 0.0 - 1.0
  };

  // Rejection details
  rejectionDetail?: {
    reason: string;
    alternativeProvided: boolean;
  };

  // Trust override
  overrideDetail?: {
    direction: "upgrade" | "downgrade";
    fromLevel: TrustLevel;
    toLevel: TrustLevel;
    reason?: string;
  };

  // Feedback
  feedbackDetail?: {
    content: string;
    sentiment: "positive" | "neutral" | "negative";
    categories: string[];       // e.g. ["accuracy", "style"]
  };
}

/** Implicit signal: inferred from system observation */
interface ImplicitSignal {
  type:
    | "review_speed"            // Review speed
    | "rollback"                // Rollback operation
    | "undo"                    // Undo operation
    | "retry"                   // Asking AI to retry
    | "switch_to_manual"        // Switch to manual mode
    | "skip_review"             // Skip review
    | "deep_dive"               // Deep dive into details
    | "quick_approve";          // Quick approve (< 3 seconds)
  timestamp: number;
  taskId: string;
  taskType: TaskType;
  value: number;                // Signal intensity
}

/** Context signal: environmental factors */
interface ContextSignal {
  type: "task_risk" | "time_pressure" | "historical_success_rate" | "ai_confidence";
  timestamp: number;
  taskType: TaskType;
  value: number;
  metadata?: Record<string, unknown>;
}

/** Signal collector */
interface SignalCollector {
  /** Record explicit signal */
  recordExplicit(signal: ExplicitSignal): void;

  /** Record implicit signal */
  recordImplicit(signal: ImplicitSignal): void;

  /** Record context signal */
  recordContext(signal: ContextSignal): void;

  /** Get aggregated signals within a specified time window */
  getAggregatedSignals(
    userId: string,
    taskType: TaskType,
    windowMs: number
  ): SignalAggregation;
}

interface SignalAggregation {
  windowStart: number;
  windowEnd: number;

  explicit: {
    approvalCount: number;
    rejectionCount: number;
    modificationCount: number;
    avgReviewDurationMs: number;
    avgModificationRatio: number;
    approvalRate: number;         // approvals / (approvals + rejections)
  };

  implicit: {
    rollbackCount: number;
    undoCount: number;
    retryCount: number;
    manualSwitchCount: number;
    skipReviewCount: number;
    avgReviewSpeed: number;       // Normalized review speed
    quickApproveRate: number;     // Quick approve rate
  };

  context: {
    avgTaskRisk: number;
    avgAiConfidence: number;
    historicalSuccessRate: number;
  };
}
```

#### 1.3 Trust Score Calculation Engine

```typescript
/** Trust score calculation engine */
interface TrustScoreEngine {
  /** Calculate or update trust score */
  calculate(
    userId: string,
    factoryId: string,
    taskType?: TaskType
  ): CompositeTrustScore;

  /** Get score for a specific dimension */
  getDimensionScore(
    userId: string,
    dimension: TrustDimension
  ): DimensionScore;

  /** Get score for a specific task type */
  getTaskTypeScore(
    userId: string,
    taskType: TaskType
  ): number;

  /** Get permission level suggestion */
  getPermissionSuggestion(
    userId: string,
    taskType: TaskType
  ): PermissionSuggestion;

  /** Get trust score trend */
  getTrend(
    userId: string,
    dimension?: TrustDimension,
    periodMs?: number
  ): TrustTrend;
}

interface PermissionSuggestion {
  currentLevel: TrustLevel;
  suggestedLevel: TrustLevel;
  reasoning: string;
  confidence: number;           // Suggestion confidence
  requirements: string[];       // Conditions still needed for upgrade (if any)
}

interface TrustTrend {
  period: { start: number; end: number };
  startScore: number;
  endScore: number;
  change: number;               // Positive = improving, negative = declining
  volatility: number;           // Volatility (standard deviation)
  direction: "improving" | "stable" | "declining" | "volatile";
}
```

### 2. Trust Score Calculation Formula

#### 2.1 Single Dimension Score Calculation

```typescript
class DimensionScorer {
  /**
   * Calculate trust score for a single dimension
   *
   * Formula:
   *   score = base * decay_factor
   *        + w_approval * approval_rate
   *        + w_review * review_speed_score
   *        + w_rollback * (1 - rollback_rate)
   *        + w_confidence * ai_confidence_alignment
   *
   * Where:
   *   base: Base trust score (new user = 0.5, with history use previous score)
   *   decay_factor: Time decay factor
   *   approval_rate: Approval rate (last N interactions)
   *   review_speed_score: Review speed score (fast = high trust)
   *   rollback_rate: Rollback rate
   *   ai_confidence_alignment: Alignment between AI self-assessed confidence and human feedback
   */
  calculateDimension(
    dimension: TrustDimension,
    signals: SignalAggregation,
    previousScore: number,
    timeSinceLastUpdateMs: number
  ): DimensionScore {
    const weights = this.getDimensionWeights(dimension);
    const decay = this.calculateDecay(timeSinceLastUpdateMs);

    // Factor calculations
    const approvalFactor = signals.explicit.approvalRate;
    const reviewSpeedFactor = this.normalizeReviewSpeed(signals.implicit.avgReviewSpeed);
    const rollbackFactor = 1 - this.calculateRollbackRate(signals.implicit);
    const confidenceFactor = this.calculateConfidenceAlignment(signals.context);

    // Weighted sum
    const rawScore =
      weights.approval * approvalFactor +
      weights.reviewSpeed * reviewSpeedFactor +
      weights.rollback * rollbackFactor +
      weights.confidence * confidenceFactor;

    // Blend with historical score (exponential moving average)
    const alpha = this.getSmoothingFactor(signals.explicit.approvalCount + signals.explicit.rejectionCount);
    const newScore = alpha * rawScore + (1 - alpha) * previousScore;

    // Apply time decay
    const finalScore = Math.max(0, Math.min(1, newScore * decay));

    return {
      dimension,
      score: finalScore,
      confidence: this.calculateConfidence(signals),
      sampleSize: signals.explicit.approvalCount + signals.explicit.rejectionCount,
      lastUpdated: Date.now(),
      trend: this.calculateTrend(finalScore, previousScore),
      history: [],  // Managed externally
    };
  }

  /** Dimension weight configuration */
  private getDimensionWeights(dimension: TrustDimension): DimensionWeights {
    const base: Record<TrustDimension, DimensionWeights> = {
      accuracy:    { approval: 0.35, reviewSpeed: 0.15, rollback: 0.30, confidence: 0.20 },
      consistency: { approval: 0.25, reviewSpeed: 0.20, rollback: 0.25, confidence: 0.30 },
      safety:      { approval: 0.20, reviewSpeed: 0.05, rollback: 0.50, confidence: 0.25 },
      creativity:  { approval: 0.40, reviewSpeed: 0.10, rollback: 0.15, confidence: 0.35 },
      efficiency:  { approval: 0.25, reviewSpeed: 0.30, rollback: 0.20, confidence: 0.25 },
    };
    return base[dimension];
  }
}

interface DimensionWeights {
  approval: number;
  reviewSpeed: number;
  rollback: number;
  confidence: number;
}
```

#### 2.2 Adaptive Weight Engine

```typescript
class AdaptiveWeightEngine {
  /**
   * Dynamically adjust dimension weights based on task type
   *
   * Example:
   *   code_generation task:
   *     accuracy: 0.35, consistency: 0.25, safety: 0.15, creativity: 0.10, efficiency: 0.15
   *
   *   creative_design task:
   *     accuracy: 0.10, consistency: 0.10, safety: 0.05, creativity: 0.55, efficiency: 0.20
   *
   *   deployment task:
   *     accuracy: 0.20, consistency: 0.15, safety: 0.40, creativity: 0.00, efficiency: 0.25
   */
  getWeightsForTaskType(taskType: TaskType): Record<TrustDimension, number> {
    const weightProfiles: Record<TaskType, Record<TrustDimension, number>> = {
      code_generation:    { accuracy: 0.35, consistency: 0.25, safety: 0.15, creativity: 0.10, efficiency: 0.15 },
      code_review:        { accuracy: 0.40, consistency: 0.25, safety: 0.15, creativity: 0.05, efficiency: 0.15 },
      architecture_design:{ accuracy: 0.25, consistency: 0.20, safety: 0.20, creativity: 0.20, efficiency: 0.15 },
      file_operation:     { accuracy: 0.20, consistency: 0.15, safety: 0.40, creativity: 0.00, efficiency: 0.25 },
      external_api_call:  { accuracy: 0.25, consistency: 0.20, safety: 0.35, creativity: 0.00, efficiency: 0.20 },
      data_operation:     { accuracy: 0.25, consistency: 0.15, safety: 0.45, creativity: 0.00, efficiency: 0.15 },
      deployment:         { accuracy: 0.20, consistency: 0.15, safety: 0.40, creativity: 0.00, efficiency: 0.25 },
      creative_design:    { accuracy: 0.10, consistency: 0.10, safety: 0.05, creativity: 0.55, efficiency: 0.20 },
      documentation:      { accuracy: 0.25, consistency: 0.20, safety: 0.05, creativity: 0.30, efficiency: 0.20 },
      testing:            { accuracy: 0.40, consistency: 0.25, safety: 0.10, creativity: 0.05, efficiency: 0.20 },
    };

    return weightProfiles[taskType];
  }

  /**
   * Calculate composite trust score for a task type
   * taskScore = sum(dimension_score * dimension_weight for each dimension)
   */
  calculateTaskTypeScore(
    dimensions: Record<TrustDimension, DimensionScore>,
    taskType: TaskType
  ): number {
    const weights = this.getWeightsForTaskType(taskType);
    let score = 0;

    for (const [dim, weight] of Object.entries(weights)) {
      const dimScore = dimensions[dim as TrustDimension];
      if (dimScore) {
        // Consider score confidence: low-confidence dimensions have reduced weight
        const effectiveWeight = weight * (0.5 + 0.5 * dimScore.confidence);
        score += dimScore.score * effectiveWeight;
      }
    }

    // Normalize (ensure weights sum to 1)
    const totalWeight = Object.values(weights).reduce((a, b) => a + b, 0);
    return Math.max(0, Math.min(1, score / totalWeight));
  }
}
```

#### 2.3 Decay and Recovery Mechanism

```typescript
class DecayRecoveryEngine {
  /**
   * Time decay formula:
   *   decay(t) = e^(-lambda * t)
   *
   * Where:
   *   lambda: Decay rate (default 0.01/day, i.e., ~100 days to decay to 37%)
   *   t: Days since last interaction
   *
   * Design principles:
   *   - Short-term disuse (< 7 days): almost no decay
   *   - Medium-term disuse (7-30 days): slow decay
   *   - Long-term disuse (> 30 days): noticeable decay
   *   - Very long-term disuse (> 90 days): decay to base level
   */
  calculateDecay(timeSinceLastUpdateMs: number): number {
    const daysSinceUpdate = timeSinceLastUpdateMs / (1000 * 60 * 60 * 24);
    const lambda = 0.01;  // Decay rate
    return Math.exp(-lambda * daysSinceUpdate);
  }

  /**
   * Event-driven recovery
   *
   * Different events have different impacts on trust:
   *   - Task successfully completed (approved without modifications): +0.02 ~ +0.05
   *   - Task successfully completed (approved with minor tweaks): +0.01 ~ +0.02
   *   - Task rejected: -0.05 ~ -0.15
   *   - Rollback occurred: -0.10 ~ -0.20
   *   - Safety incident occurred: -0.30 ~ -0.50 (immediate downgrade)
   *   - AI proactively admits error: +0.01 (honesty bonus)
   *   - Human proactively upgrades trust: +0.10
   *   - Human proactively downgrades trust: -0.20
   */
  calculateRecovery(
    currentScore: number,
    event: TrustEvent
  ): number {
    const impact = this.getEventImpact(event);
    const newScore = currentScore + impact.delta;

    // Ensure within [0, 1] range
    return Math.max(0, Math.min(1, newScore));
  }

  private getEventImpact(event: TrustEvent): { delta: number; description: string } {
    const impacts: Record<string, { delta: number; description: string }> = {
      // Positive events
      "task_approved_no_changes":    { delta: +0.03, description: "Task approved without modifications" },
      "task_approved_minor_changes": { delta: +0.01, description: "Task approved with minor changes" },
      "task_approved_major_changes": { delta: -0.01, description: "Task approved with major changes" },
      "ai_honest_error_report":      { delta: +0.01, description: "AI proactively reported error" },
      "human_trust_upgrade":         { delta: +0.10, description: "Human proactively upgraded trust" },
      "consecutive_success_10":      { delta: +0.05, description: "10 consecutive successes" },
      "consecutive_success_50":      { delta: +0.08, description: "50 consecutive successes" },

      // Negative events
      "task_rejected":               { delta: -0.08, description: "Task rejected" },
      "rollback_executed":           { delta: -0.15, description: "Rollback executed" },
      "undo_executed":               { delta: -0.10, description: "Undo executed" },
      "safety_violation":            { delta: -0.40, description: "Safety violation" },
      "data_loss_event":             { delta: -0.50, description: "Data loss event" },
      "human_trust_downgrade":       { delta: -0.20, description: "Human proactively downgraded trust" },
      "consecutive_failure_3":       { delta: -0.10, description: "3 consecutive failures" },
      "consecutive_failure_5":       { delta: -0.20, description: "5 consecutive failures" },
    };

    return impacts[event.type] ?? { delta: 0, description: "Unknown event" };
  }
}

interface TrustEvent {
  type: string;
  timestamp: number;
  taskId?: string;
  taskType?: TaskType;
  metadata?: Record<string, unknown>;
}
```

### 3. Permission Mapping Table

```typescript
class PermissionMapper {
  /**
   * Trust score -> Trust level mapping
   *
   * Mapping rules:
   *   very_high (0.80 - 1.00) -> Level 3 (anomaly confirmation)
   *   high      (0.60 - 0.80) -> Level 2 (key checkpoint confirmation)
   *   medium    (0.40 - 0.60) -> Level 1 (step-by-step confirmation)
   *   low       (0.20 - 0.40) -> Level 0 (locked mode)
   *   very_low  (0.00 - 0.20) -> Level 0 (locked mode + extra warning)
   */
  mapScoreToLevel(score: number): TrustLevel {
    if (score >= 0.80) return "very_high";
    if (score >= 0.60) return "high";
    if (score >= 0.40) return "medium";
    if (score >= 0.20) return "low";
    return "very_low";
  }

  /**
   * Trust level -> Permission level mapping
   * (Interfaces with P011 Dynamic Permission System)
   */
  mapToPermissionLevel(trustLevel: TrustLevel): number {
    const mapping: Record<TrustLevel, number> = {
      very_high: 3,
      high: 2,
      medium: 1,
      low: 0,
      very_low: 0,
    };
    return mapping[trustLevel];
  }

  /**
   * Get permission details
   */
  getPermissionDetails(
    trustLevel: TrustLevel,
    taskType: TaskType
  ): PermissionDetail {
    const baseLevel = this.mapToPermissionLevel(trustLevel);

    // Some task types have minimum permission requirements (safety floor)
    const minLevel = this.getSafetyFloor(taskType);
    const effectiveLevel = Math.max(baseLevel, minLevel);

    return {
      trustLevel,
      permissionLevel: effectiveLevel,
      isSafetyFloor: effectiveLevel > baseLevel,
      allowedActions: this.getAllowedActions(effectiveLevel, taskType),
      requiredApprovals: this.getRequiredApprovals(effectiveLevel, taskType),
      autoFixableErrors: this.getAutoFixableErrors(effectiveLevel),
    };
  }

  /** Safety floor: certain operations always require human confirmation regardless of trust */
  private getSafetyFloor(taskType: TaskType): number {
    const floors: Partial<Record<TaskType, number>> = {
      data_operation: 0,       // Data operations always require confirmation
      deployment: 1,           // Deployment requires at least key checkpoint confirmation
      external_api_call: 1,    // External API requires at least key checkpoint confirmation
    };
    return floors[taskType] ?? 0;
  }
}

interface PermissionDetail {
  trustLevel: TrustLevel;
  permissionLevel: number;
  isSafetyFloor: boolean;
  allowedActions: string[];
  requiredApprovals: string[];
  autoFixableErrors: string[];
}
```

### 4. Trust Score Evaluation Flow

```
1. Signal Collection
   ├── Human approves task -> record ExplicitSignal(approval)
   ├── Human rejects task -> record ExplicitSignal(rejection)
   ├── Human modifies AI output -> record ExplicitSignal(modification)
   ├── System detects quick approve -> record ImplicitSignal(quick_approve)
   ├── System detects rollback operation -> record ImplicitSignal(rollback)
   └── AI completes self-assessment -> record ContextSignal(ai_confidence)

2. Signal Aggregation
   └── Aggregate signals by time window -> SignalAggregation

3. Dimension Score Calculation
   ├── Accuracy score = f(approval_rate, rollback_rate, ...)
   ├── Consistency score = f(review_speed, confidence_alignment, ...)
   ├── Safety score = f(rollback_rate, safety_events, ...)
   ├── Creativity score = f(approval_rate, modification_depth, ...)
   └── Efficiency score = f(review_speed, retry_rate, ...)

4. Task Type Score
   └── taskScore = sum(dimScore * dimWeight)

5. Decay and Recovery
   ├── Apply time decay
   └── Apply event-driven recovery/penalty

6. Permission Mapping
   └── trustScore -> trustLevel -> permissionLevel

7. Persistence
   └── Save to Semantic Memory (cross-session persistence)
```

---

## Advantages

1. **Multi-dimensional**: Not a single score, but scored by dimension, avoiding "one strength hides all weaknesses" or "one weakness overshadows all strengths"
2. **Adaptive**: Weights dynamically adjust based on task type; code generation values accuracy, creative design values creativity
3. **Progressive**: Trust accumulates slowly and drops quickly, following the "stairs up, elevator down" principle
4. **Explainable**: Every score change has a corresponding event and reason; humans can understand "why the trust score changed"
5. **Personalized**: Different users and task types have independently calculated trust scores that don't interfere with each other
6. **Safety floor**: Certain operations have minimum permission requirements; no matter how high the trust, safety checks cannot be skipped
7. **Aligned with academic research**: Signal collection and scoring mechanisms are consistent with Reflexion (linguistic feedback) and DPO (preference learning)

## Risks

1. **Cold start problem**: New users have no historical data; starting at 0.5 may be inaccurate
   - Mitigation: New users are forced to start at Level 0, quickly accumulate data through shadow mode
2. **Signal noise**: Implicit signals (e.g., review speed) may be interfered with by external factors (e.g., human distraction)
   - Mitigation: Use multi-signal cross-validation; no single signal weight exceeds 0.3
3. **Over-optimization**: AI may learn to "pander" to humans to gain higher trust
   - Mitigation: Set a "honesty floor"; AI must report uncertainty; introduce random audits
4. **Privacy concerns**: Collecting human behavior data may raise privacy concerns
   - Mitigation: All data stored locally, not shared across users, provide data viewing and deletion functionality
5. **Weight drift**: Adaptive weights may drift over time, causing score instability
   - Mitigation: Rate limits on weight changes; periodic calibration with historical data

---

## Pending Decisions

- [ ] Cold start strategy: Should new users start at 0.5 or 0? Is shadow mode mandatory?
- [ ] Time decay rate: Is lambda = 0.01/day appropriate? Should different dimensions have different decay rates?
- [ ] Signal window size: How long should the signal aggregation time window be? (7 days? 30 days?)
- [ ] Weight update frequency: How often should adaptive weights be updated?
- [ ] Multi-user scenario: When the same Factory is used by multiple people, should trust be averaged or take the minimum?
- [ ] Trust score visualization: Should trust scores be displayed to humans? At what granularity?
- [ ] Interface with P011: How should trust scores be passed to the Dynamic Permission System? Real-time push or polling?
