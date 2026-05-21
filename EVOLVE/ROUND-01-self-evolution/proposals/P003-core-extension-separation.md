# Proposal P003: Core Layer and Extension Layer Separation

> **Status**: Draft
> **Proposer**: AI Assistant
> **Date**: 2026-05-16
> **Related**: VISION.md (Factory identity) / RESEARCH/SYNTHESIS.md (MemRL stability-plasticity decomposition) / brainstorm.md (Core Invariance, Evolution Layering)

---

## Summary

Factory's self-evolution faces a fundamental contradiction: **it needs to continuously improve, but must not lose its identity**. If evolution can modify any part of Factory, it may gradually drift from the original design intent ("evolution drift"); but if too many restrictions exist, Factory cannot effectively adapt.

This proposal draws on MemRL's **stability-plasticity decomposition** concept to strictly divide Factory architecture into two layers:

- **Core Layer**: Defines Factory's identity and non-negotiable behavioral contracts. Evolution **cannot** automatically modify the Core Layer; any Core Layer change must go through human approval.
- **Extension Layer**: Implements the specific strategies and tools for Factory's capabilities. Evolution **can** freely explore and optimize within the Extension Layer.

The key innovation is the **boundary check mechanism** -- automatically detecting whether an evolution touches the Core Layer, and the **tiered approval flow** -- automatically determining what level of approval is needed based on the evolution's impact scope.

---

## Design

### 1. Precise Core Layer Definition

The Core Layer is Factory's "constitution" -- it defines what Factory is, what it does, and what it does not do. Changing the Core Layer is equivalent to redefining Factory itself.

#### 1.1 Core Layer Components

```typescript
/**
 * Core Layer Configuration
 * Defines Factory's identity and behavioral contracts
 */
interface CoreLayerConfig {
  /** Core Layer version */
  version: string;

  /** Core Layer last modified time */
  lastModified: Date;

  /** Core Layer modification history */
  modificationHistory: CoreModification[];

  // --- The following are the five components of the Core Layer ---

  /** 1. Product Type Definition */
  productType: ProductTypeDefinition;

  /** 2. Input/Output Contract */
  ioContract: IOContract;

  /** 3. Human Checkpoint Positions */
  humanCheckpoints: HumanCheckpoint[];

  /** 4. Safety Policy */
  safetyPolicy: SafetyPolicy;

  /** 5. Core Validation Rules (Level 0-1) */
  coreValidationRules: CoreValidationRule[];
}

/**
 * 1. Product Type Definition
 * Defines what type of product this Factory produces
 * This is Factory's most fundamental identity identifier
 */
interface ProductTypeDefinition {
  /** Product type identifier (e.g., "blog_post", "landing_page", "api_design") */
  typeId: string;

  /** Product type name */
  name: string;

  /** Product type description */
  description: string;

  /** Product's essential attributes (immutable attribute list) */
  essentialAttributes: EssentialAttribute[];

  /** Product's quality standards (minimum requirements) */
  qualityStandards: QualityStandard[];

  /** Product's applicability scope */
  applicability: {
    /** Applicable domains */
    domains: string[];

    /** Excluded domains (explicitly excluded) */
    excludedDomains: string[];
  };
}

interface EssentialAttribute {
  /** Attribute name */
  name: string;

  /** Attribute description */
  description: string;

  /** Whether it must exist (if false, means "must not exist") */
  mustExist: boolean;

  /** Validation method */
  validation: 'structural' | 'semantic' | 'manual';
}

interface QualityStandard {
  /** Standard name */
  name: string;

  /** Standard description */
  description: string;

  /** Minimum acceptable value */
  minAcceptable: number;         // 0-1

  /** Ideal value */
  idealValue: number;            // 0-1
}

/**
 * 2. Input/Output Contract
 * Defines what inputs Factory accepts and what outputs it produces
 * This is Factory's interface with the external world
 */
interface IOContract {
  /** Input specification */
  input: InputSpecification;

  /** Output specification */
  output: OutputSpecification;

  /** Side effect declarations (possible external impacts during Factory execution) */
  sideEffects: SideEffectDeclaration[];
}

interface InputSpecification {
  /** Required input fields */
  requiredFields: InputField[];

  /** Optional input fields */
  optionalFields: InputField[];

  /** Input validation rules */
  validationRules: InputValidationRule[];

  /** Input format constraints */
  formatConstraints: {
    maxInputSize: number;        // Maximum input size (characters)
    supportedFormats: string[];  // Supported input formats
    encoding: string;            // Encoding requirements
  };
}

interface InputField {
  /** Field name */
  name: string;

  /** Field type */
  type: 'string' | 'number' | 'boolean' | 'object' | 'array' | 'enum';

  /** Field description */
  description: string;

  /** If enum type, list of allowed values */
  enumValues?: string[];

  /** Whether this is a core field (core fields cannot be removed via evolution) */
  isCore: boolean;
}

interface OutputSpecification {
  /** Output structure definition */
  structure: OutputStructure;

  /** Output format constraints */
  formatConstraints: {
    requiredFormats: string[];   // Required output formats
    encoding: string;
  };

  /** Output quality requirements */
  qualityRequirements: OutputQualityRequirement[];
}

interface OutputStructure {
  /** Required output sections */
  requiredSections: OutputSection[];

  /** Optional output sections */
  optionalSections: OutputSection[];
}

interface OutputSection {
  /** Section identifier */
  id: string;

  /** Section name */
  name: string;

  /** Section description */
  description: string;

  /** Whether this is a core section (core sections cannot be removed via evolution) */
  isCore: boolean;

  /** Content type */
  contentType: 'text' | 'code' | 'data' | 'media' | 'mixed';
}

interface OutputQualityRequirement {
  /** Requirement name */
  name: string;

  /** Description */
  description: string;

  /** Minimum acceptable value */
  minAcceptable: number;         // 0-1
}

interface SideEffectDeclaration {
  /** Side effect type */
  type: 'file_write' | 'api_call' | 'database_write' | 'email_send' | 'notification';

  /** Side effect description */
  description: string;

  /** Whether this side effect is allowed */
  allowed: boolean;

  /** Constraints */
  constraints?: {
    maxFrequency?: number;       // Maximum frequency
    scope?: string[];            // Allowed scope
    requiresApproval?: boolean;  // Whether additional approval is needed
  };
}

/**
 * 3. Human Checkpoint Positions
 * Defines at which points in the Pipeline execution must pause for human confirmation
 * These are human control points over Factory
 */
interface HumanCheckpoint {
  /** Checkpoint identifier */
  id: string;

  /** Checkpoint position (linked to Pipeline step) */
  pipelineStepId: string;

  /** Checkpoint name */
  name: string;

  /** Checkpoint description */
  description: string;

  /** Checkpoint type */
  type: 'approval' | 'review' | 'selection' | 'correction';

  /** Checkpoint timing */
  timing: 'before_step' | 'after_step' | 'on_condition';

  /** Trigger condition (if on_condition) */
  condition?: string;

  /** Whether this is a core checkpoint (core checkpoints cannot be removed via evolution) */
  isCore: boolean;

  /** Timeout behavior (how to handle when human does not respond for a long time) */
  timeoutBehavior: {
    duration: number;            // Timeout duration (milliseconds)
    action: 'wait_indefinitely' | 'skip_with_warning' | 'auto_approve' | 'fail';
  };
}

/**
 * 4. Safety Policy
 * Defines Factory's safety boundaries
 */
interface SafetyPolicy {
  /** Resource limits */
  resourceLimits: {
    maxExecutionTime: number;    // Maximum execution time (milliseconds)
    maxTokenBudget: number;      // Maximum Token budget
    maxApiCallsPerWorkOrder: number;
    maxConcurrentWorkOrders: number;
  };

  /** Data security */
  dataSecurity: {
    /** Allowed data sources */
    allowedDataSources: string[];

    /** Blocked data sources */
    blockedDataSources: string[];

    /** PII handling rules */
    piiHandling: 'block' | 'redact' | 'allow_with_warning';

    /** Data retention policy */
    retentionPolicy: {
      workOrderData: number;     // WorkOrder data retention days
      memoryData: number;        // Memory data retention days
    };
  };

  /** Behavioral constraints */
  behavioralConstraints: BehavioralConstraint[];

  /** Emergency stop conditions */
  emergencyStopConditions: EmergencyStopCondition[];
}

interface BehavioralConstraint {
  /** Constraint identifier */
  id: string;

  /** Constraint description */
  description: string;

  /** Constraint type */
  type: 'prohibition' | 'requirement' | 'limitation';

  /** Constraint expression (executable check logic) */
  expression: string;

  /** Severity when violated */
  severity: 'critical' | 'high' | 'medium';
}

interface EmergencyStopCondition {
  /** Condition description */
  description: string;

  /** Trigger condition */
  trigger: string;

  /** Action after trigger */
  action: 'stop_immediately' | 'pause_and_notify' | 'switch_to_safe_mode';
}

/**
 * 5. Core Validation Rules (Level 0-1)
 * Level 0: Absolutely unviolable rules (violation = product unusable)
 * Level 1: Strongly recommended rules (violation = significant quality degradation)
 */
interface CoreValidationRule {
  /** Rule identifier */
  id: string;

  /** Rule name */
  name: string;

  /** Rule description */
  description: string;

  /** Validation level */
  level: 0 | 1;

  /** Validation scope */
  scope: 'input' | 'output' | 'process' | 'side_effect';

  /** Validation logic (executable check function signature) */
  validationLogic: string;

  /** Handling on violation */
  onViolation: {
    level0: 'reject_immediately';
    level1: 'warn_and_allow' | 'require_human_review';
  }[0 | 1];

  /** Whether this is a core rule (core rules cannot be removed or downgraded via evolution) */
  isCore: boolean;
}
```

