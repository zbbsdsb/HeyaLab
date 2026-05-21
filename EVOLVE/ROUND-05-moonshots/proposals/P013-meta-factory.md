# Proposal P013: MetaFactory -- The Factory of Factories

> **Status**: Draft
> **Proposed by**: AI Assistant
> **Date**: 2026-05-16
> **Related**: ROUND-05 brainstorm, VISION.md ("AI transforms itself"), ROADMAP Phase 3 (Self-Evolution Framework), RESEARCH/SYNTHESIS.md (Self-Evolving Agents, MetaGPT)

---

## Summary

MetaFactory is a special Factory whose product is not code, design, or content, but **the design and optimization of other Factories**. MetaFactory analyzes user intent, automatically designs Factory architecture (Pipeline, verification rules, memory strategy, checkpoints), tests in a sandbox, and delivers to the user. After delivery, MetaFactory continuously monitors the Factory's runtime performance, automatically proposes optimization suggestions, and safely applies changes upon human approval.

MetaFactory is the ultimate automation of Heya's "AI transforms itself" philosophy -- when MetaFactory is mature enough, users only need to describe "what I want," and the entire Factory's design, deployment, optimization, and evolution are handled by AI.

---

## Design

### Overall Architecture

```
┌──────────────────────────────────────────────────────────────────────┐
│                         MetaFactory                                   │
│                                                                      │
│  ┌─────────────────┐  ┌──────────────────┐  ┌────────────────────┐  │
│  │ Intent          │  │ Factory          │  │ Optimization       │  │
│  │ Analyzer        │  │ Architect        │  │ Engine             │  │
│  │ (Intent Analysis)│  │ (Architecture    │  │ (Optimization      │  │
│  │                 │  │  Design)         │  │  Engine)           │  │
│  └────────┬────────┘  └────────┬─────────┘  └────────┬───────────┘  │
│           │                    │                      │              │
│  ┌────────┴────────────────────┴──────────────────────┴───────────┐  │
│  │                    Sandbox & Evaluation                        │  │
│  │  ┌──────────────┐  ┌──────────────┐  ┌──────────────────────┐  │  │
│  │  │ Sandbox      │  │ A/B Testing  │  │ Regression Testing   │  │  │
│  │  │ Executor     │  │ Framework    │  │ Engine               │  │  │
│  │  └──────────────┘  └──────────────┘  └──────────────────────┘  │  │
│  └────────────────────────────────────────────────────────────────┘  │
│           │                    │                      │              │
│  ┌────────┴────────────────────┴──────────────────────┴───────────┐  │
│  │                    Safe Deployment                             │  │
│  │  ┌──────────────┐  ┌──────────────┐  ┌──────────────────────┐  │  │
│  │  │ Change       │  │ Rollback     │  │ Human Approval       │  │  │
│  │  │ Validator    │  │ Manager      │  │ Gate                 │  │  │
│  │  └──────────────┘  └──────────────┘  └──────────────────────┘  │  │
│  └────────────────────────────────────────────────────────────────┘  │
│                                                                      │
│  ┌────────────────────────────────────────────────────────────────┐  │
│  │                    Template & Pattern Library                   │  │
│  │  Factory Templates | Optimization Pattern Library | Best Practices Library | Anti-Pattern Library │  │
│  └────────────────────────────────────────────────────────────────┘  │
└──────────────────────────────────────────────────────────────────────┘
```

### 1. Intent Analyzer

Transforms the user's natural language requirements into a structured Factory design specification.

