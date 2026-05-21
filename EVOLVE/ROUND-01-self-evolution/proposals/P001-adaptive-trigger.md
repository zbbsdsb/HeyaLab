# Proposal P001: Adaptive Trigger System

> **Status**: Draft
> **Proposer**: AI Assistant
> **Date**: 2026-05-16
> **Related**: VISION.md (Factory gets measurably better) / RESEARCH/SYNTHESIS.md (Self-Evolving Agents Survey, MemRL) / brainstorm.md (Evolution Trigger Conditions)

---

## Summary

Traditional fixed-threshold trigger mechanisms cannot adapt to changes in Factory maturity. This proposal designs a **dynamic baseline system based on historical data**, allowing trigger conditions to adaptively adjust according to the Factory's operational state. Core idea: instead of "trigger when success rate drops below 70%," use "trigger when success rate is significantly below **this Factory's own historical level**."

---

## Design

### 1. Dynamic Baseline Algorithm

The baseline is not a fixed value but a statistical indicator calculated using a sliding window.

```typescript
/**
 * Dynamic Baseline Calculator
 * Builds adaptive thresholds based on Exponentially Weighted Moving Average (EWMA) and standard deviation
 */
interface DynamicBaselineConfig {
  /** Sliding window size (most recent N WorkOrders) */
  windowSize: number;           // default: 50

  /** EWMA smoothing factor (0-1), smaller = smoother */
  smoothingFactor: number;      // default: 0.3

  /** Standard deviation multiplier for calculating anomaly boundaries */
  stdDevMultiplier: number;     // default: 2.0

  /** Minimum sample size; below this, do not trigger evolution */
  minSamples: number;           // default: 10
}

interface DynamicBaseline {
  /** Current baseline value */
  value: number;

  /** Current standard deviation */
  stdDev: number;

  /** Upper bound (signal of unusually good performance, usable for positive evolution) */
  upperBound: number;

  /** Lower bound (signal of unusually poor performance, triggers evolution) */
  lowerBound: number;

  /** Trend direction */
  trend: 'improving' | 'stable' | 'declining';

  /** Trend strength (consecutive rising/declining WorkOrder count) */
  trendStreak: number;

  /** Confidence (based on sample size) */
  confidence: number;           // 0-1
}

class DynamicBaselineCalculator {
  private config: DynamicBaselineConfig;
  private history: number[] = [];

  constructor(config: Partial<DynamicBaselineConfig> = {}) {
    this.config = { ...DEFAULT_BASELINE_CONFIG, ...config };
  }

  /**
   * Add new observation and update baseline
   */
  update(newValue: number): DynamicBaseline {
    this.history.push(newValue);

    // Maintain window size
    if (this.history.length > this.config.windowSize) {
      this.history.shift();
    }

    if (this.history.length < this.config.minSamples) {
      return {
        value: newValue,
        stdDev: 0,
        upperBound: Infinity,
        lowerBound: -Infinity,
        trend: 'stable',
        trendStreak: 0,
        confidence: this.history.length / this.config.minSamples,
      };
    }

    const mean = this.calculateEWMA();
    const stdDev = this.calculateStdDev(mean);
    const trend = this.calculateTrend();

    return {
      value: mean,
      stdDev,
      upperBound: mean + this.config.stdDevMultiplier * stdDev,
      lowerBound: mean - this.config.stdDevMultiplier * stdDev,
      trend: trend.direction,
      trendStreak: trend.streak,
      confidence: Math.min(1, this.history.length / this.config.windowSize),
    };
  }

  private calculateEWMA(): number {
    const alpha = this.config.smoothingFactor;
    let ewma = this.history[0];
    for (let i = 1; i < this.history.length; i++) {
      ewma = alpha * this.history[i] + (1 - alpha) * ewma;
    }
    return ewma;
  }

  private calculateStdDev(mean: number): number {
    const variance =
      this.history.reduce((sum, val) => sum + Math.pow(val - mean, 2), 0) /
      this.history.length;
    return Math.sqrt(variance);
  }

  private calculateTrend(): { direction: 'improving' | 'stable' | 'declining'; streak: number } {
    if (this.history.length < 3) {
      return { direction: 'stable', streak: 0 };
    }

    // Compare moving average of recent values with overall mean
    const recentWindow = Math.min(5, Math.floor(this.history.length / 3));
    const recent = this.history.slice(-recentWindow);
    const recentMean = recent.reduce((a, b) => a + b, 0) / recent.length;
    const overallMean = this.calculateEWMA();

    const diff = recentMean - overallMean;
    const threshold = this.config.stdDevMultiplier * 0.5 *
      (this.calculateStdDev(overallMean) || 0.01);

    if (diff > threshold) {
      return { direction: 'improving', streak: this.countConsecutiveDirection('up') };
    } else if (diff < -threshold) {
      return { direction: 'declining', streak: this.countConsecutiveDirection('down') };
    }
    return { direction: 'stable', streak: 0 };
  }

  private countConsecutiveDirection(dir: 'up' | 'down'): number {
    let streak = 0;
    for (let i = this.history.length - 1; i > 0; i--) {
      const diff = this.history[i] - this.history[i - 1];
      if ((dir === 'up' && diff > 0) || (dir === 'down' && diff < 0)) {
        streak++;
      } else {
        break;
      }
    }
    return streak;
  }
}
```

