# Research Synthesis: Academic Foundations for Heya

> This document maps academic research to Heya's architecture decisions, providing evidence-based grounding for our design choices.

---

## Overview

We surveyed 15 papers from 2023-2026 across five research areas. This document summarizes what we adopt, what we adapt, and what we reject from each.

---

## 1. Self-Organizing AI Architecture

### Papers Surveyed

| Paper | Key Insight |
|-------|-------------|
| **Self-Evolving AI Agents Survey** (Fang et al., 2025) | Unified feedback loop: Inputs → Agent System → Environment → Optimisers |
| **MetaGPT** (Hong et al., 2023) | Encode human SOPs as LLM prompt sequences; structured communication reduces hallucination |
| **ChatDev** (Qian et al., 2023) | Chat chain orchestration; communicative dehallucination |
| **LATM** (Cai et al., 2023) | LLMs create reusable tools; tool maker/tool user division of labor |
| **AutoGen** (Wu et al., 2023) | Multi-agent conversation with human input integration |

### What We Adopt

**From MetaGPT — SOP Encoding:**
- Heya's factory pipelines are encoded as structured procedures, not ad-hoc prompt chains
- Each pipeline step has a defined role, input, output, and verification gate
- Communication between steps uses structured documents, not free-form dialogue

**From Self-Evolving Agents Survey — Feedback Loop:**
- Heya's self-evolution follows the four-component loop:
  - **System Inputs**: User intent + WorkOrder
  - **Agent System**: The factory (AI organized capabilities)
  - **Environment**: Tools, storage, external systems
  - **Optimisers**: Metacognition layer + human feedback

### What We Adapt

**From LATM — Dynamic Tool Creation:**
- LATM focuses on code-level tool creation; Heya adapts this to capability-level assembly
- In Heya, the AI doesn't write tool code — it assembles the right combination of reasoning strategies, models, and external tools for each task

**From ChatDev — Chat Chain:**
- ChatDev uses chat chains for software development; Heya generalizes this to any product type
- Heya's "chat chain" is the conversation between AI and human at checkpoints, not between AI agents

### What We Reject

**From AutoGen — Fully Flexible Agent Topology:**
- AutoGen allows arbitrary agent-to-agent communication patterns
- Heya rejects this because it leads to unpredictable behavior
- Heya uses a structured pipeline with defined checkpoints instead

---

## 2. Human-AI Hybrid Collaboration

### Papers Surveyed

| Paper | Key Insight |
|-------|-------------|
| **Reflexion** (Shinn et al., 2023) | Verbal reinforcement learning; language feedback instead of weight updates |
| **OpenHands** (Wang et al., 2024) | Sandbox execution; multi-agent coordination for software development |
| **AgentInstruct** (Crispino et al., 2023) | Agents guide LLM reasoning; multi-layer guidance (human → AI → AI) |

### What We Adopt

**From Reflexion — Verbal Reinforcement Learning:**
- Human feedback is stored as language (not scores) in episodic memory
- AI reflects on feedback before subsequent attempts
- This is the foundation of Heya's "human feedback → AI improvement" loop

**From AgentInstruct — Multi-Layer Guidance:**
- Heya adopts a three-layer guidance model:
  1. Human guides the AI (at checkpoints)
  2. AI guides itself (metacognition)
  3. AI guides its sub-tasks (reasoning strategy selection)

### What We Adapt

**From OpenHands — Sandbox Execution:**
- OpenHands uses sandboxes for code execution; Heya generalizes to any artifact type
- Heya's "sandbox" is the verification system — any artifact must pass checks before human review

### What We Reject

**From OpenHands — Autonomous Agent Model:**
- OpenHands aims for fully autonomous software development
- Heya explicitly requires human checkpoints for high-stakes decisions
- We reject full autonomy in favor of the hybrid model (ADR-003)

---

## 3. Long-Term Memory

### Papers Surveyed

| Paper | Key Insight |
|-------|-------------|
| **MemGPT / Letta** (Packer et al., 2023-2024) | OS-inspired virtual context management; fast/slow memory hierarchy |
| **CoALA** (Sumers et al., 2023) | Cognitive architecture: episodic, semantic, procedural memory modules |
| **MemRL** (Zhang et al., 2026) | Runtime RL on episodic memory; stability-plasticity decomposition |
| **Generative Agents** (Park et al., 2023) | Memory stream with retrieval, reflection, and planning |
| **EverMemOS / Mem0** (Industry, 2025) | Memory as the third core component of AI systems |

### What We Adopt

**From CoALA — Modular Memory Architecture:**
- Heya's four memory tiers map directly to CoALA's framework:
  - **Working Memory** ↔ CoALA's "working memory" (current task context)
  - **Episodic Memory** ↔ CoALA's "episodic memory" (events, decisions, failures)
  - **Semantic Memory** ↔ CoALA's "semantic memory" (knowledge, patterns, preferences)
  - **Artifact Memory** ↔ Heya's addition (produced outputs with provenance)

