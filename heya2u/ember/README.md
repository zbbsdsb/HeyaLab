# Ember — The Creative Seeds

> An ember is the glowing remnant of a fire that has the potential to ignite something entirely new — a spark waiting for the right moment.

---

## What is Ember?

Ember is the creative seed bank of the Heya ecosystem. It captures, preserves, and nurtures raw creative intent — ideas, visions, half-formed concepts — before they enter Hearth as active projects. Ember is where **inspiration lives**.

```
Traditional Systems:
  Notes App → Flat list of text, no creative context
  Task Manager → Action items, not creative visions
  Bookmark Manager → References, not ideas

Ember:
  Living Seeds → Ideas that grow, branch, combine,
  and ignite into Hearth projects when ready.
  Every creation starts as an ember.
```

### Key Insight

Not every creative idea is ready to become a project. Some need to simmer. Some need to combine with other ideas. Some are ahead of their time. Ember gives ideas a place to exist, evolve, and wait for the right moment — without the overhead of activating a full Hearth space.

---

## Core Concepts

### 1. Seed

The fundamental unit of Ember. A seed is a creative intent in its rawest form.

```typescript
interface Seed {
  id: SeedId;
  title: string;
  description: string;

  // Content
  content: SeedContent;
  mood: SeedMood;

  // Lifecycle
  status: SeedStatus;
  temperature: number;       // 0.0 (cold) → 1.0 (burning hot)
  createdAt: Date;
  updatedAt: Date;
  lastIgnitedAt?: Date;

  // Relationships
  parentSeed?: SeedId;       // What this grew from
  childSeeds: SeedId[];      // What grew from this
  relatedSeeds: SeedId[];    // Connected ideas
  linkedArtifacts: ArtifactId[];  // Mantle artifacts referenced

  // Hearth connection
  ignitedInto?: ProjectId[]; // Hearth projects this seed has spawned
  ignitionCount: number;

  // Metadata
  tags: string[];
  source: SeedSource;
}

type SeedContent =
  | { type: 'text'; value: string }
  | { type: 'voice'; value: AudioBlob; transcript?: string }
  | { type: 'sketch'; value: ImageBlob; description?: string }
  | { type: 'reference'; value: URL; notes?: string }
  | { type: 'composite'; parts: SeedContent[] };

type SeedMood =
  | 'spark'         // Flash of inspiration, very raw
  | 'glow'          // Developing idea, gaining clarity
  | 'warmth'        // Mature idea, almost ready
  | 'blaze'         // Ready to ignite into a project
  | 'ember'         // Cooling down, parked for later
  | 'ash';          // Abandoned or consumed

type SeedStatus =
  | 'active'        // Growing, being nurtured
  | 'dormant'       // Parked, waiting
  | 'ignited'       // Has spawned a Hearth project
  | 'consumed'      // Fully realized, no longer a seed
  | 'abandoned';    // Deliberately discarded

type SeedSource =
  | 'user'          // User created directly
  | 'hey'           // Hey suggested based on patterns
  | 'hearth'        // Emerged from a Hearth project (byproduct)
  | 'grove'         // Inspired by someone else's creation
  | 'external';     // Imported from outside
```

### 2. SeedGrowth

Seeds are not static — they grow over time.

```typescript
interface SeedGrowth {
  // Organic growth
  addThought(seedId: SeedId, thought: string): Seed;
  refine(seedId: SeedId, refinement: SeedRefinement): Seed;

  // Structural growth
  split(seedId: SeedId, splitPoint: string): [Seed, Seed];  // One seed → two
  merge(seeds: SeedId[]): Seed;                               // Many seeds → one
  branch(seedId: SeedId, direction: string): Seed;            // One seed → variant

  // Contextual growth
  attachReference(seedId: SeedId, reference: ArtifactId): Seed;
  linkSeed(seedId: SeedId, relatedSeedId: SeedId): Seed;
  addTag(seedId: SeedId, tag: string): Seed;
}
```

### 3. Ignition

The act of turning a seed into a Hearth project.

```typescript
interface Ignition {
  // Prepare seed for Hearth
  prepareForIgnition(seedId: SeedId): IgnitionPlan;

  // Ignite — create Hearth project from seed
  ignite(seedId: SeedId, config?: IgnitionConfig): ProjectId;

  // Post-ignition
  trackIgnition(seedId: SeedId, projectId: ProjectId): IgnitionRecord;
}

interface IgnitionPlan {
  seed: Seed;
  suggestedBoundaries: Boundary[];
  suggestedComponents: ComponentSuggestion[];
  readinessScore: number;  // 0.0 → 1.0
  gaps: string[];          // What's missing before ignition
}

interface IgnitionConfig {
  boundaries?: Boundary[];
  spaceName?: string;
  priority?: number;
  inheritFromSeed?: boolean;  // Carry seed metadata into Hearth
}
```

---

