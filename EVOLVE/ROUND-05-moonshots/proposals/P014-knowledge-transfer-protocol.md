# Proposal P014: Cross-Factory Knowledge Transfer Protocol

> **Status**: Draft
> **Proposed by**: AI Assistant
> **Date**: 2026-05-16
> **Related**: ROUND-05 brainstorm, VISION.md ("A factory designed for one domain can be adapted to a different domain"), RESEARCH/SYNTHESIS.md (Transfer Learning, MemRL), P013 (MetaFactory), P015 (Factory DNA)

---

## Summary

The Cross-Factory Knowledge Transfer Protocol defines a standardized mechanism that enables one Factory's experiences, best practices, and optimization strategies to be abstracted, packaged, and transferred to another Factory, with adaptation to the target domain. Core components include: a Knowledge Abstraction Layer (distilling concrete experiences into domain-agnostic abstract knowledge), a Transfer Format (standardized knowledge packaging format), and a Domain Adapter (adapting abstract knowledge to specific scenarios in the target domain).

This protocol makes it possible for "a coding Factory's experience to help a copywriting Factory," achieving the "cross-domain adaptation" goal in Heya's vision and creating network effects -- the more Factories there are, the richer the shared knowledge, and the higher the starting point for each new Factory.

---

## Design

### Overall Architecture

```
┌──────────────────────────────────────────────────────────────────────┐
│                    Knowledge Transfer Protocol                        │
│                                                                      │
│  ┌──────────────┐     ┌──────────────────┐     ┌────────────────┐   │
│  │ Knowledge    │     │ Transfer         │     │ Domain         │   │
│  │ Extractor    │────>│ Format           │────>│ Adapter        │   │
│  │ (Knowledge   │     │ (Transfer        │     │ (Domain        │   │
│  │  Extraction) │     │  Format)         │     │  Adaptation)   │   │
│  └──────────────┘     └──────────────────┘     └────────────────┘   │
│         ^                      |                       |            │
│         |                      v                       v            │
│  ┌──────┴──────┐     ┌──────────────────┐     ┌────────────────┐   │
│  │ Source      │     │ Knowledge        │     │ Target         │   │
│  │ Factory     │     │ Registry         │     │ Factory        │   │
│  │ (Source     │     │ (Knowledge       │     │ (Target        │   │
│  │  Factory)   │     │  Registry)       │     │  Factory)      │   │
│  └─────────────┘     └──────────────────┘     └────────────────┘   │
│                               |                                     │
│  ┌────────────────────────────┴──────────────────────────────────┐  │
│  │                    Transfer Validator                          │  │
│  │  ┌──────────────┐  ┌──────────────┐  ┌──────────────────────┐  │  │
│  │  │ Compatibility │  │ Safety       │  │ Effectiveness       │  │  │
│  │  │ Checker      │  │ Guard        │  │ Evaluator           │  │  │
│  │  └──────────────┘  └──────────────┘  └──────────────────────┘  │  │
│  └────────────────────────────────────────────────────────────────┘  │
└──────────────────────────────────────────────────────────────────────┘
```

### 1. Knowledge Extractor

Distills a Factory's concrete experiences into domain-agnostic abstract knowledge.