#### 1.2 Core Layer Modification Record

```typescript
/**
 * Core Layer Modification Record
 * Every Core Layer change must record complete audit information
 */
interface CoreModification {
  /** Modification identifier */
  id: string;

  /** Modification time */
  timestamp: Date;

  /** Modified by (human ID) */
  modifiedBy: string;

  /** Modified element */
  targetElement: 'productType' | 'ioContract' | 'humanCheckpoints' | 'safetyPolicy' | 'coreValidationRules';

  /** Value before modification (complete snapshot) */
  before: unknown;

  /** Value after modification (complete snapshot) */
  after: unknown;

  /** Modification reason */
  reason: string;

  /** Associated evolution ID (if modification was triggered by evolution) */
  evolutionId?: string;

  /** Approval record */
  approvalRecord: {
    approvedBy: string;
    approvedAt: Date;
    approvalType: 'human_explicit' | 'human_implicit';
    comments?: string;
  };
}
```

---

### 2. Extension Layer Definition

The Extension Layer is Factory's "muscle" -- it implements the capabilities defined by the Core Layer, but the specific implementation strategies can freely evolve.

#### 2.1 Extension Layer Components

```typescript
/**
 * Extension Layer Configuration
 * Defines the specific implementation strategies for Factory's capabilities
 */
interface ExtensionLayerConfig {
  /** Extension Layer version */
  version: string;

  /** Extension Layer last modified time */
  lastModified: Date;

  /** Extension Layer modification history */
  modificationHistory: ExtensionModification[];

  // --- The following are the six components of the Extension Layer ---

  /** 1. Pipeline Implementation */
  pipelineImplementation: PipelineImplementation;

  /** 2. Tool Selection and Configuration */
  toolStrategy: ToolStrategy;

  /** 3. Prompt Templates */
  promptTemplates: PromptTemplate[];

  /** 4. Advanced Validation Rules (Level 2-4) */
  advancedValidationRules: AdvancedValidationRule[];

  /** 5. Memory Strategy */
  memoryStrategy: MemoryStrategy;

  /** 6. Repair Strategies */
  repairStrategies: RepairStrategy[];
}

/**
 * 1. Pipeline Implementation
 * Specific Pipeline steps, ordering, conditional branches, etc.
 * The Core Layer defines human checkpoint positions; the Extension Layer defines
 * the specific logic between checkpoints
 */
interface PipelineImplementation {
  /** Pipeline step list */
  steps: PipelineStep[];

  /** Data flow definitions between steps */
  dataFlow: DataFlowDefinition[];

  /** Conditional branch logic */
  conditionalBranches: ConditionalBranch[];
}

interface PipelineStep {
  /** Step identifier */
  id: string;

  /** Step name */
  name: string;

  /** Step type */
  type: 'generation' | 'validation' | 'transformation' | 'research' | 'review';

  /** Step configuration */
  config: Record<string, unknown>;

  /** Associated prompt template ID */
  promptTemplateId?: string;

  /** Associated tool ID list */
  toolIds: string[];

  /** Step timeout (milliseconds) */
  timeout: number;

  /** Retry policy */
  retryPolicy: {
    maxRetries: number;
    backoffStrategy: 'exponential' | 'linear' | 'fixed';
    backoffBase: number;
  };

  /** Whether this step can be optimized via evolution */
  evolvable: boolean;

  /** Evolution constraints (if evolvable is true) */
  evolutionConstraints?: {
    /** Allowed evolution types */
    allowedEvolutionTypes: ('prompt_tweak' | 'tool_swap' | 'step_reorder' | 'parameter_adjustment')[];
    /** Forbidden evolution types */
    forbiddenEvolutionTypes: string[];
  };
}

interface DataFlowDefinition {
  /** Source step ID */
  fromStepId: string;

  /** Target step ID */
  toStepId: string;

  /** Data fields to pass */
  fields: string[];

  /** Data transformation logic (optional) */
  transform?: string;
}

interface ConditionalBranch {
  /** Branch identifier */
  id: string;

  /** Condition expression */
  condition: string;

  /** Step to take when condition is true */
  trueStepId: string;

  /** Step to take when condition is false */
  falseStepId: string;
}

/**
 * 2. Tool Selection and Configuration
 */
interface ToolStrategy {
  /** Available tools list */
  availableTools: ToolDefinition[];

  /** Tool selection strategy */
  selectionStrategy: {
    /** Default tool mapping (step ID -> tool ID) */
    defaultMapping: Record<string, string>;

    /** Dynamic selection rules */
    dynamicRules: DynamicToolRule[];
  };

  /** Tool invocation parameter optimization */
  parameterOptimizations: Record<string, Record<string, unknown>>;
}

interface ToolDefinition {
  /** Tool identifier */
  id: string;

  /** Tool name */
  name: string;

  /** Tool description */
  description: string;

  /** Tool capabilities */
  capabilities: string[];

  /** Tool configuration */
  config: Record<string, unknown>;

  /** Whether this tool can be replaced via evolution */
  replaceable: boolean;

  /** Replacement constraints */
  replacementConstraints?: {
    /** Required capabilities for replacement tool */
    requiredCapabilities: string[];
    /** Performance requirements for replacement tool */
    performanceRequirements: Record<string, number>;
  };
}

interface DynamicToolRule {
  /** Rule description */
  description: string;

  /** Trigger condition */
  condition: string;

  /** Tool ID to use when condition is met */
  toolId: string;

  /** Rule source (manually set / discovered by evolution) */
  source: 'manual' | 'evolved';
}

/**
 * 3. Prompt Templates
 */
interface PromptTemplate {
  /** Template identifier */
  id: string;

  /** Template name */
  name: string;

  /** Associated Pipeline step ID */
  pipelineStepId: string;

  /** Template content (supports variable interpolation) */
  template: string;

  /** Template variable definitions */
  variables: PromptVariable[];

  /** Template version history */
  versions: PromptTemplateVersion[];

  /** Current version */
  currentVersion: number;

  /** Whether this template can be optimized via evolution */
  evolvable: boolean;
}

interface PromptVariable {
  /** Variable name */
  name: string;

  /** Variable source */
  source: 'workorder_input' | 'previous_step_output' | 'memory' | 'context' | 'static';

  /** Default value */
  defaultValue?: string;

  /** Variable description */
  description: string;
}

interface PromptTemplateVersion {
  /** Version number */
  version: number;

  /** Template content */
  template: string;

  /** Change reason */
  changeReason: string;

  /** Change source */
  source: 'manual' | 'evolution';

  /** Change time */
  timestamp: Date;

  /** Performance comparison (vs previous version) */
  performanceDelta?: {
    successRateDelta: number;
    qualityScoreDelta: number;
    latencyDelta: number;
  };
}

/**
 * 4. Advanced Validation Rules (Level 2-4)
 * Level 2: Quality enhancement rules (violation = minor quality degradation)
 * Level 3: Optimization suggestion rules (violation = room for optimization)
 * Level 4: Style preference rules (violation = does not match specific style)
 */
interface AdvancedValidationRule {
  /** Rule identifier */
  id: string;

  /** Rule name */
  name: string;

  /** Rule description */
  description: string;

  /** Validation level */
  level: 2 | 3 | 4;

  /** Validation scope */
  scope: 'output_quality' | 'style' | 'optimization' | 'consistency';

  /** Validation logic */
  validationLogic: string;

  /** Handling on violation */
  onViolation: {
    level2: 'auto_fix' | 'warn';
    level3: 'suggest_improvement';
    level4: 'note_preference';
  }[2 | 3 | 4];

  /** Whether this rule can be modified via evolution */
  evolvable: boolean;

  /** Rule source */
  source: 'manual' | 'evolved';

  /** Rule effectiveness tracking */
  effectiveness: {
    triggeredCount: number;
    fixSuccessRate: number;
    lastEvaluated: Date;
  };
}

/**
 * 5. Memory Strategy
 */
interface MemoryStrategy {
  /** Memory storage strategy */
  storage: {
    /** Storage format */
    format: 'structured' | 'semi_structured' | 'natural_language';

    /** Indexing strategy */
    indexing: {
      method: 'keyword' | 'semantic' | 'hybrid';
      embeddingModel?: string;
      indexUpdateFrequency: 'realtime' | 'batch' | 'periodic';
    };
  };

  /** Memory retrieval strategy */
  retrieval: {
    /** Retrieval method */
    method: 'keyword' | 'semantic' | 'hybrid';

    /** Retrieval parameters */
    topK: number;                // Return top K results
    similarityThreshold: number;  // Minimum similarity threshold
    rerankingEnabled: boolean;    // Whether reranking is enabled
  };

  /** Memory management strategy */
  management: {
    /** Forgetting strategy */
    forgetting: {
      enabled: boolean;
      strategy: 'ttl' | 'importance_decay' | 'access_frequency';
      params: Record<string, unknown>;
    };

    /** Compression strategy */
    compression: {
      enabled: boolean;
      strategy: 'merge_similar' | 'summarize' | 'extract_rules';
      trigger: 'periodic' | 'on_threshold';
      params: Record<string, unknown>;
    };

    /** Memory importance scoring */
    importanceScoring: {
      factors: ('recency' | 'frequency' | 'outcome' | 'correction_depth')[];
      weights: Record<string, number>;
    };
  };
}

/**
 * 6. Repair Strategies
 */
interface RepairStrategy {
  /** Strategy identifier */
  id: string;

  /** Strategy name */
  name: string;

  /** Strategy description */
  description: string;

  /** Applicable scenarios */
  applicableScenarios: string[];

  /** Repair steps */
  steps: RepairStep[];

  /** Maximum retry count */
  maxRetries: number;

  /** Priority */
  priority: number;

  /** Whether this strategy can be modified via evolution */
  evolvable: boolean;
}

interface RepairStep {
  /** Step description */
  description: string;

  /** Step type */
  type: 'retry' | 'alternative_approach' | 'simplify' | 'escalate' | 'skip';

  /** Step configuration */
  config: Record<string, unknown>;
}

/**
 * Extension Layer Modification Record
 */
interface ExtensionModification {
  /** Modification identifier */
  id: string;

  /** Modification time */
  timestamp: Date;

  /** Modification source */
  source: 'evolution' | 'human' | 'auto_optimization';

  /** Modified element */
  targetElement: keyof ExtensionLayerConfig;

  /** Modification description */
  description: string;

  /** Summary of value before modification */
  beforeSummary: string;

  /** Summary of value after modification */
  afterSummary: string;

  /** Associated evolution ID */
  evolutionId?: string;

  /** Validation result (whether it passed regression testing) */
  validationResult?: {
    passed: boolean;
    score: number;
  };
}
```

