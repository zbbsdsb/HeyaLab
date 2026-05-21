# ADR-006: Adopted Design Patterns from Academic Research

**Date**: 2026-05-15
**Status**: Accepted

> **Note**: Items 1 (SOP Encoding for Factory Pipelines) and the rejection of "Emergent Social Behavior" (item 3) may need reconsideration under the Hearth ecosystem paradigm documented in [ADR-007](./007-hearth-ecosystem-paradigm.md). A future ADR will address whether these patterns should be adapted or reconsidered.
**References**: [RESEARCH/SYNTHESIS.md](../RESEARCH/SYNTHESIS.md)

## Context

After surveying 15 academic papers from 2023-2026, we need to formally document which design patterns we adopt, adapt, or reject. This prevents future debates and ensures consistency.

## Decision

We adopt design patterns from 5 primary papers, adapt patterns from 5 secondary papers, and explicitly reject patterns that conflict with our core principles.

## Adopted Patterns (from Tier 1 Papers)

### 1. SOP Encoding for Factory Pipelines
**Source**: MetaGPT (Hong et al., 2023)
**What we adopt**: Factory pipelines are encoded as structured procedures with defined roles, inputs, outputs, and verification gates. Communication between steps uses structured documents.
**Why**: Reduces hallucination, enables intermediate verification, makes factory behavior predictable.

### 2. Virtual Context Management for Memory
**Source**: MemGPT (Packer et al., 2023)
**What we adopt**: Fast/slow memory hierarchy with intelligent context window management. Working memory (fast) is in-context; episodic/semantic memory (slow) is retrieved on demand.
**Why**: Solves the context window limitation while maintaining project-level knowledge persistence.

### 3. Modular Memory Architecture
**Source**: CoALA (Sumers et al., 2023)
**What we adopt**: Four memory modules — working, episodic, semantic, artifact — each with distinct retrieval and storage semantics.
**Why**: Provides theoretical grounding for our memory tier design from cognitive science.

### 4. Stability-Plasticity Decomposition
**Source**: MemRL (Zhang et al., 2026)
**What we adopt**: Separate stable reasoning (factory structure, policies) from plastic memory (new experiences). Semantic memory is updated only after validation.
**Why**: Prevents the factory from forgetting core behaviors while still learning from experience.

### 5. Verbal Reinforcement Learning
**Source**: Reflexion (Shinn et al., 2023)
**What we adopt**: Human feedback is stored as language in episodic memory. AI reflects on feedback before subsequent attempts. No weight updates needed.
**Why**: Enables continuous improvement without model retraining.

### 6. Self-Evolution Feedback Loop
**Source**: Self-Evolving AI Agents Survey (Fang et al., 2025)
**What we adopt**: Four-component feedback loop: System Inputs → Agent System → Environment → Optimisers.
**Why**: Provides a unified framework for Heya's self-improvement mechanism.

## Adapted Patterns (from Tier 2 Papers)

### 7. Dynamic Capability Assembly (adapted from LATM)
**Source**: LATM (Cai et al., 2023)
**Original**: LLMs create reusable code-level tools
**Our adaptation**: AI assembles capability combinations (reasoning strategies + models + tools) per task, without writing tool code

### 8. Human-AI Checkpoint Protocol (adapted from AutoGen)
**Source**: AutoGen (Wu et al., 2023)
**Original**: Flexible human input integration in multi-agent conversations
**Our adaptation**: Structured checkpoint types with defined triggers, context, and response options

### 9. Multi-Layer Guidance (adapted from AgentInstruct)
**Source**: AgentInstruct (Crispino et al., 2023)
**Original**: Agents guide LLM reasoning for zero-shot improvement
**Our adaptation**: Three-layer model: human guides AI → AI guides itself (metacognition) → AI guides sub-tasks

### 10. Universal Verification Pipeline (adapted from SWE-agent)
**Source**: SWE-agent (Princeton NLP, 2024)
**Original**: Automated code verification on real repositories
**Our adaptation**: Multi-level verification for any artifact type (code, design, content, strategy, etc.)

## Explicitly Rejected Patterns

### 1. Fully Autonomous Agent Execution
**Source**: OpenHands, SWE-agent, Devin
**Why rejected**: Conflicts with ADR-003 (Human-AI Hybrid). Heya requires human checkpoints for high-stakes decisions.

### 2. Arbitrary Agent Communication Topology
**Source**: AutoGen
**Why rejected**: Unpredictable behavior. Heya uses structured pipelines with defined checkpoints.

### 3. Emergent Social Behavior
**Source**: Generative Agents
**Why rejected**: All factory actions must be traceable to user intent or human decisions. Emergent behavior is not acceptable.

### 4. Code-Only Artifact Scope
**Source**: SWE-agent, ChatDev
**Why rejected**: Conflicts with ADR-005 (Universal Product Scope). Heya supports any artifact type.

## Consequences

- All future architecture decisions should reference these adopted patterns
- New patterns can be adopted through the ADR process
- Rejected patterns may be revisited if new evidence emerges, but only through a new ADR