### 2. Trigger Condition Definitions

```typescript
/**
 * Evolution Trigger -- unified management of all trigger conditions
 */
type TriggerLevel = 'critical' | 'pattern' | 'scheduled' | 'human' | 'environmental';

type EvolutionScope =
  | 'pipeline_step'      // Fine-tuning of a single Pipeline step
  | 'pipeline_topology'  // Structural Pipeline changes
  | 'tool_strategy'      // Tool usage strategy
  | 'prompt_engineering' // Prompt optimization
  | 'verification_rule'  // Validation rules
  | 'memory_strategy'    // Memory strategy
  | 'full_review';       // Comprehensive review

interface EvolutionTrigger {
  id: string;
  level: TriggerLevel;
  scope: EvolutionScope;
  reason: string;
  evidence: TriggerEvidence[];
  priority: number;         // 0-100
  suggestedAction: string;
  requiresHumanApproval: boolean;
  timestamp: Date;
}

interface TriggerEvidence {
  type: 'metric' | 'feedback' | 'pattern' | 'trend' | 'external';
  description: string;
  data: Record<string, unknown>;
  confidence: number;       // 0-1
}

/**
 * Trigger Condition Evaluator
 */
class EvolutionTriggerEvaluator {
  private baselines: Map<string, DynamicBaselineCalculator> = new Map();
  private feedbackBuffer: HumanFeedback[] = [];
  private cooldowns: Map<string, Date> = new Map();

  /**
   * Called after each WorkOrder completes
   * Evaluates whether evolution should be triggered
   */
  evaluateAfterWorkOrder(result: WorkOrderResult): EvolutionTrigger[] {
    const triggers: EvolutionTrigger[] = [];

    // 1. Performance baseline check
    const successBaseline = this.getBaseline('success_rate');
    const updated = successBaseline.update(result.success ? 1 : 0);

    if (updated.confidence >= 0.8) {
      // Check if below dynamic lower bound
      if (result.success === false && updated.trend === 'declining' && updated.trendStreak >= 3) {
        triggers.push({
          id: generateId(),
          level: 'critical',
          scope: 'full_review',
          reason: `Success rate declining for ${updated.trendStreak} consecutive times, current baseline ${updated.value.toFixed(2)}`,
          evidence: [{
            type: 'trend',
            description: 'Sustained declining trend in success rate',
            data: { baseline: updated.value, streak: updated.trendStreak },
            confidence: updated.confidence,
          }],
          priority: 90,
          suggestedAction: 'immediate_review',
          requiresHumanApproval: true,
          timestamp: new Date(),
        });
      }
    }

    // 2. Cost efficiency check
    const costBaseline = this.getBaseline('cost_efficiency');
    const costScore = result.expectedCost > 0
      ? result.actualCost / result.expectedCost
      : 1;
    const costUpdated = costBaseline.update(costScore);

    if (costUpdated.value > 1.3 && costUpdated.confidence >= 0.8) {
      triggers.push({
        id: generateId(),
        level: 'pattern',
        scope: 'pipeline_step',
        reason: `Cost efficiency consistently high (current: ${(costUpdated.value * 100).toFixed(0)}%)`,
        evidence: [{
          type: 'metric',
          description: 'Cost exceeds expectations',
          data: { costRatio: costUpdated.value, baseline: costUpdated.value },
          confidence: costUpdated.confidence,
        }],
        priority: 60,
        suggestedAction: 'cost_optimization',
        requiresHumanApproval: false,
        timestamp: new Date(),
      });
    }

    // 3. Human correction depth check
    if (result.humanCorrections.length > 0) {
      const maxDepth = Math.max(...result.humanCorrections.map(c => c.depth));
      if (maxDepth >= 3) {
        triggers.push({
          id: generateId(),
          level: 'pattern',
          scope: 'prompt_engineering',
          reason: `Human performed deep correction (depth=${maxDepth}), prompts may need adjustment`,
          evidence: result.humanCorrections.map(c => ({
            type: 'feedback' as const,
            description: c.description,
            data: { depth: c.depth, stepId: c.stepId },
            confidence: 1,
          })),
          priority: 70,
          suggestedAction: 'prompt_revision',
          requiresHumanApproval: false,
          timestamp: new Date(),
        });
      }
    }

    return this.applyCooldowns(triggers);
  }

  /**
   * Analyze patterns in the feedback buffer
   * Should be called periodically (e.g., once daily)
   */
  analyzeFeedbackPatterns(): EvolutionTrigger[] {
    const triggers: EvolutionTrigger[] = [];

    // Cluster analysis: detect similar feedback
    const clusters = this.clusterFeedback(this.feedbackBuffer);

    for (const cluster of clusters) {
      if (cluster.size >= 3 && cluster.recencyDays <= 7) {
        triggers.push({
          id: generateId(),
          level: 'pattern',
          scope: this.inferScopeFromFeedback(cluster),
          reason: `Detected ${cluster.size} similar feedback entries: "${cluster.summary}"`,
          evidence: cluster.feedbacks.map(f => ({
            type: 'feedback' as const,
            description: f.content,
            data: { severity: f.severity, timestamp: f.timestamp },
            confidence: 0.8,
          })),
          priority: 50 + cluster.size * 5,
          suggestedAction: 'pattern_based_optimization',
          requiresHumanApproval: false,
          timestamp: new Date(),
        });
      }
    }

    return this.applyCooldowns(triggers);
  }

  /**
   * Periodic review trigger
   * Called every N WorkOrders or on a schedule
   */
  periodicReview(config: {
    workOrderCount: number;
    workOrderThreshold: number;
  }): EvolutionTrigger | null {
    if (config.workOrderCount % config.workOrderThreshold !== 0) {
      return null;
    }

    return {
      id: generateId(),
      level: 'scheduled',
      scope: 'full_review',
      reason: `Completed ${config.workOrderCount} WorkOrders, triggering periodic review`,
      evidence: [{
        type: 'metric',
        description: 'Periodic review trigger',
        data: { workOrderCount: config.workOrderCount },
        confidence: 1,
      }],
      priority: 30,
      suggestedAction: 'comprehensive_review',
      requiresHumanApproval: false,  // Periodic review triggers automatically, but evolution proposals still require approval
      timestamp: new Date(),
    };
  }

  /**
   * Cooldown mechanism: prevents the same type of trigger from firing too frequently
   */
  private applyCooldowns(triggers: EvolutionTrigger[]): EvolutionTrigger[] {
    const now = new Date();
    const cooldownPeriods: Record<TriggerLevel, number> = {
      critical: 4 * 60 * 60 * 1000,     // 4 hours
      pattern: 24 * 60 * 60 * 1000,      // 24 hours
      scheduled: 7 * 24 * 60 * 60 * 1000, // 7 days
      human: 0,                            // Human requests have no cooldown
      environmental: 24 * 60 * 60 * 1000,  // 24 hours
    };

    return triggers.filter(trigger => {
      const cooldownKey = `${trigger.level}:${trigger.scope}`;
      const lastTriggered = this.cooldowns.get(cooldownKey);

      if (lastTriggered) {
        const elapsed = now.getTime() - lastTriggered.getTime();
        if (elapsed < cooldownPeriods[trigger.level]) {
          return false;
        }
      }

      this.cooldowns.set(cooldownKey, now);
      return true;
    });
  }

  private getBaseline(metric: string): DynamicBaselineCalculator {
    if (!this.baselines.has(metric)) {
      this.baselines.set(metric, new DynamicBaselineCalculator());
    }
    return this.baselines.get(metric)!;
  }

  private clusterFeedback(feedbacks: HumanFeedback[]): FeedbackCluster[] {
    // Semantic clustering implementation (simplified)
    // Actual implementation should use embedding + clustering algorithms
    return [];
  }

  private inferScopeFromFeedback(cluster: FeedbackCluster): EvolutionScope {
    // Infer evolution scope from feedback content
    return 'prompt_engineering';
  }
}
```

