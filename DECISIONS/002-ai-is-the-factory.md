# ADR-002: AI Is the Factory

**Date**: 2026-05-15
**Status**: Accepted

## Context

We debated whether the factory is a container that holds AI tools, or whether the AI itself transforms into the factory.

## Options Considered

1. **Factory as container**: The factory is a configuration/system, and AI models are tools inside it
2. **AI as the factory**: The AI transforms itself into a factory structure based on user intent
3. **Separation**: The factory is an orchestration layer independent of any specific AI

## Decision

**AI is the factory.** The AI transforms itself into the factory based on what the user wants to create.

## Rationale

- If the factory is just a container, users need to configure it — this creates a barrier to entry
- If the AI is the factory, users only need to describe their intent, and the AI derives the optimal structure
- This makes Heya fundamentally different from workflow engines (Dify, Temporal) where humans design the flow
- The AI can reorganize itself for different tasks — a "game engine factory" and a "marketing factory" are the same AI with different capability assemblies

## Consequences

- Factory design is an AI capability, not a user configuration task
- The internal representation (FactoryBlueprint) exists for reproducibility and transparency, but users interact through conversation
- The AI must be capable of reasoning about its own structure and capabilities
- Model routing becomes critical — different tasks may need different models within the same factory