---

### 3. Boundary Check Mechanism

Core challenge: How to automatically detect whether an evolution touches the Core Layer? This is not a simple file path check but semantic-level analysis.

#### 3.1 Boundary Check Interface Definition

```typescript
/**
 * Evolution Boundary Check Result
 */
interface EvolutionBoundaryCheck {
  /** Check identifier */
  checkId: string;

  /** Evolution ID being checked */
  evolutionId: string;

  /** Overall verdict */
  verdict: 'extension_only' | 'touches_core' | 'violates_core';

  /** Impact analysis */
  impactAnalysis: ImpactAnalysis;

  /** Touched Core Layer elements (if any) */
  touchedCoreElements: TouchedCoreElement[];

  /** Core Layer violation details (if any) */
  coreViolations: CoreViolation[];

  /** Recommended approval level */
  recommendedApprovalLevel: ApprovalLevel;

  /** Check time */
  checkedAt: Date;

  /** Check confidence */
  confidence: number;           // 0-1
}

/**
 * Impact Analysis
 */
interface ImpactAnalysis {
  /** Directly impacted Extension Layer elements */
  directExtensionImpact: {
    element: string;
    changeType: 'add' | 'modify' | 'remove';
    changeDescription: string;
  }[];

  /** Indirect impact analysis (impacts propagated through dependency chains) */
  indirectImpact: {
    path: string[];              // Impact propagation path
    affectedElement: string;
    impactType: 'behavior_change' | 'quality_change' | 'performance_change';
    severity: 'low' | 'medium' | 'high';
    description: string;
  }[];

  /** Impact assessment on Core Layer contracts */
  contractImpact: {
    inputContractAffected: boolean;
    outputContractAffected: boolean;
    humanCheckpointsAffected: boolean;
    safetyPolicyAffected: boolean;
    coreValidationAffected: boolean;
  };

  /** Risk assessment */
  riskAssessment: {
    overallRisk: 'low' | 'medium' | 'high' | 'critical';
    reversibility: 'easy' | 'moderate' | 'difficult';
    blastRadius: 'single_step' | 'multiple_steps' | 'entire_pipeline';
  };
}

/**
 * Touched Core Layer Elements
 */
interface TouchedCoreElement {
  /** Core Layer element type */
  elementType: keyof CoreLayerConfig;

  /** Specific element identifier */
  elementId: string;

  /** Touch method */
  touchType: 'direct_modification' | 'indirect_effect' | 'semantic_drift';

  /** Touch description */
  description: string;

  /** Severity */
  severity: 'low' | 'medium' | 'high';
}

/**
 * Core Layer Violations
 */
interface CoreViolation {
  /** Violated Core Layer element */
  coreElement: string;

  /** Violation type */
  violationType: 'removal' | 'modification' | 'weakening' | 'bypass';

  /** Violation description */
  description: string;

  /** Violation severity */
  severity: 'critical' | 'high';
}

/**
 * Approval Level
 */
type ApprovalLevel =
  | 'auto'          // Automatic approval (Extension Layer only, low risk)
  | 'notify'        // Notify human (Extension Layer, medium risk, human can veto)
  | 'review'        // Human review (touches Core Layer boundary, needs human confirmation)
  | 'approve'       // Human approval (modifies Core Layer, needs explicit human approval)
  | 'committee';    // Committee approval (major Core Layer changes, needs multi-person approval)
```

