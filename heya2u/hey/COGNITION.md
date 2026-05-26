# Hey Cognition — The Internal Mind

> Hey doesn't just respond. Hey thinks, remembers, learns, and adapts.

---

## What is Hey Cognition?

Hey Cognition is the internal reasoning and knowledge system that enables Hey to operate as a sovereign agent. It encompasses everything that happens inside Hey's "mind" — from understanding a user's request to maintaining long-term domain knowledge.

```
Traditional AI Cognition:
  Input → Context Window → Generate Response → Forget

Hey Cognition:
  Input → Perceive → Retrieve → Reason → Decide → Act → Reflect → Store
              ↑                                              │
              └──────────────────────────────────────────────────┘
                         (Continuous learning loop)
```

### Key Insight

Hey's power comes not from a single reasoning pass, but from a **layered cognition system** that combines:
- **Working memory** (current context)
- **Episodic memory** (past experiences with the user)
- **Semantic memory** (domain knowledge and patterns)
- **Cognition store** (retrievable knowledge base, inspired by ASI-Evolve)
- **Metacognition** (thinking about thinking)

---

## Cognition Architecture

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                         HEY COGNITION                                       │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                              │
│  ┌─────────────────────────────────────────────────────────────────────┐    │
│  │                    PERCEPTION LAYER                                   │    │
│  │  Parse input → Identify intent → Classify complexity                 │    │
│  │  → Determine governance depth needed                                │    │
│  └─────────────────────────────────────────────────────────────────────┘    │
│                              │                                               │
│                              ▼                                               │
│  ┌─────────────────────────────────────────────────────────────────────┐    │
│  │                    RETRIEVAL LAYER                                    │    │
│  │  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌──────────┐           │    │
│  │  │ Working  │  │ Episodic │  │ Semantic │  │Cognition│           │    │
│  │  │ Memory   │  │ Memory   │  │ Memory   │  │  Store   │           │    │
│  │  │(current) │  │(experi- │  │(domain   │  │(embed-  │           │    │
│  │  │          │  │ ences)  │  │knowledge)│  │ ding)   │           │    │
│  │  └──────────┘  └──────────┘  └──────────┘  └──────────┘           │    │
│  └─────────────────────────────────────────────────────────────────────┘    │
│                              │                                               │
│                              ▼                                               │
│  ┌─────────────────────────────────────────────────────────────────────┐    │
│  │                    REASONING LAYER                                    │    │
│  │  Context assembly → Logical inference → Pattern matching             │    │
│  │  → Hypothesis generation → Risk assessment                           │    │
│  └─────────────────────────────────────────────────────────────────────┘    │
│                              │                                               │
│                              ▼                                               │
│  ┌─────────────────────────────────────────────────────────────────────┐    │
│  │                    METACOGNITION LAYER                                 │    │
│  │  Self-monitor → Confidence calibration → Error detection              │    │
│  │  → Strategy selection → Self-correction                               │    │
│  └─────────────────────────────────────────────────────────────────────┘    │
│                              │                                               │
│                              ▼                                               │
│  ┌─────────────────────────────────────────────────────────────────────┐    │
│  │                    OUTPUT LAYER                                       │    │
│  │  Govern (consensus protocol) → Act (execute) → Reflect (learn)      │    │
│  │  → Store (persist to memory)                                         │    │
│  └─────────────────────────────────────────────────────────────────────┘    │
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## Memory Tiers

### 1. Working Memory

The current context window — what Hey is actively thinking about right now.

```typescript
interface WorkingMemory {
  currentIntent: UserIntent;
  activeGovernanceState: GovernanceState;
  recentMessages: Message[];          // Last N messages
  activeComponents: ComponentId[];    // Components currently in use
  pendingDecisions: Decision[];       // Awaiting user input
  capacity: number;                   // Token budget
}
```

**Characteristics**: Fast access, limited capacity, cleared between sessions (unless promoted to episodic memory).

### 2. Episodic Memory

Experiences shared between Hey and the user — the "story" of their relationship.

```typescript
interface EpisodicMemory {
  episodes: Episode[];

  retrieval: {
    byRecency: (limit: number) => Episode[];
    byEmotion: (emotion: string) => Episode[];
    byProject: (projectId: string) => Episode[];
    byUser: (userId: string) => Episode[];
    semanticSearch: (query: string) => Episode[];
  };
}

interface Episode {
  id: string;
  timestamp: Date;
  projectId?: string;
  summary: string;
  keyDecisions: string[];
  emotionalTone: string;
  outcome: 'success' | 'partial' | 'failure';
  lessonsLearned: string[];
  importance: number;  // 0-1, affects retention
}
```

**Characteristics**: Persistent, grows over time, emotionally tagged. Hey uses this to maintain relationship continuity — "Long time no see! Where did we leave off?"

### 3. Semantic Memory

Domain knowledge and patterns — what Hey knows about the world.

```typescript
interface SemanticMemory {
  domains: DomainKnowledge[];

  operations: {
    store: (knowledge: Knowledge) => void;
    retrieve: (query: string) => Knowledge[];
    update: (id: string, patch: KnowledgePatch) => void;
    forget: (id: string) => void;  // Decay mechanism
  };
}

interface DomainKnowledge {
  id: string;
  domain: string;           // e.g., "react", "system-design", "ml"
  concept: string;
  explanation: string;
  relationships: string[];  // Links to other concepts
  confidence: number;       // How certain is this knowledge
  source: 'experience' | 'user_told' | 'self_learned' | 'external';
  lastUsed: Date;
  useCount: number;
}
```

