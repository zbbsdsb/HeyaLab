# Proposal P002: Evolution Sandbox and Canary Deployment

> **Status**: Draft
> **Proposer**: AI Assistant
> **Date**: 2026-05-16
> **Related**: VISION.md (Factory gets measurably better) / RESEARCH/SYNTHESIS.md (Self-Evolving Agents Survey) / brainstorm.md (Sandbox Testing, Progressive Deployment, Evolution Budget)

---

## Summary

Every evolution carries the risk of breaking existing capabilities. This proposal designs a **complete evolution validation and progressive deployment system**, ensuring each evolution undergoes rigorous isolated testing and phased canary validation before reaching production. Core mechanisms include: (1) **Sandbox Cloning** -- replicating Factory's complete state into an isolated environment, using historical WorkOrders as a regression test set; (2) **Three-Phase Deployment** -- Shadow Mode -> Canary 5% -> Full 100%; (3) **Automatic Rollback** -- based on real-time metric monitoring, automatically reverting to the previous stable version when metrics deteriorate.

This system enables Factory to "boldly hypothesize, carefully verify": freely exploring evolution directions in the sandbox, but only evolutions that pass rigorous validation can enter production.

---

## Design

### 1. Sandbox Cloning Mechanism

The sandbox is not a simple "empty environment" but a **complete functional replica** of Factory. It needs to replicate all of Factory's state so that test results in the sandbox can directly predict production performance.

#### 1.1 Clone Scope Definition

```typescript
/**
 * Sandbox Configuration -- defines sandbox environment parameters
 */
interface SandboxConfig {
  /** Sandbox unique identifier */
  sandboxId: string;

  /** Source Factory identifier */
  sourceFactoryId: string;

  /** Evolution version to clone */
  evolutionVersion: string;

  /** Clone scope control */
  cloneScope: {
    /** Whether to clone Pipeline definitions */
    pipeline: boolean;           // default: true

    /** Whether to clone tool configuration */
    toolConfig: boolean;         // default: true

    /** Whether to clone prompt templates */
    promptTemplates: boolean;    // default: true

    /** Whether to clone validation rules */
    validationRules: boolean;    // default: true

    /** Whether to clone memory system (selectively cloneable) */
    memory: MemoryCloneConfig;

    /** Whether to clone core configuration */
    coreConfig: boolean;         // default: true
  };

  /** Memory clone strategy */
  memoryCloneConfig?: MemoryCloneConfig;

  /** Resource limits */
  resourceLimits: SandboxResourceLimits;

  /** Sandbox lifecycle */
  lifecycle: SandboxLifecycle;

  /** Creation time */
  createdAt: Date;

  /** Expected expiration time */
  expiresAt: Date;
}

/**
 * Memory Clone Strategy
 * Full cloning is expensive; supports on-demand selection
 */
interface MemoryCloneConfig {
  /** Clone mode */
  mode: 'full' | 'sampled' | 'empty';

  /** Sampling strategy in sampled mode */
  sampling?: {
    /** Maximum number of samples */
    maxMemories: number;         // default: 100

    /** Sampling strategy */
    strategy: 'recent' | 'diverse' | 'relevant';

    /** If relevant mode, specify the related evolution scope */
    relevantScope?: string;

    /** Random seed (ensures reproducibility) */
    seed?: number;
  };
}

/**
 * Sandbox Resource Limits
 * Prevents runaway evolution from consuming excessive resources
 */
interface SandboxResourceLimits {
  /** Maximum runtime (milliseconds) */
  maxRuntime: number;            // default: 3600000 (1 hour)

  /** Maximum API call count */
  maxApiCalls: number;           // default: 500

  /** Maximum Token budget */
  maxTokenBudget: number;        // default: 500000

  /** Maximum concurrent WorkOrder count */
  maxConcurrentWorkOrders: number; // default: 1

  /** CPU/memory limits (container level) */
  containerLimits?: {
    cpuCores: number;
    memoryMB: number;
  };
}

/**
 * Sandbox Lifecycle Management
 */
interface SandboxLifecycle {
  /** Automatic expiration time (milliseconds) */
  ttl: number;                   // default: 86400000 (24 hours)

  /** Whether to auto-cleanup after expiration */
  autoCleanup: boolean;          // default: true

  /** Whether to retain after test completion (for debugging) */
  retainOnFailure: boolean;      // default: true

  /** Maximum number of snapshots to retain */
  maxSnapshots: number;          // default: 10
}
```

#### 1.2 Clone Executor

```typescript
/**
 * Sandbox Clone Executor
 * Responsible for fully replicating Factory state into an isolated environment
 */
class SandboxCloner {
  constructor(
    private factoryStore: FactoryStore,
    private memoryStore: MemoryStore,
    private sandboxManager: SandboxManager
  ) {}

  /**
   * Clone a Factory into a sandbox environment
   */
  async clone(config: SandboxConfig): Promise<SandboxInstance> {
    const startTime = Date.now();

    // 1. Create isolated environment (container/virtual environment)
    const environment = await this.sandboxManager.createEnvironment({
      id: config.sandboxId,
      resourceLimits: config.resourceLimits,
    });

    // 2. Clone core configuration
    if (config.cloneScope.coreConfig) {
      const coreConfig = await this.factoryStore.getCoreConfig(
        config.sourceFactoryId
      );
      await environment.inject('coreConfig', coreConfig);
    }

    // 3. Clone Pipeline definitions
    if (config.cloneScope.pipeline) {
      const pipeline = await this.factoryStore.getPipeline(
        config.sourceFactoryId
      );
      // Apply pending evolution changes to Pipeline
      const evolvedPipeline = await this.applyEvolution(
        pipeline,
        config.evolutionVersion
      );
      await environment.inject('pipeline', evolvedPipeline);
    }

    // 4. Clone tool configuration
    if (config.cloneScope.toolConfig) {
      const tools = await this.factoryStore.getToolConfig(
        config.sourceFactoryId
      );
      await environment.inject('toolConfig', tools);
    }

    // 5. Clone prompt templates
    if (config.cloneScope.promptTemplates) {
      const prompts = await this.factoryStore.getPrompts(
        config.sourceFactoryId
      );
      await environment.inject('prompts', prompts);
    }

    // 6. Clone validation rules
    if (config.cloneScope.validationRules) {
      const rules = await this.factoryStore.getValidationRules(
        config.sourceFactoryId
      );
      await environment.inject('validationRules', rules);
    }

    // 7. Clone memory (by strategy)
    if (config.cloneScope.memory) {
      const memories = await this.cloneMemories(
        config.sourceFactoryId,
        config.cloneScope.memory
      );
      await environment.inject('memory', memories);
    }

    // 8. Verify sandbox integrity
    const healthCheck = await environment.healthCheck();
    if (!healthCheck.isHealthy) {
      await environment.destroy();
      throw new SandboxCloneError(
        `Sandbox health check failed: ${healthCheck.issues.join(', ')}`
      );
    }

    return {
      sandboxId: config.sandboxId,
      environment,
      cloneTime: Date.now() - startTime,
      healthCheck,
    };
  }

  /**
   * Clone memory by strategy
   */
  private async cloneMemories(
    factoryId: string,
    config: MemoryCloneConfig
  ): Promise<Memory[]> {
    switch (config.mode) {
      case 'full':
        return this.memoryStore.getAll(factoryId);

      case 'sampled':
        return this.memoryStore.sample(factoryId, {
          maxCount: config.sampling!.maxMemories,
          strategy: config.sampling!.strategy,
          relevantScope: config.sampling!.relevantScope,
          seed: config.sampling!.seed,
        });

      case 'empty':
        return [];

      default:
        return [];
    }
  }

  /**
   * Apply evolution changes to Pipeline
   */
  private async applyEvolution(
    pipeline: Pipeline,
    evolutionVersion: string
  ): Promise<Pipeline> {
    const evolution = await this.factoryStore.getEvolution(evolutionVersion);
    // Modify pipeline based on evolution's diff information
    return applyDiff(pipeline, evolution.diff);
  }
}
```

