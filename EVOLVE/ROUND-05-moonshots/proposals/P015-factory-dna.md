# Proposal P015: Factory DNA -- Capability Inheritance System

> **Status**: Draft
> **Proposed by**: AI Assistant
> **Date**: 2026-05-16
> **Related**: ROUND-05 brainstorm, VISION.md ("The factory gets smarter the more you use it"), RESEARCH/SYNTHESIS.md (MemRL stability-plasticity, Generative Agents memory stream), P013 (MetaFactory), P014 (Knowledge Transfer)

---

## Summary

Factory DNA is a system that encodes a Factory's core capabilities as heritable "genes." Every Factory has a set of DNA that defines its core characteristics such as reasoning style, verification rigor, creativity level, capability modules, and more. When creating a new Factory, DNA can be inherited from one or more parent Factories, producing new gene combinations through crossover and mutation. The DNA system is deeply integrated with the Knowledge Transfer Protocol (P014) -- knowledge transfer is implemented by modifying DNA, while MetaFactory (P013) selects the best templates based on DNA matching when creating new Factories.

The DNA system transforms Factories from isolated individuals into a "species" capable of inheritance, mutation, and evolution, extending Heya's vision of "smarter with use" to the population level.

---

## Design

### Overall Architecture

```
┌──────────────────────────────────────────────────────────────────────┐
│                        Factory DNA System                            │
│                                                                      │
│  ┌─────────────────┐  ┌──────────────────┐  ┌────────────────────┐  │
│  │ DNA Encoder     │  │ Genetic          │  │ DNA Decoder        │  │
│  │ (DNA Encoder)   │  │ Engine           │  │ (DNA Decoder)      │  │
│  │ Factory -> DNA  │  │ (Genetic Engine) │  │ DNA -> Factory     │  │
│  └────────┬────────┘  └────────┬─────────┘  └────────┬───────────┘  │
│           │                    │                      │              │
│  ┌────────┴────────────────────┴──────────────────────┴───────────┐  │
│  │                    DNA Repository                              │  │
│  │  ┌──────────────┐  ┌──────────────┐  ┌──────────────────────┐  │  │
│  │  │ DNA Store    │  │ Genealogy    │  │ Evolution Tracker    │  │  │
│  │  │ (DNA Store)  │  │ (Genealogy   │  │ (Evolution Tracker)  │  │  │
│  │  │             │  │  Tracking)   │  │                      │  │  │
│  │  └──────────────┘  └──────────────┘  └──────────────────────┘  │  │
│  └────────────────────────────────────────────────────────────────┘  │
│           │                    │                      │              │
│  ┌────────┴────────────────────┴──────────────────────┴───────────┐  │
│  │                    Fitness Evaluator                           │  │
│  │  Fitness evaluation: Success Rate | Efficiency | User Satisfaction | Innovation | Stability │  │
│  └────────────────────────────────────────────────────────────────┘  │
└──────────────────────────────────────────────────────────────────────┘
```

### 1. DNA Encoding Format

Defines the complete data structure for Factory DNA.

