# ADR-005: Universal Product Scope

**Date**: 2026-05-15
**Status**: Accepted

## Context

Should Heya factories be limited to software/code generation, or should they support any type of product?

## Options Considered

1. **Code-only**: Focus on software development (like Cursor, Copilot)
2. **Digital products**: Software + content + design
3. **Universal**: Any type of product, including physical specs

## Decision

**Universal**: Heya factories can create any type of product.

## Rationale

- The factory metaphor is domain-agnostic — a factory can produce anything
- Limiting to code would make Heya a "better Cursor" instead of a fundamentally new category
- The AI's core capabilities (reasoning, memory, verification) are applicable to any creative task
- The human-AI hybrid model works for any domain where quality decisions matter
- Early focus may be on software (for validation), but the architecture must not assume code-only

## Product Categories

| Category | Examples |
|----------|---------|
| Software | Games, SaaS apps, CLI tools, libraries |
| Design | UI/UX mockups, brand identities, architectural plans |
| Content | Courses, video scripts, music, novels, marketing copy |
| Strategy | Business plans, go-to-market strategies, investment theses |
| Knowledge | Research reports, technical documentation, patent drafts |
| Physical | Hardware specs, 3D print models, supply chain designs |

## Consequences

- Verification system must be extensible (not just "compile + test")
- Artifact types must be a schema, not an enum
- The factory design guide must include non-software examples
- Tool system must support domain-specific tools beyond compilers and linters