---

### 2. Regression Testing Strategy

The evolved version in the sandbox needs to be compared against the production version. The test set is constructed from historical WorkOrders, ensuring evolution does not break existing capabilities.

#### 2.1 Test Suite Management

```typescript
/**
 * Regression Test Suite Configuration
 */
interface RegressionTestSuite {
  /** Test suite identifier */
  id: string;

  /** Test suite name */
  name: string;

  /** Test case list */
  cases: RegressionTestCase[];

  /** Test suite metadata */
  metadata: TestSuiteMetadata;
}

/**
 * Single regression test case
 * Each case corresponds to a historical WorkOrder
 */
interface RegressionTestCase {
  /** Test case identifier */
  id: string;

  /** Source WorkOrder ID */
  sourceWorkOrderId: string;

  /** Input (original WorkOrder input) */
  input: WorkOrderInput;

  /** Expected output (original successful output, or human-corrected final output) */
  expectedOutput: WorkOrderOutput;

  /** Expected output source */
  expectedOutputSource: 'original_success' | 'human_corrected' | 'consensus';

  /** Test weight (important cases have higher weight) */
  weight: number;                // 0.0 - 1.0

  /** Related evolution scopes (for selective testing) */
  relatedScopes: EvolutionScope[];

  /** Tags */
  tags: string[];
}

/**
 * Test suite metadata
 */
interface TestSuiteMetadata {
  /** Test suite build time */
  builtAt: Date;

  /** Covered WorkOrder time range */
  coversPeriod: { start: Date; end: Date };

  /** Total test case count */
  totalCases: number;

  /** Distribution by evolution scope */
  scopeDistribution: Record<EvolutionScope, number>;

  /** Distribution by output source */
  sourceDistribution: {
    originalSuccess: number;
    humanCorrected: number;
    consensus: number;
  };
}

/**
 * Test Suite Builder
 * Automatically builds regression test suites from historical WorkOrders
 */
class RegressionTestSuiteBuilder {
  constructor(
    private workOrderStore: WorkOrderStore,
    private memoryStore: MemoryStore
  ) {}

  /**
   * Build regression test suite
   */
  async build(config: TestSuiteBuildConfig): Promise<RegressionTestSuite> {
    const cases: RegressionTestCase[] = [];

    // 1. Collect candidate WorkOrders
    const candidates = await this.workOrderStore.query({
      factoryId: config.factoryId,
      period: config.coversPeriod,
      minHumanCorrections: 0,
    });

    // 2. Generate test case for each candidate
    for (const wo of candidates) {
      const testCase = await this.buildTestCase(wo, config);
      if (testCase) {
        cases.push(testCase);
      }
    }

    // 3. Stratified sampling by evolution scope, ensuring balanced coverage
    const balanced = this.balanceByScope(cases, config);

    // 4. Compute metadata
    const metadata = this.computeMetadata(balanced, config);

    return {
      id: generateId(),
      name: config.name ?? `Regression test suite ${new Date().toISOString().slice(0, 10)}`,
      cases: balanced,
      metadata,
    };
  }

  /**
   * Build test case from a single WorkOrder
   */
  private async buildTestCase(
    wo: WorkOrder,
    config: TestSuiteBuildConfig
  ): Promise<RegressionTestCase | null> {
    // Determine expected output
    let expectedOutput: WorkOrderOutput;
    let source: RegressionTestCase['expectedOutputSource'];

    if (wo.humanCorrections.length === 0 && wo.success) {
      // Original successful output
      expectedOutput = wo.output;
      source = 'original_success';
    } else if (wo.humanCorrections.length > 0) {
      // Human-corrected final output
      expectedOutput = wo.finalOutput ?? wo.output;
      source = 'human_corrected';
    } else {
      // Failed WorkOrders without corrections are not suitable for regression testing
      return null;
    }

    // Calculate weight: cases with human corrections have higher weight (indicating error-prone areas)
    const weight = Math.min(1, 0.3 + wo.humanCorrections.length * 0.2);

    // Infer related evolution scopes
    const relatedScopes = this.inferRelatedScopes(wo);

    return {
      id: generateId(),
      sourceWorkOrderId: wo.id,
      input: wo.input,
      expectedOutput,
      expectedOutputSource: source,
      weight,
      relatedScopes,
      tags: this.extractTags(wo),
    };
  }

  /**
   * Stratified sampling by evolution scope, ensuring sufficient test cases per scope
   */
  private balanceByScope(
    cases: RegressionTestCase[],
    config: TestSuiteBuildConfig
  ): RegressionTestCase[] {
    const maxCases = config.maxCases ?? 200;

    if (cases.length <= maxCases) {
      return cases;
    }

    // Group by scope
    const byScope = new Map<EvolutionScope, RegressionTestCase[]>();
    for (const c of cases) {
      for (const scope of c.relatedScopes) {
        if (!byScope.has(scope)) byScope.set(scope, []);
        byScope.get(scope)!.push(c);
      }
    }

    // Allocate quota per scope
    const perScopeQuota = Math.ceil(maxCases / byScope.size);
    const selected: RegressionTestCase[] = [];
    const selectedIds = new Set<string>();

    for (const [, scopeCases] of byScope) {
      // Sort by weight, prioritizing high-weight cases
      scopeCases.sort((a, b) => b.weight - a.weight);
      const quota = Math.min(perScopeQuota, scopeCases.length);
      for (let i = 0; i < quota; i++) {
        if (!selectedIds.has(scopeCases[i].id)) {
          selected.push(scopeCases[i]);
          selectedIds.add(scopeCases[i].id);
        }
      }
    }

    return selected;
  }
}

interface TestSuiteBuildConfig {
  factoryId: string;
  name?: string;
  coversPeriod: { start: Date; end: Date };
  maxCases?: number;
}
```