```typescript
// ============ Intent Analysis Interfaces ============

interface FactoryDesignRequest {
  /** User's natural language description */
  userIntent: string;
  /** Additional context provided by the user */
  context?: {
    existingFactoryId?: string;       // Optimize based on existing Factory
    referenceFactoryIds?: string[];   // Reference Factories
    constraints?: FactoryConstraint[];
    preferences?: UserPreference[];
  };
  /** Session ID for tracking multi-turn conversations */
  sessionId: string;
}

interface FactoryConstraint {
  type: "budget" | "timeline" | "quality" | "privacy" | "compliance";
  description: string;
  priority: "must" | "should" | "nice_to_have";
  params?: Record<string, unknown>;
}

interface UserPreference {
  dimension: "speed" | "quality" | "cost" | "creativity" | "consistency";
  weight: number;  // 0-1
}

interface FactoryDesignSpec {
  /** Design spec ID */
  specId: string;
  /** Intent summary */
  intentSummary: string;
  /** Product type */
  productType: ProductType;
  /** Required capabilities list */
  requiredCapabilities: CapabilityRequirement[];
  /** Suggested Pipeline structure */
  suggestedPipeline: PipelineDesign;
  /** Suggested verification rules */
  suggestedVerification: VerificationDesign;
  /** Suggested memory strategy */
  suggestedMemory: MemoryStrategy;
  /** Suggested checkpoints */
  suggestedCheckpoints: CheckpointDesign[];
  /** Estimated resources */
  estimatedResources: ResourceEstimate;
  /** Matched templates */
  matchedTemplates: TemplateMatch[];
  /** Design confidence */
  confidence: number;  // 0-1
  /** Questions requiring human clarification */
  clarificationQuestions?: string[];
}

type ProductType =
  | "software"
  | "design"
  | "content"
  | "strategy"
  | "data_analysis"
  | "documentation"
  | "custom";

interface CapabilityRequirement {
  name: string;
  description: string;
  criticality: "essential" | "important" | "optional";
  estimatedComplexity: "low" | "medium" | "high";
}

interface TemplateMatch {
  templateId: string;
  name: string;
  similarity: number;  // 0-1
  adaptations: string[];  // Required adjustments
}

interface ResourceEstimate {
  avgTokensPerWorkOrder: number;
  avgCostPerWorkOrder: number;  // USD
  avgTimePerWorkOrder: number;  // ms
  recommendedModelTier: "fast" | "balanced" | "premium";
}
```

**Intent analysis pseudocode**:

```typescript
class IntentAnalyzerImpl implements IntentAnalyzer {
  async analyze(request: FactoryDesignRequest): Promise<FactoryDesignSpec> {
    // Step 1: Understand core intent
    const intentUnderstanding = await this.llm.analyze<{
      summary: string;
      productType: ProductType;
      coreGoal: string;
      implicitRequirements: string[];
    }>({
      systemPrompt: META_FACTORY_INTENT_PROMPT,
      userMessage: request.userIntent,
      schema: IntentUnderstandingSchema,
    });

    // Step 2: Extract capability requirements
    const capabilities = await this.extractCapabilities(
      intentUnderstanding.summary,
      intentUnderstanding.implicitRequirements
    );

    // Step 3: Search for matching templates
    const templates = await this.templateLibrary.search({
      productType: intentUnderstanding.productType,
      capabilities: capabilities.map(c => c.name),
    });

    // Step 4: If matching templates exist, generate design based on template
    if (templates.length > 0 && templates[0].similarity > 0.7) {
      return this.designFromTemplate(
        templates[0],
        capabilities,
        request.context
      );
    }

    // Step 5: Otherwise, design from scratch
    return this.designFromScratch(
      intentUnderstanding,
      capabilities,
      request.context
    );
  }

  private async extractCapabilities(
    summary: string,
    implicitRequirements: string[]
  ): Promise<CapabilityRequirement[]> {
    const capabilityCatalog = await this.getCapabilityCatalog();

    const analysis = await this.llm.analyze<{
      required: Array<{ name: string; reason: string }>;
    }>({
      systemPrompt: CAPABILITY_EXTRACTION_PROMPT,
      userMessage: `Summary: ${summary}\nImplicit: ${implicitRequirements.join(", ")}`,
      context: `Available capabilities: ${JSON.stringify(capabilityCatalog)}`,
    });

    return analysis.required.map(r => ({
      name: r.name,
      description: r.reason,
      criticality: "essential" as const,
      estimatedComplexity: capabilityCatalog[r.name]?.complexity ?? "medium",
    }));
  }
}
```