## Architecture

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                               EMBER                                          │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                              │
│  ┌─────────────────────────────────────────────────────────────────────┐    │
│  │                        SEED BANK                                     │    │
│  │  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌──────────┐           │    │
│  │  │  Spark   │  │   Glow   │  │  Warmth  │  │  Blaze   │  ...      │    │
│  │  │  Seeds   │  │  Seeds   │  │  Seeds   │  │  Seeds   │           │    │
│  │  └──────────┘  └──────────┘  └──────────┘  └──────────┘           │    │
│  └─────────────────────────────────────────────────────────────────────┘    │
│                                                                              │
│  ┌─────────────────────────────────────────────────────────────────────┐    │
│  │                      GROWTH ENGINE                                    │    │
│  │  Thought Addition → Refinement → Split → Merge → Branch              │    │
│  └─────────────────────────────────────────────────────────────────────┘    │
│                                                                              │
│  ┌─────────────────────────────────────────────────────────────────────┐    │
│  │                      RELATIONSHIP WEB                                 │    │
│  │  Seed ↔ Seed → Semantic connections                                 │    │
│  │  Seed ↔ Artifact → Mantle references                                │    │
│  │  Seed ↔ Project → Hearth connections                                │    │
│  └─────────────────────────────────────────────────────────────────────┘    │
│                                                                              │
│  ┌─────────────────────────────────────────────────────────────────────┐    │
│  │                      IGNITION ENGINE                                  │    │
│  │  Readiness Assessment → Boundary Suggestion → Hearth Activation      │    │
│  └─────────────────────────────────────────────────────────────────────┘    │
│                                                                              │
│  ┌─────────────────────────────────────────────────────────────────────┐    │
│  │                      TEMPERATURE SYSTEM                               │    │
│  │  Activity → Temperature → Mood → Priority                            │    │
│  └─────────────────────────────────────────────────────────────────────┘    │
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## Temperature System

Seeds have a **temperature** that reflects their creative energy. Temperature is influenced by activity and decays over time.

```typescript
interface TemperatureSystem {
  // Heating events
  onThoughtAdded(seedId: SeedId): void;       // +0.1
  onRefinement(seedId: SeedId): void;         // +0.15
  onReferenceAttached(seedId: SeedId): void;  // +0.05
  onLinked(seedId: SeedId): void;             // +0.08
  onViewed(seedId: SeedId): void;             // +0.02

  // Cooling
  decay(): void;  // All seeds cool slightly over time

  // Mood transitions
  temperature: number;
  mood: SeedMood;

  // Thresholds
  // 0.0 - 0.2: ember (cooling)
  // 0.2 - 0.4: spark (raw)
  // 0.4 - 0.6: glow (developing)
  // 0.6 - 0.8: warmth (mature)
  // 0.8 - 1.0: blaze (ready to ignite)
}
```

---

## Seed Lifecycle

```
INSPIRATION
    │
    ▼
┌─────────────────────────────────────────────────────────────────────┐
│  CAPTURE                                                              │
│  User speaks, types, sketches, or Hey suggests                       │
│  → Seed created with mood: "spark"                                   │
└─────────────────────────────────────────────────────────────────────┘
    │
    ▼
┌─────────────────────────────────────────────────────────────────────┐
│  NURTURE                                                              │
│  Add thoughts, refine, attach references, link to other seeds        │
│  → Temperature rises, mood evolves: spark → glow → warmth → blaze   │
└─────────────────────────────────────────────────────────────────────┘
    │
    ├───► SPLIT (one idea becomes two)
    ├───► MERGE (two ideas become one)
    ├───► BRANCH (explore a variant)
    │
    ▼
┌─────────────────────────────────────────────────────────────────────┐
│  IGNITION (optional)                                                  │
│  Seed is ready → Activate as Hearth project                           │
│  → Seed status: "ignited", linked to ProjectId                       │
│  → Seed remains in Ember (can ignite again, spawning variants)       │
└─────────────────────────────────────────────────────────────────────┘
    │
    ▼
┌─────────────────────────────────────────────────────────────────────┐
│  AFTERMATH                                                            │
│  Hearth produces artifacts → Mantle stores them                       │
│  → Artifacts linked back to originating seed                          │
│  → New ideas from Hearth project → New seeds in Ember (cycle)        │
└─────────────────────────────────────────────────────────────────────┘
```

---

## Relationship to Other Components

```
User ──captures idea──► Ember
Hey ──suggests seed──► Ember
Hearth ──byproduct ideas──► Ember
Grove ──inspiration from others──► Ember

Ember ──ignites──► Hearth (seed becomes project)
Ember ──references──► Mantle (seeds link to artifacts)
Ember ──observed by──► Veil (seed bank visualization)
Ember ──shared via──► Grove (publish seeds)
```

**Key Principle**: Ember is an **input** to the ecosystem, not an output. Seeds flow *into* Hearth, not out of it. Ember is where creation begins — before boundaries are set, before components are invented, before anything exists.

---

## Design Principles

1. **Low Friction Capture**: Creating a seed should be as easy as having a thought. No forms, no templates — just express.

2. **Organic Growth**: Seeds grow naturally through accumulation of thoughts, not through forced structuring.

3. **Patience**: Not every seed needs to ignite. Ember is comfortable with ideas that simmer for weeks, months, or forever.

4. **Cross-Pollination**: Seeds from different contexts can connect, combine, and inspire each other. A game design seed might combine with a music composition seed.

5. **Full Circle**: Hearth projects can produce new seeds (byproduct ideas), creating a creative cycle: seed → project → artifact → new seed.

---

## MVP Scope

For the initial implementation:

- [x] Seed creation (text-based)
- [x] Basic seed management (list, view, edit, delete)
- [x] Simple ignition (convert seed to Hearth project)
- [x] Temperature tracking (basic activity-based)
- [ ] Voice/sketch capture (post-MVP)
- [ ] Seed splitting/merging (post-MVP)
- [ ] Hey-suggested seeds (post-MVP)
- [ ] Cross-pollination discovery (post-MVP)

---

*Last Updated: 2026-05-22*
*Status: Core Design Defined*
*See also: [ADR-008](../../DECISIONS/008-four-complementary-components.md)*