```typescript
// ============ Knowledge Extraction Interfaces ============

interface KnowledgeExtractor {
  /** Extract knowledge from Factory's operation history */
  extractFromHistory(factoryId: string, options?: ExtractionOptions): Promise<KnowledgePackage[]>;

  /** Extract knowledge from a single successful case */
  extractFromCase(workOrderId: string, factoryId: string): Promise<KnowledgePackage | null>;

  /** Extract knowledge from evolution records */
  extractFromEvolution(evolutionId: string, factoryId: string): Promise<KnowledgePackage | null>;

  /** Manually submit knowledge (by human or MetaFactory) */
  submitKnowledge(knowledge: RawKnowledge): Promise<KnowledgePackage>;
}

interface ExtractionOptions {
  timeWindow?: { start: number; end: number };
  minSuccessRate?: number;          // Only extract experiences with success rate above this value
  minOccurrences?: number;          // Minimum occurrence count (filter out one-off events)
  categories?: KnowledgeCategory[];
  maxPackages?: number;
}

type KnowledgeCategory =
  | "pipeline_pattern"              // Pipeline design pattern
  | "verification_strategy"         // Verification strategy
  | "error_handling"                // Error handling approach
  | "prompt_technique"              // Prompt engineering technique
  | "memory_optimization"           // Memory optimization strategy
  | "communication_pattern"         // Communication pattern
  | "tool_usage"                    // Tool usage technique
  | "quality_improvement"           // Quality improvement method
  | "efficiency_optimization"       // Efficiency optimization method
  | "human_interaction"             // Human-AI interaction technique
  | "domain_insight";               // Domain insight

// ============ Knowledge Package Structure ============

interface KnowledgePackage {
  /** Knowledge package ID */
  packageId: string;
  /** Knowledge type */
  category: KnowledgeCategory;
  /** Abstract name */
  name: string;
  /** Abstract description (domain-agnostic) */
  abstractDescription: string;
  /** Concrete description (source domain-specific) */
  concreteDescription: string;
  /** Provenance information */
  provenance: KnowledgeProvenance;
  /** Abstract pattern (core knowledge) */
  pattern: KnowledgePattern;
  /** Applicable domains */
  applicableDomains: DomainApplicability[];
  /** Effectiveness data */
  effectiveness: EffectivenessData;
  /** Migration guide */
  migrationGuide: MigrationGuide;
  /** Version */
  version: string;
  /** Creation time */
  createdAt: number;
  /** Metadata */
  metadata: KnowledgeMetadata;
}

interface KnowledgeProvenance {
  sourceFactoryId: string;
  sourceFactoryDomain: string;
  sourceFactoryName: string;
  extractedFrom: "history" | "case" | "evolution" | "manual";
  sourceWorkOrderIds?: string[];
  contributorType: "ai" | "human" | "meta_factory";
  confidence: number;               // Extraction confidence 0-1
}

interface KnowledgePattern {
  /** Pattern type */
  type: PatternType;
  /** Trigger condition (when this pattern applies) */
  triggerCondition: string;
  /** Core logic (domain-agnostic description) */
  coreLogic: string;
  /** Implementation template (parameterized pseudocode/configuration) */
  implementationTemplate: ImplementationTemplate;
  /** Expected outcome */
  expectedOutcome: string;
  /** Known limitations */
  knownLimitations: string[];
}

type PatternType =
  | "before_after"                  // Before-after pattern (do Y before X)
  | "wrapper"                       // Wrapper pattern (wrap X with Y)
  | "parallel"                      // Parallel pattern (do X and Y simultaneously)
  | "fallback"                      // Fallback pattern (use Y when X fails)
  | "iteration"                     // Iteration pattern (repeat X until condition met)
  | "decomposition"                 // Decomposition pattern (break X into Y and Z)
  | "composition"                   // Composition pattern (combine X and Y into Z)
  | "transformation"                // Transformation pattern (transform X into Y)
  | "filter"                        // Filter pattern (select Y from X)
  | "cache"                         // Cache pattern (cache X's result for later use)
  | "custom";

interface ImplementationTemplate {
  /** Template type */
  templateType: "pipeline_modification" | "prompt_addition" | "config_change" | "code_snippet";
  /** Parameterized template content */
  template: string;
  /** Parameter definitions */
  parameters: TemplateParameter[];
  /** Application position */
  applyPosition: string;
  /** Example (concrete application in source domain) */
  sourceExample: string;
}

interface TemplateParameter {
  name: string;
  description: string;
  type: "string" | "number" | "boolean" | "enum" | "object";
  required: boolean;
  defaultValue?: unknown;
  validation?: string;
}

interface DomainApplicability {
  domain: string;
  applicability: "high" | "medium" | "low" | "unknown";
  adaptationNotes?: string;
  evidence?: string;
}

interface EffectivenessData {
  /** Quantified effect in source domain */
  sourceEffect: {
    metric: string;
    beforeValue: number;
    afterValue: number;
    improvement: number;            // Percentage
    sampleSize: number;
  };
  /** Transfer effects in other domains (if any) */
  transferEffects?: Array<{
    domain: string;
    targetFactoryId: string;
    metric: string;
    improvement: number;
    sampleSize: number;
  }>;
  /** Statistical significance */
  statisticalSignificance: number;  // p-value
}

interface MigrationGuide {
  /** Migration steps */
  steps: string[];
  /** Domain-specific concepts to replace */
  conceptMapping: ConceptMapping[];
  /** Additional dependencies that may be needed */
  additionalDependencies?: string[];
  /** Estimated adaptation time */
  estimatedAdaptationTime: string;
  /** Common pitfalls */
  commonPitfalls: string[];
}

interface ConceptMapping {
  sourceConcept: string;            // Concept in source domain
  targetConcept: string;            // Corresponding concept in target domain
  similarity: number;               // 0-1
  notes?: string;
}

interface KnowledgeMetadata {
  tags: string[];
  qualityScore: number;             // 0-1
  usageCount: number;
  successRate: number;              // Success rate after transfer
  rating: number;                   // User rating 1-5
  lastUsedAt: number;
  expiresAt?: number;
}
```

