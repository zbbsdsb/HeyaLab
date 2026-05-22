# Veil — The Perception Layer

> The veil is the shimmering boundary between the fire's raw energy and the human eye — it reveals what matters, conceals what doesn't.

---

## What is Veil?

Veil is the perception layer of the Heya ecosystem. It translates the ecosystem's internal state — components, interactions, emergence patterns, evolution metrics — into human-perceptible forms. Veil does not modify the ecosystem; it **observes and renders**.

```
Traditional Systems:
  Dashboard → Predefined metrics, fixed views
  Log Viewer → Raw data, no interpretation
  Debug UI → Developer-centric, overwhelming

Veil:
  Adaptive Perception → Shows what matters to YOU,
  at the level of detail YOU need,
  in the format YOU understand
```

### Key Insight

A creation ecosystem generates enormous internal complexity — hundreds of components, thousands of interactions, emergent patterns, evolution cycles. Most of this complexity is noise to the user. Veil's job is to find the **signal** and present it in a way that enables understanding and decision-making.

---

## Core Concepts

### 1. Perception Level

Veil adapts its presentation based on who is looking and what they need.

```typescript
interface PerceptionLevel {
  novice: {
    // Show only: current task, progress, results
    // Hide: component internals, state graph, evolution
    focus: ['task_progress', 'results', 'next_steps'];
  };
  practitioner: {
    // Show: task + active components, recent decisions
    // Hide: full state graph, raw metrics
    focus: ['task_progress', 'active_components', 'decisions', 'results'];
  };
  architect: {
    // Show: full system state, emergence patterns, evolution
    // Full transparency
    focus: ['everything'];
  };
}
```

### 2. View

A specific rendering of ecosystem state for a specific purpose.

```typescript
interface VeilView {
  id: ViewId;
  type: ViewType;
  perceptionLevel: PerceptionLevel;
  dataSource: VeilDataSource;
  renderConfig: RenderConfig;
  refreshPolicy: RefreshPolicy;
}

type ViewType =
  | 'dashboard'        // Overview of current state
  | 'component_map'    // Visual graph of components and interactions
  | 'evolution_timeline' // How components have evolved
  | 'emergence_feed'   // Real-time feed of emergent patterns
  | 'artifact_preview' // Rendered output from Mantle
  | 'decision_log'     // History of decisions made
  | 'resource_monitor' // Resource usage and pressure
  | 'creation_flow'    // How a specific artifact was created
  | 'explorer';        // Free-form exploration of ecosystem state

interface VeilDataSource {
  // What to observe
  source: 'hearth' | 'mantle' | 'ember' | 'grove' | 'system';
  filters: VeilFilter[];
  aggregation: AggregationStrategy;
}

type RefreshPolicy =
  | 'realtime'    // WebSocket updates
  | 'polling'     // Periodic refresh
  | 'on_demand'   // Manual refresh
  | 'snapshot';   // Point-in-time capture
```

### 3. Rendering

How data is presented to the user.

```typescript
interface RenderConfig {
  format: RenderFormat;
  layout: LayoutStrategy;
  interactivity: InteractionLevel;
  accessibility: AccessibilityConfig;
}

type RenderFormat =
  | 'text'         // Plain text / markdown
  | 'visual'       // Charts, graphs, diagrams
  | 'interactive'  // Draggable, zoomable, clickable
  | 'narrative'    // Natural language description
  | 'mixed';       // Combination of formats

type InteractionLevel =
  | 'read_only'    // View only
  | 'explore'      // Click to drill down
  | 'manipulate'   // Rearrange, filter, customize
  | 'control';     // Can trigger actions through Veil
```

---

