# State Management

> Graph-based shared state and persistence.

---

## The State Graph

Hearth uses a dynamic graph as its shared memory system. Unlike traditional databases, the graph structure itself carries semantic meaning.

```
Traditional Database:                  Hearth State Graph:
┌─────────────────┐                    ┌─────────────────┐
│  Tables         │                    │  Graph          │
│  ┌───┐ ┌───┐   │                    │  ┌───┐   ┌───┐  │
│  │Row│ │Row│   │                    │  │ A │──►│ B │  │
│  ├───┤ ├───┤   │                    │  └───┘   └───┘  │
│  │Row│ │Row│   │                    │    │  ╲   │     │
│  └───┘ └───┘   │                    │    ▼   ╲  ▼     │
│                 │                    │  ┌───┐   ┌───┐  │
│  Rigid schema   │                    │  │ C │◄──┘ D │  │
│  Fixed relations│                    │  └───┘   └───┘  │
└─────────────────┘                    │                 │
                                       │  Semantic edges │
                                       │  Evolving structure
                                       └─────────────────┘
```

---

## Graph Structure

### Four-Layer Architecture (M-FLOW Inspired)

```
Layer 4: Experiences (Concrete interactions)
    ├── User said: "Write a book about dragons"
    ├── PlotGenerator created outline
    ├── CharacterDesigner created protagonist
    └── ...
    ↓
Layer 3: Network (Relationship web)
    ├── "dragon" concept connected to:
    │   ├── Plot (as subject)
    │   ├── Character (as companion)
    │   └── World (as inhabitant)
    └── ...
    ↓
Layer 2: Edges (Connections)
    ├── subject_of
    ├── companion_of
    ├── inhabits
    └── ...
    ↓
Layer 1: Anchors (Core concepts)
    ├── "dragon"
    ├── "plot"
    ├── "character"
    └── "world"
```

### Node Types

```typescript
interface StateNode {
  id: NodeId;
  type: NodeType;
  data: unknown;
  
  // Metadata
  metadata: {
    createdBy: ComponentId;
    createdAt: Date;
    lastModified: Date;
    accessCount: number;
    version: number;
  };
  
  // Connections
  edges: EdgeId[];
  
  // Semantic
  embeddings?: Vector[];      // For semantic search
  tags: string[];
}

type NodeType = 
  | 'concept'        // Abstract ideas (plot, character, theme)
  | 'entity'         // Concrete things (specific character, location)
  | 'event'          // Things that happened (interactions, executions)
  | 'artifact'       // Created outputs (text, images, code)
  | 'component_ref'  // Reference to a component
  | 'user_intent'    // User's goals and requests
  | 'context'        // Session/project context
  | 'memory'         // Hey's memory
  | 'temporal'       // Time-based nodes
  | 'spatial';       // Spatial/structural nodes
```

### Edge Types

```typescript
interface StateEdge {
  id: EdgeId;
  from: NodeId;
  to: NodeId;
  type: EdgeType;
  
  // Weight represents strength/confidence
  weight: number;  // 0.0 - 1.0
  
  // Temporal
  createdAt: Date;
  lastActivated: Date;
  activationCount: number;
  
  // Context
  context?: string;  // Why this edge exists
  source: 'explicit' | 'inferred' | 'derived';
}

type EdgeType =
  // Structural
  | 'contains'       // Parent-child
  | 'part_of'        // Child-parent
  | 'depends_on'     // Dependency
  | 'references'     // Citation/link
  
  // Semantic
  | 'is_a'           // Type/instance
  | 'similar_to'     // Similarity
  | 'opposite_of'    // Antonym
  | 'related_to'     // General relation
  
  // Temporal
  | 'precedes'       // Happens before
  | 'follows'        // Happens after
  | 'concurrent_with' // Happens together
  
  // Causal
  | 'causes'         // Causation
  | 'enables'        // Enables
  | 'prevents'       // Prevents
  
  // Agent
  | 'created_by'     // Authorship
  | 'uses'           // Usage
  | 'produces'       // Output
  | 'consumes'       // Input
  
  // User
  | 'wants'          // User intent
  | 'likes'          // Preference
  | 'dislikes'       // Aversion
  | 'owns';          // Ownership
```

---

## Graph Operations

### Querying

