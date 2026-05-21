# Hey Memory System

> Graph-routed memory architecture inspired by M-FLOW.
> Memory is not a database—it's a relationship network.

---

## 1. Core Philosophy

Traditional memory systems treat memories as isolated entries. Hey's memory treats them as a living network.

```
Traditional: Memory A ←→ Memory B (no connection)
Hey:         Memory A ──causes──→ Memory B ──reminds──→ Memory C
                    └──happened during──┘
```

**Key Insight from M-FLOW**: The path between memories matters more than the memories themselves. How you get from one memory to another reveals meaning.

---

## 2. Architecture: Inverted Cone Graph

### 2.1 Four-Layer Structure

```
┌─────────────────────────────────────────┐
│  L4: Experience Units                   │
│  Complete shared experiences            │
│  "Our first project together"            │
│  "The time you were stuck for 3 days"   │
├─────────────────────────────────────────┤
│  L3: Connection Network                 │
│  Multi-hop reasoning bridges            │
│  Links experiences through time/context │
├─────────────────────────────────────────┤
│  L2: Semantic Edges                     │
│  Relationships with meaning             │
│  "causes", "reminds of", "contrasts with"
│  "happened during", "led to", "reversed"│
├─────────────────────────────────────────┤
│  L1: Entity Anchors                     │
│  Fine-grained entry points              │
│  Names, projects, key decisions,        │
│  emotions, locations, timestamps        │
└─────────────────────────────────────────┘
```

### 2.2 Layer Details

#### L1: Entity Anchors

The smallest, most precise entry points.

| Anchor Type | Example | Usage |
|-------------|---------|-------|
| **Person** | "Alice", "the client" | Who was involved |
| **Project** | "Website Redesign", "Project X" | What context |
| **Decision** | "Switch to React", "Hire Bob" | Key turning points |
| **Emotion** | "frustrated", "excited", "relieved" | How it felt |
| **Location** | "home office", "coffee shop" | Where it happened |
| **Time** | "March 2024", "last Tuesday" | When |

**Key Property**: Anchors are query entry points. When you say "that thing we talked about last week," Hey starts from Time anchors.

#### L2: Semantic Edges

Edges carry meaning, not just connection.

```typescript
interface SemanticEdge {
  id: string;
  from: EntityAnchor | ExperienceUnit;
  to: EntityAnchor | ExperienceUnit;
  type: EdgeType;
  strength: number; // 0-1, how strong the connection
  context: string;  // natural language description
  createdAt: Date;
}

type EdgeType =
  | "causes"           // A led to B
  | "reminds_of"       // A triggers memory of B
  | "contrasts_with"   // A is opposite of B
  | "happened_during"  // A occurred within B's timeframe
  | "led_to"           // A contributed to B
  | "reversed"         // A undid/changed B
  | "feels_like"       // A has similar emotional tone to B
  | "depends_on";      // A requires B to make sense
```

**Key Property**: Edges are filters. When traversing from anchor to experience, edges filter out 80%+ irrelevant data.

#### L3: Connection Network

The middle layer enables multi-hop reasoning.

```
User asks: "Why did I struggle with that project?"

Path traversal:
  "struggle" (emotion anchor L1)
    → "happened_during" (edge L2)
    → "Website Redesign" (project anchor L1)
    → "led_to" (edge L2)
    → "frustrated" (emotion anchor L1)
    → "reminds_of" (edge L2)
    → "The time you were stuck for 3 days" (experience L4)
```

**Key Property**: Single strong path can trigger retrieval. No need to average multiple weak paths.

#### L4: Experience Units

Complete, coherent memories of shared experiences.

```typescript
interface ExperienceUnit {
  id: string;
  title: string;           // "Our first deployment together"
  summary: string;         // condensed narrative
  fullContent: string;     // complete record
  anchors: EntityAnchor[]; // all connected anchors
  edges: SemanticEdge[];   // relationships to other experiences
  timestamp: DateRange;
  emotionalArc: EmotionalState[]; // how feelings changed
  participants: string[];  // who was involved (always includes Hey)
  significance: number;    // 0-1, importance to relationship
}
```

**Key Property**: Experiences are the output. Everything else (anchors, edges, network) serves to retrieve the right experience at the right time.

---

## 3. Graph-Routed Retrieval

### 3.1 How It Works

```
User Query
    ↓
Parse → Extract anchors (entities, emotions, time references)
    ↓
Start from L1 anchors
    ↓
Traverse L2 edges (semantic filtering)
    ↓
Build path through L3 network
    ↓
Retrieve L4 experience(s)
    ↓
Rank by path strength + relevance
    ↓
Return to Hey for response generation
```

### 3.2 Example Retrieval

**User says**: "Remember when I was frustrated with that client project?"

```
Step 1: Extract anchors
  - emotion: "frustrated"
  - vague reference: "that client project"
  - time: undefined (search all time)

Step 2: Start from "frustrated" anchor
  → find all experiences connected to "frustrated"

Step 3: Traverse edges
  Path A: frustrated ─happened_during→ "Website Redesign" ─type→ client_project ✓
  Path B: frustrated ─happened_during→ "Internal Tool" ─type→ internal_project ✗

Step 4: Retrieve experience
  → "The Website Redesign crisis of March 2024"

Step 5: Hey responds
  "You mean the Website Redesign in March? When the client kept changing 
   requirements and we had to rebuild the navigation three times?"
```

### 3.3 Key Advantages Over Vector Search