### 2. Factory Architect

Generates a complete Factory configuration based on the design specification.

```typescript
// ============ Factory Architecture Design Interfaces ============

interface FactoryBlueprint {
  /** Factory ID */
  factoryId: string;
  /** Factory name */
  name: string;
  /** Factory description */
  description: string;
  /** Version */
  version: string;
  /** Creation time */
  createdAt: number;
  /** Design spec this is based on */
  designSpecId: string;
  /** Pipeline definition */
  pipeline: PipelineDefinition;
  /** Verification configuration */
  verification: VerificationConfig;
  /** Memory configuration */
  memory: MemoryConfig;
  /** Checkpoint configuration */
  checkpoints: CheckpointConfig[];
  /** Agent configuration */
  agents: AgentConfig[];
  /** Tool configuration */
  tools: ToolConfig[];
  /** Model configuration */
  modelConfig: ModelConfig;
  /** Evolution configuration */
  evolution: EvolutionConfig;
  /** DNA (if enabled) */
  dna?: FactoryDNA;
}

interface PipelineDefinition {
  steps: PipelineStep[];
  parallelGroups?: ParallelGroup[];
  fallbackPaths?: FallbackPath[];
}

interface PipelineStep {
  id: string;
  name: string;
  description: string;
  role: string;                     // Agent role
  inputSchema: JSONSchema;
  outputSchema: JSONSchema;
  verification: StepVerification;
  retryPolicy: RetryPolicy;
  timeoutMs: number;
  modelTier: "fast" | "balanced" | "premium";
  promptTemplate?: string;
  dependencies: string[];           // IDs of prerequisite steps
}

interface StepVerification {
  enabled: boolean;
  level: 0 | 1 | 2 | 3 | 4;       // Verification level
  autoFixEnabled: boolean;
  autoFixMaxRetries: number;
  evaluatorModelTier: "fast" | "balanced";
}

interface VerificationConfig {
  globalLevel: 0 | 1 | 2 | 3 | 4;
  stepOverrides: Record<string, StepVerification>;
  customValidators: CustomValidator[];
}

interface MemoryConfig {
  tiers: {
    working: WorkingMemoryConfig;
    episodic: EpisodicMemoryConfig;
    semantic: SemanticMemoryConfig;
    artifact: ArtifactMemoryConfig;
  };
  retrieval: RetrievalConfig;
  consolidation: ConsolidationConfig;
}

interface EvolutionConfig {
  enabled: boolean;
  triggerMode: "performance" | "feedback" | "periodic" | "hybrid";
  autoApplyThreshold: number;       // Confidence above this value allows auto-apply
  requireHumanApproval: boolean;
  maxChangesPerCycle: number;
  cooldownMs: number;
}
```

**Architecture design pseudocode**:

```typescript
class FactoryArchitectImpl implements FactoryArchitect {
  async design(spec: FactoryDesignSpec): Promise<FactoryBlueprint> {
    const factoryId = generateFactoryId();

    // Step 1: Design Pipeline
    const pipeline = await this.designPipeline(spec);

    // Step 2: Design verification strategy
    const verification = this.designVerification(spec, pipeline);

    // Step 3: Design memory strategy
    const memory = this.designMemory(spec, pipeline);

    // Step 4: Design checkpoints
    const checkpoints = this.designCheckpoints(spec, pipeline);

    // Step 5: Design Agent configuration
    const agents = this.designAgents(pipeline);

    // Step 6: Assemble Blueprint
    const blueprint: FactoryBlueprint = {
      factoryId,
      name: this.generateName(spec.intentSummary),
      description: spec.intentSummary,
      version: "1.0.0",
      createdAt: Date.now(),
      designSpecId: spec.specId,
      pipeline,
      verification,
      memory,
      checkpoints,
      agents,
      tools: this.selectTools(spec),
      modelConfig: this.selectModels(spec),
      evolution: {
        enabled: true,
        triggerMode: "hybrid",
        autoApplyThreshold: 0.9,
        requireHumanApproval: true,
        maxChangesPerCycle: 3,
        cooldownMs: 86400000,  // 24 hours
      },
    };

    // Step 7: Self-review
    const review = await this.selfReview(blueprint);
    if (review.issues.length > 0) {
      await this.fixIssues(blueprint, review.issues);
    }

    return blueprint;
  }

  private async designPipeline(spec: FactoryDesignSpec): Promise<PipelineDefinition> {
    // Design optimal Pipeline based on product type and capability requirements
    const design = await this.llm.analyze<PipelineDefinition>({
      systemPrompt: PIPELINE_DESIGN_PROMPT,
      userMessage: JSON.stringify({
        productType: spec.productType,
        capabilities: spec.requiredCapabilities,
        constraints: spec.suggestedPipeline,
      }),
      schema: PipelineDefinitionSchema,
    });

    // Validate Pipeline completeness and soundness
    const validation = this.validatePipeline(design);
    if (!validation.valid) {
      throw new Error(`Invalid Pipeline design: ${validation.errors.join(", ")}`);
    }

    return design;
  }

  private selfReview(blueprint: FactoryBlueprint): Promise<DesignReview> {
    // Use a separate LLM call to review the design
    return this.llm.analyze<DesignReview>({
      systemPrompt: DESIGN_REVIEW_PROMPT,
      userMessage: JSON.stringify(blueprint),
      schema: DesignReviewSchema,
    });
  }
}
```

### 3. Optimization Engine

Continuously monitors Factory performance and automatically proposes optimization suggestions.

