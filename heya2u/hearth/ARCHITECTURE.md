# Hearth Architecture

> Core technical architecture for the creation space where agents invent components and capabilities emerge.

---

## System Overview

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                              HEARTH ECOSYSTEM                                │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                              │
│  ┌─────────────┐    ┌─────────────┐    ┌─────────────┐                      │
│  │    User     │◄──►│     Hey     │◄──►│   Agents    │                      │
│  └─────────────┘    └─────────────┘    └──────┬──────┘                      │
│                                                │                             │
│                                                ▼                             │
│  ┌─────────────────────────────────────────────────────────────────────┐    │
│  │                         CREATION SPACE                               │    │
│  │  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌─────────────────────┐  │    │
│  │  │ Boundaries│  │ Resources│  │  State   │  │ Component Registry  │  │    │
│  │  │  (Rules)  │  │ (Limits) │  │ (Graph)  │  │   (Evolution Log)   │  │    │
│  │  └──────────┘  └──────────┘  └──────────┘  └─────────────────────┘  │    │
│  │                                                                      │    │
│  │  ┌─────────────────────────────────────────────────────────────┐    │    │
│  │  │              COMPONENT ECOSYSTEM (Dynamic)                   │    │    │
│  │  │  ┌─────────┐  ┌─────────┐  ┌─────────┐  ┌─────────┐         │    │    │
│  │  │  │Component│◄─┤Component│◄─┤Component│◄─┤Component│ ...     │    │    │
│  │  │  │    A    │─►│    B    │─►│    C    │─►│    D    │         │    │    │
│  │  │  └─────────┘  └─────────┘  └─────────┘  └─────────┘         │    │    │
│  │  │                                                              │    │    │
│  │  │  Interactions: data_flow, control_flow, synergy, inhibition  │    │    │
│  │  └─────────────────────────────────────────────────────────────┘    │    │
│  └─────────────────────────────────────────────────────────────────────┘    │
│                                                                              │
│  ┌─────────────────────────────────────────────────────────────────────┐    │
│  │                         EMERGENCE LAYER                              │    │
│  │  Pattern Recognition → Fitness Evaluation → Natural Selection       │    │
│  └─────────────────────────────────────────────────────────────────────┘    │
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## Core Subsystems

### 1. Boundary Engine

**Purpose**: Define and enforce what is possible within Hearth.

```typescript
interface BoundaryEngine {
  // Boundary definitions
  boundaries: Map<BoundaryId, Boundary>;
  
  // Validation
  validate(component: ComponentDraft): ValidationResult;
  validate(interaction: Interaction): ValidationResult;
  validate(resourceRequest: ResourceRequest): ValidationResult;
  
  // Dynamic boundary updates
  addBoundary(boundary: Boundary): void;
  removeBoundary(id: BoundaryId): void;
  updateBoundary(id: BoundaryId, patch: BoundaryPatch): void;
}

interface ValidationResult {
  allowed: boolean;
  violations: Violation[];
  suggestions: string[];
}
```

**Boundary Types**:
- **Hard Boundaries**: Absolute constraints (safety, security)
- **Soft Boundaries**: Guidelines with override capability (performance, style)
- **Dynamic Boundaries**: Context-dependent constraints (time of day, project phase)

---

### 2. Resource Manager

**Purpose**: Allocate and monitor resources for components.

```typescript
interface ResourceManager {
  // Resource pools
  compute: ComputePool;
  memory: MemoryPool;
  storage: StoragePool;
  network: NetworkPool;
  
  // Allocation
  allocate(request: ResourceRequest): ResourceAllocation;
  release(allocation: ResourceAllocation): void;
  
  // Monitoring
  getUsage(componentId: ComponentId): ResourceUsage;
  getAvailable(): ResourceAvailability;
  
  // Pressure handling
  applyPressure(): void;  // Triggered when resources are scarce
}

interface ResourceRequest {
  componentId: ComponentId;
  compute: { cpu: number; duration: number };
  memory: { min: number; max: number };
  storage: { temp: number; persistent: number };
  priority: number;
}
```

**Resource Pressure Handling**:
1. **Normal**: All components run at full capacity
2. **Elevated**: Lower-priority components throttled
3. **Critical**: Only essential components run; fitness evaluation accelerated
4. **Emergency**: Emergency component preservation protocol