#### 3.2 Boundary Checker Core Implementation

```typescript
/**
 * Evolution Boundary Checker
 * Automatically detects whether an evolution touches the Core Layer
 */
class EvolutionBoundaryChecker {
  constructor(
    private coreLayer: CoreLayerConfig,
    private extensionLayer: ExtensionLayerConfig,
    private dependencyGraph: DependencyGraph
  ) {}

  /**
   * Check whether an evolution touches the Core Layer
   *
   * Check strategy consists of three layers:
   * 1. Direct impact analysis: check the evolution's direct modification targets
   * 2. Dependency propagation analysis: check whether modifications affect
   *    the Core Layer through dependency chains
   * 3. Semantic drift analysis: check whether modifications semantically
   *    change behaviors defined by the Core Layer
   */
  async check(evolution: EvolutionDiff): Promise<EvolutionBoundaryCheck> {
    const results: BoundaryCheckResult[] = [];

    // --- Layer 1: Direct Impact Analysis ---
    const directCheck = this.checkDirectImpact(evolution);
    results.push(directCheck);

    // --- Layer 2: Dependency Propagation Analysis ---
    const dependencyCheck = await this.checkDependencyPropagation(evolution);
    results.push(dependencyCheck);

    // --- Layer 3: Semantic Drift Analysis ---
    const semanticCheck = await this.checkSemanticDrift(evolution);
    results.push(semanticCheck);

    // --- Synthesize Results ---
    return this.synthesizeResults(evolution, results);
  }

  /**
   * Layer 1: Direct Impact Analysis
   * Check whether the evolution's direct modification targets belong to the Core Layer
   */
  private checkDirectImpact(evolution: EvolutionDiff): BoundaryCheckResult {
    const touchedCore: TouchedCoreElement[] = [];
    const violations: CoreViolation[] = [];

    for (const change of evolution.changes) {
      const targetPath = change.targetPath; // e.g., "pipeline.steps.3.timeout"

      // Check whether modification target is in Core Layer definitions
      if (this.isCoreLayerPath(targetPath)) {
        const coreElement = this.resolveCoreElement(targetPath);

        touchedCore.push({
          elementType: coreElement.type,
          elementId: coreElement.id,
          touchType: 'direct_modification',
          description: `Evolution directly modifies a Core Layer element: ${targetPath}`,
          severity: 'high',
        });

        // Check whether this constitutes a violation
        if (this.isCoreViolation(change, coreElement)) {
          violations.push({
            coreElement: targetPath,
            violationType: this.classifyViolation(change),
            description: `Core Layer violation: ${change.type} operation on ${targetPath}`,
            severity: 'critical',
          });
        }
      }
    }

    return {
      layer: 'direct',
      touchedCore,
      violations,
      confidence: 1.0, // Direct check has highest confidence
    };
  }

  /**
   * Layer 2: Dependency Propagation Analysis
   * Check whether Extension Layer modifications indirectly affect the Core Layer
   *    through dependency chains
   *
   * Example: modifying a Pipeline step's prompt -> affects step's output format
   * -> if that step's output is part of the Core Layer IO contract -> touches Core Layer
   */
  private async checkDependencyPropagation(
    evolution: EvolutionDiff
  ): Promise<BoundaryCheckResult> {
    const touchedCore: TouchedCoreElement[] = [];
    const violations: CoreViolation[] = [];

    // Only analyze Extension Layer changes
    const extensionChanges = evolution.changes.filter(
      c => !this.isCoreLayerPath(c.targetPath)
    );

    for (const change of extensionChanges) {
      // Find paths from modification target to Core Layer in dependency graph
      const paths = this.dependencyGraph.findPathsToCore(change.targetPath);

      for (const path of paths) {
        const coreTarget = path[path.length - 1]; // Path endpoint is a Core Layer element

        // Evaluate propagation impact strength
        const impactStrength = this.evaluatePropagationImpact(change, path);

        if (impactStrength > 0.7) {
          // High-strength propagation, treated as touching Core Layer
          touchedCore.push({
            elementType: coreTarget.type,
            elementId: coreTarget.id,
            touchType: 'indirect_effect',
            description: `Extension Layer change "${change.targetPath}" affects Core Layer element "${coreTarget.id}" through dependency chain`,
            severity: impactStrength > 0.9 ? 'high' : 'medium',
          });
        }
      }
    }

    return {
      layer: 'dependency',
      touchedCore,
      violations,
      confidence: 0.8, // Dependency analysis has relatively high confidence
    };
  }

  /**
   * Layer 3: Semantic Drift Analysis
   * Check whether modifications semantically change behaviors defined by the Core Layer
   *
   * This is the most complex but most important check.
   * Example: evolution does not modify Core Layer human checkpoint configuration,
   * but modifies steps before the checkpoint, making the context at checkpoint
   * arrival completely different, reducing the checkpoint's effectiveness.
   */
  private async checkSemanticDrift(
    evolution: EvolutionDiff
  ): Promise<BoundaryCheckResult> {
    const touchedCore: TouchedCoreElement[] = [];
    const violations: CoreViolation[] = [];

    // 1. Check semantic integrity of human checkpoints
    const checkpointDrift = await this.checkCheckpointSemanticDrift(evolution);
    touchedCore.push(...checkpointDrift);

    // 2. Check semantic integrity of IO contract
    const contractDrift = await this.checkContractSemanticDrift(evolution);
    touchedCore.push(...contractDrift);

    // 3. Check effectiveness of core validation rules
    const validationDrift = await this.checkValidationSemanticDrift(evolution);
    touchedCore.push(...validationDrift);

    // 4. Check semantic drift of product type definition
    const productDrift = await this.checkProductSemanticDrift(evolution);
    touchedCore.push(...productDrift);

    return {
      layer: 'semantic',
      touchedCore,
      violations,
      confidence: 0.6, // Semantic analysis has lower confidence, needs human review
    };
  }

  /**
   * Check semantic drift of human checkpoints
   *
   * Core idea: if evolution modifies steps before a checkpoint,
   * need to verify the checkpoint can still see sufficient information to make a judgment.
   */
  private async checkCheckpointSemanticDrift(
    evolution: EvolutionDiff
  ): Promise<TouchedCoreElement[]> {
    const results: TouchedCoreElement[] = [];

    for (const checkpoint of this.coreLayer.humanCheckpoints) {
      // Find all steps before the checkpoint
      const precedingSteps = this.getPrecedingSteps(checkpoint.pipelineStepId);

      // Check whether evolution modified these steps
      const modifiedPreceding = evolution.changes.filter(c =>
        precedingSteps.some(s => c.targetPath.includes(s.id))
      );

      if (modifiedPreceding.length > 0) {
        // Analyze whether modifications affect information needed by the checkpoint
        const requiredInfo = this.getCheckpointRequiredInfo(checkpoint);
        const affectedInfo = this.analyzeInfoImpact(modifiedPreceding, requiredInfo);

        if (affectedInfo.coverage > 0.5) {
          results.push({
            elementType: 'humanCheckpoints',
            elementId: checkpoint.id,
            touchType: 'semantic_drift',
            description: `Evolution modified steps before checkpoint "${checkpoint.name}", potentially affecting checkpoint effectiveness. Affected information: ${affectedInfo.details.join(', ')}`,
            severity: affectedInfo.coverage > 0.8 ? 'high' : 'medium',
          });
        }
      }
    }

    return results;
  }

  /**
   * Check semantic drift of IO contract
   *
   * Core idea: even if evolution does not directly modify IO contract definitions,
   * if modifications cause actual output to no longer satisfy contract requirements,
   * that is semantic drift.
   */
  private async checkContractSemanticDrift(
    evolution: EvolutionDiff
  ): Promise<TouchedCoreElement[]> {
    const results: TouchedCoreElement[] = [];

    // Analyze evolution's impact on output structure
    const outputChanges = evolution.changes.filter(c =>
      c.targetPath.startsWith('pipeline.steps') ||
      c.targetPath.startsWith('promptTemplates')
    );

    if (outputChanges.length > 0) {
      // Simulated analysis: do these changes potentially cause output to
      // miss core sections?
      const coreSections = this.coreLayer.ioContract.output.structure.requiredSections
        .filter(s => s.isCore);

      for (const section of coreSections) {
        const dependency = this.dependencyGraph.findProducer(section.id);
        if (dependency && outputChanges.some(c => c.targetPath.includes(dependency))) {
          results.push({
            elementType: 'ioContract',
            elementId: section.id,
            touchType: 'semantic_drift',
            description: `Evolution modified the production step for core output section "${section.name}", potentially causing output to not satisfy contract`,
            severity: 'high',
          });
        }
      }
    }

    return results;
  }

  /**
   * Synthesize three-layer check results into final verdict
   */
  private synthesizeResults(
    evolution: EvolutionDiff,
    results: BoundaryCheckResult[]
  ): EvolutionBoundaryCheck {
    const allTouched = results.flatMap(r => r.touchedCore);
    const allViolations = results.flatMap(r => r.violations);

    // Verdict logic
    let verdict: EvolutionBoundaryCheck['verdict'];
    let approvalLevel: ApprovalLevel;

    if (allViolations.length > 0) {
      // Core Layer violations exist
      verdict = 'violates_core';
      approvalLevel = 'committee';
    } else if (allTouched.some(t => t.touchType === 'direct_modification')) {
      // Direct Core Layer modification
      verdict = 'touches_core';
      approvalLevel = 'approve';
    } else if (allTouched.some(t => t.severity === 'high')) {
      // High-severity indirect impact or semantic drift
      verdict = 'touches_core';
      approvalLevel = 'review';
    } else if (allTouched.length > 0) {
      // Low-severity impact
      verdict = 'touches_core';
      approvalLevel = 'notify';
    } else {
      // Pure Extension Layer change
      verdict = 'extension_only';
      // Determine approval level based on risk level
      const risk = this.assessExtensionRisk(evolution);
      approvalLevel = risk === 'low' ? 'auto' : 'notify';
    }

    // Build impact analysis
    const impactAnalysis = this.buildImpactAnalysis(evolution, allTouched);

    // Calculate overall confidence
    const confidence = this.calculateOverallConfidence(results);

    return {
      checkId: generateId(),
      evolutionId: evolution.id,
      verdict,
      impactAnalysis,
      touchedCoreElements: allTouched,
      coreViolations: allViolations,
      recommendedApprovalLevel: approvalLevel,
      checkedAt: new Date(),
      confidence,
    };
  }

  /**
   * Assess risk level of Extension Layer changes
   */
  private assessExtensionRisk(evolution: EvolutionDiff): 'low' | 'medium' | 'high' {
    let riskScore = 0;

    for (const change of evolution.changes) {
      // Larger modification scope = higher risk
      if (change.type === 'remove') riskScore += 3;
      else if (change.type === 'modify') riskScore += 2;
      else if (change.type === 'add') riskScore += 1;

      // More core the modified element = higher risk
      if (change.targetPath.includes('pipeline.steps')) riskScore += 1;
      if (change.targetPath.includes('safetyPolicy')) riskScore += 3;
    }

    if (riskScore <= 3) return 'low';
    if (riskScore <= 7) return 'medium';
    return 'high';
  }

  // --- Helper Methods ---

  private isCoreLayerPath(path: string): boolean {
    const corePaths = [
      'productType', 'ioContract', 'humanCheckpoints',
      'safetyPolicy', 'coreValidationRules',
    ];
    return corePaths.some(cp => path.startsWith(cp));
  }

  private resolveCoreElement(path: string): { type: keyof CoreLayerConfig; id: string } {
    const [type, ...rest] = path.split('.');
    return {
      type: type as keyof CoreLayerConfig,
      id: rest.join('.'),
    };
  }

  private isCoreViolation(change: EvolutionChange, element: { type: string; id: string }): boolean {
    // Elements marked as isCore in the Core Layer cannot be removed
    if (change.type === 'remove') {
      return this.isMarkedAsCore(element.type, element.id);
    }
    return false;
  }

  private isMarkedAsCore(type: string, id: string): boolean {
    // Check whether there is an isCore: true marker in the Core Layer
    // Specific implementation depends on Core Layer data structure
    return true; // Simplified
  }

  private classifyViolation(change: EvolutionChange): CoreViolation['violationType'] {
    switch (change.type) {
      case 'remove': return 'removal';
      case 'modify': return 'modification';
      default: return 'weakening';
    }
  }

  private evaluatePropagationImpact(change: EvolutionChange, path: DependencyPath): number {
    // Evaluate propagation impact based on path length and dependency strength
    const pathLength = path.length;
    const avgStrength = path.reduce((sum, edge) => sum + edge.strength, 0) / path.length;
    return avgStrength / (1 + pathLength * 0.1);
  }

  private getPrecedingSteps(stepId: string): PipelineStep[] {
    // Get all steps before the specified step
    return [];
  }

  private getCheckpointRequiredInfo(checkpoint: HumanCheckpoint): string[] {
    // Get information types needed by the checkpoint
    return [];
  }

  private analyzeInfoImpact(
    changes: EvolutionChange[],
    requiredInfo: string[]
  ): { coverage: number; details: string[] } {
    // Analyze the impact of changes on required information
    return { coverage: 0, details: [] };
  }

  private buildImpactAnalysis(
    evolution: EvolutionDiff,
    touchedCore: TouchedCoreElement[]
  ): ImpactAnalysis {
    // Build complete impact analysis
    return {
      directExtensionImpact: [],
      indirectImpact: [],
      contractImpact: {
        inputContractAffected: touchedCore.some(t => t.elementType === 'ioContract'),
        outputContractAffected: touchedCore.some(t => t.elementType === 'ioContract'),
        humanCheckpointsAffected: touchedCore.some(t => t.elementType === 'humanCheckpoints'),
        safetyPolicyAffected: touchedCore.some(t => t.elementType === 'safetyPolicy'),
        coreValidationAffected: touchedCore.some(t => t.elementType === 'coreValidationRules'),
      },
      riskAssessment: {
        overallRisk: touchedCore.length === 0 ? 'low' : touchedCore.some(t => t.severity === 'high') ? 'high' : 'medium',
        reversibility: 'easy',
        blastRadius: touchedCore.length === 0 ? 'single_step' : 'multiple_steps',
      },
    };
  }

  private calculateOverallConfidence(results: BoundaryCheckResult[]): number {
    // Weighted average of each layer's check confidence
    const weights = { direct: 0.4, dependency: 0.35, semantic: 0.25 };
    let totalWeight = 0;
    let weightedSum = 0;

    for (const result of results) {
      const weight = weights[result.layer as keyof typeof weights] ?? 0;
      weightedSum += result.confidence * weight;
      totalWeight += weight;
    }

    return totalWeight > 0 ? weightedSum / totalWeight : 0;
  }
}

// --- Auxiliary Types ---

interface BoundaryCheckResult {
  layer: 'direct' | 'dependency' | 'semantic';
  touchedCore: TouchedCoreElement[];
  violations: CoreViolation[];
  confidence: number;
}

interface EvolutionDiff {
  id: string;
  changes: EvolutionChange[];
}

interface EvolutionChange {
  targetPath: string;
  type: 'add' | 'modify' | 'remove';
  before?: unknown;
  after?: unknown;
  description: string;
}

interface DependencyGraph {
  findPathsToCore(fromPath: string): DependencyPath[];
  findProducer(outputId: string): string | null;
}

interface DependencyPath extends Array<{ nodeId: string; strength: number }> {}
```

