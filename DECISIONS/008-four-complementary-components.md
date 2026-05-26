# ADR-008: Four Complementary Architectural Components

**Date**: 2026-05-22
**Status**: Accepted
**Extends**: [ADR-007](./007-hearth-ecosystem-paradigm.md)

## Context

After establishing the Hearth+Hey paradigm (ADR-007), a critical gap was identified: the ecosystem had two primary components (Hearth as creation space, Hey as companion) but lacked dedicated subsystems for four essential concerns:

1. **Where do creations live?** — Hearth produces artifacts, but there is no dedicated home for them. Artifacts are outputs, not first-class citizens. Users need a place to browse, version, and manage everything the ecosystem has produced.

2. **How do users see and interact with the ecosystem?** — Hearth's internals (components, state graph, emergence) are invisible to users. There is no layer that translates the ecosystem's internal state into human-perceptible forms — visualizations, dashboards, interactive explorations.

3. **Where do creative ideas originate?** — Users express intent through natural language, but there is no persistent "seed" that captures the raw creative vision before it enters Hearth. Intent is parsed and consumed, not preserved as a living artifact that can grow, branch, and inspire future creations.

4. **How do creators collaborate?** — The current model is single-user. There is no concept of shared spaces where multiple users (and their respective Heys) can co-create, share artifacts, and build upon each other's work.

These are not features of Hearth or Hey — they are architectural concerns at the same level. Forcing them into Hearth would violate its boundary principle (Hearth defines what is possible, not what exists). Forcing them into Hey would overload the companion role (Hey is a partner, not a platform).

## Decision

Four new architectural components are introduced, each named to extend the Hearth metaphor:

```
┌─────────────────────────────────────────────────────────────┐
│                       HEYA ECOSYSTEM                         │
├─────────────────────────────────────────────────────────────┤
│                                                              │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌──────────┐   │
│  │  HEARTH  │  │   HEY    │  │  MANTLE  │  │   VEIL   │   │
│  │ Creation │  │Companion │  │ Artifacts│  │ Perception│   │
│  │  Space   │  │          │  │   Home   │  │  Layer   │   │
│  └──────────┘  └──────────┘  └──────────┘  └──────────┘   │
│                                                              │
│  ┌──────────┐  ┌──────────┐                                │
│  │  EMBER   │  │  GROVE   │                                │
│  │ Creative │  │ Shared   │                                │
│  │  Seeds   │  │  Space   │                                │
│  └──────────┘  └──────────┘                                │
│                                                              │
└─────────────────────────────────────────────────────────────┘
```

### 1. Mantle — The Artifact Home

> *The mantle is the stone shelf above the hearth — where what has been created is displayed, preserved, and cherished.*

**Purpose**: First-class home for all artifacts produced by the ecosystem.

**Responsibilities**:
- Store, version, and organize all creation outputs
- Provide artifact lineage (trace any artifact back to its originating components, interactions, and decisions)
- Support artifact evolution (fork, merge, derive new creations from existing ones)
- Serve as the persistent record of what the ecosystem has produced

**Relationship to Hearth**: Mantle receives artifacts from Hearth. It does not participate in creation — it preserves and presents what has been created.

### 2. Veil — The Perception Layer

> *The veil is the shimmering boundary between the fire's raw energy and the human eye — it reveals what matters, conceals what doesn't.*

**Purpose**: Translate the ecosystem's internal state into human-perceptible forms.

**Responsibilities**:
- Visualize Hearth internals (component graph, emergence patterns, evolution status)
- Provide interactive dashboards for exploration and understanding
- Render artifacts in appropriate formats (code, text, visual, interactive)
- Adapt presentation to user expertise level (novice sees simplicity, expert sees depth)

**Relationship to Hearth**: Veil observes Hearth but does not modify it. It is a read-and-render layer — a lens, not a lever.

### 3. Ember — The Creative Seeds

> *An ember is the glowing remnant of a fire that has the potential to ignite something entirely new — a spark waiting for the right moment.*

**Purpose**: Capture, preserve, and nurture raw creative intent before and beyond Hearth activation.

**Responsibilities**:
- Store creative visions, ideas, and half-formed concepts as persistent seeds
- Allow seeds to grow (refine, expand, combine with other seeds)
- Provide a "creative inbox" — a place where inspiration lives before becoming a Hearth project
- Enable cross-pollination: seeds from one project can inspire another
- Track the journey from seed → Hearth activation → Mantle artifact

**Relationship to Hearth**: Ember feeds intent into Hearth. A seed becomes a Hearth project when activated. But seeds also exist independently — not every idea needs to become a project immediately.