```typescript
// ============ Optimization Engine Interfaces ============

interface OptimizationEngine {
  /** Analyze Factory performance */
  analyzePerformance(factoryId: string, window: TimeWindow): PerformanceAnalysis;

  /** Generate optimization proposals */
  generateOptimizations(analysis: PerformanceAnalysis): OptimizationProposal[];

  /** Validate optimization effects in sandbox */
  validateOptimization(
    factoryId: string,
    proposal: OptimizationProposal
  ): Promise<ValidationResult>;

  /** Apply optimization (requires human approval) */
  applyOptimization(
    factoryId: string,
    proposal: OptimizationProposal,
    approval: HumanApproval
  ): Promise<ApplicationResult>;
}

interface TimeWindow {
  start: number;
  end: number;
  granularity: "hour" | "day" | "week";
}

interface PerformanceAnalysis {
  factoryId: string;
  window: TimeWindow;
  metrics: PerformanceMetrics;
  trends: TrendAnalysis[];
  anomalies: Anomaly[];
  bottlenecks: Bottleneck[];
  optimizationOpportunities: OptimizationOpportunity[];
}

interface PerformanceMetrics {
  // Output metrics
  totalWorkOrders: number;
  successRate: number;              // 0-1
  avgQualityScore: number;          // 0-1
  avgTimeToComplete: number;        // ms

  // Resource metrics
  avgTokensPerWorkOrder: number;
  avgCostPerWorkOrder: number;      // USD
  totalCost: number;                // USD

  // Interaction metrics
  humanCorrectionRate: number;      // 0-1
  avgCorrectionsPerWorkOrder: number;
  humanApprovalRate: number;        // 0-1

  // Evolution metrics
  evolutionCount: number;
  evolutionSuccessRate: number;     // 0-1
}

interface TrendAnalysis {
  metric: keyof PerformanceMetrics;
  direction: "improving" | "stable" | "declining";
  rate: number;                     // Rate of change
  confidence: number;               // Trend confidence
  startDate: number;
}

interface Anomaly {
  id: string;
  type: "spike" | "drop" | "pattern_change";
  metric: string;
  severity: "low" | "medium" | "high";
  detectedAt: number;
  description: string;
  probableCause: string;
}

interface Bottleneck {
  pipelineStepId: string;
  stepName: string;
  avgDuration: number;
  p99Duration: number;
  queueTime: number;
  failureRate: number;
  impact: "low" | "medium" | "high";
}

interface OptimizationOpportunity {
  type: OptimizationType;
  target: string;                   // Affected component
  description: string;
  estimatedImpact: ImpactEstimate;
  effort: "low" | "medium" | "high";
  risk: "low" | "medium" | "high";
}

type OptimizationType =
  | "pipeline_reorder"             // Reorder steps
  | "step_add"                     // Add step
  | "step_remove"                  // Remove step
  | "step_merge"                   // Merge steps
  | "verification_adjust"          // Adjust verification level
  | "memory_strategy_change"       // Change memory strategy
  | "model_upgrade"                // Upgrade model
  | "model_downgrade"              // Downgrade model (cost saving)
  | "prompt_optimization"          // Optimize prompts
  | "checkpoint_add"               // Add checkpoint
  | "checkpoint_remove"            // Remove checkpoint
  | "tool_add"                     // Add tool
  | "tool_remove"                  // Remove tool
  | "parallel_execution"           // Parallelize execution
  | "cache_add"                    // Add cache
  | "custom";                      // Custom optimization

interface ImpactEstimate {
  qualityImprovement: number;      // Expected quality improvement (0-1)
  speedImprovement: number;        // Expected speed improvement (0-1)
  costReduction: number;           // Expected cost reduction (0-1)
  confidence: number;              // Estimate confidence (0-1)
}

interface OptimizationProposal {
  proposalId: string;
  factoryId: string;
  type: OptimizationType;
  title: string;
  description: string;
  reasoning: string;                // Why this optimization is recommended
  currentBehavior: string;         // Current behavior description
  proposedBehavior: string;        // Post-optimization behavior description
  changes: BlueprintChange[];
  estimatedImpact: ImpactEstimate;
  risk: RiskAssessment;
  rollbackPlan: string;
  validationPlan: ValidationPlan;
  requiresHumanApproval: boolean;
  priority: "low" | "medium" | "high" | "critical";
}

interface BlueprintChange {
  path: string;                     // Path in Blueprint, e.g. "pipeline.steps[2].timeoutMs"
  oldValue: unknown;
  newValue: unknown;
  description: string;
}

interface RiskAssessment {
  level: "low" | "medium" | "high" | "critical";
  factors: string[];
  mitigations: string[];
  canRollback: boolean;
  affectedComponents: string[];
}

interface ValidationPlan {
  sandboxTestCases: string[];       // Sandbox test case descriptions
  regressionTestScope: string;      // Regression test scope
  successCriteria: string[];        // Success criteria
  abTestConfig?: ABTestConfig;      // A/B test configuration
}

interface ABTestConfig {
  trafficSplit: number;             // New version traffic proportion (0-1)
  durationMs: number;               // Test duration
  metrics: string[];                // Monitored metrics
  minSampleSize: number;            // Minimum sample size
}

interface ValidationResult {
  proposalId: string;
  passed: boolean;
  sandboxResults: SandboxResult[];
  regressionResults: RegressionResult;
  abTestResults?: ABTestResult;
  recommendation: "apply" | "modify" | "reject";
  confidence: number;
  warnings: string[];
}

interface ApplicationResult {
  proposalId: string;
  factoryId: string;
  status: "applied" | "rolled_back" | "partial";
  newVersion: string;
  appliedChanges: BlueprintChange[];
  skippedChanges: BlueprintChange[];
  appliedAt: number;
  monitoringStatus: "active" | "completed" | "alert";
}
```

**Optimization engine pseudocode**:

```typescript
class OptimizationEngineImpl implements OptimizationEngine {
  async analyzePerformance(
    factoryId: string,
    window: TimeWindow
  ): Promise<PerformanceAnalysis> {
    // 1. Collect raw metrics
    const rawMetrics = await this.metricsCollector.collect(factoryId, window);

    // 2. Calculate aggregated metrics
    const metrics = this.aggregateMetrics(rawMetrics);

    // 3. Analyze trends
    const trends = await this.analyzeTrends(factoryId, metrics, window);

    // 4. Detect anomalies
    const anomalies = await this.detectAnomalies(rawMetrics);

    // 5. Identify bottlenecks
    const bottlenecks = this.identifyBottlenecks(rawMetrics);

    // 6. Discover optimization opportunities
    const opportunities = await this.discoverOpportunities(
      factoryId, metrics, trends, bottlenecks
    );

    return { factoryId, window, metrics, trends, anomalies, bottlenecks, opportunities };
  }

  async generateOptimizations(
    analysis: PerformanceAnalysis
  ): Promise<OptimizationProposal[]> {
    const proposals: OptimizationProposal[] = [];

    for (const opportunity of analysis.optimizationOpportunities) {
      // Generate specific proposal for each optimization opportunity
      const proposal = await this.generateProposal(opportunity, analysis);
      if (proposal) {
        proposals.push(proposal);
      }
    }

    // Sort by priority
    proposals.sort((a, b) => {
      const priorityOrder = { critical: 4, high: 3, medium: 2, low: 1 };
      const impactA = a.estimatedImpact.qualityImprovement * 0.4
                    + a.estimatedImpact.speedImprovement * 0.3
                    + a.estimatedImpact.costReduction * 0.3;
      const impactB = b.estimatedImpact.qualityImprovement * 0.4
                    + b.estimatedImpact.speedImprovement * 0.3
                    + b.estimatedImpact.costReduction * 0.3;
      return (priorityOrder[b.priority] * impactB) - (priorityOrder[a.priority] * impactA);
    });

    return proposals;
  }

  async validateOptimization(
    factoryId: string,
    proposal: OptimizationProposal
  ): Promise<ValidationResult> {
    // Step 1: Test in sandbox
    const sandboxFactory = await this.sandbox.createFactory(factoryId, proposal.changes);
    const sandboxResults = await this.runSandboxTests(sandboxFactory, proposal.validationPlan);

    // Step 2: Regression testing
    const regressionResults = await this.runRegressionTests(
      factoryId,
      proposal.changes,
      proposal.validationPlan.regressionTestScope
    );

    // Step 3: Comprehensive evaluation
    const passed = sandboxResults.every(r => r.passed)
                && regressionResults.passed;

    const recommendation = passed
      ? (proposal.risk.level === "low" ? "apply" : "modify")
      : "reject";

    return {
      proposalId: proposal.proposalId,
      passed,
      sandboxResults,
      regressionResults,
      recommendation,
      confidence: this.calculateConfidence(sandboxResults, regressionResults),
      warnings: this.collectWarnings(sandboxResults, regressionResults),
    };
  }

  async applyOptimization(
    factoryId: string,
    proposal: OptimizationProposal,
    approval: HumanApproval
  ): Promise<ApplicationResult> {
    const factory = await this.factoryStore.get(factoryId);

    // Step 1: Create new version
    const newVersion = this.incrementVersion(factory.version);

    // Step 2: Apply changes
    const appliedChanges: BlueprintChange[] = [];
    const skippedChanges: BlueprintChange[] = [];

    for (const change of proposal.changes) {
      try {
        await this.applyChange(factory, change);
        appliedChanges.push(change);
      } catch (error) {
        skippedChanges.push(change);
        this.logger.warn(`Failed to apply change: ${change.path}`, error);
      }
    }

    // Step 3: Save new version
    factory.version = newVersion;
    await this.factoryStore.save(factory);

    // Step 4: Start monitoring
    await this.monitoring.startWatching(factoryId, {
      proposalId: proposal.proposalId,
      expectedImpact: proposal.estimatedImpact,
      rollbackAfterMs: 3600000,  // Auto-evaluate after 1 hour
    });

    return {
      proposalId: proposal.proposalId,
      factoryId,
      status: appliedChanges.length === proposal.changes.length ? "applied" : "partial",
      newVersion,
      appliedChanges,
      skippedChanges,
      appliedAt: Date.now(),
      monitoringStatus: "active",
    };
  }
}
```