**Knowledge extraction pseudocode**:

```typescript
class KnowledgeExtractorImpl implements KnowledgeExtractor {
  async extractFromHistory(
    factoryId: string,
    options?: ExtractionOptions
  ): Promise<KnowledgePackage[]> {
    // Step 1: Get Factory's operation history
    const factory = await this.factoryStore.get(factoryId);
    const history = await this.historyStore.getHistory(factoryId, options?.timeWindow);

    // Step 2: Identify repeated successful patterns
    const patterns = await this.patternMiner.mine(history, {
      minSuccessRate: options?.minSuccessRate ?? 0.8,
      minOccurrences: options?.minOccurrences ?? 3,
    });

    // Step 3: Generate knowledge package for each pattern
    const packages: KnowledgePackage[] = [];
    for (const pattern of patterns) {
      // Abstraction: distill domain-specific patterns into domain-agnostic knowledge
      const abstracted = await this.abstractPattern(pattern, factory.domain);

      if (abstracted.confidence < MIN_CONFIDENCE_THRESHOLD) continue;

      packages.push({
        packageId: generateKnowledgeId(),
        category: this.classifyCategory(pattern),
        name: abstracted.name,
        abstractDescription: abstracted.description,
        concreteDescription: pattern.description,
        provenance: {
          sourceFactoryId: factoryId,
          sourceFactoryDomain: factory.domain,
          sourceFactoryName: factory.name,
          extractedFrom: "history",
          sourceWorkOrderIds: pattern.workOrderIds,
          contributorType: "ai",
          confidence: abstracted.confidence,
        },
        pattern: abstracted.pattern,
        applicableDomains: await this.assessDomainApplicability(abstracted),
        effectiveness: {
          sourceEffect: {
            metric: pattern.metric,
            beforeValue: pattern.beforeValue,
            afterValue: pattern.afterValue,
            improvement: pattern.improvement,
            sampleSize: pattern.sampleSize,
          },
          statisticalSignificance: pattern.pValue,
        },
        migrationGuide: await this.generateMigrationGuide(abstracted, factory.domain),
        version: "1.0.0",
        createdAt: Date.now(),
        metadata: {
          tags: abstracted.tags,
          qualityScore: abstracted.confidence,
          usageCount: 0,
          successRate: 0,
          rating: 0,
          lastUsedAt: 0,
        },
      });
    }

    return packages.sort((a, b) => b.metadata.qualityScore - a.metadata.qualityScore);
  }

  private async abstractPattern(
    pattern: RawPattern,
    sourceDomain: string
  ): Promise<AbstractedPattern> {
    // Use LLM to abstract the concrete pattern
    return this.llm.analyze<AbstractedPattern>({
      systemPrompt: KNOWLEDGE_ABSTRACTION_PROMPT,
      userMessage: JSON.stringify({
        sourceDomain,
        patternDescription: pattern.description,
        patternType: pattern.type,
        beforeAfter: { before: pattern.beforeValue, after: pattern.afterValue },
        context: pattern.context,
      }),
      schema: AbstractedPatternSchema,
    });
  }
}
```

### 2. Transfer Format