#### 2.2 Test Execution and Pass Criteria

```typescript
/**
 * Regression Test Result
 */
interface RegressionTestResult {
  /** Test execution identifier */
  testRunId: string;

  /** Test suite identifier */
  testSuiteId: string;

  /** Sandbox identifier */
  sandboxId: string;

  /** Evolution version */
  evolutionVersion: string;

  /** Overall pass/fail */
  passed: boolean;

  /** Weighted pass rate */
  weightedPassRate: number;      // 0.0 - 1.0

  /** Simple pass rate (unweighted) */
  simplePassRate: number;        // 0.0 - 1.0

  /** Per-case results */
  caseResults: TestCaseResult[];

  /** Pass rate by evolution scope */
  scopeResults: Record<EvolutionScope, ScopeTestResult>;

  /** Performance comparison (vs production version) */
  performanceComparison: PerformanceComparison;

  /** Test execution time (milliseconds) */
  executionTime: number;

  /** Resource consumption */
  resourceUsage: {
    apiCalls: number;
    tokensUsed: number;
  };

  /** Test conclusion */
  conclusion: TestConclusion;
}

/**
 * Single test case result
 */
interface TestCaseResult {
  /** Test case identifier */
  caseId: string;

  /** Whether passed */
  passed: boolean;

  /** Pass score (0-1, not a simple pass/fail) */
  score: number;

  /** Sandbox output */
  sandboxOutput: WorkOrderOutput;

  /** Diff with expected output */
  diff: OutputDiff;

  /** Failure reason (if failed) */
  failureReason?: string;

  /** Execution time (milliseconds) */
  executionTime: number;
}

/**
 * Output difference analysis
 */
interface OutputDiff {
  /** Structural differences (step count, types, etc.) */
  structural: {
    hasDifference: boolean;
    details: string[];
  };

  /** Content differences (text, code, etc.) */
  content: {
    similarity: number;          // 0-1, semantic similarity
    differences: ContentDifference[];
  };

  /** Quality differences (format, completeness, etc.) */
  quality: {
    sandboxScore: number;
    expectedScore: number;
    delta: number;
  };
}

interface ContentDifference {
  type: 'addition' | 'removal' | 'modification';
  location: string;
  description: string;
  severity: 'low' | 'medium' | 'high';
}

/**
 * Test result by evolution scope
 */
interface ScopeTestResult {
  scope: EvolutionScope;
  totalCases: number;
  passedCases: number;
  passRate: number;
  weightedPassRate: number;
  worstCase: TestCaseResult | null;
}

/**
 * Performance comparison
 */
interface PerformanceComparison {
  /** Average execution time comparison */
  avgExecutionTime: {
    production: number;
    sandbox: number;
    delta: number;               // Positive value means sandbox is slower
    deltaPercent: number;
  };

  /** Average Token usage comparison */
  avgTokenUsage: {
    production: number;
    sandbox: number;
    delta: number;
    deltaPercent: number;
  };

  /** Cost efficiency comparison */
  costEfficiency: {
    production: number;
    sandbox: number;
    delta: number;
  };
}

/**
 * Test conclusion
 */
interface TestConclusion {
  /** Overall verdict */
  verdict: 'pass' | 'pass_with_concerns' | 'fail' | 'inconclusive';

  /** Verdict reasons */
  reasons: string[];

  /** Recommended next step */
  recommendedAction: 'proceed_to_canary' | 'revise_evolution' | 'abort_evolution' | 'extend_testing';

  /** Areas of concern */
  concerns: string[];
}

/**
 * Regression test pass criteria
 */
interface RegressionTestThresholds {
  /** Minimum weighted pass rate */
  minWeightedPassRate: number;       // default: 0.90

  /** Minimum simple pass rate */
  minSimplePassRate: number;         // default: 0.85

  /** Maximum allowed performance degradation (percentage) */
  maxPerformanceDegradation: number; // default: 0.20 (20%)

  /** Maximum allowed cost increase (percentage) */
  maxCostIncrease: number;           // default: 0.15 (15%)

  /** Minimum pass rate for any single evolution scope */
  minScopePassRate: number;          // default: 0.80

  /** Maximum number of high-severity differences */
  maxHighSeverityDiffs: number;      // default: 0
}
```

#### 2.3 Test Executor