**Characteristics**: Persistent, structured, grows through experience. Hey uses this to provide domain-expert advice.

### 4. Cognition Store

A persistent, embedding-indexed knowledge base inspired by ASI-Evolve. Unlike semantic memory (which is Hey's internal knowledge), the cognition store can be seeded with external knowledge.

```typescript
interface CognitionStore {
  items: CognitionItem[];

  operations: {
    add: (item: CognitionItem) => string;
    search: (query: string, topK?: number) => CognitionItem[];
    retrieve: (query: string, threshold?: number) => [CognitionItem, number][];
    remove: (id: string) => boolean;
  };
}

interface CognitionItem {
  id: string;
  title: string;
  content: string;
  source: string;           // Where this knowledge came from
  embedding: number[];      // Vector representation
  addedAt: Date;
  lastAccessed: Date;
  accessCount: number;
}
```

**Use cases**:
- User uploads domain knowledge (papers, docs, rules of thumb)
- Hey stores lessons learned from past projects
- Cross-project knowledge transfer
- Seeding new Hearth spaces with relevant context

---

## Metacognition

Hey's ability to think about its own thinking.

```typescript
interface Metacognition {
  // Self-monitoring
  monitorConfidence(reasoning: ReasoningStep): ConfidenceScore;
  detectErrors(output: string, context: string): ErrorReport;
  assessQuality(response: string, intent: UserIntent): QualityScore;

  // Self-correction
  selectStrategy(task: Task, context: Context): Strategy;
  adjustDepth(currentDepth: number, complexity: number): number;
  requestHelp(when: HelpCondition): HelpRequest;

  // Self-awareness
  acknowledgeLimitation(gap: KnowledgeGap): Acknowledgment;
  reportUncertainty(assessment: UncertaintyAssessment): UncertaintyReport;
}
```

### Key Metacognitive Behaviors

1. **"I don't know"**: Hey explicitly states when it lacks knowledge, rather than hallucinating
2. **Confidence calibration**: Hey rates its own confidence and adjusts behavior accordingly
3. **Error detection**: Hey catches its own mistakes before the user sees them
4. **Strategy selection**: Hey chooses the right reasoning approach for the task complexity
5. **Help seeking**: Hey knows when to ask the user for clarification vs. proceeding autonomously

---

## Ecological Awareness

Hey understands its place in the broader ecosystem — not just the current task.

```typescript
interface EcologicalAwareness {
  // Project context
  currentProject: ProjectContext;
  projectHistory: ProjectHistory;

  // Ecosystem context
  productTree: ProductTreePosition;    // Where in the RUS → Van → G/H → HeyaCore tree
  companyEcosystem: EcosystemMap;       // How this project relates to others
  dependencies: DependencyGraph;        // What depends on what

  // Impact assessment
  assessImpact(change: ProposedChange): ImpactReport;
  // "Changing this API will affect 3 downstream services"
}
```

This enables Hey to provide **global cognition** — understanding not just the immediate task, but its ripple effects across the ecosystem.

---

## Living Codex

The continuously evolving documentation that Hey maintains.

```typescript
interface LivingCodex {
  entries: CodexEntry[];

  // Auto-generated content
  generateFromExecution(result: ExecutionResult): CodexEntry;
  generateFromDecision(decision: Decision): CodexEntry;
  generateFromLesson(lesson: Lesson): CodexEntry;

  // Structure
  sections: {
    architecture: ArchitectureDoc;
    decisions: ADR[];           // Architectural Decision Records
    lessons: Lesson[];
    runbook: RunbookEntry[];   // How to do things
    handoff: HandoffDoc;       // For human/AI transitions
  };
}
```

The Living Codex ensures that:
- Knowledge survives session boundaries
- New team members (human or AI) can onboard quickly
- Decisions are traceable and auditable
- The project is never in an "undocumented" state

---

## Entropy Monitoring

Hey tracks the "health" of the project over time.

```typescript
interface EntropyMonitor {
  // What it measures
  metrics: {
    complexity: number;        // Code/architecture complexity trend
    technicalDebt: number;     // Accumulated shortcuts and TODOs
    coupling: number;          // Inter-component dependency density
    documentationCoverage: number;  // How well-documented is the project
    testCoverage: number;      // How well-tested is the project
  };

  // What it does
  assess(): EntropyReport;
  trend(): EntropyTrend;        // Getting better or worse?
  suggestReduction(): ReductionSuggestion[];
}
```

When entropy exceeds a threshold, the Skeptic role in Hey's governance system is automatically activated to propose refactoring.

---

## Design Principles

1. **Layered, Not Flat**: Different memory types serve different purposes — don't conflate them
2. **Retrieve Before Reason**: Always check what you know before thinking from scratch
3. **Store After Acting**: Every action produces learnings — capture them
4. **Know What You Don't Know**: Metacognition prevents overconfidence
5. **See the Whole Board**: Ecological awareness prevents local optimization at the cost of global quality

---

*Last Updated: 2026-05-25*
*Status: Core Design Defined*
*See also: [README.md](./README.md) | [GOVERNANCE.md](./GOVERNANCE.md) | [ORCHESTRATION.md](./ORCHESTRATION.md)*