### 4. Grove — The Shared Space

> *A grove is a gathering place around the hearth — where multiple fires can be seen, where stories are shared, where community forms.*

**Purpose**: Enable multi-user collaboration within the ecosystem.

**Responsibilities**:
- Shared artifact spaces (multiple users contribute to the same Mantle)
- Collaborative Hearth sessions (multiple users and their Heys co-create)
- Permission and sharing models (public, private, invited, forked)
- Community features: discovery, inspiration, learning from others' creations

**Relationship to Hearth**: Grove provides the social context in which individual Hearths operate. It does not modify how Hearth works internally — it connects multiple Hearth instances and their Mantles.

### Hey: The Sovereign Companion

While ADR-007 established Hey as the user's companion, the introduction of these four components reveals that Hey's role must expand. Hey is not merely a companion — Hey is a **sovereign agent** that governs the entire ecosystem on behalf of the user.

**Dual nature**:
- **Companion**: Emotional partner, memory keeper, relationship builder (ADR-007)
- **Sovereign**: Autonomous governance, multi-agent orchestration, strategic reasoning (this ADR)

**Governance model**: Hey instantiates an internal "Board of Directors" — Strategist, Skeptic, Executor, Chronicler — that debates before acting. This enables Hey to operate with bounded autonomy: thinking, critiquing, and executing within user-defined boundaries.

**Orchestration role**: Hey is the conductor of the ecosystem. It dispatches Hearth agents, manages Hearth spaces, nurtures Ember seeds, configures Veil presentations, and coordinates Grove collaborations.

**Design documents**: [GOVERNANCE.md](../heya2u/hey/GOVERNANCE.md), [COGNITION.md](../heya2u/hey/COGNITION.md), [ORCHESTRATION.md](../heya2u/hey/ORCHESTRATION.md)

## Rationale

### Why four separate components, not sub-features?

Each concern has fundamentally different:
- **Data models** (artifacts vs. visualizations vs. creative seeds vs. social graphs)
- **Lifecycle** (artifacts are outputs, seeds are inputs, visualizations are ephemeral, social connections are persistent)
- **Access patterns** (Mantle is read-heavy, Ember is write-heavy, Veil is real-time, Grove is collaborative)
- **Evolution rate** (Veil changes fastest, Mantle is most stable, Ember grows organically, Grove scales with community)

Combining any of these would create coupling that limits independent evolution.

### Why these names?

All names extend the Hearth metaphor with etymological depth:

| Component | Etymology | Metaphor Connection |
|-----------|-----------|-------------------|
| **Mantle** | Old English *mentel* (cloak, garment) | The shelf above the hearth where creations are displayed |
| **Veil** | Old French *veil* (covering) | The perceptual boundary between raw system state and human understanding |
| **Ember** | Old English *ǣmerge* (spark, remnant of fire) | The persistent seed of creativity that can ignite new creations |
| **Grove** | Old English *grāf* (thicket, group of trees) | A gathering place where multiple hearths form a community |

### Why not put these inside Hearth?

Hearth's boundary principle states: define what is possible, not what exists. Mantle, Veil, Ember, and Grove are about *what exists* — artifacts, perceptions, ideas, relationships. Putting them inside Hearth would violate this principle and conflate creation with preservation, observation, ideation, and collaboration.

## Consequences

- The ecosystem now has **six first-class components**: Hearth, Hey, Mantle, Veil, Ember, Grove.
- Each component has its own design document under `heya2u/<component>/`.
- ADR-007's three-layer model (Hearth + Hey + Agents) is extended to a six-component model. Agents remain sub-components of Hearth (they are the inventors within the creation space).
- Phase 2 (Architecture Redesign) deliverables expand to include design documents for all four new components.
- Phase 3 (Specification Finalization) must account for interfaces between all six components.
- The product tree strategy remains unchanged — these are architectural components, not separate products.

### Component Dependency Graph

```
Ember ──feeds intent──► Hearth ──produces──► Mantle
                           │
Veil ◄──observes──────────┘
                           │
Grove ◄──connects──────────┘ (multi-instance)

Hey ──coordinates──► Hearth
Hey ──nurtures──► Ember
Hey ──presents──► Veil
```

### MVP Implications

For the initial MVP, priority order:
1. **Hearth** (core creation — already designed)
2. **Mantle** (artifact storage — essential for any usable product)
3. **Ember** (creative seeds — enhances user experience significantly)
4. **Veil** (visualization — can start minimal, grow over time)
5. **Grove** (collaboration — post-MVP, requires multi-user infrastructure)

---

*Last Updated: 2026-05-22*