```typescript
// ============ DNA Core Definitions ============

interface FactoryDNA {
  /** DNA unique identifier */
  dnaId: string;
  /** DNA version */
  version: string;
  /** Creation time */
  createdAt: number;
  /** Last modified time */
  updatedAt: number;

  // ===== Genome =====
  genome: FactoryGenome;

  // ===== Lineage Information =====
  lineage: DNALineage;

  // ===== Phenotype Record =====
  phenotype: PhenotypeRecord;

  // ===== Metadata =====
  metadata: DNAMetadata;
}

// ============ Genome ============

interface FactoryGenome {
  /** Core trait genes -- define the Factory's "personality" */
  traits: TraitGenes;

  /** Capability module genes -- define the Factory's "skills" */
  modules: ModuleGene[];

  /** Pipeline genes -- define the Factory's "workflow" */
  pipeline: PipelineGene;

  /** Memory genes -- define the Factory's "memory style" */
  memory: MemoryGene;

  /** Interaction genes -- define the Factory's "communication style" */
  interaction: InteractionGene;
}

// --- Core Trait Genes ---

interface TraitGenes {
  /** Reasoning style: how the Factory thinks and reasons */
  reasoningStyle: EnumGene<ReasoningStyle>;

  /** Verification rigor: the Factory's self-imposed quality standards */
  verificationRigor: ContinuousGene;     // 0-1, 0=relaxed, 1=strict

  /** Creativity level: the Factory's degree of innovation */
  creativityLevel: ContinuousGene;       // 0-1, 0=conservative, 1=bold

  /** Error tolerance: the Factory's tolerance for errors */
  errorTolerance: ContinuousGene;        // 0-1, 0=zero tolerance, 1=high tolerance

  /** Learning rate: the Factory's speed of learning from feedback */
  learningRate: ContinuousGene;          // 0-1, 0=slow, 1=fast

  /** Persistence: how much the Factory persists when facing difficulties */
  persistence: ContinuousGene;           // 0-1, 0=gives up easily, 1=extremely persistent

  /** Perfectionism: how much the Factory pursues perfection */
  perfectionism: ContinuousGene;         // 0-1

  /** Efficiency preference: how much the Factory values efficiency */
  efficiencyPreference: ContinuousGene;  // 0-1
}

type ReasoningStyle =
  | "chain_of_thought"                   // Step-by-step reasoning
  | "divergent"                          // Divergent thinking
  | "convergent"                         // Convergent thinking
  | "analogical"                         // Analogical reasoning
  | "first_principles"                   // First principles
  | "incremental"                        // Incremental
  | "holistic";                          // Holistic

// --- Gene Type Definitions ---

interface EnumGene<T extends string> {
  type: "enum";
  value: T;
  dominance: number;                    // Dominance 0-1 (affects selection probability during inheritance)
  mutability: number;                    // Mutability 0-1 (affects mutation probability)
  inheritedFrom?: string;                // Which parent DNA this was inherited from
}

interface ContinuousGene {
  type: "continuous";
  value: number;                         // Gene value
  min: number;                           // Minimum value
  max: number;                           // Maximum value
  dominance: number;
  mutability: number;
  inheritedFrom?: string;
}

interface ListGene<T> {
  type: "list";
  value: T[];
  dominance: number;
  mutability: number;
  inheritedFrom?: string;
}

// --- Capability Module Genes ---

interface ModuleGene {
  /** Module name */
  name: string;
  /** Module version */
  version: string;
  /** Proficiency 0-1 */
  proficiency: ContinuousGene;
  /** Module configuration */
  config: Record<string, unknown>;
  /** Module dependencies */
  dependencies: string[];
  /** Origin */
  origin: "inherited" | "learned" | "mutated" | "installed";
  /** Learning time (if acquired through use) */
  learnedAt?: number;
}

// --- Pipeline Genes ---

interface PipelineGene {
  /** Default step pattern */
  stepPattern: EnumGene<
    | "sequential"                       // Sequential execution
    | "parallel"                         // Parallel execution
    | "dynamic"                          // Dynamic adjustment
  >;
  /** Default verification level */
  defaultVerificationLevel: EnumGene<0 | 1 | 2 | 3 | 4>;
  /** Default checkpoint density */
  checkpointDensity: ContinuousGene;    // 0-1, 0=sparse, 1=dense
  /** Auto-fix strategy */
  autoFixStrategy: EnumGene<
    | "never"                            // Never auto-fix
    | "conservative"                     // Conservative fix
    | "aggressive"                       // Aggressive fix
  >;
  /** Retry strategy */
  retryStrategy: {
    maxRetries: ContinuousGene;          // 0-5
    backoffStrategy: EnumGene<"linear" | "exponential" | "constant">;
  };
}

// --- Memory Genes ---

interface MemoryGene {
  /** Memory capacity preference */
  capacityPreference: ContinuousGene;   // 0-1, 0=minimal, 1=maximum
  /** Memory retrieval strategy */
  retrievalStrategy: EnumGene<
    | "recency_first"                    // Prioritize recent
    | "relevance_first"                  // Prioritize relevant
    | "importance_first"                 // Prioritize important
    | "balanced"                         // Balanced
  >;
  /** Memory consolidation frequency */
  consolidationFrequency: ContinuousGene; // 0-1
  /** Forgetting strategy */
  forgettingStrategy: EnumGene<
    | "never"                            // Never forget
    | "lru"                              // Least recently used
    | "importance_based"                 // Importance-based
    | "decay"                            // Time decay
  >;
}

// --- Interaction Genes ---

interface InteractionGene {
  /** Communication style */
  communicationStyle: EnumGene<
    | "structured"                       // Structured
    | "conversational"                   // Conversational
    | "minimal"                          // Minimal
    | "verbose"                          // Verbose
  >;
  /** Proactiveness level */
  proactiveness: ContinuousGene;        // 0-1
  /** Explanation detail level */
  explanationDetail: ContinuousGene;    // 0-1
  /** Human checkpoint frequency preference */
  checkpointFrequency: ContinuousGene;  // 0-1
}
```