---

### 4. Evolution Approval Flow

Based on boundary check results, automatically determine the evolution's approval path.

#### 4.1 Approval Flow Interface Definition

```typescript
/**
 * Evolution Approval Flow
 */
interface EvolutionApprovalFlow {
  /** Flow identifier */
  id: string;

  /** Associated evolution ID */
  evolutionId: string;

  /** Associated boundary check result */
  boundaryCheck: EvolutionBoundaryCheck;

  /** Current approval level */
  currentLevel: ApprovalLevel;

  /** Approval status */
  status: ApprovalStatus;

  /** Approval step list */
  steps: ApprovalStep[];

  /** Current step index */
  currentStepIndex: number;

  /** Approval result */
  result?: ApprovalResult;

  /** Creation time */
  createdAt: Date;

  /** Last update time */
  updatedAt: Date;
}

type ApprovalStatus =
  | 'pending'           // Awaiting approval
  | 'in_review'         // Under review
  | 'approved'          // Approved
  | 'rejected'          // Rejected
  | 'conditionally_approved' // Conditionally approved
  | 'expired';          // Approval timed out

/**
 * Approval Step
 */
interface ApprovalStep {
  /** Step identifier */
  id: string;

  /** Step type */
  type: 'auto_check' | 'human_review' | 'human_approve' | 'committee_vote';

  /** Step name */
  name: string;

  /** Step description */
  description: string;

  /** Step status */
  status: 'pending' | 'in_progress' | 'completed' | 'skipped';

  /** Step result */
  result?: StepResult;

  /** Assignee (for human approval) */
  assignee?: string;

  /** Deadline */
  deadline?: Date;

  /** Timeout behavior */
  timeoutBehavior: 'auto_approve' | 'auto_reject' | 'escalate';
}

interface StepResult {
  /** Whether passed */
  passed: boolean;

  /** Result details */
  details: string;

  /** Conditions (if conditionally approved) */
  conditions?: ApprovalCondition[];

  /** Review comments */
  comments?: string;

  /** Completion time */
  completedAt: Date;
}

/**
 * Approval Conditions (constraints attached to conditional approval)
 */
interface ApprovalCondition {
  /** Condition description */
  description: string;

  /** Condition type */
  type: 'scope_limit' | 'duration_limit' | 'monitoring_requirement' | 'rollback_plan';

  /** Condition parameters */
  params: Record<string, unknown>;

  /** Condition verification method */
  verification: 'automatic' | 'manual';
}

/**
 * Approval Result
 */
interface ApprovalResult {
  /** Final decision */
  decision: 'approved' | 'rejected' | 'conditionally_approved';

  /** Attached conditions */
  conditions?: ApprovalCondition[];

  /** Approval summary */
  summary: string;

  /** Completion time */
  completedAt: Date;
}
```