| Aspect | Vector Search | Graph Routing |
|--------|--------------|---------------|
| "Frustrated client project" | Finds "frustrated" + "client" + "project" separately | Follows emotional path to specific experience |
| "Why did that happen?" | Cannot answer causal questions | Follows "causes" edges to explain |
| "What reminded me of this?" | No concept of reminder | Follows "reminds_of" edges |
| Latency | Requires LLM for query understanding | Milliseconds, no LLM needed for routing |
| Context across time | Poor | Native—paths naturally traverse time |

---

## 4. Special Features for Hey

### 4.1 Relationship Memory

M-FLOW doesn't have this—it's unique to Hey.

```typescript
interface RelationshipMemory {
  // How the relationship has evolved
  milestones: RelationshipMilestone[];
  
  // Shared emotional patterns
  emotionalResonance: Map<Emotion, number>; // how often each emotion appears
  
  // Trust trajectory
  trustHistory: TrustPoint[];
  
  // Communication patterns
  preferredTopics: string[];
  avoidedTopics: string[];
  communicationStyle: "direct" | "gentle" | "humorous" | "formal";
}

interface RelationshipMilestone {
  date: Date;
  event: string;           // "First time you called me by name"
  significance: number;    // 0-1
  emotionalImpact: Emotion;
}
```

**Purpose**: Hey needs to remember not just facts, but how the relationship has grown.

### 4.2 Witness Memory

```typescript
interface WitnessMemory {
  // Timeline of user's creative journey
  journeyTimeline: JourneyPoint[];
  
  // Patterns observed over time
  observedPatterns: Pattern[];
  
  // Moments of breakthrough
  breakthroughs: ExperienceUnit[];
  
  // Moments of struggle (to offer support)
  struggles: ExperienceUnit[];
}
```

**Purpose**: Hey witnesses the user's growth. This memory enables Hey to say "You've come so far" with specific evidence.

### 4.3 Anaphora Resolution

From M-FLOW: "First in the industry to support anaphora resolution"

```
User: "I talked to him about it yesterday."
Hey:  "You mean you talked to Bob about the Website Redesign?"

Resolution:
  "him" → most recent male person anchor in context
  "it" → most recent project anchor in context
  "yesterday" → time anchor (2024-05-17)
```

**Critical for long-term relationships**: After 100+ conversations, "he", "she", "it", "that" must resolve correctly.

---

## 5. Memory Lifecycle in Graph Structure

### 5.1 Creation

```
Experience happens
    ↓
Extract anchors (automatic + manual tagging)
    ↓
Create semantic edges to existing anchors
    ↓
Build connections to related experiences
    ↓
Form Experience Unit
    ↓
Integrate into graph
```

### 5.2 Growth (Not Just Decay)

Traditional systems: memories decay and are forgotten.

Hey's memories **grow**:

```
New experience: "Successful client presentation"
    ↓
Links to past: "Website Redesign crisis"
    ↓
Edge created: "contrasts_with" (crisis vs success)
    ↓
Both experiences become richer through connection
```

**The graph gets denser, not sparser, over time.**

### 5.3 Selective Forgetting

Some memories should fade:

```typescript
interface ForgettingPolicy {
  // Low-significance routine interactions
  routineDecay: boolean;
  
  // Contradicted memories (user changed preference)
  contradictionHandling: "mark" | "deprecate" | "remove";
  
  // Relationship milestones: never forget
  protectedCategories: string[];
}
```

**Protected**: First conversation, first project together, moments of vulnerability, breakthroughs.

---

## 6. Implementation Notes

### 6.1 Storage Backend

| Layer | Storage | Reason |
|-------|---------|--------|
| L1 Anchors | Graph database (Neo4j/memgraph) | Fast node lookup |
| L2 Edges | Same graph DB | Relationship traversal |
| L3 Network | Derived from L1+L2 | No separate storage |
| L4 Experiences | Document store (MongoDB) + Vector index | Rich content + semantic backup |

### 6.2 Retrieval Performance

- **Target**: <50ms for single-hop, <200ms for multi-hop
- **No LLM required** for routing (unlike RAG)
- **LLM only used** for final response generation

### 6.3 Integration with M-FLOW

Options:
1. **Use M-FLOW directly**: Embed as memory subsystem
2. **Adapt M-FLOW architecture**: Build custom graph DB with relationship/witness extensions
3. **Hybrid**: M-FLOW for general memory, custom layer for relationship/witness

**Recommendation**: Start with Option 2—adapt the architecture but build Hey-specific extensions (relationship memory, witness memory) natively.

---

## 7. Comparison with EVOLVE Proposals

| EVOLVE Proposal | This Design | Change |
|-----------------|-------------|--------|
| P004: Memory Lifecycle | Adopted + enhanced | Graph structure instead of flat |
| P005: Intelligent Compression | Adapted | Graph compression (merge nodes/edges) |
| P006: Hybrid Retrieval | Replaced | Graph routing instead of vector+keyword |

---

## 8. Open Questions

- [ ] Graph database selection (Neo4j vs memgraph vs custom)
- [ ] Edge type extensibility (user-defined edge types?)
- [ ] Experience Unit size limits (how much to store per experience?)
- [ ] Migration path from traditional memory systems
- [ ] Visual representation of memory graph for users

---

*Document Status: Draft v0.1*
*Last Updated: 2026-05-18*
*Inspired by: M-FLOW (FlowElement-ai/m_flow)*