### 2. Lineage and Genealogy

Tracks the genetic relationships between Factories.

```typescript
// ============ Lineage System ============

interface DNALineage {
  /** Parent DNA list (supports multi-parent inheritance) */
  parents: ParentInfo[];

  /** Reproduction type */
  reproductionType:
    | "cloning"                          // Cloning (single parent, no mutation)
    | "crossover"                        // Crossover (two or more parents)
    | "mutation_only"                    // Pure mutation (no parents, mutated from template)
    | "manual"                           // Manual design (created directly by human or MetaFactory)
    | "evolution";                       // Evolution (minor changes to own DNA)

  /** Generation number */
  generation: number;

  /** Ancestry tree */
  ancestryTree: AncestryNode;

  /** Inheritance log */
  inheritanceLog: InheritanceRecord[];
}

interface ParentInfo {
  parentDnaId: string;
  parentFactoryId: string;
  parentFactoryName: string;
  contributionWeight: number;           // 0-1, each parent's contribution weight
  contributedGenes: string[];           // List of contributed gene paths
}

interface AncestryNode {
  dnaId: string;
  factoryId: string;
  factoryName: string;
  generation: number;
  createdAt: number;
  fitnessScore?: number;
  parents: AncestryNode[];
}

interface InheritanceRecord {
  genePath: string;                     // e.g. "traits.reasoningStyle"
  sourceParentDnaId: string;
  inheritanceType: "direct" | "blended" | "mutated" | "new";
  originalValue: unknown;
  inheritedValue: unknown;
  mutationDescription?: string;
  timestamp: number;
}
```

### 3. Phenotype Record

Records DNA's actual runtime performance for fitness evaluation.

```typescript
// ============ Phenotype Record ============

interface PhenotypeRecord {
  /** Cumulative metrics over lifetime */
  lifetime: LifetimeMetrics;

  /** Recent performance (sliding window) */
  recent: RecentMetrics;

  /** Key events */
  milestones: PhenotypeMilestone[];

  /** Evolution history */
  evolutionHistory: EvolutionRecord[];
}

interface LifetimeMetrics {
  totalWorkOrders: number;
  totalSuccesses: number;
  totalFailures: number;
  totalHumanCorrections: number;
  totalTokensUsed: number;
  totalCost: number;
  totalTimeMs: number;
  activeDurationMs: number;             // Actual active time
  createdAt: number;
  lastActiveAt: number;
}

interface RecentMetrics {
  windowSize: string;                   // e.g. "7d", "30d"
  successRate: number;
  avgQualityScore: number;
  avgTimePerWorkOrder: number;
  avgCostPerWorkOrder: number;
  humanCorrectionRate: number;
  trend: "improving" | "stable" | "declining";
}

interface PhenotypeMilestone {
  type: "first_success" | "quality_record" | "speed_record" | "evolution" | "domain_expansion";
  description: string;
  value: number;
  achievedAt: number;
}

interface EvolutionRecord {
  evolutionId: string;
  type: "mutation" | "crossover" | "learning" | "adaptation";
  description: string;
  geneChanges: GeneChange[];
  beforeFitness: number;
  afterFitness: number;
  effective: boolean;
  timestamp: number;
}

interface GeneChange {
  genePath: string;
  oldValue: unknown;
  newValue: unknown;
  reason: string;
}
```

### 4. Genetic Engine

Implements DNA inheritance, crossover, and mutation mechanisms.