**From MemGPT — Virtual Context Management:**
- Heya adopts the fast/slow memory hierarchy:
  - Fast: Working memory (in context window)
  - Slow: Episodic + Semantic (external storage, retrieved on demand)
- Context window management via intelligent summarization and retrieval

**From MemRL — Stability-Plasticity Decomposition:**
- Heya separates stable reasoning (factory structure, policies) from plastic memory (new experiences)
- Episodic memory is continuously updated; semantic memory is updated only after validation
- This prevents the factory from "forgetting" core behaviors while still learning

**From Generative Agents — Memory Stream:**
- Heya adopts the memory stream concept for episodic memory:
  - Each memory entry has a timestamp, content, and importance score
  - Retrieval uses a combination of recency, relevance, and importance

### What We Adapt

**From Mem0 — Memory Middleware:**
- Mem0 provides a generic memory API; Heya adapts this with factory-specific semantics
- Heya's memory is scoped to a factory (not global), with namespace isolation per project

### What We Reject

**From Generative Agents — Autonomous Social Behavior:**
- Generative Agents simulate autonomous social behavior (emerging from memory + reflection)
- Heya rejects emergent behavior — all factory actions must be traceable to user intent or human decisions

---

## 4. Artifact Verification

### Papers Surveyed

| Paper | Key Insight |
|-------|-------------|
| **LLM Agent Survey** (Li, 2024) | Evaluator role + feedback learning workflow taxonomy |
| **SWE-agent** (Princeton NLP, 2024) | Automated code verification on real repositories |
| **MetaGPT** (Hong et al., 2023) | Intermediate result verification to prevent error propagation |

### What We Adopt

**From MetaGPT — Intermediate Verification:**
- Heya verifies at every pipeline step, not just at the end
- Each step produces a structured output that is validated before the next step begins
- This prevents error propagation through the pipeline

**From LLM Agent Survey — Evaluator Role:**
- Heya's verification system includes an "evaluator" component that is separate from the "generator"
- The evaluator uses different (often simpler) models than the generator
- This separation prevents the generator from evaluating its own work

### What We Adapt

**From SWE-agent — Real Repository Verification:**
- SWE-agent verifies against real codebases; Heya generalizes to any artifact type
- For code artifacts: compile + test + benchmark
- For non-code artifacts: schema validation + human review + domain-specific checks

### What We Reject

**From SWE-agent — Fully Automated Fixing:**
- SWE-agent autonomously fixes issues; Heya requires human approval for fixes
- Verification failures in Heya trigger a human checkpoint, not an autonomous retry loop

---

## 5. Intent-Driven Design

### Papers Surveyed

| Paper | Key Insight |
|-------|-------------|
| **Generative Agents** (Park et al., 2023) | High-level intent → reflection → planning → low-level actions |
| **MetaGPT** (Hong et al., 2023) | Human workflow SOPs encoded as executable agent procedures |
| **AgentInstruct** (Crispino et al., 2023) | Agent-guided reasoning improves zero-shot performance |

### What We Adopt

**From Generative Agents — Intent Decomposition:**
- Heya's Factory Designer follows a three-step decomposition:
  1. Understand intent (what does the user want to create?)
  2. Reflect on constraints (what are the limitations, preferences, past decisions?)
  3. Plan the factory (what capabilities, tools, verification, and checkpoints are needed?)

**From MetaGPT — SOP Encoding:**
- Heya encodes common factory patterns as reusable templates
- When a user's intent matches a known pattern, the AI can propose a factory faster
- Templates are evolved based on experience (successful factories improve the template)

### What We Adapt

**From AgentInstruct — Guided Reasoning:**
- AgentInstruct uses agents to guide LLM reasoning; Heya adapts this for factory design
- The "guide" is the accumulated memory of past factory designs and their outcomes

---

## Summary: Research-to-Architecture Mapping

| Heya Component | Primary Research Foundation | Secondary References |
|---------------|---------------------------|---------------------|
| Factory Designer | MetaGPT (SOP encoding), Generative Agents (intent decomposition) | AgentInstruct |
| Reasoning Engine | Self-Evolving Agents Survey (feedback loop), AgentInstruct | CoALA |
| Memory System | MemGPT (virtual context), CoALA (modular memory), MemRL (stability-plasticity) | Generative Agents, Mem0 |
| Verification System | MetaGPT (intermediate verification), LLM Agent Survey (evaluator role) | SWE-agent, OpenHands |
| Human Checkpoints | Reflexion (verbal RL), AutoGen (human input integration) | OpenHands |
| Tool System | LATM (tool creation), OpenHands (sandbox execution) | AutoGen |
| Metacognition Layer | Self-Evolving Agents Survey (optimiser), MemRL (runtime RL) | Reflexion |
| Self-Evolution | Self-Evolving Agents Survey (full framework), MemRL (memory-based RL) | Reflexion |