```typescript
/**
 * Regression Test Runner
 * Runs test suites in the sandbox and collects results
 */
class RegressionTestRunner {
  constructor(
    private sandboxManager: SandboxManager,
    private evaluator: OutputEvaluator,
    private thresholds: RegressionTestThresholds
  ) {}

  /**
   * Execute complete regression test
   */
  async run(config: {
    testSuite: RegressionTestSuite;
    sandbox: SandboxInstance;
    evolutionVersion: string;
    productionFactory: FactoryInstance;
  }): Promise<RegressionTestResult> {
    const startTime = Date.now();
    let totalApiCalls = 0;
    let totalTokens = 0;

    const caseResults: TestCaseResult[] = [];

    // Execute test cases one by one
    for (const testCase of config.testSuite.cases) {
      try {
        // Execute WorkOrder in sandbox
        const sandboxResult = await config.sandbox.executeWorkOrder(
          testCase.input,
          { timeout: 60000 }
        );

        totalApiCalls += sandboxResult.apiCalls;
        totalTokens += sandboxResult.tokensUsed;

        // Evaluate output
        const diff = await this.evaluator.compare(
          sandboxResult.output,
          testCase.expectedOutput
        );

        const score = this.calculateScore(diff);
        const passed = score >= 0.7; // 0.7 is the minimum passing score

        caseResults.push({
          caseId: testCase.id,
          passed,
          score,
          sandboxOutput: sandboxResult.output,
          diff,
          executionTime: sandboxResult.executionTime,
        });
      } catch (error) {
        // Sandbox execution exception treated as failure
        caseResults.push({
          caseId: testCase.id,
          passed: false,
          score: 0,
          sandboxOutput: { error: String(error) },
          diff: {
            structural: { hasDifference: true, details: ['Execution exception'] },
            content: { similarity: 0, differences: [] },
            quality: { sandboxScore: 0, expectedScore: 1, delta: -1 },
          },
          failureReason: `Sandbox execution exception: ${error}`,
          executionTime: 0,
        });
      }
    }

    // Compute aggregate metrics
    const weightedPassRate = this.calculateWeightedPassRate(
      caseResults,
      config.testSuite.cases
    );
    const simplePassRate =
      caseResults.filter(r => r.passed).length / caseResults.length;

    // Aggregate by scope
    const scopeResults = this.aggregateByScope(
      caseResults,
      config.testSuite.cases
    );

    // Performance comparison (vs production version)
    const performanceComparison = await this.comparePerformance(
      config.sandbox,
      config.productionFactory,
      config.testSuite.cases.slice(0, 10) // Take first 10 for performance comparison
    );

    // Generate conclusion
    const conclusion = this.drawConclusion({
      weightedPassRate,
      simplePassRate,
      scopeResults,
      performanceComparison,
      caseResults,
    });

    return {
      testRunId: generateId(),
      testSuiteId: config.testSuite.id,
      sandboxId: config.sandbox.id,
      evolutionVersion: config.evolutionVersion,
      passed: conclusion.verdict === 'pass' || conclusion.verdict === 'pass_with_concerns',
      weightedPassRate,
      simplePassRate,
      caseResults,
      scopeResults,
      performanceComparison,
      executionTime: Date.now() - startTime,
      resourceUsage: { apiCalls: totalApiCalls, tokensUsed: totalTokens },
      conclusion,
    };
  }

  /**
   * Generate test conclusion based on thresholds
   */
  private drawConclusion(params: {
    weightedPassRate: number;
    simplePassRate: number;
    scopeResults: Record<EvolutionScope, ScopeTestResult>;
    performanceComparison: PerformanceComparison;
    caseResults: TestCaseResult[];
  }): TestConclusion {
    const reasons: string[] = [];
    const concerns: string[] = [];
    let verdict: TestConclusion['verdict'] = 'pass';

    // Check weighted pass rate
    if (params.weightedPassRate < this.thresholds.minWeightedPassRate) {
      verdict = 'fail';
      reasons.push(
        `Weighted pass rate ${params.weightedPassRate.toFixed(2)} below threshold ${this.thresholds.minWeightedPassRate}`
      );
    }

    // Check simple pass rate
    if (params.simplePassRate < this.thresholds.minSimplePassRate) {
      if (verdict === 'pass') verdict = 'fail';
      reasons.push(
        `Simple pass rate ${params.simplePassRate.toFixed(2)} below threshold ${this.thresholds.minSimplePassRate}`
      );
    }

    // Check per-scope pass rates
    for (const [scope, result] of Object.entries(params.scopeResults)) {
      if (result.passRate < this.thresholds.minScopePassRate) {
        if (verdict === 'pass') verdict = 'pass_with_concerns';
        concerns.push(
          `${scope} scope pass rate only ${result.passRate.toFixed(2)}`
        );
      }
    }

    // Check performance degradation
    if (params.performanceComparison.avgExecutionTime.deltaPercent > this.thresholds.maxPerformanceDegradation) {
      if (verdict === 'pass') verdict = 'pass_with_concerns';
      concerns.push(
        `Execution time degraded by ${params.performanceComparison.avgExecutionTime.deltaPercent.toFixed(0)}%`
      );
    }

    // Check cost increase
    if (params.performanceComparison.costEfficiency.delta > this.thresholds.maxCostIncrease) {
      if (verdict === 'pass') verdict = 'pass_with_concerns';
      concerns.push(
        `Cost increased by ${params.performanceComparison.costEfficiency.delta.toFixed(0)}%`
      );
    }

    // Check high-severity differences
    const highSevDiffs = params.caseResults.filter(
      r => r.diff.content.differences.some(d => d.severity === 'high')
    ).length;
    if (highSevDiffs > this.thresholds.maxHighSeverityDiffs) {
      verdict = 'fail';
      reasons.push(
        `${highSevDiffs} high-severity differences found`
      );
    }

    // Determine recommended next step
    let recommendedAction: TestConclusion['recommendedAction'];
    switch (verdict) {
      case 'pass':
        recommendedAction = 'proceed_to_canary';
        break;
      case 'pass_with_concerns':
        recommendedAction = 'proceed_to_canary'; // Proceed to canary with concerns
        break;
      case 'fail':
        recommendedAction = 'revise_evolution';
        break;
      case 'inconclusive':
        recommendedAction = 'extend_testing';
        break;
    }

    return { verdict, reasons, recommendedAction, concerns };
  }

  private calculateWeightedPassRate(
    results: TestCaseResult[],
    cases: RegressionTestCase[]
  ): number {
    let totalWeight = 0;
    let passedWeight = 0;

    for (let i = 0; i < results.length; i++) {
      const weight = cases[i].weight;
      totalWeight += weight;
      if (results[i].passed) {
        passedWeight += weight;
      }
    }

    return totalWeight > 0 ? passedWeight / totalWeight : 0;
  }

  private calculateScore(diff: OutputDiff): number {
    // Synthesize three dimensions: structure, content, quality
    const structureScore = diff.structural.hasDifference ? 0.5 : 1.0;
    const contentScore = diff.content.similarity;
    const qualityScore = diff.quality.delta > -0.3
      ? 1.0 + diff.quality.delta
      : 0.5;

    return structureScore * 0.2 + contentScore * 0.5 + qualityScore * 0.3;
  }

  private aggregateByScope(
    results: TestCaseResult[],
    cases: RegressionTestCase[]
  ): Record<EvolutionScope, ScopeTestResult> {
    // Aggregate test results by evolution scope
    const aggregated: Record<string, { total: number; passed: number; worst: TestCaseResult | null }> = {};

    for (let i = 0; i < cases.length; i++) {
      for (const scope of cases[i].relatedScopes) {
        if (!aggregated[scope]) {
          aggregated[scope] = { total: 0, passed: 0, worst: null };
        }
        aggregated[scope].total++;
        if (results[i].passed) aggregated[scope].passed++;
        if (!aggregated[scope].worst || results[i].score < (aggregated[scope].worst?.score ?? 1)) {
          aggregated[scope].worst = results[i];
        }
      }
    }

    const result: Record<EvolutionScope, ScopeTestResult> = {} as any;
    for (const [scope, data] of Object.entries(aggregated)) {
      result[scope as EvolutionScope] = {
        scope: scope as EvolutionScope,
        totalCases: data.total,
        passedCases: data.passed,
        passRate: data.total > 0 ? data.passed / data.total : 0,
        weightedPassRate: 0, // Simplified
        worstCase: data.worst,
      };
    }

    return result;
  }
}
```