Standardized knowledge packaging and transmission format.

```typescript
// ============ Transfer Format Definition ============

interface TransferBundle {
  /** Transfer bundle ID */
  bundleId: string;
  /** Version */
  version: string;
  /** Contained knowledge packages */
  knowledgePackages: KnowledgePackage[];
  /** Transfer context */
  context: TransferContext;
  /** Integrity information */
  integrity: IntegrityInfo;
}

interface TransferContext {
  /** Source Factory information */
  source: {
    factoryId: string;
    domain: string;
    name: string;
    capabilities: string[];
    performanceLevel: number;       // 0-1
  };
  /** Target Factory information (if known) */
  target?: {
    factoryId: string;
    domain: string;
    name: string;
    capabilities: string[];
  };
  /** Transfer reason */
  reason: string;
  /** Priority */
  priority: "low" | "medium" | "high";
  /** Requester */
  requestedBy: "ai" | "human" | "meta_factory";
}

interface IntegrityInfo {
  /** Content hash */
  contentHash: string;
  /** Digital signature */
  signature?: string;
  /** Creation time */
  createdAt: number;
  /** Expiration time */
  expiresAt: number;
}

// ============ Knowledge Registry ============

interface KnowledgeRegistry {
  /** Register knowledge package */
  register(package: KnowledgePackage): Promise<string>;
  /** Search knowledge packages */
  search(query: KnowledgeSearchQuery): Promise<KnowledgeSearchResult[]>;
  /** Get knowledge package details */
  get(packageId: string): Promise<KnowledgePackage | null>;
  /** Update transfer effect data */
  reportTransferEffect(packageId: string, effect: TransferEffectReport): Promise<void>;
  /** Get popular knowledge */
  getPopular(options?: { domain?: string; limit?: number }): Promise<KnowledgePackage[]>;
  /** Get recommended knowledge (based on target Factory's needs) */
  getRecommendations(targetFactoryId: string): Promise<Recommendation[]>;
}

interface KnowledgeSearchQuery {
  keywords?: string[];
  category?: KnowledgeCategory;
  sourceDomain?: string;
  targetDomain?: string;
  minQualityScore?: number;
  minSuccessRate?: number;
  minApplicability?: "high" | "medium";
  sortBy?: "quality" | "effectiveness" | "popularity" | "relevance";
  limit?: number;
}

interface KnowledgeSearchResult {
  package: KnowledgePackage;
  relevanceScore: number;
  matchReasons: string[];
}

interface TransferEffectReport {
  targetFactoryId: string;
  targetDomain: string;
  applied: boolean;
  effective: boolean;
  metric: string;
  beforeValue: number;
  afterValue: number;
  adaptationNotes?: string;
}

interface Recommendation {
  package: KnowledgePackage;
  reason: string;
  estimatedBenefit: number;
  adaptationDifficulty: "easy" | "medium" | "hard";
}
```

**Knowledge Registry pseudocode**:

```typescript
class KnowledgeRegistryImpl implements KnowledgeRegistry {
  async search(query: KnowledgeSearchQuery): Promise<KnowledgeSearchResult[]> {
    // Step 1: Basic filtering
    let candidates = await this.store.query({
      category: query.category,
      minQualityScore: query.minQualityScore,
    });

    // Step 2: Keyword matching
    if (query.keywords?.length) {
      const keywordMatches = await this.vectorSearch.search(
        query.keywords.join(" "),
        { filter: { packageId: candidates.map(c => c.packageId) } }
      );
      candidates = candidates.filter(c =>
        keywordMatches.some(m => m.packageId === c.packageId && m.score > 0.5)
      );
    }

    // Step 3: Domain applicability filtering
    if (query.targetDomain) {
      candidates = candidates.filter(pkg =>
        pkg.applicableDomains.some(d =>
          d.domain === query.targetDomain &&
          (d.applicability === "high" || (query.minApplicability === "medium" && d.applicability === "medium"))
        )
      );
    }

    // Step 4: Sorting
    candidates.sort((a, b) => {
      switch (query.sortBy) {
        case "quality": return b.metadata.qualityScore - a.metadata.qualityScore;
        case "effectiveness": return b.effectiveness.sourceEffect.improvement - a.effectiveness.sourceEffect.improvement;
        case "popularity": return b.metadata.usageCount - a.metadata.usageCount;
        default: return 0;
      }
    });

    // Step 5: Generate match reasons
    return candidates.slice(0, query.limit ?? 10).map(pkg => ({
      package: pkg,
      relevanceScore: this.calculateRelevance(pkg, query),
      matchReasons: this.generateMatchReasons(pkg, query),
    }));
  }

  async getRecommendations(targetFactoryId: string): Promise<Recommendation[]> {
    const targetFactory = await this.factoryStore.get(targetFactoryId);

    // Analyze target Factory's weaknesses
    const weaknesses = await this.analyzeWeaknesses(targetFactoryId);

    // Search for knowledge that can address weaknesses
    const recommendations: Recommendation[] = [];
    for (const weakness of weaknesses) {
      const results = await this.search({
        keywords: [weakness.area, "improvement", "optimization"],
        targetDomain: targetFactory.domain,
        minQualityScore: 0.7,
        sortBy: "effectiveness",
        limit: 3,
      });

      for (const result of results) {
        recommendations.push({
          package: result.package,
          reason: `Target Factory performs weakly in "${weakness.area}"; this knowledge package has a ${result.package.effectiveness.sourceEffect.improvement}% improvement record in that area`,
          estimatedBenefit: result.package.effectiveness.sourceEffect.improvement * 0.5, // Conservative estimate
          adaptationDifficulty: this.estimateAdaptationDifficulty(result.package, targetFactory),
        });
      }
    }

    return recommendations
      .sort((a, b) => b.estimatedBenefit - a.estimatedBenefit)
      .slice(0, 10);
  }
}
```

### 3. Domain Adapter

Adapts abstract knowledge to specific scenarios in the target domain.

```typescript
// ============ Domain Adaptation Interfaces ============

interface DomainAdapter {
  /** Assess knowledge package applicability in target domain */
  assessApplicability(
    packageId: string,
    targetFactoryId: string
  ): Promise<ApplicabilityAssessment>;

  /** Adapt knowledge package to target domain */
  adapt(
    packageId: string,
    targetFactoryId: string,
    options?: AdaptationOptions
  ): Promise<AdaptationResult>;

  /** Preview adaptation effects (without actually applying) */
  preview(
    packageId: string,
    targetFactoryId: string
  ): Promise<AdaptationPreview>;
}

interface AdaptationOptions {
  /** Adaptation strategy */
  strategy: "auto" | "conservative" | "aggressive";
  /** Whether to auto-apply (skip human approval) */
  autoApply: boolean;
  /** Custom concept mapping */
  customConceptMapping?: ConceptMapping[];
  /** Adaptation constraints */
  constraints?: AdaptationConstraint[];
}

interface AdaptationConstraint {
  type: "max_changes" | "no_checkpoint_removal" | "no_pipeline_reorder" | "cost_limit";
  value: unknown;
}

interface ApplicabilityAssessment {
  packageId: string;
  targetFactoryId: string;
  overallApplicability: "high" | "medium" | "low" | "not_applicable";
  score: number;                     // 0-1
  factors: ApplicabilityFactor[];
  risks: AdaptationRisk[];
  requiredAdaptations: string[];
  estimatedBenefit: number;
  estimatedEffort: "low" | "medium" | "high";
  recommendation: "recommended" | "consider" | "not_recommended";
}

interface ApplicabilityFactor {
  name: string;
  score: number;                     // 0-1
  weight: number;                    // Weight
  description: string;
}

interface AdaptationRisk {
  type: "negative_transfer" | "compatibility" | "performance" | "quality";
  severity: "low" | "medium" | "high";
  description: string;
  mitigation: string;
}

interface AdaptationResult {
  packageId: string;
  targetFactoryId: string;
  status: "success" | "partial" | "failed";
  adaptations: AppliedAdaptation[];
  validationResults: AdaptationValidation[];
  rollbackAvailable: boolean;
  appliedAt: number;
  nextSteps: string[];
}

interface AppliedAdaptation {
  type: "pipeline_change" | "prompt_change" | "config_change" | "memory_change";
  description: string;
  beforeState: unknown;
  afterState: unknown;
  autoValidated: boolean;
}

interface AdaptationValidation {
  type: "syntax" | "semantic" | "integration" | "performance";
  passed: boolean;
  message: string;
  details?: string;
}

interface AdaptationPreview {
  packageId: string;
  targetFactoryId: string;
  proposedChanges: ProposedChange[];
  estimatedImpact: {
    quality: number;
    speed: number;
    cost: number;
  };
  risks: AdaptationRisk[];
  requiresHumanApproval: boolean;
  approvalPrompt: string;
}

interface ProposedChange {
  component: string;                 // Affected component
  changeType: string;
  description: string;
  diff: string;                      // Change description similar to git diff
  confidence: number;
}
```