```typescript
interface GraphQuery {
  // Pattern matching
  match(pattern: GraphPattern): QueryResult;
  
  // Traversal
  traverse(start: NodeId, options: TraversalOptions): Path[];
  
  // Search
  search(query: string): Node[];                    // Text search
  semanticSearch(vector: Vector): Node[];           // Similarity search
  
  // Navigation
  neighbors(nodeId: NodeId, options?: NeighborOptions): Node[];
  path(from: NodeId, to: NodeId): Path | null;
  
  // Aggregation
  subgraph(center: NodeId, radius: number): Graph;
}

// Example queries
const queries = {
  // Find all characters in the story
  findCharacters: () => ({
    pattern: {
      nodes: [{ type: 'entity', tags: ['character'] }],
      edges: []
    }
  }),
  
  // Find plot events involving the protagonist
  findProtagonistEvents: (protagonistId: NodeId) => ({
    start: protagonistId,
    traversal: {
      edgeTypes: ['participates_in', 'causes', 'experiences'],
      nodeTypes: ['event'],
      maxDepth: 2
    }
  }),
  
  // Find similar concepts
  findSimilar: (conceptId: NodeId) => ({
    vector: getNodeEmbedding(conceptId),
    similarity: 'cosine',
    threshold: 0.8
  }),
  
  // Find causal chain
  findCausalChain: (eventId: NodeId) => ({
    start: eventId,
    traversal: {
      edgeTypes: ['causes'],
      direction: 'forward',
      maxDepth: 5
    }
  })
};
```

### Mutation

```typescript
interface GraphMutation {
  // Node operations
  createNode(data: NodeData): Node;
  updateNode(id: NodeId, patch: NodePatch): Node;
  deleteNode(id: NodeId): void;
  mergeNodes(ids: NodeId[]): Node;
  
  // Edge operations
  createEdge(from: NodeId, to: NodeId, type: EdgeType): Edge;
  updateEdge(id: EdgeId, patch: EdgePatch): Edge;
  deleteEdge(id: EdgeId): void;
  strengthenEdge(id: EdgeId, amount: number): void;
  weakenEdge(id: EdgeId, amount: number): void;
  
  // Batch operations
  batch(mutations: Mutation[]): BatchResult;
  
  // Transactions
  transaction(): Transaction;
}

// Example: Recording a component execution
function recordExecution(
  component: Component,
  inputs: unknown,
  outputs: unknown,
  context: ExecutionContext
): void {
  const tx = graph.transaction();
  
  // Create execution node
  const executionNode = tx.createNode({
    type: 'event',
    data: {
      type: 'component_execution',
      component: component.id,
      inputs,
      outputs,
      duration: context.duration,
      success: context.success
    },
    tags: ['execution', component.signature.category]
  });
  
  // Link to component
  tx.createEdge(executionNode.id, component.id, 'instance_of');
  
  // Link inputs
  for (const input of extractNodes(inputs)) {
    tx.createEdge(executionNode.id, input, 'consumes');
  }
  
  // Link outputs
  for (const output of extractNodes(outputs)) {
    tx.createEdge(executionNode.id, output, 'produces');
  }
  
  // Link to context
  tx.createEdge(executionNode.id, context.sessionId, 'part_of');
  
  tx.commit();
}
```

---

## Persistence

### Snapshot System

```typescript
interface SnapshotSystem {
  // Create snapshot
  checkpoint(): Snapshot;
  
  // Restore
  restore(snapshot: Snapshot): void;
  
  // Branching
  branch(from: Snapshot, name: string): Branch;
  
  // Merging
  merge(branch: Branch, strategy: MergeStrategy): void;
}

interface Snapshot {
  id: string;
  timestamp: Date;
  graph: GraphData;
  metadata: {
    description: string;
    tags: string[];
    creator: string;
  };
}
```

### Incremental Persistence

```typescript
interface IncrementalPersistence {
  // Log changes
  log(mutation: Mutation): void;
  
  // Replay
  replay(since: Date): Mutation[];
  
  // Compress
  compress(log: Mutation[]): CompressedLog;
}

// Example: Event sourcing
class EventSourcedGraph {
  private log: Mutation[] = [];
  private state: Graph;
  
  apply(mutation: Mutation): void {
    // Validate
    if (!this.validate(mutation)) {
      throw new Error('Invalid mutation');
    }
    
    // Apply
    this.state.apply(mutation);
    
    // Log
    this.log.push({
      ...mutation,
      timestamp: new Date(),
      sequence: this.log.length
    });
    
    // Persist
    this.persist(mutation);
  }
  
  replay(toSequence: number): void {
    this.state = new Graph();
    for (let i = 0; i < toSequence && i < this.log.length; i++) {
      this.state.apply(this.log[i]);
    }
  }
}
```

---

## Semantic Layer

### Embeddings

```typescript
interface SemanticIndex {
  // Generate embeddings
  embed(node: StateNode): Vector;
  
  // Similarity search
  findSimilar(query: Vector, k: number): SimilarNode[];
  
  // Clustering
  cluster(nodes: NodeId[]): Cluster[];
  
  // Analogy
  analogy(a: NodeId, b: NodeId, c: NodeId): NodeId;  // a:b :: c:?
}

// Example: Semantic navigation
function semanticNavigate(
  from: NodeId, 
  towards: string
): Path {
  const targetVector = embedText(towards);
  const currentVector = getNodeEmbedding(from);
  
  // Find direction in embedding space
  const direction = subtract(targetVector, currentVector);
  
  // Follow edges that move in that direction
  return greedyWalk(from, direction);
}
```