---

### 3. A/B Three-Phase Deployment

After passing regression tests, evolution enters the progressive deployment flow. Three phases ensure risk is gradually reduced.

#### 3.1 Deployment Phase Definitions

```typescript
/**
 * Deployment stage enumeration
 */
type DeploymentStage = 'shadow' | 'canary' | 'full';

/**
 * Deployment stage configuration
 */
interface DeploymentStageConfig {
  /** Current stage */
  stage: DeploymentStage;

  /** Stage start time */
  startedAt: Date;

  /** Stage maximum duration (milliseconds) */
  maxDuration: number;

  /** Stage promotion criteria */
  promotionCriteria: PromotionCriteria;

  /** Stage rollback criteria */
  rollbackCriteria: RollbackCriteria;

  /** Traffic allocation */
  trafficAllocation: TrafficAllocation;
}

/**
 * Traffic allocation configuration
 */
interface TrafficAllocation {
  /** New version traffic percentage */
  newVersionPercent: number;

  /** Old version traffic percentage */
  oldVersionPercent: number;

  /** Routing strategy */
  routingStrategy: 'random' | 'round_robin' | 'workorder_type_based';

  /** If workorder_type_based, specify which WorkOrder types route to new version */
  targetWorkOrderTypes?: string[];
}

/**
 * Specific configuration for each stage
 */
const STAGE_CONFIGS: Record<DeploymentStage, Omit<DeploymentStageConfig, 'startedAt'>> = {
  shadow: {
    stage: 'shadow',
    maxDuration: 2 * 60 * 60 * 1000,  // 2 hours
    promotionCriteria: {
      minSampleSize: 20,
      minSuccessRate: 0.85,
      maxErrorRate: 0.05,
      minHumanSatisfaction: null,       // Shadow mode does not collect human feedback
      requireNoCriticalErrors: true,
    },
    rollbackCriteria: {
      maxErrorRate: 0.10,
      maxLatencyIncrease: 0.50,        // 50%
      criticalErrorPatterns: ['timeout', 'crash', 'data_loss'],
    },
    trafficAllocation: {
      newVersionPercent: 0,
      oldVersionPercent: 100,
      routingStrategy: 'random',
    },
  },
  canary: {
    stage: 'canary',
    maxDuration: 48 * 60 * 60 * 1000, // 48 hours
    promotionCriteria: {
      minSampleSize: 50,
      minSuccessRate: 0.90,
      maxErrorRate: 0.03,
      minHumanSatisfaction: 0.80,      // Canary stage collects human satisfaction
      requireNoCriticalErrors: true,
    },
    rollbackCriteria: {
      maxErrorRate: 0.08,
      maxLatencyIncrease: 0.30,
      criticalErrorPatterns: ['timeout', 'crash', 'data_loss', 'quality_degradation'],
      minHumanSatisfaction: 0.60,      // Human satisfaction below 60% triggers rollback
    },
    trafficAllocation: {
      newVersionPercent: 5,
      oldVersionPercent: 95,
      routingStrategy: 'random',
    },
  },
  full: {
    stage: 'full',
    maxDuration: Infinity,
    promotionCriteria: {
      minSampleSize: 100,
      minSuccessRate: 0.92,
      maxErrorRate: 0.02,
      minHumanSatisfaction: 0.85,
      requireNoCriticalErrors: true,
    },
    rollbackCriteria: {
      maxErrorRate: 0.05,
      maxLatencyIncrease: 0.20,
      criticalErrorPatterns: ['timeout', 'crash', 'data_loss', 'quality_degradation'],
      minHumanSatisfaction: 0.70,
    },
    trafficAllocation: {
      newVersionPercent: 100,
      oldVersionPercent: 0,
      routingStrategy: 'random',
    },
  },
};

/**
 * Stage promotion criteria
 */
interface PromotionCriteria {
  /** Minimum sample size */
  minSampleSize: number;

  /** Minimum success rate */
  minSuccessRate: number;

  /** Maximum error rate */
  maxErrorRate: number;

  /** Minimum human satisfaction (null means not required) */
  minHumanSatisfaction: number | null;

  /** Whether zero critical errors are required */
  requireNoCriticalErrors: boolean;
}

/**
 * Stage rollback criteria
 */
interface RollbackCriteria {
  /** Error rate above this value triggers rollback */
  maxErrorRate: number;

  /** Latency increase above this ratio triggers rollback */
  maxLatencyIncrease: number;

  /** These error patterns trigger immediate rollback */
  criticalErrorPatterns: string[];

  /** Human satisfaction below this value triggers rollback (null means not monitored) */
  minHumanSatisfaction?: number;
}
```

#### 3.2 Deployment Orchestrator (Core Pseudocode)

