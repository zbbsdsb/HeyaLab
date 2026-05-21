# Evolution

> Component lifecycle, fitness, and natural selection.

---

## The Evolutionary Paradigm

Hearth treats components as evolving organisms in an ecosystem. They are born, compete, reproduce, mutate, and die based on their fitness.

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                         EVOLUTIONARY CYCLE                                   │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                              │
│   ┌───────────────┐                                                         │
│   │   BIRTH       │                                                         │
│   │  (creation)   │                                                         │
│   └───────┬───────┘                                                         │
│           │                                                                 │
│           ▼                                                                 │
│   ┌───────────────┐                                                         │
│   │  COMPETITION  │◄─────────────────────────────────────┐                  │
│   │  (execution)  │                                      │                  │
│   └───────┬───────┘                                      │                  │
│           │                                              │                  │
│           ▼                                              │                  │
│   ┌───────────────┐     ┌───────────────┐               │                  │
│   │   FITNESS     │────►│   SELECTION   │               │                  │
│   │  (evaluation) │     │  (pressure)   │               │                  │
│   └───────────────┘     └───────┬───────┘               │                  │
│                                 │                        │                  │
│                    ┌────────────┼────────────┐          │                  │
│                    ▼            ▼            ▼          │                  │
│              ┌─────────┐  ┌─────────┐  ┌─────────┐      │                  │
│              │REPRODUCE│  │ MUTATE  │  │   DIE   │      │                  │
│              │(spawn)  │  │(improve)│  │(remove) │      │                  │
│              └────┬────┘  └────┬────┘  └─────────┘      │                  │
│                   │            │                         │                  │
│                   └────────────┘                         │                  │
│                          │                               │                  │
│                          └───────────────────────────────┘                  │
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## Fitness Function

Fitness determines which components survive and reproduce.

```typescript
interface FitnessFunction {
  // Core metrics
  evaluate(component: Component): FitnessScore;
  
  // Weighted components
  weights: {
    utility: number;           // How useful is this component?
    efficiency: number;        // Resource usage vs output
    reliability: number;       // Success rate
    popularity: number;        // How often used
    satisfaction: number;      // User feedback
    innovation: number;        // Novelty bonus
  };
}

interface FitnessScore {
  overall: number;             // 0.0 - 1.0
  breakdown: {
    utility: number;
    efficiency: number;
    reliability: number;
    popularity: number;
    satisfaction: number;
    innovation: number;
  };
  history: FitnessDataPoint[];
  trend: 'improving' | 'stable' | 'declining';
}
```

### Fitness Metrics

```typescript
// 1. Utility: Does it solve real problems?
function calculateUtility(component: Component): number {
  const metrics = {
    taskCompletionRate: component.metrics.successfulExecutions / component.metrics.totalExecutions,
    outputQuality: average(component.executions.map(e => e.outputQuality)),
    versatility: countUnique(component.executions.map(e => e.taskType)),
    indispensability: countDependentComponents(component)
  };
  
  return weightedAverage(metrics, weights.utility);
}

// 2. Efficiency: Resource usage vs value produced
function calculateEfficiency(component: Component): number {
  const metrics = {
    timeEfficiency: baselineTime / component.metrics.avgExecutionTime,
    memoryEfficiency: baselineMemory / component.metrics.avgMemoryUsage,
    costEfficiency: baselineCost / component.metrics.avgCost,
    throughput: component.metrics.executionsPerMinute
  };
  
  return weightedAverage(metrics, weights.efficiency);
}

// 3. Reliability: Consistency and robustness
function calculateReliability(component: Component): number {
  const metrics = {
    successRate: component.metrics.successfulExecutions / component.metrics.totalExecutions,
    errorRecoveryRate: component.metrics.recoveredErrors / component.metrics.totalErrors,
    consistency: 1 - standardDeviation(component.executions.map(e => e.duration)),
    uptime: component.metrics.uptime / component.metrics.totalTime
  };
  
  return weightedAverage(metrics, weights.reliability);
}

// 4. Popularity: Usage frequency
function calculatePopularity(component: Component): number {
  const metrics = {
    callFrequency: component.metrics.callsPerDay,
    uniqueCallers: countUnique(component.executions.map(e => e.caller)),
    retention: component.metrics.repeatedUsers / component.metrics.uniqueUsers,
    growth: (currentCalls - previousCalls) / previousCalls
  };
  
  return weightedAverage(metrics, weights.popularity);
}

// 5. Satisfaction: User feedback
function calculateSatisfaction(component: Component): number {
  const metrics = {
    explicitRating: average(component.feedback.map(f => f.rating)),
    implicitSatisfaction: calculateImplicitSatisfaction(component),
    recommendationRate: component.feedback.filter(f => f.wouldRecommend).length / component.feedback.length,
    complaintRate: 1 - (component.complaints / component.executions)
  };
  
  return weightedAverage(metrics, weights.satisfaction);
}

// 6. Innovation: Novelty and creativity
function calculateInnovation(component: Component): number {
  const metrics = {
    uniqueness: 1 - similarityToNearestNeighbor(component),
    breakthroughPotential: component.metrics.unexpectedUses,
    crossDomainApplication: countDomains(component.executions.map(e => e.domain)),
    inspiration: countComponentsInspiredBy(component)
  };
  
  return weightedAverage(metrics, weights.innovation);
}
```