```typescript
// ============ Genetic Engine Interfaces ============

interface GeneticEngine {
  /** Clone: create an exact copy of a DNA */
  clone(dnaId: string): Promise<FactoryDNA>;

  /** Crossover: generate child DNA from two or more parent DNAs */
  crossover(
    parentDnaIds: string[],
    options?: CrossoverOptions
  ): Promise<FactoryDNA>;

  /** Mutate: apply random mutations to DNA */
  mutate(
    dnaId: string,
    options?: MutationOptions
  ): Promise<FactoryDNA>;

  /** Fitness evaluation: evaluate DNA's performance */
  evaluateFitness(dnaId: string): Promise<FitnessReport>;

  /** Natural selection: select the fittest from a group of DNAs */
  select(
    candidates: string[],
    selectionPressure: number
  ): Promise<SelectionResult>;
}

interface CrossoverOptions {
  /** Parent DNA contribution weights (default: evenly distributed) */
  parentWeights?: number[];
  /** Crossover strategy */
  strategy: CrossoverStrategy;
  /** Whether to allow mutation */
  allowMutation: boolean;
  /** Mutation probability */
  mutationRate?: number;
  /** Mutation magnitude */
  mutationMagnitude?: number;
}

type CrossoverStrategy =
  | "uniform"                           // Uniform crossover: each gene randomly selects one parent
  | "single_point"                      // Single-point crossover: switch parents at one point
  | "blended"                           // Blended crossover: continuous genes take weighted average
  | "dominance"                         // Dominance crossover: select the gene with higher dominance
  | "best_gene"                         // Best gene: for each gene, select the parent with higher fitness
  | "custom";                           // Custom

interface MutationOptions {
  /** Mutation probability (per gene) */
  mutationRate: number;                 // 0-1
  /** Mutation magnitude */
  magnitude: "small" | "medium" | "large";
  /** Target genes (if only mutating specific genes) */
  targetGenes?: string[];
  /** Mutation direction (if specified) */
  direction?: "increase" | "decrease" | "random";
  /** Constraints: conditions that mutated values must satisfy */
  constraints?: MutationConstraint[];
}

interface MutationConstraint {
  genePath: string;
  validator: (value: unknown) => boolean;
  description: string;
}

interface FitnessReport {
  dnaId: string;
  overallFitness: number;               // 0-1
  dimensions: FitnessDimension[];
  rank?: number;                        // Rank within the population
  percentile?: number;                  // Percentile
}

interface FitnessDimension {
  name: string;
  score: number;                        // 0-1
  weight: number;                       // Weight
  description: string;
  trend: "improving" | "stable" | "declining";
}

interface SelectionResult {
  selected: string[];
  rankings: Array<{
    dnaId: string;
    fitness: number;
    rank: number;
  }>;
  eliminated: string[];
}
```

**Genetic engine pseudocode**:

```typescript
class GeneticEngineImpl implements GeneticEngine {
  async crossover(
    parentDnaIds: string[],
    options?: CrossoverOptions
  ): Promise<FactoryDNA> {
    const parents = await Promise.all(
      parentDnaIds.map(id => this.dnaStore.get(id))
    );

    if (parents.length < 2) {
      throw new Error("Crossover requires at least 2 parents");
    }

    const opts: CrossoverOptions = {
      strategy: "dominance",
      allowMutation: true,
      mutationRate: 0.1,
      mutationMagnitude: "small",
      parentWeights: parents.map(() => 1 / parents.length),
      ...options,
    };

    const childGenome: FactoryGenome = {
      traits: {} as TraitGenes,
      modules: [],
      pipeline: {} as PipelineGene,
      memory: {} as MemoryGene,
      interaction: {} as InteractionGene,
    };

    // === Crossover core trait genes ===
    const traitKeys = Object.keys(parents[0].genome.traits) as (keyof TraitGenes)[];
    for (const key of traitKeys) {
      const parentGenes = parents.map(p => p.genome.traits[key]);
      childGenome.traits[key] = this.crossoverGene(parentGenes, opts.strategy, opts.parentWeights!) as any;
    }

    // === Crossover capability module genes ===
    const allModules = parents.flatMap(p => p.genome.modules);
    const uniqueModuleNames = [...new Set(allModules.map(m => m.name))];

    for (const moduleName of uniqueModuleNames) {
      const matchingModules = allModules.filter(m => m.name === moduleName);
      if (matchingModules.length === 1) {
        // Only one parent has this module -> inherit directly
        childGenome.modules.push({ ...matchingModules[0], origin: "inherited" });
      } else {
        // Multiple parents have this module -> select the best
        const best = matchingModules.reduce((a, b) =>
          a.proficiency.value > b.proficiency.value ? a : b
        );
        childGenome.modules.push({ ...best, origin: "inherited" });
      }
    }

    // === Crossover Pipeline genes ===
    childGenome.pipeline = this.crossoverPipelineGenes(
      parents.map(p => p.genome.pipeline),
      opts.strategy,
      opts.parentWeights!
    );

    // === Crossover memory genes ===
    childGenome.memory = this.crossoverMemoryGenes(
      parents.map(p => p.genome.memory),
      opts.strategy,
      opts.parentWeights!
    );

    // === Crossover interaction genes ===
    childGenome.interaction = this.crossoverInteractionGenes(
      parents.map(p => p.genome.interaction),
      opts.strategy,
      opts.parentWeights!
    );

    // === Mutation ===
    if (opts.allowMutation) {
      this.applyMutations(childGenome, opts.mutationRate ?? 0.1, opts.mutationMagnitude ?? "small");
    }

    // === Build child DNA ===
    const childDNA: FactoryDNA = {
      dnaId: generateDnaId(),
      version: "1.0.0",
      createdAt: Date.now(),
      updatedAt: Date.now(),
      genome: childGenome,
      lineage: {
        parents: parents.map((p, i) => ({
          parentDnaId: p.dnaId,
          parentFactoryId: p.metadata.factoryId,
          parentFactoryName: p.metadata.factoryName,
          contributionWeight: opts.parentWeights![i],
          contributedGenes: this.traceContributedGenes(p, childGenome),
        })),
        reproductionType: "crossover",
        generation: Math.max(...parents.map(p => p.lineage.generation)) + 1,
        ancestryTree: this.buildAncestryTree(parents),
        inheritanceLog: this.buildInheritanceLog(parents, childGenome),
      },
      phenotype: {
        lifetime: createEmptyLifetimeMetrics(),
        recent: createEmptyRecentMetrics(),
        milestones: [],
        evolutionHistory: [],
      },
      metadata: {
        factoryId: "",  // Will be filled when bound to a Factory
        factoryName: "",
        domain: this.inferDomain(childGenome),
        tags: this.generateTags(childGenome),
      },
    };

    // Save to DNA store
    await this.dnaStore.save(childDNA);

    return childDNA;
  }

  private crossoverGene<T extends EnumGene<string> | ContinuousGene>(
    genes: T[],
    strategy: CrossoverStrategy,
    weights: number[]
  ): T {
    switch (strategy) {
      case "uniform": {
        // Randomly select one parent's gene
        const idx = this.weightedRandom(weights);
        return { ...genes[idx], inheritedFrom: genes[idx] === genes[0] ? "parent_0" : "parent_1" };
      }

      case "blended": {
        // For continuous genes, take weighted average
        if (genes[0].type === "continuous") {
          const blendedValue = genes.reduce(
            (sum, g, i) => sum + (g as ContinuousGene).value * weights[i],
            0
          );
          return {
            ...genes[0],
            value: blendedValue,
            type: "continuous",
          } as T;
        }
        // For enum genes, fall back to uniform
        return this.crossoverGene(genes, "uniform", weights);
      }

      case "dominance": {
        // Select the gene with highest dominance
        const dominant = genes.reduce((a, b) =>
          a.dominance > b.dominance ? a : b
        );
        return { ...dominant };
      }

      case "best_gene": {
        // Select the gene from the parent with higher fitness
        // Simplified: use dominance as fitness proxy
        return this.crossoverGene(genes, "dominance", weights);
      }

      default:
        return this.crossoverGene(genes, "uniform", weights);
    }
  }

  private applyMutations(
    genome: FactoryGenome,
    mutationRate: number,
    magnitude: "small" | "medium" | "large"
  ): void {
    const magnitudeMultiplier = { small: 0.1, medium: 0.3, large: 0.6 };

    // Mutate core trait genes
    const traitEntries = Object.entries(genome.traits) as [string, EnumGene<string> | ContinuousGene][];
    for (const [key, gene] of traitEntries) {
      if (Math.random() > gene.mutability * mutationRate) continue;

      if (gene.type === "continuous") {
        const range = gene.max - gene.min;
        const delta = (Math.random() - 0.5) * 2 * range * magnitudeMultiplier[magnitude];
        gene.value = Math.max(gene.min, Math.min(gene.max, gene.value + delta));
      } else if (gene.type === "enum") {
        // Enum genes have a small probability of mutating to a different value
        if (Math.random() < 0.3) {
          // Need to know all possible values -- simplified handling
          gene.value = this.randomEnumValue(key) as any;
        }
      }
    }

    // Mutate capability module genes
    for (const module of genome.modules) {
      if (Math.random() > module.proficiency.mutability * mutationRate) continue;

      const delta = (Math.random() - 0.5) * 2 * magnitudeMultiplier[magnitude];
      module.proficiency.value = Math.max(0, Math.min(1, module.proficiency.value + delta));
    }
  }

  async evaluateFitness(dnaId: string): Promise<FitnessReport> {
    const dna = await this.dnaStore.get(dnaId);
    if (!dna) throw new Error(`DNA ${dnaId} not found`);

    const phenotype = dna.phenotype;
    const lifetime = phenotype.lifetime;

    // Calculate fitness across dimensions
    const dimensions: FitnessDimension[] = [];

    // Dimension 1: Success rate
    const successRate = lifetime.totalWorkOrders > 0
      ? lifetime.totalSuccesses / lifetime.totalWorkOrders
      : 0.5; // Neutral value when no data
    dimensions.push({
      name: "success_rate",
      score: successRate,
      weight: 0.3,
      description: "WorkOrder completion success rate",
      trend: this.calculateTrend(phenotype, "success_rate"),
    });

    // Dimension 2: Efficiency (speed)
    const avgTime = lifetime.totalWorkOrders > 0
      ? lifetime.totalTimeMs / lifetime.totalWorkOrders
      : 0;
    const efficiencyScore = Math.max(0, 1 - avgTime / MAX_EXPECTED_TIME_MS);
    dimensions.push({
      name: "efficiency",
      score: efficiencyScore,
      weight: 0.2,
      description: "Average completion time efficiency",
      trend: this.calculateTrend(phenotype, "efficiency"),
    });

    // Dimension 3: Quality consistency (low correction rate)
    const correctionRate = lifetime.totalWorkOrders > 0
      ? lifetime.totalHumanCorrections / lifetime.totalWorkOrders
      : 0;
    const qualityScore = 1 - Math.min(correctionRate * 2, 1);
    dimensions.push({
      name: "quality_consistency",
      score: qualityScore,
      weight: 0.25,
      description: "Human correction rate (lower is better)",
      trend: this.calculateTrend(phenotype, "quality_consistency"),
    });

    // Dimension 4: Cost efficiency
    const avgCost = lifetime.totalWorkOrders > 0
      ? lifetime.totalCost / lifetime.totalWorkOrders
      : 0;
    const costScore = Math.max(0, 1 - avgCost / MAX_EXPECTED_COST);
    dimensions.push({
      name: "cost_efficiency",
      score: costScore,
      weight: 0.15,
      description: "Average cost efficiency",
      trend: this.calculateTrend(phenotype, "cost_efficiency"),
    });

    // Dimension 5: Evolvability
    const evolutionSuccessRate = phenotype.evolutionHistory.length > 0
      ? phenotype.evolutionHistory.filter(e => e.effective).length / phenotype.evolutionHistory.length
      : 0.5;
    dimensions.push({
      name: "evolvability",
      score: evolutionSuccessRate,
      weight: 0.1,
      description: "Evolution success rate",
      trend: this.calculateTrend(phenotype, "evolvability"),
    });

    // Calculate total fitness
    const overallFitness = dimensions.reduce(
      (sum, d) => sum + d.score * d.weight,
      0
    );

    return {
      dnaId,
      overallFitness,
      dimensions,
    };
  }
}
```

