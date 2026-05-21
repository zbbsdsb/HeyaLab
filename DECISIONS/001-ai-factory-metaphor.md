# ADR-001: Heya is an AI Factory

**Date**: 2026-05-15
**Status**: Superseded by [ADR-007](./007-hearth-ecosystem-paradigm.md)

## Context

We need a clear, memorable way to describe what Heya is and differentiate it from existing AI tools (chatbots, code assistants, agent frameworks, workflow engines).

## Decision

Heya is defined as an **AI Factory** — a system where users design factories with AI assistance, then use those factories to create products.

## Rationale

- The "factory" metaphor is intuitive: factories have inputs, processes, quality control, and outputs
- It communicates that Heya is about **production**, not just conversation
- It differentiates from chatbots (factories produce things), code assistants (factories create any product type), and agent frameworks (factories are designed, not coded)
- The metaphor scales: a factory can be simple (one pipeline) or complex (multiple product lines)

## Consequences

- All documentation and communication should use the factory metaphor consistently
- The metaphor should not be over-stretched (e.g., don't call memory a "warehouse" just for the sake of the metaphor)
- When the metaphor doesn't fit, use plain technical language instead

---

> **Note**: This ADR defined the original "AI Factory" metaphor. It has been superseded by [ADR-007](./007-hearth-ecosystem-paradigm.md), which documents the paradigm shift to the "Hearth ecosystem" model. The factory metaphor served its purpose during initial architecture definition but was replaced as the design matured to embrace emergence, invention, and companion relationships.