**Domain adaptation pseudocode**:

```typescript
class DomainAdapterImpl implements DomainAdapter {
  async assessApplicability(
    packageId: string,
    targetFactoryId: string
  ): Promise<ApplicabilityAssessment> {
    const pkg = await this.registry.get(packageId);
    const targetFactory = await this.factoryStore.get(targetFactoryId);

    if (!pkg || !targetFactory) {
      throw new Error("Knowledge package or target factory not found");
    }

    // Assess applicability across dimensions
    const factors: ApplicabilityFactor[] = [];

    // Factor 1: Domain similarity
    const domainSimilarity = this.calculateDomainSimilarity(
      pkg.provenance.sourceFactoryDomain,
      targetFactory.domain
    );
    factors.push({
      name: "domain_similarity",
      score: domainSimilarity,
      weight: 0.25,
      description: `Similarity between source domain "${pkg.provenance.sourceFactoryDomain}" and target domain "${targetFactory.domain}"`,
    });

    // Factor 2: Capability match
    const capabilityMatch = this.calculateCapabilityMatch(
      pkg.pattern.implementationTemplate,
      targetFactory.capabilities
    );
    factors.push({
      name: "capability_match",
      score: capabilityMatch,
      weight: 0.2,
      description: "Whether the target Factory has the capabilities needed to apply this knowledge",
    });

    // Factor 3: Historical transfer success rate
    const historicalSuccessRate = this.getHistoricalTransferSuccessRate(
      pkg.packageId,
      targetFactory.domain
    );
    factors.push({
      name: "historical_success",
      score: historicalSuccessRate,
      weight: 0.2,
      description: "Historical transfer success rate of this knowledge package in similar domains",
    });

    // Factor 4: Knowledge quality
    factors.push({
      name: "knowledge_quality",
      score: pkg.metadata.qualityScore,
      weight: 0.15,
      description: "Quality score of the knowledge package itself",
    });

    // Factor 5: Source domain effect strength
    const sourceEffectStrength = Math.min(
      pkg.effectiveness.sourceEffect.improvement / 50, // Normalize: 50% improvement = 1.0
      1.0
    );
    factors.push({
      name: "source_effect_strength",
      score: sourceEffectStrength,
      weight: 0.1,
      description: `Produced a ${pkg.effectiveness.sourceEffect.improvement}% improvement in the source domain`,
    });

    // Factor 6: Adaptation complexity (simpler is better)
    const adaptationComplexity = 1 - this.estimateAdaptationComplexity(pkg, targetFactory);
    factors.push({
      name: "adaptation_simplicity",
      score: adaptationComplexity,
      weight: 0.1,
      description: "Complexity of adapting to the target domain",
    });

    // Calculate total score
    const totalScore = factors.reduce((sum, f) => sum + f.score * f.weight, 0);

    // Identify risks
    const risks = await this.identifyRisks(pkg, targetFactory);

    // Generate recommendation
    const recommendation = totalScore > 0.7
      ? "recommended"
      : totalScore > 0.4
        ? "consider"
        : "not_recommended";

    return {
      packageId,
      targetFactoryId,
      overallApplicability: totalScore > 0.7 ? "high" : totalScore > 0.4 ? "medium" : "low",
      score: totalScore,
      factors,
      risks,
      requiredAdaptations: pkg.migrationGuide.steps,
      estimatedBenefit: pkg.effectiveness.sourceEffect.improvement * totalScore * 0.6,
      estimatedEffort: totalScore > 0.7 ? "low" : totalScore > 0.4 ? "medium" : "high",
      recommendation,
    };
  }

  async adapt(
    packageId: string,
    targetFactoryId: string,
    options?: AdaptationOptions
  ): Promise<AdaptationResult> {
    // Step 1: Assess applicability
    const assessment = await this.assessApplicability(packageId, targetFactoryId);
    if (assessment.overallApplicability === "not_applicable") {
      return {
        packageId, targetFactoryId,
        status: "failed",
        adaptations: [],
        validationResults: [{ type: "semantic", passed: false, message: "Knowledge is not applicable to the target Factory" }],
        rollbackAvailable: false,
        appliedAt: Date.now(),
        nextSteps: ["Search for other more applicable knowledge packages"],
      };
    }

    const pkg = await this.registry.get(packageId)!;
    const targetFactory = await this.factoryStore.get(targetFactoryId)!;

    // Step 2: Generate adaptation plan
    const adaptationPlan = await this.generateAdaptationPlan(pkg, targetFactory, assessment);

    // Step 3: Validate in sandbox
    const sandboxResults = await this.validateInSandbox(adaptationPlan, targetFactory);

    // Step 4: If human approval required and auto-apply not set
    if (assessment.requiresHumanApproval && !options?.autoApply) {
      await this.requestHumanApproval(adaptationPlan, assessment);
    }

    // Step 5: Apply adaptation
    const adaptations: AppliedAdaptation[] = [];
    const validationResults: AdaptationValidation[] = [];

    for (const change of adaptationPlan.changes) {
      try {
        const beforeState = await this.getState(targetFactoryId, change.component);
        await this.applyChange(targetFactoryId, change);
        const afterState = await this.getState(targetFactoryId, change.component);

        // Auto-validate
        const validation = await this.autoValidate(change, targetFactoryId);
        validationResults.push(validation);

        adaptations.push({
          type: change.type,
          description: change.description,
          beforeState,
          afterState,
          autoValidated: validation.passed,
        });
      } catch (error) {
        validationResults.push({
          type: "integration",
          passed: false,
          message: `Failed to apply change: ${error.message}`,
        });
      }
    }

    // Step 6: Record transfer effect (to be updated later)
    await this.registry.registerUsage(packageId, targetFactoryId);

    return {
      packageId,
      targetFactoryId,
      status: adaptations.length > 0 ? "success" : "failed",
      adaptations,
      validationResults,
      rollbackAvailable: true,
      appliedAt: Date.now(),
      nextSteps: [
        "Monitor Factory performance changes",
        "If effects are positive, report success to the Knowledge Registry",
        "If effects are negative, execute rollback",
      ],
    };
  }

  private async generateAdaptationPlan(
    pkg: KnowledgePackage,
    targetFactory: FactoryBlueprint,
    assessment: ApplicabilityAssessment
  ): Promise<AdaptationPlan> {
    // Use LLM to generate adaptation plan based on migration guide and target Factory's specifics
    return this.llm.analyze<AdaptationPlan>({
      systemPrompt: DOMAIN_ADAPTATION_PROMPT,
      userMessage: JSON.stringify({
        knowledgePattern: pkg.pattern,
        migrationGuide: pkg.migrationGuide,
        targetFactory: {
          domain: targetFactory.domain,
          pipeline: targetFactory.pipeline,
          capabilities: targetFactory.agents.map(a => a.capabilities),
        },
        applicabilityFactors: assessment.factors,
        risks: assessment.risks,
      }),
      schema: AdaptationPlanSchema,
    });
  }
}
```