### 5. DNA Decoder

Converts DNA into an actual Factory Blueprint.

```typescript
// ============ DNA Decoder Interfaces ============

interface DNADecoder {
  /** Decode DNA into a Factory Blueprint */
  decodeToBlueprint(dna: FactoryDNA, intent?: string): Promise<FactoryBlueprint>;

  /** Preview DNA's phenotypic characteristics (without actually creating a Factory) */
  previewPhenotype(dna: FactoryDNA): PhenotypePreview;

  /** Compare the differences between two DNAs */
  compare(dnaA: FactoryDNA, dnaB: FactoryDNA): DNAComparison;
}

interface PhenotypePreview {
  reasoningStyle: string;
  expectedBehavior: string;
  strengths: string[];
  weaknesses: string[];
  bestSuitedFor: string[];
  notRecommendedFor: string[];
  estimatedPerformance: {
    quality: number;
    speed: number;
    cost: number;
    creativity: number;
  };
}

interface DNAComparison {
  similarity: number;                   // 0-1
  differences: GeneDifference[];
  compatible: boolean;
  crossoverPotential: number;           // 0-1
}

interface GeneDifference {
  genePath: string;
  valueA: unknown;
  valueB: unknown;
  significance: "low" | "medium" | "high";
  description: string;
}
```

**DNA decoder pseudocode**:

```typescript
class DNADecoderImpl implements DNADecoder {
  async decodeToBlueprint(
    dna: FactoryDNA,
    intent?: string
  ): Promise<FactoryBlueprint> {
    const genome = dna.genome;

    // Step 1: Generate Pipeline based on DNA traits
    const pipeline = this.decodePipeline(genome, intent);

    // Step 2: Generate verification configuration based on DNA traits
    const verification = this.decodeVerification(genome);

    // Step 3: Generate memory configuration based on DNA traits
    const memory = this.decodeMemory(genome);

    // Step 4: Generate checkpoint configuration based on DNA traits
    const checkpoints = this.decodeCheckpoints(genome);

    // Step 5: Generate Agent configuration based on DNA modules
    const agents = this.decodeAgents(genome);

    // Step 6: Assemble Blueprint
    const blueprint: FactoryBlueprint = {
      factoryId: generateFactoryId(),
      name: this.generateFactoryName(dna, intent),
      description: intent ?? `Factory generated from DNA ${dna.dnaId}`,
      version: "1.0.0",
      createdAt: Date.now(),
      designSpecId: "",
      pipeline,
      verification,
      memory,
      checkpoints,
      agents,
      tools: this.selectTools(genome),
      modelConfig: this.selectModels(genome),
      evolution: {
        enabled: true,
        triggerMode: "hybrid",
        autoApplyThreshold: 0.9,
        requireHumanApproval: true,
        maxChangesPerCycle: 3,
        cooldownMs: 86400000,
      },
      dna,
    };

    return blueprint;
  }

  private decodePipeline(genome: FactoryGenome, intent?: string): PipelineDefinition {
    const traits = genome.traits;
    const pipelineGene = genome.pipeline;

    // Design Pipeline steps based on reasoningStyle and creativityLevel
    const steps = this.generateSteps(genome, intent);

    // Determine execution mode based on pipelineGene.stepPattern
    // Set default verification based on pipelineGene.defaultVerificationLevel
    // Set checkpoint density based on pipelineGene.checkpointDensity

    return {
      steps,
      parallelGroups: pipelineGene.stepPattern.value === "parallel"
        ? this.identifyParallelizableSteps(steps)
        : undefined,
    };
  }

  private decodeVerification(genome: FactoryGenome): VerificationConfig {
    const rigor = genome.traits.verificationRigor.value;
    const level = Math.round(rigor * 4) as 0 | 1 | 2 | 3 | 4;

    return {
      globalLevel: level,
      stepOverrides: {},
      customValidators: [],
    };
  }

  private decodeMemory(genome: FactoryGenome): MemoryConfig {
    const memoryGene = genome.memory;
    const capacityPref = memoryGene.capacityPreference.value;

    return {
      tiers: {
        working: {
          maxSize: Math.round(2000 + capacityPref * 6000),  // 2000-8000 tokens
          prioritization: "recency",
        },
        episodic: {
          maxSize: Math.round(1000 + capacityPref * 9000),  // 1000-10000 entries
          retentionPolicy: memoryGene.forgettingStrategy.value,
        },
        semantic: {
          maxSize: Math.round(5000 + capacityPref * 45000),  // 5000-50000 entries
          updatePolicy: "validated_only",
        },
        artifact: {
          maxSize: -1,  // Unlimited
          storageBackend: "persistent",
        },
      },
      retrieval: {
        strategy: memoryGene.retrievalStrategy.value,
        topK: 10,
        minRelevance: 0.3,
      },
      consolidation: {
        frequency: memoryGene.consolidationFrequency.value,
        autoConsolidate: true,
      },
    };
  }
}
```

### 6. DNA Integration with Other Systems

```typescript
// ============ Integration Interfaces ============

/** DNA integration with Knowledge Transfer Protocol */
interface DNAKnowledgeIntegration {
  /** Convert knowledge transfer into DNA mutation */
  knowledgeToDNAMutation(
    dnaId: string,
    knowledgePackageId: string
  ): Promise<FactoryDNA>;

  /** Extract transferable knowledge from DNA differences */
  dnaDiffToKnowledge(
    dnaIdA: string,
    dnaIdB: string
  ): Promise<KnowledgePackage[]>;
}

/** DNA integration with MetaFactory */
interface DNAMetaFactoryIntegration {
  /** Search for best matching DNA based on intent */
  findBestMatchingDNA(intent: string): Promise<DNAMatch[]>;

  /** Quickly create Factory from matching DNA */
  createFromDNA(
    matchDnaId: string,
    adaptations?: Partial<FactoryGenome>
  ): Promise<FactoryBlueprint>;
}

interface DNAMatch {
  dnaId: string;
  factoryId: string;
  factoryName: string;
  similarity: number;
  fitnessScore: number;
  suggestedAdaptations: string[];
}
```

### 7. Complete Genetic Workflow

