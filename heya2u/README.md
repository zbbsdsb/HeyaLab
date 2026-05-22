# Heya2U - Heya Official Design & Planning

> This directory contains the official design documents for Heya, to be handed over to the community for development.
> The HeyaLab repository is responsible for research and planning; this directory contains the deliverables.

---

## Heya Architecture Overview

Heya consists of six core components, organized around the Hearth metaphor:

```
┌─────────────────────────────────────────────────────────────────────────┐
│                            HEYA ECOSYSTEM                                │
├─────────────────────────────────────────────────────────────────────────┤
│                                                                          │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐                 │
│  │    HEARTH    │  │     HEY      │  │    MANTLE    │                 │
│  │  Creation    │  │  Companion   │  │  Artifacts   │                 │
│  │  Space       │  │              │  │  Home        │                 │
│  │              │  │              │  │              │                 │
│  │  Where agents│  │  Your AI     │  │  Where what  │                 │
│  │  invent and  │  │  partner who │  │  has been    │                 │
│  │  capabilities│  │  grows with  │  │  created is  │                 │
│  │  emerge      │  │  you         │  │  preserved   │                 │
│  └──────────────┘  └──────────────┘  └──────────────┘                 │
│                                                                          │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐                 │
│  │     VEIL     │  │    EMBER     │  │    GROVE     │                 │
│  │  Perception  │  │  Creative    │  │  Shared      │                 │
│  │  Layer       │  │  Seeds       │  │  Space       │                 │
│  │              │  │              │  │              │                 │
│  │  How you see │  │  Where ideas │  │  Where       │                 │
│  │  and         │  │  live before │  │  creators    │                 │
│  │  understand  │  │  they become │  │  collaborate │                 │
│  │  the system  │  │  projects    │  │  and share   │                 │
│  └──────────────┘  └──────────────┘  └──────────────┘                 │
│                                                                          │
└─────────────────────────────────────────────────────────────────────────┘
```

### Component Relationships

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

### Key Relationships

| Component | Role | Analogy |
|-----------|------|---------|
| **Hearth** | Core creation space | The hearth fire where creation happens |
| **Hey** | User's AI companion | Your partner sitting by the hearth |
| **Mantle** | Artifact home | The shelf above the hearth where creations are displayed |
| **Veil** | Perception layer | The shimmering air that reveals what matters |
| **Ember** | Creative seeds | Glowing remnants with potential to ignite new creations |
| **Grove** | Shared space | A gathering of hearths where community forms |

### Agents

Agents are sub-components of Hearth — stateless, replaceable execution units that invent components within Hearth's boundaries. They are the evolving life within the creation space.

---

## HeyaCore Architecture

HeyaCore consists of two parts:

```
┌─────────────────────────────────────────┐
│              HeyaCore                    │
├─────────────────────────────────────────┤
│  Boundary                                │
│  - Expands as AI capabilities improve    │
│  - Expands as Foundation matures         │
│  - Defines what Heya can do              │
├─────────────────────────────────────────┤
│  Foundation                              │
│  - Core runtime                          │
│  - Memory system                         │
│  - Tool system                           │
│  - Communication system                  │
└─────────────────────────────────────────┘
```

**Core Principle:** The boundary is not fixed. Today's "impossible" is tomorrow's "basic feature."

---

## Document Index

### Core Components

| Document | Content | Status |
|----------|---------|--------|
| [hearth/](./hearth/) | Hearth — The creation space | ✅ Complete |
| [hearth/ARCHITECTURE.md](./hearth/ARCHITECTURE.md) | Core architecture and subsystems | ✅ Complete |
| [hearth/BOUNDARIES.md](./hearth/BOUNDARIES.md) | Boundary types and enforcement | ✅ Complete |
| [hearth/COMPONENT-CREATION.md](./hearth/COMPONENT-CREATION.md) | How agents invent components | ✅ Complete |
| [hearth/EMERGENCE.md](./hearth/EMERGENCE.md) | How patterns self-organize | ✅ Complete |
| [hearth/EVOLUTION.md](./hearth/EVOLUTION.md) | Component fitness and selection | ✅ Complete |
| [hearth/STATE.md](./hearth/STATE.md) | Shared state and memory | ✅ Complete |
| [hearth/INTERFACES.md](./hearth/INTERFACES.md) | User, Hey, and Agent interfaces | ✅ Complete |
| [hey/](./hey/) | Hey — User's AI companion | 🔄 In Design |
| [hey/OASIS-HEY-PROTOCOL.md](./hey/OASIS-HEY-PROTOCOL.md) | The covenant between user and Hey | ✅ Complete |
| [hey/MEMORY-SYSTEM.md](./hey/MEMORY-SYSTEM.md) | Hey's graph-routed memory system | ✅ Complete |
| [mantle/](./mantle/) | Mantle — The artifact home | ✅ Complete |
| [veil/](./veil/) | Veil — The perception layer | ✅ Complete |
| [ember/](./ember/) | Ember — The creative seeds | ✅ Complete |
| [grove/](./grove/) | Grove — The shared space | ✅ Complete |

### Decision Records

| ADR | Decision | Status |
|-----|----------|--------|
| [007](../DECISIONS/007-hearth-ecosystem-paradigm.md) | Hearth ecosystem paradigm | ✅ Accepted |
| [008](../DECISIONS/008-four-complementary-components.md) | Four complementary components | ✅ Accepted |

---

## Design Principles

1. **Hearth First**: The core creation capability is paramount
2. **Companion Layer**: Hey adds relationship and continuity, but is optional
3. **Artifact Native**: Mantle gives creations a first-class home
4. **Perception Adaptive**: Veil reveals what matters, hides what doesn't
5. **Seed Before Fire**: Ember captures ideas before they become projects
6. **Community When Ready**: Grove enables collaboration without forcing it
7. **Natural Language Driven**: Everything defined and operated through natural language
8. **Continuous Evolution**: System improves through usage
9. **Community Built**: Design documents are open; community participates in development

---

## Usage Modes

Heya supports multiple usage modes:

### Mode 1: Direct Hearth (Power Users)
```
User ↔ Hearth → Mantle
```
- User operates Hearth directly
- Full control, maximum efficiency
- No companion layer overhead

### Mode 2: With Hey (Recommended)
```
User ↔ Hey ↔ Hearth → Mantle
        ↓
     Ember (creative seeds)
     Veil (perception)
```
- User works with Hey as companion
- Hey coordinates Hearth, nurtures Ember, presents via Veil
- Relationship, memory, and continuity

### Mode 3: Collaborative (With Grove)
```
User A ↔ Hey A ─┐
                ├─► Shared Hearth → Shared Mantle
User B ↔ Hey B ─┘        ↓
                    Grove (community)
```
- Multiple users co-create in shared spaces
- Artifacts and seeds flow between ecosystems
- Community discovery and learning

---

## MVP Priority

Components in implementation priority order:

| Priority | Component | Rationale |
|----------|-----------|-----------|
| 1 | Hearth | Core creation — the fire itself |
| 2 | Mantle | Artifact storage — essential for any usable product |
| 3 | Ember | Creative seeds — significantly enhances user experience |
| 4 | Veil | Visualization — can start minimal, grow over time |
| 5 | Grove | Collaboration — post-MVP, requires multi-user infrastructure |

---

*Last Updated: 2026-05-22*