### 4. Transfer Validator

Ensures the safety and effectiveness of knowledge transfer.

```typescript
// ============ Transfer Validation Interfaces ============

interface TransferValidator {
  /** Compatibility check */
  checkCompatibility(
    packageId: string,
    targetFactoryId: string
  ): Promise<CompatibilityReport>;

  /** Safety check */
  checkSafety(package: KnowledgePackage, targetFactory: FactoryBlueprint): SafetyReport;

  /** Post-transfer effect evaluation */
  evaluateEffect(
    packageId: string,
    targetFactoryId: string,
    window: TimeWindow
  ): Promise<EffectEvaluation>;
}

interface CompatibilityReport {
  compatible: boolean;
  checks: CompatibilityCheck[];
  requiredModifications: string[];
}

interface CompatibilityCheck {
  aspect: string;
  compatible: boolean;
  details: string;
  fixSuggestion?: string;
}

interface SafetyReport {
  safe: boolean;
  concerns: SafetyConcern[];
  requiredGuards: string[];
}

interface SafetyConcern {
  type: "data_leak" | "behavior_change" | "performance_regression" | "security";
  severity: "low" | "medium" | "high" | "critical";
  description: string;
  guard: string;
}

interface EffectEvaluation {
  packageId: string;
  targetFactoryId: string;
  effective: boolean;
  metrics: {
    qualityDelta: number;
    speedDelta: number;
    costDelta: number;
  };
  statisticalSignificance: boolean;
  recommendation: "keep" | "modify" | "rollback";
  report: string;
}
```