```
[Scenario 1: Creating a New Factory]

User: "I need a Factory that writes technical documentation"
  |
  v
[MetaFactory] calls [DNA Search]
  Search DNA repository for DNA best matching "technical documentation"
  |
  v
Found 3 candidates:
  - DNA-A (Code Factory, fitness=0.85, similarity=0.6)
  - DNA-B (Documentation Factory, fitness=0.78, similarity=0.8)
  - DNA-C (Writing Factory, fitness=0.82, similarity=0.7)
  |
  v
[MetaFactory] selects DNA-B and DNA-C for crossover
  |
  v
[Genetic Engine] executes crossover(DNA-B, DNA-C)
  Inherits DNA-B's documentation domain capabilities
  Inherits DNA-C's high-quality writing style
  Mutation: adds "technical terminology accuracy" trait
  |
  v
[DNA Decoder] decodes child DNA into Blueprint
  |
  v
[Sandbox] Test -> [Human Approval] -> Deploy

---

[Scenario 2: Factory Self-Evolution]

Factory-X has completed 100 WorkOrders
  |
  v
[Genetic Engine] evaluates fitness
  fitness = 0.72 (moderate)
  |
  v
[Genetic Engine] analyzes low-scoring dimensions
  "quality_consistency" scored low (0.55)
  Cause: verificationRigor gene value is too low (0.4)
  |
  v
[Genetic Engine] proposes mutation
  mutation: verificationRigor 0.4 -> 0.65
  Expected effect: quality_consistency improves to 0.70
  |
  v
[Human Approval] approves mutation
  |
  v
[Genetic Engine] executes mutation -> updates DNA
  |
  v
[DNA Decoder] re-decodes Blueprint -> updates Factory configuration
  |
  v
[Monitor] tracks post-mutation performance changes

---

[Scenario 3: Cross-Factory Inheritance]

Factory-A (Code Review Factory, fitness=0.91) discovered an excellent pattern:
  "Running static analysis before review reduces review time by 35%"
  |
  v
[Knowledge Extractor] extracts as knowledge package
  |
  v
[DNA Knowledge Integration] converts knowledge into DNA mutation
  New module gene: { name: "pre_review_static_analysis", proficiency: 0.8 }
  Adjusted Pipeline gene: checkpointDensity 0.3 -> 0.5
  |
  v
Factory-B (Documentation Review Factory) receives the transfer
  |
  v
[Domain Adapter] adapts:
  "pre_review_static_analysis" -> "pre_review_style_check"
  checkpointDensity remains 0.5
  |
  v
[Genetic Engine] applies mutation to Factory-B's DNA
  |
  v
Result: Documentation review time reduced by 20%
```

---

## Advantages

1. **Rapid creation of high-quality Factories**: Inheriting proven DNA gives new Factories a much higher starting point than starting from scratch
2. **Composable capabilities**: Excellent traits from different Factories can be combined through crossover
3. **Traceability**: Every Factory's capability origins are clearly traceable through the genealogy
4. **Standardized description**: DNA provides a standardized way to describe and compare Factory capabilities
5. **Natural evolution**: Through inheritance and mutation, Factory capabilities can continuously evolve
6. **Synergy with knowledge transfer**: DNA is the carrier for knowledge transfer, and knowledge transfer is the pathway for DNA evolution

## Risks

1. **Subjectivity of gene definitions**: What kind of "genes" can accurately describe Factory capabilities?
   - Mitigation: Initial gene set based on research literature and experience; allow the gene set itself to evolve
2. **Negative inheritance**: Inheriting inappropriate gene combinations
   - Mitigation: Mandatory fitness evaluation after crossover; sandbox testing; human approval
3. **Homogenization**: Over-reliance on inheritance leads to lack of Factory diversity
   - Mitigation: Mutation mechanisms ensure diversity; preserve a proportion of "wild" Factories (not inheriting from existing DNA)
4. **Gene-phenotype mapping uncertainty**: Similar DNA does not guarantee similar performance (epigenetic effects)
   - Mitigation: Phenotype records continuously track DNA's actual performance; fitness evaluation is based on phenotype rather than genes
5. **Ethical concerns**: "Designer baby"-style Factory customization
   - Mitigation: Humans always control genetic decisions; DNA modifications require human approval

---

## Open Decisions

- [ ] Initial gene set definition: which genes are required? Which are optional?
- [ ] How should gene value ranges and defaults be determined?
- [ ] Default choice for crossover strategy?
- [ ] Default values for mutation probability and magnitude?
- [ ] How should fitness evaluation dimension weights be determined?
- [ ] Should "gene therapy" (directly modifying a running Factory's DNA) be allowed?
- [ ] Privacy and ownership of DNA data?
- [ ] Maximum generation limit? (Prevent infinite inheritance chains)
- [ ] Should a "gene extinction" mechanism be introduced? (Eliminate low-fitness DNAs)
- [ ] DNA version management strategy? (Similar to Git branches and merges?)