### Inference

```typescript
interface InferenceEngine {
  // Infer missing edges
  inferEdges(node: NodeId): InferredEdge[];
  
  // Predict next nodes
  predictNext(current: NodeId[]): Prediction[];
  
  // Find contradictions
  findContradictions(): Contradiction[];
  
  // Suggest completions
  suggestCompletions(partial: Graph): Completion[];
}

// Example: Inferring relationships
function inferRelationships(graph: Graph): void {
  // Transitive inference
  // If A causes B, and B causes C, then A causes C
  const causalChains = graph.findPaths({
    edgeType: 'causes',
    minLength: 2
  });
  
  for (const chain of causalChains) {
    const from = chain[0];
    const to = chain[chain.length - 1];
    
    if (!graph.hasEdge(from, to, 'causes')) {
      graph.createEdge(from, to, 'causes', {
        source: 'inferred',
        confidence: calculateConfidence(chain)
      });
    }
  }
  
  // Similarity inference
  // If A is similar to B, and B is similar to C, then A is similar to C
  // (with decreasing confidence)
}
```

---

## Access Patterns

### Component Access

```typescript
interface ComponentAccess {
  // Read
  read(query: GraphQuery): QueryResult;
  
  // Write
  write(mutation: GraphMutation): WriteResult;
  
  // Subscribe
  subscribe(query: GraphQuery, callback: Callback): Subscription;
  
  // Lock (for coordination)
  lock(nodeIds: NodeId[]): Lock;
}

// Access control
const accessControl = {
  // Components can read anything
  read: 'allow',
  
  // Components can write to their own namespace
  write: (component, node) => 
    node.metadata.createdBy === component.id ||
    node.type === 'temp',
  
  // Components can subscribe to related nodes
  subscribe: (component, query) => 
    isRelated(component, query)
};
```

### User Access

```typescript
interface UserAccess {
  // Query their data
  query(filter: UserFilter): QueryResult;
  
  // Modify their data
  modify(mutation: Mutation): ModifyResult;
  
  // Export
  export(format: 'json' | 'graphml' | 'rdf'): ExportResult;
  
  // Import
  import(data: unknown): ImportResult;
}
```

---

## Performance

### Optimization Strategies

```typescript
interface GraphOptimization {
  // Indexing
  indexNodes(field: string): Index;
  indexEdges(type: EdgeType): Index;
  
  // Caching
  cache(query: GraphQuery, result: QueryResult): void;
  invalidateCache(pattern: CachePattern): void;
  
  // Partitioning
  partition(strategy: PartitionStrategy): Partition[];
  
  // Lazy loading
  loadOnDemand(nodeIds: NodeId[]): Promise<void>;
}

// Example: Query optimization
function optimizeQuery(query: GraphQuery): OptimizedQuery {
  // Use indexes when possible
  if (query.pattern.nodes[0]?.id) {
    return { type: 'index_lookup', id: query.pattern.nodes[0].id };
  }
  
  // Use embeddings for semantic queries
  if (query.semantic) {
    return { type: 'vector_search', vector: query.semantic };
  }
  
  // Use traversal for path queries
  if (query.path) {
    return { type: 'traversal', ...query.path };
  }
  
  // Full scan as fallback
  return { type: 'scan', filter: query.pattern };
}
```

---

## Best Practices

### For Component Developers

1. **Write Meaningful Edges**: Edges carry semantic weight
2. **Use Appropriate Types**: Choose the right node and edge types
3. **Clean Up**: Remove temporary nodes when done
4. **Version Important Nodes**: Track changes to critical data
5. **Subscribe, Don't Poll**: Use subscriptions for reactive updates

### For System Designers

1. **Plan for Scale**: Graph can grow very large
2. **Index Strategically**: Index fields that are queried often
3. **Monitor Performance**: Track query latency and throughput
4. **Backup Regularly**: Snapshots protect against data loss
5. **Evolve Schema**: Allow node/edge types to evolve

---

## Summary

| Aspect | Traditional DB | State Graph |
|--------|---------------|-------------|
| **Structure** | Tables | Dynamic graph |
| **Relations** | Foreign keys | Semantic edges |
| **Query** | SQL | Pattern matching |
| **Semantics** | None | Embeddings + inference |
| **Evolution** | Schema migrations | Continuous growth |
| **Context** | None | Rich contextual edges |

**Key Insight**: The state graph is not just storage—it's a living representation of knowledge that grows and evolves with the system.

---

*Last Updated: 2026-05-20*
*Status: State System Defined*