## Architecture

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                               VEIL                                           │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                              │
│  ┌─────────────────────────────────────────────────────────────────────┐    │
│  │                      OBSERVATION LAYER                               │    │
│  │  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌──────────┐           │    │
│  │  │  Hearth  │  │  Mantle  │  │  Ember   │  │  Grove   │           │    │
│  │  │ Observer │  │ Observer │  │ Observer │  │ Observer │           │    │
│  │  └──────────┘  └──────────┘  └──────────┘  └──────────┘           │    │
│  └─────────────────────────────────────────────────────────────────────┘    │
│                              │                                               │
│                              ▼                                               │
│  ┌─────────────────────────────────────────────────────────────────────┐    │
│  │                      ABSTRACTION LAYER                                │    │
│  │  Raw Events → Filtered Streams → Aggregated Models → Views           │    │
│  └─────────────────────────────────────────────────────────────────────┘    │
│                              │                                               │
│                              ▼                                               │
│  ┌─────────────────────────────────────────────────────────────────────┐    │
│  │                      PERCEPTION ENGINE                                │    │
│  │  User Profile → Perception Level → Relevance Filter → Priority       │    │
│  └─────────────────────────────────────────────────────────────────────┘    │
│                              │                                               │
│                              ▼                                               │
│  ┌─────────────────────────────────────────────────────────────────────┐    │
│  │                      RENDERING ENGINE                                │    │
│  │  View Templates → Format Selection → Layout → Output                 │    │
│  └─────────────────────────────────────────────────────────────────────┘    │
│                              │                                               │
│                              ▼                                               │
│  ┌─────────────────────────────────────────────────────────────────────┐    │
│  │                      PRESENTATION                                    │    │
│  │  Dashboard │ Component Map │ Timeline │ Feed │ Explorer │ ...        │    │
│  └─────────────────────────────────────────────────────────────────────┘    │
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## Core Views

### Dashboard

The default view when a user opens the ecosystem.

```typescript
interface DashboardView {
  // What's happening right now
  activeTask: TaskSummary;
  progress: ProgressIndicator;

  // What's emerging
  recentPatterns: EmergentPattern[];
  newComponents: Component[];

  // What needs attention
  pendingCheckpoints: Checkpoint[];
  resourceAlerts: Alert[];

  // What's been created
  recentArtifacts: ArtifactPreview[];
  evolutionHighlights: EvolutionEvent[];
}
```

### Component Map

Visual representation of the component ecosystem.

```typescript
interface ComponentMapView {
  // Graph visualization
  nodes: ComponentNode[];
  edges: InteractionEdge[];

  // Layout options
  layout: 'force_directed' | 'hierarchical' | 'clustered' | 'timeline';

  // Interaction
  selectComponent(id: ComponentId): ComponentDetail;
  highlightPath(from: ComponentId, to: ComponentId): void;
  filterByType(type: ComponentType): void;
  showEvolution(componentId: ComponentId): EvolutionView;
}
```

### Creation Flow

Trace how a specific artifact was created.

```typescript
interface CreationFlowView {
  artifactId: ArtifactId;

  // Timeline of creation
  steps: CreationStep[];

  // Components involved
  components: ComponentInvolvement[];

  // Decisions made
  decisions: DecisionRecord[];

  // Human interactions
  humanTouchpoints: HumanInteraction[];
}

interface CreationStep {
  timestamp: Date;
  type: 'component_execution' | 'interaction' | 'human_input' | 'emergence';
  description: string;
  involvedComponents: ComponentId[];
  output: string;
}
```

---

## Relationship to Other Components

```
Veil ◄──observes── Hearth (components, state, emergence, evolution)
Veil ◄──observes── Mantle (artifacts, lineage, versions)
Veil ◄──observes── Ember (seeds, growth, activation)
Veil ◄──observes── Grove (collaboration, sharing)

Veil ──renders to──► User (via dashboard, views, narratives)
Veil ──provides context──► Hey (enables better coordination)
```

**Key Principle**: Veil is a **lens**, not a **lever**. It observes and renders but never modifies. All actions triggered through Veil are delegated to the appropriate component (e.g., "approve checkpoint" goes to Hearth, "share artifact" goes to Grove).

---

## Design Principles

1. **Reveal, Don't Overwhelm**: Show what matters, hide what doesn't. Complexity is available on demand, not forced on the user.

2. **Adapt to the User**: A novice sees a progress bar. An architect sees the full component graph. Same system, different perceptions.

3. **Multiple Formats**: Some users prefer visual graphs, others prefer text narratives, others prefer interactive exploration. Veil supports all.

4. **Real-Time When It Matters**: Critical events (checkpoints, errors, emergence) are shown immediately. Background information is updated on demand.

5. **Actionable**: Every view should enable the user to take action — approve, redirect, explore deeper, share. Veil is not passive observation.

---

## MVP Scope

For the initial implementation:

- [x] Basic dashboard (active task, progress, recent artifacts)
- [x] Simple component list (not full graph)
- [x] Artifact preview (rendered output from Mantle)
- [ ] Interactive component map (post-MVP)
- [ ] Creation flow visualization (post-MVP)
- [ ] Emergence feed (post-MVP)
- [ ] Adaptive perception levels (post-MVP)

---

*Last Updated: 2026-05-22*
*Status: Core Design Defined*
*See also: [ADR-008](../../DECISIONS/008-four-complementary-components.md)*