```typescript
/**
 * Deployment Orchestrator
 * Manages the three-phase progressive deployment of evolutions
 */
class DeploymentOrchestrator {
  private currentStage: DeploymentStageConfig | null = null;
  private metricsCollector: DeploymentMetricsCollector;
  private versionManager: VersionManager;

  constructor(
    private evolution: Evolution,
    private factory: FactoryInstance,
    private config: DeploymentOrchestratorConfig
  ) {}

  /**
   * Start deployment flow
   */
  async startDeployment(): Promise<void> {
    // 0. Pre-check: confirm regression test has passed
    const testResult = await this.getRegressionTestResult(this.evolution.id);
    if (!testResult?.passed) {
      throw new DeploymentError(
        `Evolution ${this.evolution.id} did not pass regression testing, cannot deploy`
      );
    }

    // 1. Create version snapshot (for rollback)
    const snapshot = await this.versionManager.createSnapshot(
      this.factory.id,
      {
        reason: `Snapshot before deploying evolution ${this.evolution.id}`,
        evolutionId: this.evolution.id,
      }
    );

    // 2. Enter shadow mode
    await this.transitionTo('shadow', snapshot);
  }

  /**
   * Core stage transition logic
   */
  private async transitionTo(
    targetStage: DeploymentStage,
    rollbackSnapshot: VersionSnapshot
  ): Promise<void> {
    const stageConfig = STAGE_CONFIGS[targetStage];
    this.currentStage = {
      ...stageConfig,
      startedAt: new Date(),
    };

    // Log deployment event
    await this.logDeploymentEvent({
      type: 'stage_enter',
      stage: targetStage,
      evolutionId: this.evolution.id,
      timestamp: new Date(),
    });

    // Execute different logic based on stage
    switch (targetStage) {
      case 'shadow':
        await this.runShadowMode(rollbackSnapshot);
        break;
      case 'canary':
        await this.runCanaryMode(rollbackSnapshot);
        break;
      case 'full':
        await this.runFullMode(rollbackSnapshot);
        break;
    }
  }

  /**
   * Shadow mode: new and old versions run in parallel, but only old version results are returned
   * Used to collect new version performance data without affecting user experience
   */
  private async runShadowMode(
    rollbackSnapshot: VersionSnapshot
  ): Promise<void> {
    const startTime = Date.now();
    let sampleCount = 0;
    let errorCount = 0;
    let criticalError = false;

    // Start shadow executor
    const shadowExecutor = new ShadowExecutor({
      factory: this.factory,
      evolutionVersion: this.evolution.version,
      onResult: async (shadowResult, productionResult) => {
        sampleCount++;

        // Compare results from both versions
        const comparison = await this.compareResults(
          shadowResult,
          productionResult
        );

        // Record metrics
        await this.metricsCollector.record({
          stage: 'shadow',
          workOrderId: shadowResult.workOrderId,
          shadowSuccess: shadowResult.success,
          productionSuccess: productionResult.success,
          shadowLatency: shadowResult.executionTime,
          productionLatency: productionResult.executionTime,
          outputSimilarity: comparison.similarity,
        });

        // Check for critical errors
        if (shadowResult.error) {
          errorCount++;
          if (this.isCriticalError(shadowResult.error)) {
            criticalError = true;
          }
        }
      },
    });

    // Run continuously until promotion or rollback conditions are met
    while (true) {
      await sleep(this.config.evaluationInterval);

      const elapsed = Date.now() - startTime;
      const errorRate = sampleCount > 0 ? errorCount / sampleCount : 0;

      // Check rollback conditions
      if (criticalError || errorRate > this.currentStage!.rollbackCriteria.maxErrorRate) {
        await this.executeRollback(rollbackSnapshot, {
          reason: criticalError ? 'Critical error detected' : `Error rate ${errorRate.toFixed(2)} exceeds threshold`,
          stage: 'shadow',
        });
        return;
      }

      // Check timeout
      if (elapsed > this.currentStage!.maxDuration) {
        await this.executeRollback(rollbackSnapshot, {
          reason: 'Shadow mode timed out, failed to meet promotion criteria within the specified time',
          stage: 'shadow',
        });
        return;
      }

      // Check promotion conditions
      if (sampleCount >= this.currentStage!.promotionCriteria.minSampleSize) {
        const successRate = await this.metricsCollector.getSuccessRate('shadow');
        if (successRate >= this.currentStage!.promotionCriteria.minSuccessRate) {
          // Shadow mode passed, enter canary mode
          await this.transitionTo('canary', rollbackSnapshot);
          return;
        }
      }
    }
  }

  /**
   * Canary mode: 5% of traffic routes to new version
   * Starts collecting real user feedback
   */
  private async runCanaryMode(
    rollbackSnapshot: VersionSnapshot
  ): Promise<void> {
    const startTime = Date.now();
    let sampleCount = 0;
    let errorCount = 0;
    let criticalError = false;

    // Configure traffic allocation
    await this.factory.setTrafficAllocation({
      newVersionPercent: 5,
      newVersionId: this.evolution.version,
      routingStrategy: 'random',
    });

    // Continuous monitoring
    while (true) {
      await sleep(this.config.evaluationInterval);

      const elapsed = Date.now() - startTime;
      const metrics = await this.metricsCollector.getAggregatedMetrics('canary');
      sampleCount = metrics.totalSamples;
      errorCount = metrics.errorCount;

      // Check for critical error patterns
      criticalError = metrics.criticalErrors.length > 0;

      // Check rollback conditions
      const shouldRollback =
        criticalError ||
        metrics.errorRate > this.currentStage!.rollbackCriteria.maxErrorRate ||
        (this.currentStage!.rollbackCriteria.minHumanSatisfaction != null &&
          metrics.humanSatisfaction < this.currentStage!.rollbackCriteria.minHumanSatisfaction) ||
        metrics.avgLatencyIncrease > this.currentStage!.rollbackCriteria.maxLatencyIncrease;

      if (shouldRollback) {
        await this.executeRollback(rollbackSnapshot, {
          reason: this.determineRollbackReason(metrics),
          stage: 'canary',
        });
        return;
      }

      // Check timeout
      if (elapsed > this.currentStage!.maxDuration) {
        await this.executeRollback(rollbackSnapshot, {
          reason: 'Canary mode timed out',
          stage: 'canary',
        });
        return;
      }

      // Check promotion conditions
      const promotionCriteria = this.currentStage!.promotionCriteria;
      if (
        sampleCount >= promotionCriteria.minSampleSize &&
        metrics.successRate >= promotionCriteria.minSuccessRate &&
        metrics.errorRate <= promotionCriteria.maxErrorRate &&
        (promotionCriteria.minHumanSatisfaction == null ||
          metrics.humanSatisfaction >= promotionCriteria.minHumanSatisfaction) &&
        !criticalError
      ) {
        // Canary passed, enter full deployment
        await this.transitionTo('full', rollbackSnapshot);
        return;
      }
    }
  }

  /**
   * Full deployment: 100% traffic routes to new version
   * Still continuously monitors, supports rollback
   */
  private async runFullMode(
    rollbackSnapshot: VersionSnapshot
  ): Promise<void> {
    // Switch all traffic to new version
    await this.factory.setTrafficAllocation({
      newVersionPercent: 100,
      newVersionId: this.evolution.version,
      routingStrategy: 'random',
    });

    // Mark evolution as deployed
    await this.evolution.markAsDeployed({
      deployedAt: new Date(),
      version: this.evolution.version,
    });

    // Retain old version snapshot for a period (for rollback)
    await this.versionManager.setSnapshotRetention(rollbackSnapshot.id, {
      retainUntil: new Date(Date.now() + 7 * 24 * 60 * 60 * 1000), // Retain for 7 days
      reason: 'Safe rollback window after full deployment',
    });

    // Start post-deployment monitoring (async)
    this.startPostDeploymentMonitoring(rollbackSnapshot);

    await this.logDeploymentEvent({
      type: 'deployment_complete',
      stage: 'full',
      evolutionId: this.evolution.id,
      timestamp: new Date(),
    });
  }

  /**
   * Post-deployment continuous monitoring
   * Automatic rollback still possible within a certain timeframe
   */
  private startPostDeploymentMonitoring(
    rollbackSnapshot: VersionSnapshot
  ): void {
    const monitoringWindow = 7 * 24 * 60 * 60 * 1000; // 7 days
    const checkInterval = 30 * 60 * 1000;              // 30 minutes

    const monitor = async () => {
      const elapsed = Date.now() - this.currentStage!.startedAt.getTime();
      if (elapsed > monitoringWindow) {
        // Monitoring window ended, mark evolution as stable
        await this.evolution.markAsStable();
        await this.versionManager.setSnapshotRetention(
          rollbackSnapshot.id,
          { retainUntil: new Date(Date.now() + 30 * 24 * 60 * 60 * 1000) }
        );
        return;
      }

      const metrics = await this.metricsCollector.getAggregatedMetrics('full');
      const criteria = this.currentStage!.rollbackCriteria;

      if (
        metrics.errorRate > criteria.maxErrorRate ||
        (criteria.minHumanSatisfaction != null &&
          metrics.humanSatisfaction < criteria.minHumanSatisfaction)
      ) {
        await this.executeRollback(rollbackSnapshot, {
          reason: `Anomaly detected during post-deployment monitoring: error rate=${metrics.errorRate.toFixed(2)}`,
          stage: 'full',
        });
        return;
      }

      setTimeout(monitor, checkInterval);
    };

    setTimeout(monitor, checkInterval);
  }
}
```