#### 4.2 Approval Flow Orchestrator

```typescript
/**
 * Evolution Approval Flow Orchestrator
 * Automatically builds approval flow based on boundary check results
 */
class ApprovalFlowOrchestrator {
  constructor(
    private boundaryChecker: EvolutionBoundaryChecker,
    private notificationService: NotificationService,
    private config: ApprovalFlowConfig
  ) {}

  /**
   * Create approval flow for an evolution
   */
  async createFlow(evolution: EvolutionDiff): Promise<EvolutionApprovalFlow> {
    // 1. Execute boundary check
    const boundaryCheck = await this.boundaryChecker.check(evolution);

    // 2. Build approval steps based on approval level
    const steps = this.buildApprovalSteps(boundaryCheck);

    // 3. Create approval flow
    const flow: EvolutionApprovalFlow = {
      id: generateId(),
      evolutionId: evolution.id,
      boundaryCheck,
      currentLevel: boundaryCheck.recommendedApprovalLevel,
      status: 'pending',
      steps,
      currentStepIndex: 0,
      createdAt: new Date(),
      updatedAt: new Date(),
    };

    // 4. If auto-approval level, execute immediately
    if (boundaryCheck.recommendedApprovalLevel === 'auto') {
      await this.executeAutoApproval(flow);
    } else {
      // Otherwise notify human
      await this.notifyHuman(flow);
    }

    return flow;
  }

  /**
   * Build approval steps based on approval level
   */
  private buildApprovalSteps(check: EvolutionBoundaryCheck): ApprovalStep[] {
    const steps: ApprovalStep[] = [];

    // All approval flows have a prerequisite step: automatic compliance check
    steps.push({
      id: generateId(),
      type: 'auto_check',
      name: 'Automatic Compliance Check',
      description: 'Check whether evolution meets basic compliance requirements',
      status: 'pending',
      timeoutBehavior: 'auto_reject',
    });

    // Add additional steps based on approval level
    switch (check.recommendedApprovalLevel) {
      case 'auto':
        // Only automatic check, no human involvement needed
        break;

      case 'notify':
        // Notify human, but no need to wait for approval
        steps.push({
          id: generateId(),
          type: 'human_review',
          name: 'Human Notification',
          description: `Evolution "${check.evolutionId}" has been automatically approved; notifying human. Changed scope: ${check.impactAnalysis.directExtensionImpact.map(i => i.element).join(', ')}`,
          status: 'pending',
          timeoutBehavior: 'auto_approve',
          deadline: new Date(Date.now() + 7 * 24 * 60 * 60 * 1000), // 7 days to veto
        });
        break;

      case 'review':
        // Needs human review
        steps.push({
          id: generateId(),
          type: 'human_review',
          name: 'Human Review',
          description: `Evolution touches Core Layer boundary, needs human review. Touched elements: ${check.touchedCoreElements.map(e => e.elementId).join(', ')}`,
          status: 'pending',
          timeoutBehavior: 'escalate',
          deadline: new Date(Date.now() + 3 * 24 * 60 * 60 * 1000), // 3 days
        });
        break;

      case 'approve':
        // Needs explicit human approval
        steps.push({
          id: generateId(),
          type: 'human_approve',
          name: 'Human Approval',
          description: `Evolution modifies Core Layer elements, needs explicit human approval. Changed content: ${check.touchedCoreElements.map(e => e.description).join('; ')}`,
          status: 'pending',
          timeoutBehavior: 'auto_reject',
          deadline: new Date(Date.now() + 5 * 24 * 60 * 60 * 1000), // 5 days
        });
        break;

      case 'committee':
        // Needs committee approval
        steps.push({
          id: generateId(),
          type: 'human_review',
          name: 'Technical Review',
          description: `Evolution has Core Layer violations, conduct technical review first. Violation details: ${check.coreViolations.map(v => v.description).join('; ')}`,
          status: 'pending',
          timeoutBehavior: 'auto_reject',
          deadline: new Date(Date.now() + 2 * 24 * 60 * 60 * 1000), // 2 days
        });
        steps.push({
          id: generateId(),
          type: 'committee_vote',
          name: 'Committee Vote',
          description: 'Based on technical review results, committee conducts final vote',
          status: 'pending',
          timeoutBehavior: 'auto_reject',
          deadline: new Date(Date.now() + 5 * 24 * 60 * 60 * 1000), // 5 days
        });
        break;
    }

    return steps;
  }

  /**
   * Execute automatic approval
   */
  private async executeAutoApproval(flow: EvolutionApprovalFlow): Promise<void> {
    // Execute automatic compliance check
    const complianceCheck = await this.runComplianceCheck(flow);

    if (complianceCheck.passed) {
      flow.status = 'approved';
      flow.result = {
        decision: 'approved',
        summary: 'Automatic compliance check passed, evolution auto-approved',
        completedAt: new Date(),
      };
    } else {
      flow.status = 'rejected';
      flow.result = {
        decision: 'rejected',
        summary: `Automatic compliance check failed: ${complianceCheck.details}`,
        completedAt: new Date(),
      };
    }

    flow.updatedAt = new Date();
  }

  /**
   * Notify human
   */
  private async notifyHuman(flow: EvolutionApprovalFlow): Promise<void> {
    const message = this.formatApprovalNotification(flow);
    await this.notificationService.send({
      channels: ['in_app', 'email'],
      subject: `Evolution approval request [${flow.boundaryCheck.recommendedApprovalLevel}]`,
      body: message,
      priority: this.getNotificationPriority(flow.currentLevel),
      actionUrl: `/evolution/approval/${flow.id}`,
    });
  }

  /**
   * Handle human approval response
   */
  async handleHumanResponse(
    flowId: string,
    response: HumanApprovalResponse
  ): Promise<void> {
    const flow = await this.getFlow(flowId);
    const currentStep = flow.steps[flow.currentStepIndex];

    // Record response
    currentStep.result = {
      passed: response.decision === 'approve' || response.decision === 'conditional_approve',
      details: response.comments ?? '',
      conditions: response.conditions,
      comments: response.comments,
      completedAt: new Date(),
    };
    currentStep.status = 'completed';

    // Handle conditional approval
    if (response.decision === 'conditional_approve' && response.conditions) {
      flow.status = 'conditionally_approved';
      flow.result = {
        decision: 'conditionally_approved',
        conditions: response.conditions,
        summary: `Conditionally approved: ${response.conditions.map(c => c.description).join('; ')}`,
        completedAt: new Date(),
      };
      flow.updatedAt = new Date();
      return;
    }

    if (response.decision === 'reject') {
      flow.status = 'rejected';
      flow.result = {
        decision: 'rejected',
        summary: response.comments ?? 'Human rejected',
        completedAt: new Date(),
      };
      flow.updatedAt = new Date();
      return;
    }

    // Advance to next step
    flow.currentStepIndex++;
    if (flow.currentStepIndex >= flow.steps.length) {
      // All steps completed
      flow.status = 'approved';
      flow.result = {
        decision: 'approved',
        summary: 'All approval steps passed',
        completedAt: new Date(),
      };
    } else {
      // Execute next step
      const nextStep = flow.steps[flow.currentStepIndex];
      nextStep.status = 'in_progress';

      if (nextStep.type === 'auto_check') {
        await this.executeAutoApproval(flow);
      } else {
        await this.notifyHuman(flow);
      }
    }

    flow.updatedAt = new Date();
  }

  // --- Helper Methods ---

  private async runComplianceCheck(flow: EvolutionApprovalFlow): Promise<{ passed: boolean; details: string }> {
    // Execute automatic compliance check
    // Includes: format check, dependency integrity check, resource limit check, etc.
    return { passed: true, details: '' };
  }

  private formatApprovalNotification(flow: EvolutionApprovalFlow): string {
    const check = flow.boundaryCheck;
    return [
      `Evolution ${check.evolutionId} requests approval`,
      ``,
      `Approval level: ${check.recommendedApprovalLevel}`,
      `Boundary check result: ${check.verdict}`,
      `Confidence: ${(check.confidence * 100).toFixed(0)}%`,
      ``,
      check.touchedCoreElements.length > 0
        ? `Touched Core Layer elements: ${check.touchedCoreElements.map(e => `${e.elementId} (${e.touchType})`).join(', ')}`
        : 'No Core Layer elements touched',
      ``,
      check.coreViolations.length > 0
        ? `Core Layer violations: ${check.coreViolations.map(v => v.description).join('; ')}`
        : '',
      ``,
      `Risk level: ${check.impactAnalysis.riskAssessment.overallRisk}`,
      `Impact scope: ${check.impactAnalysis.riskAssessment.blastRadius}`,
    ].filter(Boolean).join('\n');
  }

  private getNotificationPriority(level: ApprovalLevel): 'low' | 'medium' | 'high' | 'urgent' {
    switch (level) {
      case 'auto': return 'low';
      case 'notify': return 'low';
      case 'review': return 'medium';
      case 'approve': return 'high';
      case 'committee': return 'urgent';
    }
  }
}

interface HumanApprovalResponse {
  decision: 'approve' | 'reject' | 'conditional_approve';
  comments?: string;
  conditions?: ApprovalCondition[];
}

interface ApprovalFlowConfig {
  defaultTimeouts: Record<ApprovalLevel, number>;
  notificationChannels: string[];
}
```

