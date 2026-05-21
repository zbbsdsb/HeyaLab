# ADR-007: Hearth Ecosystem Paradigm

**Date**: 2026-05-22
**Status**: Accepted
**Supersedes**: [ADR-001](./001-ai-factory-metaphor.md)

## Context

The "AI Factory" metaphor (ADR-001) served its purpose during the initial architecture phase by communicating that Heya is about production, not just conversation. However, as the design matured through research (252 papers across 10 categories) and the formal design documents in `heya2u/`, several limitations of the factory metaphor became apparent:

1. **Factory implies predefined processes**: A factory has assembly lines, SOPs, and fixed workflows. But Heya's core insight is that agents *invent* components on demand, not invoke predefined ones. When a user says "write a book," there is no predetermined workflow — agents create the necessary components, they interact and compete, and good structures emerge organically.

2. **Factory implies top-down design**: Factories are designed by engineers. But Heya's creation process is *emergent* — good patterns persist and spread, bad patterns die out, and the system evolves through use.

3. **Factory lacks relationship**: A factory is a building. But Heya includes Hey — a companion with identity, persistent memory, and growth. Users define Hey's role, and Hey coordinates Hearth and Agents on the user's behalf. This relationship dimension has no place in the factory metaphor.

4. **Factory is rigid**: Factories produce predictable outputs. But Heya embraces emergent possibilities that arise from simple interactions within boundaries — capabilities no one explicitly designed.

## Decision

Heya is redefined as a **Hearth ecosystem** consisting of three layers:

1. **Hearth** — The creation space. Not a workflow engine, but a space where creativity emerges. Agents invent components within boundaries, and capabilities evolve through natural selection. Hearth defines what is *possible* (through boundaries), not what *exists* (through predefined components).

2. **Hey** — The user's AI companion. An independent program with identity, persistent memory, and growth. Users define Hey's role. Hey coordinates Hearth and Agents on the user's behalf, witnesses the creation process, and provides continuity across sessions.

3. **Agents** — Stateless, replaceable execution units. Tools that Hearth or Hey can invoke for specific tasks. Agents are the inventors — they create components within Hearth's boundaries.

The core philosophy shifts from:

| Old (Factory) | New (Hearth Ecosystem) |
|---|---|
| AI organizes itself into a factory | Agents invent components within boundaries |
| Predefined assembly lines | Self-organizing interactions |
| Fixed components | Evolving species |
| Controlled processes | Natural selection |
| Predictable outputs | Emergent possibilities |
| User operates the factory | User creates alongside Hey |

## Rationale

- **Boundaries, not prescriptions**: Hearth defines what is possible, not what exists. Within boundaries, everything is permitted. This enables infinite possibilities while maintaining safety and resource constraints.

- **Invention, not invocation**: The power lies in agents creating components on demand, not calling pre-built ones. This is what gives Heya infinite possibilities — it is not limited by what its creators imagined.

- **Emergence, not design**: Good structures evolve through trial and interaction, not top-down engineering. When a user says "write a book," the writing workflow emerges from component interactions, not from a predetermined template.

- **Ecosystem, not factory**: Complex capabilities arise from simple interactions, like species in an ecosystem. The most powerful capabilities in Hearth are not designed — they emerge.

- **Companion, not tool**: Hey provides relationship, continuity, and growth — addressing the loneliness of creation. Hey is not just an interface to Hearth; it is a partner in the creative process.

The factory metaphor was not wrong for its time — it established that Heya is about production, not just conversation. The ecosystem metaphor preserves that intent while adding emergence, relationship, and evolution.

## Consequences

- All top-level documentation (VISION.md, ROADMAP.md, README.md, CONTRIBUTING.md) must be updated to use the Hearth+Hey paradigm.

- **ADR-001** is superseded by this ADR.

- **ADR-002** ("AI is the factory") is conceptually superseded — the AI is now the Hearth ecosystem. However, the core insight (AI transforms itself based on intent, rather than being a static tool) remains valid.

- **ADR-003** (Human-AI Hybrid), **ADR-004** (Intent-Driven Design), and **ADR-005** (Universal Product Scope) remain valid — their principles translate directly to the new paradigm.

- **ADR-006** (Adopted Design Patterns) requires review: the "SOP Encoding for Factory Pipelines" pattern (item 1) and the "Explicitly Rejected: Emergent Social Behavior" (item 3) may need reconsideration under the emergence paradigm. This should be addressed in a future ADR.

- The `ARCHITECTURE/` documents (01-06) are historical and reference the factory metaphor. They are superseded by the `heya2u/` design documents. A future cleanup may formally deprecate them.

- The product tree strategy (RUS → Van → Product G/H → HeyaCore) remains valid — it describes the development approach, not the architecture.