### 5. Complete Transfer Workflow

```
[Source Factory in operation]
  |
  v
[Knowledge Extractor]
  Extract successful patterns from operation history
  |
  v
[Knowledge Abstraction]
  Distill concrete experiences into domain-agnostic abstract knowledge
  |
  v
[Knowledge Registry]
  Register knowledge packages, store in knowledge base
  |
  v
[Target Factory needs improvement]
  |
  v
[Knowledge Registry]
  Search for applicable knowledge packages
  |
  v
[Domain Adapter]
  Assess applicability -> Generate adaptation plan
  |
  v
[Transfer Validator]
  Compatibility check + Safety check
  |
  v
[Sandbox Validation]
  Validate adaptation effects in sandbox
  |
  v
[Human Approval Gate]
  Present adaptation plan -> User approval
  |
  v
[Apply Adaptation]
  Apply adaptation -> Monitor effects
  |
  v
[Effect Evaluation]
  Evaluate transfer effects -> Update knowledge base
  |
  v (feedback loop)
  Success -> Knowledge package's transferEffects updated
  Failure -> Rollback + Record failure reason
```

---

## Advantages

1. **Cross-domain learning**: One Factory's experience can benefit completely unrelated Factories
2. **Network effects**: The more Factories there are, the richer the knowledge base, and the higher the starting point for each new Factory
3. **Standardization**: A unified transfer format ensures knowledge can flow between any Factories
4. **Safe and controllable**: Multi-layer validation (compatibility + safety + sandbox + human approval) prevents negative transfer
5. **Continuous improvement**: The feedback loop of transfer effects makes the knowledge base increasingly accurate
6. **Synergy with MetaFactory**: MetaFactory can leverage the knowledge base to select best practices for new Factories

## Risks

1. **Negative transfer**: Inappropriate knowledge may degrade target Factory performance
   - Mitigation: Strict applicability assessment + sandbox validation + human approval + one-click rollback
2. **Uneven knowledge quality**: Auto-extracted knowledge may be inaccurate
   - Mitigation: Quality scoring + user ratings + usage statistics + periodic review
3. **Privacy leakage**: Source Factory's knowledge may contain sensitive information
   - Mitigation: Remove all domain-specific information during knowledge abstraction; knowledge packages do not contain raw data
4. **Over-reliance on transfer**: Factories may lose the ability to innovate independently
   - Mitigation: Transferred knowledge serves as "suggestions" rather than "mandates," preserving space for Factory autonomous evolution

---

## Open Decisions

- [ ] Knowledge package review mechanism: automatic review vs manual review vs hybrid?
- [ ] Knowledge package intellectual property: who owns the extracted knowledge? The source Factory's user?
- [ ] Cross-tenant knowledge transfer: can Factories from different users/organizations share knowledge?
- [ ] Knowledge package expiration policy: how long before unused knowledge packages should be archived?
- [ ] Transfer effect evaluation window: how long after application to evaluate effects?
- [ ] Knowledge extraction trigger frequency: auto-extract after each evolution vs periodic batch extraction?
- [ ] Should "negative feedback" knowledge be allowed (recording what should not be done)?
