# Heya2U - Heya Official Design & Planning

> This directory contains the official design documents for Heya, to be handed over to the community for development.
> The HeyaLab repository is responsible for research and planning; this directory contains the deliverables.

---

## Heya Architecture Overview

Heya consists of three core components:

```
┌─────────────────────────────────────────────────────────┐
│                        Heya                              │
├─────────────────────────────────────────────────────────┤
│  Hearth (Core)                                          │
│  - The creation engine                                  │
│  - Users can operate directly OR through Hey             │
│  - Creates artifacts, executes workflows                 │
├─────────────────────────────────────────────────────────┤
│  Hey (Companion)                                         │
│  - User's AI companion                                   │
│  - Grows, accompanies, and witnesses                     │
│  - Can invoke Agents on behalf of the user               │
│  - Optional but recommended                              │
├─────────────────────────────────────────────────────────┤
│  Agents (Tools)                                          │
│  - Executed by Hearth or invoked by Hey                 │
│  - Perform specific tasks                                │
│  - Stateless, replaceable                                │
└─────────────────────────────────────────────────────────┘
```

### Key Relationships

| Component | Role | Relationship |
|-----------|------|--------------|
| **Hearth** | Core creation engine | Users can use directly; Hey can coordinate |
| **Hey** | Companion | Optional layer that adds relationship and continuity |
| **Agents** | Execution units | Tools that Hearth or Hey can invoke |

**Analogy:**
- Hearth = The hearth fire where creation happens—warm, central, alive
- Hey = Your partner/brother sitting by the hearth with you
- Agents = Hired helpers that either you or Hey can call upon

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

| Document | Content | Status |
|----------|---------|--------|
| [hey/](./hey/) | Hey - User's AI companion | 🔄 In Design |
| [hey/OASIS-HEY-PROTOCOL.md](./hey/OASIS-HEY-PROTOCOL.md) | The covenant between user and Hey | ✅ Complete |
| [hey/MEMORY-SYSTEM.md](./hey/MEMORY-SYSTEM.md) | Hey's graph-routed memory system | ✅ Complete |
| (TBD) | Hearth Core Design | ⏳ Pending |
| (TBD) | Agent System Design | ⏳ Pending |
| (TBD) | Boundary Definition | ⏳ Pending |

---

## Design Principles

1. **Hearth First**: The core creation capability is paramount
2. **Companion Layer**: Hey adds relationship and continuity, but is optional
3. **Flexible Interaction**: Users can work directly with Hearth or through Hey
4. **Natural Language Driven**: Everything defined and operated through natural language
5. **Continuous Evolution**: System improves through usage
6. **Community Built**: Design documents are open; community participates in development

---

## Usage Modes

Heya supports multiple usage modes:

### Mode 1: Direct Hearth (Power Users)
```
User ↔ Hearth → Artifacts
```
- User operates Hearth directly
- Full control, maximum efficiency
- No companion layer overhead

### Mode 2: With Hey (Recommended)
```
User ↔ Hey ↔ Hearth → Artifacts
        ↓
     Agents
```
- User works with Hey as companion
- Hey can invoke Agents and coordinate Hearth
- Relationship, memory, and continuity

### Mode 3: Hybrid
```
User → Hearth (direct for some tasks)
  ↓
Hey → Hearth (for complex workflows)
```
- User chooses per-task whether to use Hey
- Seamless switching between modes

---

*Last Updated: 2026-05-18*
