# Hearth - The Creation Space

> Hearth is not a workflow engine. It is a space where creativity emerges.
> Agents do not call components—they create them.

---

## What is Hearth

Hearth is the core creation space of Heya. It provides the boundaries within which Agents freely invent components, and the environment where those components come alive.

```
Traditional Systems:
  Fixed Component Pool → Combinations → Limited Possibilities

Hearth:
  Boundaries + Creation Environment → Agents Invent Components → Emergent Capabilities → Infinite Possibilities
```

### Key Insight

The power of Heya lies not in pre-built components, but in Agents' ability to **create components on demand**.

When a user says "write a book":
- There is no "book writing workflow"
- Agents analyze the intent and invent the necessary components
- Components interact, compete, combine
- Good structures emerge and self-reinforce
- The user and Hey witness, guide, and select
- A unique "book" structure emerges organically

---

## Architecture Philosophy

### Hearth as Ecosystem, Not Factory

| Factory Metaphor | Ecosystem Metaphor |
|------------------|-------------------|
| Predefined assembly lines | Self-organizing interactions |
| Fixed components | Evolving species |
| Controlled processes | Natural selection |
| Predictable outputs | Emergent possibilities |

**Hearth is an ecosystem where:**
- **Components** are species
- **Interactions** are ecological relationships
- **Workflows** are emergent patterns, not designed procedures
- **Capabilities** evolve through use

### The Boundary Principle

Hearth defines what is possible, not what exists.

```
Boundary (Hearth defines)
├── What Agents CAN create
│   ├── Components with certain interfaces
│   ├── Connections between components
│   └── State transformations
│
└── What Agents CANNOT do
    ├── Escape the execution sandbox
    ├── Violate user-defined constraints
    └── Access unauthorized resources
```

Within these boundaries, **everything is permitted**. Agents have complete freedom to invent, experiment, and evolve.

---

## Core Abstractions

### 1. Space

The creation environment where components live and interact.

```typescript
interface Space {
  id: string;
  boundaries: Boundary[];
  resources: Resource[];
  components: Component[];  // Dynamically created
  state: Graph;             // Shared state structure
  evolutionLog: EvolutionEvent[];
}
```

### 2. Boundary

Constraints that shape what can emerge.

```typescript
interface Boundary {
  type: 'safety' | 'resource' | 'semantic' | 'user_defined';
  constraint: Constraint;
  enforcement: 'hard' | 'soft';  // Hard = reject, Soft = warn
}
```

Examples:
- **Safety boundary**: Cannot execute arbitrary system commands
- **Resource boundary**: Maximum 100MB memory per component
- **Semantic boundary**: Components must declare input/output types
- **User boundary**: "Don't use cloud APIs for this project"

### 3. Component (Agent-Created)

Capabilities invented by Agents within boundaries.

```typescript
interface Component {
  id: string;
  createdBy: Agent;
  createdAt: Date;
  
  // Self-describing capability
  signature: {
    name: string;
    description: string;
    inputs: Type[];
    outputs: Type[];
    sideEffects: SideEffect[];
  };
  
  // Implementation
  implementation: Code | Prompt | Hybrid;
  
  // Evolution
  version: number;
  parent?: Component;       // What this evolved from
  children: Component[];    // What evolved from this
  fitness: number;          // How well it performs
}
```

**Key**: Components are not pre-built. They are **invented on demand** by Agents.

### 4. Interaction

How components relate to each other.

```typescript
interface Interaction {
  from: Component;
  to: Component;
  type: 'data_flow' | 'control_flow' | 'inhibition' | 'synergy';
  strength: number;
  context: string;
}
```

Components form an **interaction network**, not a pipeline.

### 5. Emergence

When the whole becomes greater than the sum of parts.

```typescript
interface EmergentPattern {
  components: Component[];
  interactions: Interaction[];
  observedBehavior: string;
  utility: number;
  persistence: number;  // How long it survives
}
```

Good patterns persist and spread. Bad patterns die out.

---

## How It Works

### Scenario: User Says "Write a Book"

```
Step 1: Intent Analysis
  Hey parses: "write" + "book" + [implicit: genre, style, audience?]
  
Step 2: Space Activation
  Hearth activates relevant boundaries:
  - Resource: 1GB memory, 10min timeout
  - Semantic: Must produce structured text
  - User: "No cloud APIs"

Step 3: Component Invention (Agent)
  Agent analyzes: "What components do I need?"
  - Invents: PlotGenerator, CharacterDesigner, ChapterPlanner
  - Each component is created on-the-fly with:
    - Self-describing signature
    - Implementation (code + prompt)
    - Fitness tracking

Step 4: Emergent Interaction
  Components start interacting:
  PlotGenerator → CharacterDesigner: "Need protagonist traits"
  CharacterDesigner → PlotGenerator: "Protagonist is introverted"
  Both → ChapterPlanner: "Generate chapter structure"
  
  Alternative components may emerge:
  - StyleAnalyzer ("User prefers Hemingway style")
  - ResearchFetcher ("Need historical accuracy")

Step 5: Natural Selection
  Components compete for resources:
  - Effective components get more calls
  - Ineffective components are deprecated
  - Synergistic combinations are reinforced

Step 6: Structure Emerges
  After many interactions, a stable pattern emerges:
  "This is how we write THIS book"
  
  The pattern is:
  - Not predefined
  - Unique to this book
  - Evolved through trial and error

Step 7: User Interaction
  User sees emerging structure:
  - "Chapter 1 outline: [content]"
  - "Character A: [profile]"
  - "Plot twist at Chapter 5: [idea]"
  
  User can:
  - Accept: Pattern continues
  - Reject: Components adapt or die
  - Redirect: New components invented

Step 8: Persistence
  Successful components are:
  - Saved to Component Library
  - Available for future "book writing"
  - Evolved through use
```

---

## Document Index

| Document | Content |
|----------|---------|
| [ARCHITECTURE.md](./ARCHITECTURE.md) | Core architecture and abstractions |
| [BOUNDARIES.md](./BOUNDARIES.md) | Boundary types and enforcement |
| [COMPONENT-CREATION.md](./COMPONENT-CREATION.md) | How Agents invent components |
| [EMERGENCE.md](./EMERGENCE.md) | How patterns self-organize |
| [EVOLUTION.md](./EVOLUTION.md) | Component fitness and selection |
| [STATE.md](./STATE.md) | Shared state and memory |
| [INTERFACES.md](./INTERFACES.md) | User, Hey, and Agent interfaces |

---

## Key Principles

1. **Boundaries, Not Prescriptions**: Hearth defines limits, not procedures
2. **Invention, Not Invocation**: Agents create components, don't call them
3. **Emergence, Not Design**: Good structures evolve, aren't engineered
4. **Evolution, Not Maintenance**: Components improve through use
5. **Ecosystem, Not Factory**: Complex capabilities arise from simple interactions

---

## Relationship to Other Heya Components

```
User
  ↓ (natural language intent)
Hey (Companion)
  ↓ (coordinates, witnesses, guides)
Hearth (Creation Space)
  ├── Boundaries (safety, resources, semantics)
  ├── Agents (invent components)
  ├── Components (evolve through use)
  └── State (shared memory)
  ↓
Artifacts (emergent creations)
```

**Hey** is the user's companion in the Hearth ecosystem.
**Hearth** is the space where creation happens.
**Agents** are the evolving life within that space.

---

*Last Updated: 2026-05-18*
*Status: Core Philosophy Defined*