### 4. Safe Deployment

Ensures optimization changes are safely applied to the production environment.

```typescript
// ============ Safe Deployment Interfaces ============

interface SafeDeploymentManager {
  /** Pre-check: verify change safety before applying */
  preCheck(factoryId: string, changes: BlueprintChange[]): PreCheckResult;

  /** Gradual rollout: progressively expand change scope */
  gradualRollout(
    factoryId: string,
    proposal: OptimizationProposal
  ): Promise<RolloutResult>;

  /** Rollback: revert to previous version */
  rollback(factoryId: string, targetVersion: string): Promise<RollbackResult>;
}

interface PreCheckResult {
  passed: boolean;
  checks: SafetyCheck[];
  blockReason?: string;
}

interface SafetyCheck {
  name: string;
  passed: boolean;
  severity: "info" | "warning" | "blocking";
  message: string;
}

interface RolloutResult {
  status: "fully_deployed" | "partial" | "rolled_back";
  stages: RolloutStage[];
  finalMetrics: PerformanceMetrics;
  decision: "continue" | "rollback" | "pause";
}

interface RolloutStage {
  percentage: number;               // Traffic proportion
  durationMs: number;
  metrics: PerformanceMetrics;
  passed: boolean;
  anomalies: string[];
}

interface RollbackResult {
  success: boolean;
  fromVersion: string;
  toVersion: string;
  durationMs: number;
  dataLoss: boolean;
}
```

**Safe deployment pseudocode**:

```typescript
class SafeDeploymentManagerImpl implements SafeDeploymentManager {
  preCheck(factoryId: string, changes: BlueprintChange[]): PreCheckResult {
    const checks: SafetyCheck[] = [];

    // Check 1: Does the change affect core invariants?
    checks.push({
      name: "core_invariance",
      passed: this.checkCoreInvariance(changes),
      severity: "blocking",
      message: "Changes must not violate Factory core behavior constraints",
    });

    // Check 2: Does the change affect human checkpoints?
    checks.push({
      name: "checkpoint_preservation",
      passed: this.checkCheckpointPreservation(changes),
      severity: "blocking",
      message: "Changes must not remove required human checkpoints",
    });

    // Check 3: Is the change scope too large?
    checks.push({
      name: "change_blast_radius",
      passed: changes.length <= MAX_CHANGES_PER_DEPLOYMENT,
      severity: "warning",
      message: `Number of changes: ${changes.length}, recommended no more than ${MAX_CHANGES_PER_DEPLOYMENT}`,
    });

    // Check 4: Is there a rollback plan?
    checks.push({
      name: "rollback_feasibility",
      passed: this.checkRollbackFeasibility(changes),
      severity: "blocking",
      message: "All changes must be rollback-capable",
    });

    const blocking = checks.filter(c => c.severity === "blocking" && !c.passed);
    return {
      passed: blocking.length === 0,
      checks,
      blockReason: blocking.length > 0 ? blocking.map(c => c.message).join("; ") : undefined,
    };
  }

  async gradualRollout(
    factoryId: string,
    proposal: OptimizationProposal
  ): Promise<RolloutResult> {
    const stages: RolloutStage[] = [];
    const rolloutPlan = [0.1, 0.3, 0.6, 1.0]; // 10% -> 30% -> 60% -> 100%

    for (const percentage of rolloutPlan) {
      // Apply to specified proportion of traffic
      await this.trafficRouter.split(factoryId, percentage, proposal.changes);

      // Wait for sufficient data
      await this.sleep(STAGE_DURATION_MS);

      // Collect metrics
      const metrics = await this.metricsCollector.collectCurrent(factoryId);

      // Check for anomalies
      const anomalies = await this.detectAnomalies(metrics);

      const stage: RolloutStage = {
        percentage,
        durationMs: STAGE_DURATION_MS,
        metrics,
        passed: anomalies.length === 0 && this.meetsSuccessCriteria(metrics, proposal),
        anomalies: anomalies.map(a => a.description),
      };
      stages.push(stage);

      if (!stage.passed) {
        // Rollback
        await this.rollback(factoryId, "previous");
        return {
          status: "rolled_back",
          stages,
          finalMetrics: metrics,
          decision: "rollback",
        };
      }
    }

    return {
      status: "fully_deployed",
      stages,
      finalMetrics: stages[stages.length - 1].metrics,
      decision: "continue",
    };
  }
}
```