---

### 3. State Graph

**Purpose**: Shared memory structured as a dynamic graph.

```typescript
interface StateGraph {
  // Graph structure
  nodes: Map<NodeId, StateNode>;
  edges: Map<EdgeId, StateEdge>;
  
  // Access patterns
  query(pattern: GraphQuery): QueryResult;
  mutate(mutation: GraphMutation): MutationResult;
  subscribe(subscription: GraphSubscription): SubscriptionHandle;
  
  // Persistence
  checkpoint(): GraphSnapshot;
  restore(snapshot: GraphSnapshot): void;
  
  // Semantic layer
  index(): SemanticIndex;
}

interface StateNode {
  id: NodeId;
  type: NodeType;
  data: unknown;
  metadata: {
    createdBy: AgentId;
    createdAt: Date;
    lastModified: Date;
    accessCount: number;
  };
  edges: EdgeId[];
}

interface StateEdge {
  id: EdgeId;
  from: NodeId;
  to: NodeId;
  type: EdgeType;
  weight: number;
  metadata: EdgeMetadata;
}
```

**Graph Layers** (inspired by M-FLOW):

```
Layer 4: Experiences (Concrete interactions)
    ↓
Layer 3: Network (Relationship web)
    ↓
Layer 2: Edges (Connections)
    ↓
Layer 1: Anchors (Core concepts)
```

---

### 4. Component Registry

**Purpose**: Track all components, their relationships, and evolution.

```typescript
interface ComponentRegistry {
  // Component storage
  components: Map<ComponentId, Component>;
  
  // Evolution tracking
  lineage: Map<ComponentId, Lineage>;
  fitness: Map<ComponentId, FitnessRecord>;
  
  // Discovery
  findBySignature(signature: Partial<ComponentSignature>): Component[];
  findByFitness(minFitness: number): Component[];
  findRelated(componentId: ComponentId): Component[];
  
  // Registration
  register(component: Component): RegistrationResult;
  unregister(componentId: ComponentId): void;
  update(componentId: ComponentId, patch: ComponentPatch): void;
}

interface Lineage {
  componentId: ComponentId;
  parent?: ComponentId;
  children: ComponentId[];
  ancestors: ComponentId[];
  descendants: ComponentId[];
  generation: number;
}

interface FitnessRecord {
  componentId: ComponentId;
  currentFitness: number;
  history: FitnessDataPoint[];
  metrics: {
    usageCount: number;
    successRate: number;
    avgExecutionTime: number;
    userSatisfaction: number;
  };
}
```

---

### 5. Interaction Engine

**Purpose**: Facilitate and monitor component interactions.

```typescript
interface InteractionEngine {
  // Interaction types
  dataFlow: DataFlowHandler;
  controlFlow: ControlFlowHandler;
  synergy: SynergyHandler;
  inhibition: InhibitionHandler;
  
  // Execution
  execute(interaction: Interaction): Promise<InteractionResult>;
  
  // Discovery
  discoverSynergies(components: ComponentId[]): PotentialSynergy[];
  detectConflicts(components: ComponentId[]): Conflict[];
  
  // Optimization
  optimizePath(from: ComponentId, to: ComponentId): OptimizedPath;
}

interface Interaction {
  id: InteractionId;
  type: InteractionType;
  from: ComponentId;
  to: ComponentId;
  payload: unknown;
  context: InteractionContext;
}

interface InteractionResult {
  success: boolean;
  output: unknown;
  metrics: {
    latency: number;
    resourceUsage: ResourceUsage;
    sideEffects: SideEffect[];
  };
}
```

---

### 6. Emergence Detector

**Purpose**: Recognize and evaluate emergent patterns.

```typescript
interface EmergenceDetector {
  // Pattern detection
  detectPatterns(): EmergentPattern[];
  
  // Pattern evaluation
  evaluateUtility(pattern: EmergentPattern): number;
  evaluateStability(pattern: EmergentPattern): number;
  
  // Pattern management
  promote(pattern: EmergentPattern): void;  // Make first-class
  demote(pattern: EmergentPattern): void;   // Deprecate
  
  // Learning
  learnFromPattern(pattern: EmergentPattern): LearnedRule;
}

interface EmergentPattern {
  id: PatternId;
  components: ComponentId[];
  interactions: InteractionId[];
  structure: GraphStructure;
  
  metrics: {
    utility: number;
    stability: number;
    persistence: number;
    novelty: number;
  };
  
  metadata: {
    firstObserved: Date;
    lastObserved: Date;
    observationCount: number;
  };
}
```