### 3. Cooldown and Priority Mechanism

```typescript
/**
 * Evolution Trigger Queue Manager
 * Responsible for prioritizing and merging multiple triggers
 */
interface TriggerQueueConfig {
  /** Maximum queue length */
  maxQueueSize: number;          // default: 20

  /** Auto-merge time window (milliseconds) */
  mergeWindow: number;           // default: 3600000 (1 hour)

  /** Maximum human approval queue length */
  maxApprovalQueueSize: number;  // default: 5
}

class EvolutionTriggerQueue {
  private pendingTriggers: EvolutionTrigger[] = [];
  private approvalQueue: EvolutionTrigger[] = [];
  private config: TriggerQueueConfig;

  constructor(config: Partial<TriggerQueueConfig> = {}) {
    this.config = { ...DEFAULT_QUEUE_CONFIG, ...config };
  }

  /**
   * Add new trigger to the queue
   */
  enqueue(trigger: EvolutionTrigger): void {
    // Check if it can be merged with an existing trigger
    const merged = this.tryMerge(trigger);
    if (merged) {
      return; // Already merged into an existing trigger
    }

    this.pendingTriggers.push(trigger);
    this.pendingTriggers.sort((a, b) => b.priority - a.priority);

    // Queue overflow handling: remove low-priority triggers
    if (this.pendingTriggers.length > this.config.maxQueueSize) {
      this.pendingTriggers = this.pendingTriggers.slice(0, this.config.maxQueueSize);
    }

    // Triggers requiring human approval move to the approval queue
    if (trigger.requiresHumanApproval) {
      this.approvalQueue.push(trigger);
      if (this.approvalQueue.length > this.config.maxApprovalQueueSize) {
        // Batch-merge low-priority approval requests
        this.batchApprovalRequests();
      }
    }
  }

  /**
   * Get the next trigger to process
   */
  dequeue(): EvolutionTrigger | null {
    return this.pendingTriggers.shift() ?? null;
  }

  /**
   * Attempt to merge similar triggers
   * Prevents the same issue from triggering multiple evolutions
   */
  private tryMerge(newTrigger: EvolutionTrigger): boolean {
    for (const existing of this.pendingTriggers) {
      if (existing.scope === newTrigger.scope &&
          existing.level === newTrigger.level) {
        // Merge evidence
        existing.evidence.push(...newTrigger.evidence);
        // Increase priority
        existing.priority = Math.min(100, existing.priority + 10);
        // Update reason
        existing.reason += ` (merged ${newTrigger.evidence.length} new evidence items)`;
        return true;
      }
    }
    return false;
  }

  /**
   * Batch-merge approval requests
   * Reduces human approval burden
   */
  private batchApprovalRequests(): void {
    // Merge low-priority approval requests into a single batch request
    const lowPriority = this.approvalQueue
      .filter(t => t.priority < 50)
      .sort((a, b) => a.priority - b.priority);

    if (lowPriority.length >= 3) {
      const batched: EvolutionTrigger = {
        id: generateId(),
        level: 'pattern',
        scope: 'full_review',
        reason: `Batch evolution request: ${lowPriority.length} low-priority optimizations`,
        evidence: lowPriority.flatMap(t => t.evidence),
        priority: 40,
        suggestedAction: 'batch_optimization',
        requiresHumanApproval: true,
        timestamp: new Date(),
      };

      this.approvalQueue = this.approvalQueue.filter(
        t => !lowPriority.includes(t)
      );
      this.approvalQueue.push(batched);
    }
  }
}
```