### 5. MetaFactory Complete Workflow

```
User: "I need a Factory that automatically generates technical blogs"
  |
  v
[Intent Analyzer]
  Analyze intent -> Generate FactoryDesignSpec
  |
  v
[Factory Architect]
  Design Blueprint -> Pipeline + Verification + Memory + Checkpoints
  |
  v
[Sandbox Executor]
  Test Blueprint in sandbox -> Run simulated WorkOrders
  |
  v
[Human Approval Gate]
  Present design -> User approval/modification
  |
  v
[Deployment]
  Deploy Factory -> User starts using it
  |
  v (continuous operation)
[Optimization Engine]
  Monitor performance -> Discover optimization opportunities -> Generate proposals
  |
  v
[Sandbox Validation]
  Validate optimization effects -> A/B testing
  |
  v
[Human Approval Gate]
  Present optimization suggestions -> User approval
  |
  v
[Safe Deployment]
  Gradual rollout -> Monitor -> Full deployment or rollback
  |
  v (loop)
```

---

## Advantages

1. **Zero-configuration experience**: Users only need to describe their needs; MetaFactory automatically handles all design work
2. **Continuous optimization**: Factory continues to improve after delivery, getting better with use
3. **Data-driven**: All optimization suggestions are based on actual runtime data, not guesswork
4. **Safe and controllable**: Sandbox testing + gradual rollout + human approval + one-click rollback
5. **Knowledge accumulation**: MetaFactory learns from all Factory experiences, designing better over time
6. **Lower barrier to entry**: Non-technical users can create and use professional-grade Factories

## Risks

1. **Recursion risk**: MetaFactory optimizing MetaFactory's recursion needs strict limits
   - Mitigation: Set maximum recursion depth (MetaFactory cannot self-optimize; only humans can optimize it)
2. **Over-optimization**: Sacrificing user experience for metrics
   - Mitigation: Optimization objectives must include human satisfaction metrics, not just automated metrics
3. **Design homogenization**: MetaFactory may tend to generate similar Factories
   - Mitigation: Introduce randomness and exploration mechanisms to encourage diverse design approaches
4. **Template dependency**: Over-reliance on existing templates prevents innovation
   - Mitigation: Regularly perform "template-free design" exercises to accumulate new design patterns

---

## Open Decisions

- [ ] Can MetaFactory optimize itself? (Recommendation: No, requires human intervention)
- [ ] What should the auto-apply threshold for optimization suggestions be set to?
- [ ] How should gradual rollout stages and durations be determined?
- [ ] Initial source for the template library? (Built-in vs user-contributed vs auto-extracted)
- [ ] What model tier should MetaFactory use? (Recommendation: premium, since design quality is critical)
- [ ] How should MetaFactory's own performance be measured? (Success rate of Factories it designs? User satisfaction?)
- [ ] Should multiple MetaFactory instances be supported for collaborative design? (Advanced scenario)