---

### 7. Evolution Controller

**Purpose**: Drive component evolution through natural selection.

```typescript
interface EvolutionController {
  // Selection
  selectForReproduction(): Component[];
  selectForTermination(): Component[];
  
  // Variation
  mutate(component: Component): Component;
  crossover(parents: [Component, Component]): Component[];
  
  // Evaluation
  evaluateFitness(component: Component): number;
  
  // Lifecycle
  birth(component: Component): void;
  death(componentId: ComponentId): void;
  
  // Pressure application
  applySelectionPressure(pressure: SelectionPressure): void;
}

interface SelectionPressure {
  type: 'resource' | 'performance' | 'user_preference' | 'task_requirement';
  intensity: number;
  duration: number;
}
```

---

## Data Flow Architecture

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                              DATA FLOW                                       │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                              │
│  User Intent                                                                  │
│      │                                                                        │
│      ▼                                                                        │
│  ┌─────────────────────────────────────────────────────────────────────┐     │
│  │                         INTENT ANALYSIS                              │     │
│  │  Parse → Decompose → Identify Required Capabilities                  │     │
│  └─────────────────────────────────────────────────────────────────────┘     │
│      │                                                                        │
│      ▼                                                                        │
│  ┌─────────────────────────────────────────────────────────────────────┐     │
│  │                      SPACE INITIALIZATION                            │     │
│  │  Load Boundaries → Allocate Resources → Initialize State Graph       │     │
│  └─────────────────────────────────────────────────────────────────────┘     │
│      │                                                                        │
│      ▼                                                                        │
│  ┌─────────────────────────────────────────────────────────────────────┐     │
│  │                     COMPONENT INVENTION (Agent)                      │     │
│  │  Analyze Need → Design Signature → Generate Implementation           │     │
│  │       │                                                              │     │
│  │       ▼                                                              │     │
│  │  ┌─────────────────┐                                                 │     │
│  │  │ Boundary Check  │──► Reject / Modify / Accept                     │     │
│  │  └─────────────────┘                                                 │     │
│  │       │                                                              │     │
│  │       ▼                                                              │     │
│  │  Register Component → Initialize State Nodes → Ready for Interaction │     │
│  └─────────────────────────────────────────────────────────────────────┘     │
│      │                                                                        │
│      ▼                                                                        │
│  ┌─────────────────────────────────────────────────────────────────────┐     │
│  │                     EMERGENT EXECUTION                               │     │
│  │                                                                      │     │
│  │   ┌─────────┐     ┌─────────┐     ┌─────────┐                       │     │
│  │   │Component│────►│Component│────►│Component│ ...                   │     │
│  │   │    A    │◄────│    B    │◄────│    C    │                       │     │
│  │   └─────────┘     └─────────┘     └─────────┘                       │     │
│  │        │               │               │                            │     │
│  │        └───────────────┼───────────────┘                            │     │
│  │                        ▼                                            │     │
│  │              ┌─────────────────┐                                    │     │
│  │              │  State Graph    │                                    │     │
│  │              │ (Shared Memory) │                                    │     │
│  │              └─────────────────┘                                    │     │
│  │                                                                      │     │
│  └─────────────────────────────────────────────────────────────────────┘     │
│      │                                                                        │
│      ▼                                                                        │
│  ┌─────────────────────────────────────────────────────────────────────┐     │
│  │                    PATTERN DETECTION                                 │     │
│  │  Monitor Interactions → Detect Patterns → Evaluate Utility           │     │
│  │       │                                                              │     │
│  │       ▼                                                              │     │
│  │  ┌─────────────────┐                                                 │     │
│  │  │ Pattern Quality │──► Promote / Ignore / Deprecate                 │     │
│  │  └─────────────────┘                                                 │     │
│  └─────────────────────────────────────────────────────────────────────┘     │
│      │                                                                        │
│      ▼                                                                        │
│  ┌─────────────────────────────────────────────────────────────────────┐     │
│  │                    EVOLUTION CYCLE                                   │     │
│  │  Fitness Evaluation → Selection → Variation → Integration            │     │
│  └─────────────────────────────────────────────────────────────────────┘     │
│      │                                                                        │
│      ▼                                                                        │
│  Artifact Output                                                              │
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## Component Lifecycle

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                         COMPONENT LIFECYCLE                                  │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                              │
│   CONCEPTION                                                                 │
│      │                                                                       │
│      ▼                                                                       │
│   ┌─────────────────────────────────────────────────────────────────────┐   │
│   │  Agent identifies need                                              │   │
│   │  ↓                                                                  │   │
│   │  Designs signature (name, inputs, outputs, side effects)            │   │
│   │  ↓                                                                  │   │
│   │  Generates implementation (code + prompt + hybrid)                  │   │
│   └─────────────────────────────────────────────────────────────────────┘   │
│      │                                                                       │
│      ▼                                                                       │
│   VALIDATION                                                                 │
│      │                                                                       │
│      ▼                                                                       │
│   ┌─────────────────────────────────────────────────────────────────────┐   │
│   │  Boundary Check: Does it violate any constraints?                   │   │
│   │  ↓                                                                  │   │
│   │  Signature Validation: Are inputs/outputs well-defined?             │   │
│   │  ↓                                                                  │   │
│   │  Resource Check: Can it run within limits?                          │   │
│   │  ↓                                                                  │   │
│   │  [PASS] → REGISTRATION  /  [FAIL] → REVISION / REJECTION            │   │
│   └─────────────────────────────────────────────────────────────────────┘   │
│      │                                                                       │
│      ▼                                                                       │
│   REGISTRATION                                                               │
│      │                                                                       │
│      ▼                                                                       │
│   ┌─────────────────────────────────────────────────────────────────────┐   │
│   │  Assign ID and version (v1.0.0)                                     │   │
│   │  ↓                                                                  │   │
│   │  Initialize fitness tracking                                        │   │
│   │  ↓                                                                  │   │
│   │  Create state graph nodes                                           │   │
│   │  ↓                                                                  │   │
│   │  Announce to ecosystem (other components can now interact)          │   │
│   └─────────────────────────────────────────────────────────────────────┘   │
│      │                                                                       │
│      ▼                                                                       │
│   OPERATION ◄───────────────────────────────────────────────────────────┐   │
│      │                                                                    │   │
│      ▼                                                                    │   │
│   ┌─────────────────────────────────────────────────────────────────────┐ │   │
│   │  Execute when called by other components or user                    │ │   │
│   │  ↓                                                                  │ │   │
│   │  Update state graph                                                 │ │   │
│   │  ↓                                                                  │ │   │
│   │  Record metrics (execution time, success/failure, resource use)     │ │   │
│   │  ↓                                                                  │ │   │
│   │  Update fitness score                                               │ │   │
│   └─────────────────────────────────────────────────────────────────────┘ │   │
│      │                                                                    │   │
│      ├───► INTERACTIONS ───► Learn from other components                  │   │
│      │                                                                    │   │
│      ├───► MUTATION ───────► Create improved version (v1.0.1, v1.1.0)    │   │
│      │                                                                    │   │
│      └───► REPRODUCTION ───► Spawn child components with variations      │   │
│                                                                            │   │
│      ┌────────────────────────────────────────────────────────────────┐   │   │
│      │ EVOLUTION TRIGGERS                                             │   │   │
│      │                                                                │   │   │
│      │ High Fitness    → Reproduce (create children)                  │   │   │
│      │ Medium Fitness  → Mutate (self-improve)                        │   │   │
│      │ Low Fitness     → Deprecate (mark for removal)                 │   │   │
│      │ Zero Fitness    → Death (remove from ecosystem)                │   │   │
│      └────────────────────────────────────────────────────────────────┘   │   │
│                              │                                             │   │
│                              └─────────────────────────────────────────────┘   │
│                                                                              │
│   DEPRECATION / DEATH                                                        │
│      │                                                                       │
│      ▼                                                                       │
│   ┌─────────────────────────────────────────────────────────────────────┐   │
│   │  Mark as deprecated (no new interactions)                           │   │
│   │  ↓                                                                  │   │
│   │  Migrate state to successor components                              │   │
│   │  ↓                                                                  │   │
│   │  Archive evolution history                                          │   │
│   │  ↓                                                                  │   │
│   │  Remove from active registry                                        │   │
│   └─────────────────────────────────────────────────────────────────────┘   │
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## Event System