---

### 4. Rollback Mechanism

#### 4.1 Rollback Strategy Definition

```typescript
/**
 * Rollback policy
 */
interface RollbackPolicy {
  /** Rollback trigger method */
  trigger: 'automatic' | 'manual' | 'scheduled';

  /** Automatic rollback conditions */
  automaticConditions?: AutomaticRollbackCondition[];

  /** Rollback execution strategy */
  execution: RollbackExecution;

  /** Post-rollback actions */
  postRollback: PostRollbackAction;
}

/**
 * Automatic rollback condition
 */
interface AutomaticRollbackCondition {
  /** Condition type */
  type: 'error_rate' | 'latency' | 'human_satisfaction' | 'critical_error' | 'cost_spike';

  /** Threshold */
  threshold: number;

  /** Comparison method */
  comparator: 'gt' | 'lt' | 'eq';

  /** Sustained duration (milliseconds), prevents transient spikes from triggering rollback */
  sustainedDuration: number;      // default: 300000 (5 minutes)

  /** Check window */
  checkWindow: number;            // default: 600000 (10 minutes)
}

/**
 * Rollback execution strategy
 */
interface RollbackExecution {
  /** Rollback mode */
  mode: 'immediate' | 'graceful';

  /** Drain timeout for graceful rollback (milliseconds) */
  drainTimeout: number;           // default: 300000 (5 minutes)

  /** Whether to automatically notify humans */
  notifyHuman: boolean;           // default: true

  /** Notification channels */
  notificationChannels: ('email' | 'slack' | 'in_app')[];

  /** Whether to automatically create an analysis task after rollback */
  autoAnalysis: boolean;          // default: true
}

/**
 * Post-rollback actions
 */
interface PostRollbackAction {
  /** Whether to automatically create an evolution fix proposal */
  createFixProposal: boolean;     // default: true

  /** Whether to retain sandbox for debugging */
  retainSandbox: boolean;         // default: true

  /** Whether to pause redeployment of the same evolution */
  pauseRedeployment: boolean;     // default: true

  /** Pause duration (milliseconds) */
  pauseDuration: number;          // default: 24 * 60 * 60 * 1000 (24 hours)
}

/**
 * Version snapshot management
 */
interface VersionSnapshot {
  /** Snapshot identifier */
  id: string;

  /** Factory identifier */
  factoryId: string;

  /** Version number */
  version: string;

  /** Snapshot creation time */
  createdAt: Date;

  /** Snapshot content hash (for integrity verification) */
  contentHash: string;

  /** Snapshot size (bytes) */
  size: number;

  /** Retention policy */
  retention: {
    retainUntil: Date;
    reason: string;
  };

  /** Associated evolution ID */
  evolutionId?: string;

  /** Snapshot tags */
  tags: string[];
}

/**
 * Rollback executor
 */
class RollbackExecutor {
  constructor(
    private versionManager: VersionManager,
    private factory: FactoryInstance,
    private notifier: Notifier
  ) {}

  /**
   * Execute rollback
   */
  async executeRollback(params: {
    snapshot: VersionSnapshot;
    reason: string;
    stage: DeploymentStage;
    policy: RollbackPolicy;
  }): Promise<RollbackResult> {
    const startTime = Date.now();

    // 1. Log rollback event
    await this.logRollbackEvent({
      type: 'rollback_start',
      snapshotId: params.snapshot.id,
      reason: params.reason,
      fromStage: params.stage,
      timestamp: new Date(),
    });

    // 2. If graceful rollback, drain in-progress WorkOrders first
    if (params.policy.execution.mode === 'graceful') {
      await this.drainInProgressWorkOrders(params.policy.execution.drainTimeout);
    }

    // 3. Restore to snapshot version
    await this.versionManager.restoreSnapshot(
      params.snapshot.id,
      this.factory.id
    );

    // 4. Reset traffic allocation
    await this.factory.setTrafficAllocation({
      newVersionPercent: 0,
      newVersionId: params.snapshot.version,
      routingStrategy: 'random',
    });

    // 5. Notify humans
    if (params.policy.execution.notifyHuman) {
      await this.notifier.send({
        channels: params.policy.execution.notificationChannels,
        subject: `Evolution rollback: ${params.reason}`,
        body: this.formatRollbackNotification(params),
        priority: 'high',
      });
    }

    // 6. Post-rollback actions
    if (params.policy.postRollback.createFixProposal) {
      await this.createFixProposal(params);
    }

    if (params.policy.postRollback.pauseRedeployment) {
      await this.pauseRedeployment({
        evolutionId: params.snapshot.evolutionId!,
        duration: params.policy.postRollback.pauseDuration,
        reason: `Post-rollback pause: ${params.reason}`,
      });
    }

    const result: RollbackResult = {
      success: true,
      snapshotId: params.snapshot.id,
      restoredVersion: params.snapshot.version,
      reason: params.reason,
      executionTime: Date.now() - startTime,
      timestamp: new Date(),
    };

    await this.logRollbackEvent({
      type: 'rollback_complete',
      ...result,
    });

    return result;
  }
}

interface RollbackResult {
  success: boolean;
  snapshotId: string;
  restoredVersion: string;
  reason: string;
  executionTime: number;
  timestamp: Date;
}
```