---

### 5. Complete Evolution Approval Flow Diagram

```
Evolution proposal generated
      |
      v
  Boundary Checker
      |
      +-- Layer 1: Direct Impact Analysis ---- Does evolution directly modify Core Layer definitions?
      |                              |
      +-- Layer 2: Dependency Propagation Analysis ---- Do Extension Layer modifications affect Core Layer through dependency chains?
      |                              |
      +-- Layer 3: Semantic Drift Analysis ---- Do modifications semantically change core behaviors?
                                     |
                                     v
                              Synthesized Verdict + Confidence
                                     |
                    +----------------+----------------+
                    |                |                |
              extension_only    touches_core    violates_core
                    |                |                |
                    v                v                v
               Risk Assessment   Severity Assessment   Committee Approval
                    |                |                |
              +-----+-----+    +-----+-----+         |
              |     |     |    |     |     |    +-----+-----+
            low  medium  high  low  medium  high  Technical   Committee
              |     |     |    |     |     |    Review      Vote
              v     v     v    v     v     v      |          |
           auto  notify review notify review approve |          |
              |     |     v    v     v     v      v          v
              v     |   Human Human Human  Human  Technical  Committee
           Auto   |   notify review review approve Review    Vote
           execute |    |     |     |     |      |          |
              |   |    |   +-+-+   |     |   +-+-+      +-+-+
              |   |    |   |   |   |     |   |   |      |   |
              |   |    |  pass fail pass  pass pass fail  pass fail
              |   |    |   |   |   |     |   |   |      |   |
              v   v    v   v   v   v     v   v   v      v   v
           +--------------------------------------------------------------+
           |                                                              |
           |  approved    -> Enter sandbox testing (P002)                 |
           |  conditionally -> Enter sandbox testing with conditions      |
           |  rejected    -> Return for revision                          |
           |                                                              |
           +--------------------------------------------------------------+
```