---

## Selection Mechanisms

### 1. Resource-Based Selection

Components compete for limited resources.

```typescript
interface ResourceSelection {
  // Resources are allocated based on fitness
  allocateResources(components: Component[], available: Resources): Allocation;
  
  // Low-fitness components get fewer resources
  calculateAllocation(component: Component, totalFitness: number): ResourceShare;
}

// Example: CPU time allocation
function allocateCpuTime(components: Component[], totalCpu: number): Map<ComponentId, number> {
  const totalFitness = sum(components.map(c => c.fitness.overall));
  
  return new Map(components.map(c => [
    c.id,
    (c.fitness.overall / totalFitness) * totalCpu
  ]));
}
```

### 2. User-Based Selection

Users implicitly select components through usage.

```typescript
interface UserSelection {
  // Components used more often are "selected"
  trackUsage(component: Component): void;
  
  // Explicit feedback
  recordFeedback(component: Component, feedback: UserFeedback): void;
  
  // Deprecation
  deprecate(component: Component): void;
}
```

### 3. Task-Based Selection

Components are selected based on task fit.

```typescript
interface TaskSelection {
  // Find best component for task
  selectForTask(task: Task, candidates: Component[]): Component;
  
  // Calculate task fit
  calculateFit(component: Component, task: Task): number;
}

function calculateTaskFit(component: Component, task: Task): number {
  const signatureMatch = similarity(component.signature, task.requirements);
  const historicalSuccess = successRateOnSimilarTasks(component, task);
  const resourceFit = canHandleResources(component, task.resources);
  
  return weightedAverage([signatureMatch, historicalSuccess, resourceFit]);
}
```

---

## Reproduction

High-fitness components reproduce to spread their genes.

```typescript
interface Reproduction {
  // Create offspring
  reproduce(parent: Component): Component[];
  
  // Crossover: Combine two parents
  crossover(parents: [Component, Component]): Component[];
  
  // Clone with variation
  cloneWithVariation(parent: Component): Component;
}

// Reproduction strategies
const reproductionStrategies = {
  // 1. Direct Copy
  directCopy: (parent: Component): Component => ({
    ...parent,
    id: generateId(),
    parent: parent.id,
    version: '1.0.0',
    fitness: { overall: 0, breakdown: {}, history: [], trend: 'stable' },
    createdAt: new Date()
  }),
  
  // 2. Specialization
  specialize: (parent: Component, context: Context): Component => ({
    ...parent,
    id: generateId(),
    parent: parent.id,
    signature: {
      ...parent.signature,
      name: `${parent.signature.name} (${context.domain})`,
      description: `${parent.signature.description} - specialized for ${context.domain}`
    },
    // Narrower scope, potentially higher fitness in specific domain
  }),
  
  // 3. Generalization
  generalize: (parent: Component): Component => ({
    ...parent,
    id: generateId(),
    parent: parent.id,
    signature: {
      ...parent.signature,
      name: `${parent.signature.name} (Universal)`,
      description: `${parent.signature.description} - generalized version`,
      inputs: abstractInputs(parent.signature.inputs),
      outputs: abstractOutputs(parent.signature.outputs)
    }
  })
};
```

---

## Mutation

Components mutate to explore the fitness landscape.