Components communicate through events:

```typescript
interface HearthEvent {
  id: EventId;
  type: EventType;
  timestamp: Date;
  source: ComponentId | 'system' | 'user' | 'hey';
  payload: unknown;
  metadata: EventMetadata;
}

type EventType = 
  // Component lifecycle
  | 'component.conceived'
  | 'component.validated'
  | 'component.registered'
  | 'component.executed'
  | 'component.mutated'
  | 'component.deprecated'
  | 'component.died'
  
  // Interactions
  | 'interaction.started'
  | 'interaction.completed'
  | 'interaction.failed'
  | 'interaction.discovered'
  
  // Emergence
  | 'pattern.detected'
  | 'pattern.promoted'
  | 'pattern.demoted'
  
  // Evolution
  | 'fitness.updated'
  | 'selection.occurred'
  | 'pressure.applied'
  
  // System
  | 'boundary.violated'
  | 'resource.pressure'
  | 'state.checkpointed';
```

---

## Scalability Considerations

### Horizontal Scaling

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                         DISTRIBUTED HEARTH                                   │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                              │
│  ┌─────────────┐    ┌─────────────┐    ┌─────────────┐                      │
│  │  Hearth     │◄──►│  Hearth     │◄──►│  Hearth     │                      │
│  │  Node 1     │    │  Node 2     │    │  Node 3     │                      │
│  └──────┬──────┘    └──────┬──────┘    └──────┬──────┘                      │
│         │                  │                  │                             │
│         └──────────────────┼──────────────────┘                             │
│                            │                                                │
│                            ▼                                                │
│                   ┌─────────────────┐                                       │
│                   │  Shared State   │                                       │
│                   │  (Distributed   │                                       │
│                   │   Graph DB)     │                                       │
│                   └─────────────────┘                                       │
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘
```

### Component Sharding

- Components are sharded by capability domain
- Cross-shard interactions use async messaging
- State graph supports distributed transactions

---

## Security Architecture

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                         SECURITY LAYERS                                      │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                              │
│  Layer 1: Sandbox                                                            │
│  ├── Isolated execution environment for each component                       │
│  ├── No direct system access                                                 │
│  └── All I/O through monitored channels                                      │
│                                                                              │
│  Layer 2: Boundary Enforcement                                               │
│  ├── Static analysis of component code                                       │
│  ├── Runtime monitoring                                                      │
│  └── Automatic termination on violation                                      │
│                                                                              │
│  Layer 3: Capability-Based Access                                            │
│  ├── Components have explicit capability grants                              │
│  ├── Principle of least privilege                                            │
│  └── Dynamic capability revocation                                           │
│                                                                              │
│  Layer 4: Audit Trail                                                        │
│  ├── All actions logged                                                      │
│  ├── Tamper-evident logs                                                     │
│  └── Forensic reconstruction capability                                      │
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## Performance Characteristics

| Metric | Target | Notes |
|--------|--------|-------|
| Component Registration | < 100ms | Including validation |
| Component Execution | Varies | Depends on implementation |
| State Query | < 10ms | Local graph traversal |
| Pattern Detection | < 1s | Background process |
| Fitness Update | < 50ms | Async batching |
| Evolution Cycle | Minutes | Background, non-blocking |

---

## Next Steps

1. **BOUNDARIES.md**: Detailed boundary types and enforcement mechanisms
2. **COMPONENT-CREATION.md**: Agent-driven component invention process
3. **EMERGENCE.md**: Pattern detection and promotion
4. **EVOLUTION.md**: Natural selection algorithms
5. **STATE.md**: Graph-based state management
6. **INTERFACES.md**: APIs for users, Hey, and agents

---

*Last Updated: 2026-05-20*
*Status: Core Architecture Defined*
