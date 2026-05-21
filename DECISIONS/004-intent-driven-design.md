# ADR-004: Intent-Driven Factory Design

**Date**: 2026-05-15
**Status**: Accepted

## Context

How should users create factories? Through configuration files, through conversation, or through a hybrid approach?

## Options Considered

1. **Declarative (YAML/JSON)**: Users write configuration files to define factories
2. **Conversational**: Users describe intent, AI generates factory design
3. **Hybrid**: Conversation generates initial design, declarative for refinement

## Decision

**Conversational-first**: Users describe intent in natural language, the AI derives and proposes the factory design. Configuration files exist as internal representations only.

## Rationale

- Configuration files create a barrier to entry — most users can't design a factory from scratch
- The AI is better at deriving capabilities from intent than humans are at specifying them
- Conversation allows clarification — the AI can ask questions when intent is ambiguous
- Internal configuration (FactoryBlueprint) still exists for reproducibility, version control, and programmatic access
- Power users can inspect and modify the internal representation if needed

## Consequences

- The Factory Designer component must be excellent at intent understanding and clarification
- The AI must present factory designs in a human-readable format (not raw YAML)
- The internal FactoryBlueprint schema must be well-documented for advanced users
- The system must support iterative refinement ("change the verification to include benchmarks")