---

### 5. Complete Deployment Flow Diagram

```
Evolution proposal approved
       |
       v
  Create version snapshot ──────────────────────────────┐
       |                                                |
       v                                                | (for rollback)
  Build regression test suite                           |
       |                                                |
       v                                                |
  Clone sandbox + execute regression testing            |
       |                                                |
       +-- Pass --> Enter shadow mode (Shadow)           |
       |              |                                  |
       |              +-- New and old versions run in    |
       |              |   parallel                       |
       |              +-- Only return old version results|
       |              +-- Collect new version metrics    |
       |              |                                  |
       |              +-- Metrics meet criteria --> Canary (Canary 5%)
       |              |              |                    |
       |              |              +-- 5% traffic to   |
       |              |              |   new version     |
       |              |              +-- Collect human   |
       |              |              |   feedback        |
       |              |              +-- Continuous      |
       |              |              |   monitoring      |
       |              |              |                    |
       |              |              +-- Metrics meet --> Full (Full 100%)
       |              |              |   criteria              |
       |              |              |                          |
       |              |              |   +-- 100% traffic to     |
       |              |              |   |   new version         |
       |              |              |   +-- Continuous          |
       |              |              |   |   monitoring for 7 days|
       |              |              |   |                       |
       |              |              |   +-- Mark as stable      |
       |              |              |                           |
       |              |              +-- Any stage anomaly --> Auto rollback --> Restore snapshot
       |              |                                          |
       |              +-- Metrics anomaly --> Auto rollback ──────────────────────> Restore snapshot
       |
       +-- Fail --> Return for revision (do not enter deployment flow)
```

---

## Advantages

1. **Zero-risk exploration**: Sandbox cloning allows Factory to boldly test evolution plans without affecting the production environment
2. **Data-driven decisions**: Regression testing validates against real historical data, not subjective judgment
3. **Progressive trust**: Three-phase deployment reduces risk as validation depth increases; shadow mode has zero user impact, canary mode has low-risk exposure
4. **Automatic self-healing**: Automatic rollback ensures quick recovery even if evolution goes wrong, without requiring real-time human monitoring
5. **Complete traceability**: Every phase has detailed metric records and event logs for post-hoc analysis
6. **Evolution metadata accumulation**: All data from the deployment process becomes valuable evolution metadata for guiding future evolution decisions

## Risks

1. **Sandbox-production environment gap**: The sandbox cannot 100% replicate all factors in production (e.g., real-time data changes, external service status)
   - **Mitigation**: Shadow mode runs the new version directly in production (but does not return results), compensating for sandbox testing limitations
2. **Insufficient regression test representativeness**: Historical data may not cover all edge cases
   - **Mitigation**: Periodically update the test suite; introduce adversarial test cases (deliberately construct difficult inputs)
3. **Insufficient canary sample size**: 5% traffic may require a long time to collect sufficient statistical samples
   - **Mitigation**: For low-frequency Factories, can appropriately increase the canary ratio (e.g., 10-15%) or extend the canary duration
4. **Rollback itself may fail**: The snapshot restoration process may encounter issues
   - **Mitigation**: Perform integrity verification after snapshot creation; periodically test the rollback flow
5. **Evolution budget consumption**: Sandbox testing and three-phase deployment consume significant compute resources
   - **Mitigation**: Set evolution budget caps; low-risk evolutions can use a simplified flow (e.g., skip shadow mode)

## Pending Decisions

- [ ] Regression test suite update frequency (suggested: automatic weekly update)
- [ ] Whether canary traffic percentage should be dynamically adjusted by Factory type
- [ ] "Sustained anomaly" determination time window for automatic rollback (suggested: 5 minutes)
- [ ] Post full-deployment monitoring window duration (suggested: 7 days)
- [ ] Whether to support "canary pause" -- manually pause at canary stage to collect more data before deciding
- [ ] Whether default sandbox environment resource limits are reasonable
- [ ] Version snapshot storage strategy (how many historical versions to retain, how to control storage costs)
- [ ] Whether to support "emergency full deployment" -- skip shadow and canary, go directly to full (emergency fixes only)