### 4. Complete Trigger Flow

```
WorkOrder completed
    |
    +--> Performance baseline evaluation --> Trigger? --> Yes --> Add to trigger queue
    |         |                              |
    |         +--> Update dynamic baseline    |
    |                                        |
    +--> Human correction analysis --> Deep correction? --> Yes --> Add to trigger queue
    |                                        |
    +--> Feedback buffer --> (periodic) pattern analysis --> Pattern found? --> Add to trigger queue
    |                                                        |
    +--> Counter --> Threshold reached? --> Yes --> Periodic review trigger --> Add to trigger queue
                                                             |
                                                    Trigger Queue Manager
                                                         |
                                            +------------+------------+
                                            |            |            |
                                       Needs approval  Auto-execute  Batch merge
                                            |            |            |
                                       Human approval   Auto         Reduce
                                       queue            evolution    approval burden
```

---

## Advantages

1. **Adaptive**: Baselines dynamically adjust with Factory maturity, avoiding over-triggering in early stages or sluggish response in later stages
2. **Multi-dimensional**: Evaluates not only success rate but also cost efficiency, human correction depth, and feedback patterns
3. **Overload prevention**: Cooldown mechanisms and batch merging prevent trigger storms
4. **Trend-aware**: Looks at trends rather than just absolute values, providing early warnings
5. **Tiered response**: Different trigger levels have different response speeds and approval requirements

## Risks

1. **Baseline drift**: If Factory remains in a low-performance state for a long time, the baseline will "adapt" to low performance, losing its warning capability
   - **Mitigation**: Set absolute lower bounds (e.g., success rate no lower than 50%); dynamic baselines cannot fall below this value
2. **Cooldown periods too long**: Urgent issues may be blocked by cooldown periods
   - **Mitigation**: `critical` level triggers have an independent short cooldown period, and human requests are not subject to cooldown
3. **Inaccurate clustering**: Feedback pattern analysis may produce false positives
   - **Mitigation**: Use embedding + clustering, and set a minimum cluster size threshold

## Pending Decisions

- [ ] Absolute lower bound values for each metric (success rate no lower than X%, cost no higher than Y times)
- [ ] Whether cooldown durations should differ by Factory type
- [ ] WorkOrder interval for periodic review (suggested: 50)
- [ ] Which embedding model to use for feedback clustering
- [ ] Whether an "evolution budget" concept is needed -- limiting the number of evolutions per unit time