```typescript
interface Mutation {
  // Apply random changes
  mutate(component: Component, rate: number): Component;
  
  // Guided mutation based on feedback
  guidedMutate(component: Component, feedback: Feedback[]): Component;
}

type MutationType = 
  | 'signature'      // Change interface
  | 'implementation' // Change code/prompt
  | 'parameters'     // Change default values
  | 'dependencies'   // Change dependencies
  | 'metadata';      // Change description, examples

const mutationOperators = {
  // 1. Signature Mutation
  mutateSignature: (sig: ComponentSignature): ComponentSignature => {
    const mutations = [
      // Add optional input
      (s) => ({
        ...s,
        inputs: [...s.inputs, generateOptionalInput()]
      }),
      // Make required input optional
      (s) => ({
        ...s,
        inputs: s.inputs.map(i => 
          i.required && Math.random() < 0.5 
            ? { ...i, required: false, default: generateDefault(i.type) }
            : i
        )
      }),
      // Add output
      (s) => ({
        ...s,
        outputs: [...s.outputs, generateOutput()]
      })
    ];
    
    return randomChoice(mutations)(sig);
  },
  
  // 2. Implementation Mutation
  mutateImplementation: (impl: Implementation): Implementation => {
    if (impl.type === 'code') {
      return {
        ...impl,
        code: applyCodeMutation(impl.code)
      };
    } else if (impl.type === 'prompt') {
      return {
        ...impl,
        template: applyPromptMutation(impl.template),
        temperature: clamp(impl.temperature + randomDelta(), 0, 1)
      };
    }
    return impl;
  },
  
  // 3. Parameter Mutation
  mutateParameters: (sig: ComponentSignature): ComponentSignature => ({
    ...sig,
    inputs: sig.inputs.map(i => 
      i.default !== undefined
        ? { ...i, default: mutateValue(i.default, i.type) }
        : i
    )
  })
};
```

---

## Death and Deprecation

Low-fitness components are removed from the ecosystem.

```typescript
interface Death {
  // Mark for deprecation
  deprecate(component: Component, reason: string): void;
  
  // Migrate users to alternatives
  migrateUsers(component: Component, alternatives: Component[]): void;
  
  // Archive and remove
  archive(component: Component): void;
}

// Deprecation criteria
const deprecationCriteria = {
  // 1. Low fitness
  lowFitness: (c: Component) => c.fitness.overall < 0.2,
  
  // 2. Unused
  unused: (c: Component) => 
    c.metrics.lastUsed < Date.now() - 30 * 24 * 60 * 60 * 1000, // 30 days
  
  // 3. Superseded
  superseded: (c: Component, alternatives: Component[]) => 
    alternatives.some(a => 
      isCompatible(a, c) && a.fitness.overall > c.fitness.overall + 0.3
    ),
  
  // 4. Violates boundaries
  boundaryViolation: (c: Component) => 
    c.violations.length > 5
};

// Death process
async function processDeath(component: Component): Promise<void> {
  // 1. Mark as deprecated
  component.status = 'deprecated';
  component.deprecatedAt = new Date();
  
  // 2. Notify dependent components
  const dependents = findDependents(component);
  for (const dependent of dependents) {
    await notify(dependent, `${component.id} is deprecated`);
  }
  
  // 3. Suggest alternatives
  const alternatives = findAlternatives(component);
  
  // 4. Grace period (30 days)
  await sleep(30 * 24 * 60 * 60 * 1000);
  
  // 5. Archive
  await archiveComponent(component);
  
  // 6. Remove from active registry
  registry.remove(component.id);
}
```

---

## Lineage Tracking

Every component has a family tree.

```typescript
interface Lineage {
  componentId: ComponentId;
  
  // Ancestry
  parent?: ComponentId;
  children: ComponentId[];
  ancestors: ComponentId[];    // All parents, grandparents, etc.
  descendants: ComponentId[];   // All children, grandchildren, etc.
  
  // Evolution history
  generation: number;          // How many generations from root
  branch: string;              // Named branch (e.g., "fast", "accurate")
  
  // Genetic information
  genes: Gene[];               // Identifiable traits
  mutations: Mutation[];       // Changes from parent
}

interface Gene {
  name: string;
  value: unknown;
  inheritedFrom?: ComponentId;
  fitness: number;
}

// Lineage visualization
function visualizeLineage(component: Component): string {
  /*
  PlotGenerator v1.0.0 (genesis)
    ├── PlotGenerator v1.1.0 (fast branch)
    │     └── PlotGenerator v1.2.0
    └── PlotGenerator v2.0.0 (accurate branch)
          ├── PlotGenerator v2.1.0
          │     └── PlotGenerator v2.1.1
          └── CharacterDrivenPlotGenerator v1.0.0 (specialization)
  */
}
```

---

## Evolution Strategies

### 1. Exploitation vs Exploration

Balance improving known good components vs discovering new ones.

```typescript
interface EvolutionStrategy {
  // Exploitation: Improve high-fitness components
  exploit(components: Component[]): Component[] {
    return components
      .filter(c => c.fitness.overall > 0.7)
      .map(c => mutate(c, rate = 0.1));  // Small mutations
  }
  
  // Exploration: Try radically new approaches
  explore(components: Component[]): Component[] {
    return components
      .filter(c => c.fitness.overall < 0.5)
      .map(c => mutate(c, rate = 0.5));  // Large mutations
  }
  
  // Crossover: Combine successful traits
  crossover(population: Component[]): Component[] {
    const pairs = selectPairs(population);
    return pairs.flatMap(([a, b]) => createOffspring(a, b));
  }
}
```

### 2. Adaptive Mutation Rate