---

## Advantages

1. **Identity protection**: The Core Layer ensures Factory will not "forget who it is" during evolution -- key identity elements such as product type, IO contract, and human checkpoints are strictly protected
2. **Freedom to explore**: The Extension Layer gives evolution ample freedom -- Pipeline implementation, tool selection, prompts, validation rules, memory strategies, and repair strategies can all be freely optimized
3. **Automatic tiering**: The boundary checker automatically determines the approval level for each evolution; low-risk evolutions pass automatically, high-risk evolutions are escalated for approval, reducing human burden
4. **Traceability**: All modifications to both Core and Extension Layers have complete audit records for retrospective analysis
5. **Academic foundation**: Directly corresponds to MemRL's stability-plasticity decomposition, with theoretical support
6. **Progressive relaxation**: As Factory matures, certain Core Layer constraints can be downgraded to Extension Layer after approval, achieving "progressive autonomy"

## Risks

1. **Boundary delineation difficulty**: The boundary between Core and Extension Layers is not black and white; certain elements may fall in a gray area
   - **Mitigation**: Introduce a "soft core" concept -- certain elements default to Core Layer but can be downgraded to Extension Layer after approval
2. **Inaccurate semantic drift detection**: Layer 3 semantic analysis relies on AI judgment and may produce false positives or false negatives
   - **Mitigation**: Semantic analysis has lower confidence (0.6); when triggered, it automatically escalates the approval level for human final judgment
3. **Overly rigid Core Layer**: If the Core Layer is defined too strictly, it may constrain Factory's evolution space
   - **Mitigation**: Periodically review Core Layer definitions (suggested quarterly); remove constraints that are no longer necessary
4. **Approval bottleneck**: If many evolutions touch Core Layer boundaries, human approval may become a bottleneck
   - **Mitigation**: Support "conditional approval" -- humans can quickly approve with conditions rather than outright rejection; support batch approval
5. **Evolution bypass**: Theoretically, multiple small Extension Layer changes may accumulate into substantive Core Layer changes ("boiling frog" effect)
   - **Mitigation**: Periodically conduct "cumulative impact analysis" -- check the cumulative effect of all Extension Layer changes since the last Core Layer review

## Pending Decisions

- [ ] Who should complete the initial Core Layer definition? (Suggested: defined by humans when Factory is created; can be evolutionarily adjusted later)
- [ ] Core Layer review frequency (suggested: quarterly)
- [ ] Downgrade approval flow for "soft core" elements
- [ ] Cumulative impact analysis trigger frequency (suggested: monthly)
- [ ] Committee approval minimum member count and voting rules (e.g., 2 out of 3 agree)
- [ ] Core Layer templates for different Factory types (whether preset templates are needed)
- [ ] Specific method for semantic drift detection (embedding similarity? LLM judgment?)
- [ ] Default behavior after approval timeout (auto-reject vs auto-escalate)