Mutation rate adjusts based on progress.

```typescript
function calculateMutationRate(component: Component): number {
  const baseRate = 0.1;
  
  // Increase if stuck (no improvement)
  if (component.fitness.trend === 'declining') {
    return baseRate * 2;
  }
  
  // Decrease if improving
  if (component.fitness.trend === 'improving') {
    return baseRate * 0.5;
  }
  
  // Increase for low-fitness components
  if (component.fitness.overall < 0.3) {
    return baseRate * 3;
  }
  
  return baseRate;
}
```

### 3. Niching

Maintain diversity to avoid local optima.

```typescript
interface Niching {
  // Identify niches (distinct domains)
  identifyNiches(components: Component[]): Niche[];
  
  // Ensure representation in each niche
  maintainDiversity(population: Component[], niches: Niche[]): Component[];
}

function maintainDiversity(
  population: Component[], 
  targetNiches: number
): Component[] {
  const clusters = clusterBySimilarity(population);
  
  // If too few clusters, encourage exploration
  if (clusters.length < targetNiches) {
    return [...population, ...generateDiverseOffspring(population)];
  }
  
  // If too many clusters, merge similar ones
  if (clusters.length > targetNiches) {
    return mergeSimilarClusters(clusters);
  }
  
  return population;
}
```

---

## Evolution Metrics

```typescript
interface EvolutionMetrics {
  // Population metrics
  populationSize: number;
  diversity: number;              // Genetic diversity
  averageFitness: number;
  fitnessVariance: number;
  
  // Evolution rate
  birthsPerDay: number;
  deathsPerDay: number;
  mutationsPerDay: number;
  
  // Selection pressure
  selectionIntensity: number;     // How strongly fitness affects survival
  survivalRate: number;           // % of components that survive
  reproductionRate: number;       // Average offspring per component
  
  // Progress metrics
  fitnessImprovementRate: number;
  bestFitness: number;
  worstFitness: number;
  
  // Lineage metrics
  maxGenerations: number;
  mostSuccessfulLineage: string;
  averageLifespan: number;
}
```

---

## Evolution Dashboard

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                      EVOLUTION DASHBOARD                                     │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                              │
│  Population: 156 components    Births Today: 12    Deaths Today: 8          │
│                                                                              │
│  Fitness Distribution:                                                       │
│  ┌─────────────────────────────────────────────────────────────────────┐    │
│  │ 0.0-0.2: ██ (12)  0.2-0.4: ████ (24)  0.4-0.6: ███████ (42)         │    │
│  │ 0.6-0.8: █████████ (54)  0.8-1.0: ██████ (24)                       │    │
│  └─────────────────────────────────────────────────────────────────────┘    │
│                                                                              │
│  Top Lineages:                                                               │
│  1. PlotGenerator (gen 5) - avg fitness: 0.87                               │
│  2. CharacterDesigner (gen 4) - avg fitness: 0.82                           │
│  3. DialogueGenerator (gen 3) - avg fitness: 0.79                           │
│                                                                              │
│  Recent Evolution Events:                                                    │
│  • PlotGenerator v2.1.0 spawned 3 children (fast branch)                    │
│  • CharacterDesigner v1.5.2 died (fitness 0.15)                             │
│  • New hybrid: CharacterDrivenPlotGenerator (crossover)                     │
│  • StyleAnalyzer mutated (temperature ↑ 0.2)                                │
│                                                                              │
│  Selection Pressure: Moderate                                                │
│  Diversity: Healthy (0.73)                                                   │
│  Trend: Improving (↑ 0.05 this week)                                         │
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## Best Practices

### For System Designers

1. **Define Fitness Carefully**: Fitness shapes what evolves
2. **Maintain Diversity**: Don't let the population converge too quickly
3. **Balance Pressure**: Too strong → premature convergence; Too weak → no progress
4. **Track Lineages**: Understand how components evolve
5. **Archive History**: Learn from past evolution

### For Users

1. **Provide Feedback**: Your ratings drive evolution
2. **Try New Components**: Help discover what works
3. **Report Issues**: Helps identify low-fitness components
4. **Name Patterns**: Help the system recognize value
5. **Be Patient**: Good evolution takes time

---

## Summary

| Aspect | Traditional Software | Evolutionary Software |
|--------|---------------------|----------------------|
| **Updates** | Version releases | Continuous evolution |
| **Improvement** | Manual refactoring | Automatic mutation |
| **Diversity** | Single version | Multiple variants |
| **Selection** | User choice | Fitness-based |
| **Adaptation** | Slow | Real-time |
| **Discovery** | Planned | Emergent |

**Key Insight**: Evolution turns software from a static product into a living system that improves itself through use.

---

*Last Updated: 2026-05-20*
*Status: Evolution System Defined*
