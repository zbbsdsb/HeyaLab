# HeyaLab Paper Library

> This directory collects academic papers related to the Heya AI Factory architecture, organized by technical domain
> **Total: 252 papers | 10 categories | 26 P0 core papers**

---

## Quick Navigation

| Need | Link |
|------|------|
| **Complete Paper Catalog** | [SUMMARY/ALL-PAPERS-CATALOG.md](./SUMMARY/ALL-PAPERS-CATALOG.md) |
| **P0 Core Papers Quick Reference** | [SUMMARY/P0-CORE-PAPERS.md](./SUMMARY/P0-CORE-PAPERS.md) |
| **Research Synthesis (English)** | [SUMMARY/RESEARCH-SYNTHESIS.md](./SUMMARY/RESEARCH-SYNTHESIS.md) |
| **Research Gap Analysis** | [SUMMARY/RESEARCH-GAPS.md](./SUMMARY/RESEARCH-GAPS.md) |
| **Implementation Priority Matrix** | [SUMMARY/IMPLEMENTATION-PRIORITY-MATRIX.md](./SUMMARY/IMPLEMENTATION-PRIORITY-MATRIX.md) |
| **Structured Data (JSON)** | [SUMMARY/all_papers.json](./SUMMARY/all_papers.json) |

---

## Browse by Tag

### By Architecture Component
| Tag | Paper Count | Categories |
|-----|-------------|------------|
| `#memory-layer` | 29 | 01, 09 |
| `#agent-mesh` | 33 | 02, 09 |
| `#safety-layer` | 30 | 03 |
| `#factory-designer` | 18 | 04 |
| `#self-evolution` | 25 | 05, 09 |
| `#human-ai-collab` | 33 | 06, 09, 11 |
| `#factory-pipeline` | 18 | 07 |
| `#knowledge-transfer` | 16 | 08 |
| `#factory-runtime` | 38 | 11 |

### By Discipline
| Tag | Paper Count | Categories |
|-----|-------------|------------|
| `#ai` | 173 | 01-08 |
| `#neuroscience` | 52 | 09 |
| `#software-engineering` | 38 | 11 |
| `#cognitive-science` | 25 | 06, 09 |

### By Research Type
| Tag | Paper Count | Description |
|-----|-------------|-------------|
| `#system` | 99 | Direct adoption |
| `#adaptive` | 141 | Adaptive adoption |
| `#reference` | 5 | Reference only |
| `#p0` | 26 | Must adopt |
| `#survey` | 15 | Survey papers |
| `#classic` | 20 | Classic works (1943-2000) |

## Category Directory

| Category | File | Paper Count | Related Architecture |
|----------|------|-------------|---------------------|
| **AI Memory Systems** | [01-memory-systems.md](./01-memory-systems.md) | 20+ | Phase 3: Memory Layer |
| **Multi-Agent Coordination** | [02-multi-agent-coordination.md](./02-multi-agent-coordination.md) | 25+ | Phase 3: Agent Mesh |
| **AI Safety and Tools** | [03-ai-safety-and-tools.md](./03-ai-safety-and-tools.md) | 30+ | Phase 3: Safety Layer + Tool Use |
| **Intent Understanding and Task Planning** | [04-intent-planning.md](./04-intent-planning.md) | 18 | Factory Designer, ADR-004 |
| **Self-Evolution and Meta-Learning** | [05-self-evolution-meta-learning.md](./05-self-evolution-meta-learning.md) | 25 | P001-P003, P013-P015 |
| **Human-AI Collaboration and Cognitive Load** | [06-human-ai-collaboration.md](./06-human-ai-collaboration.md) | 33 | P010-P012, ADR-003 |
| **Workflow Orchestration and Pipeline** | [07-workflow-orchestration.md](./07-workflow-orchestration.md) | 18 | Factory Pipeline, SOP Encoding |
| **Knowledge Transfer and Cross-Domain Learning** | [08-knowledge-transfer.md](./08-knowledge-transfer.md) | 16 | P014-P015 |
| **Neuroscience Foundations** | [09-neuroscience-foundations.md](./09-neuroscience-foundations.md) | 52 | Memory Layer, Agent Mesh, Cognitive Architecture |
| **Software Engineering and System Architecture** | [11-software-engineering-patterns.md](./11-software-engineering-patterns.md) | 38 | Factory Runtime, Verification, SPI, Self-Evolution |

**Total: 280+ core papers** (approximately 200+ unique papers after deduplication)

---

## Core Papers Quick Reference

### P0 - Must Adopt (Critical Infrastructure)

| Paper | Domain | Heya Application |
|-------|--------|-----------------|
| MemGPT | Memory Systems | Memory Layer core architecture |
| CoALA | Cognitive Architecture | Factory cognitive architecture blueprint |
| MetaGPT | Workflow Orchestration | Factory SOP encoding format |
| Constitutional AI | Safety Alignment | Safety Layer core method |
| ToolFormer/Gorilla | Tool Use | Tool Use Layer foundation |
| MCP | Tool Standard | Tool interface standardization |
| ReAct | Reasoning-Action | Agent decision loop |
| Reflexion | Self-Reflection | Factory self-improvement loop |
| Self-Evolving Agents Survey | Self-Evolution | Self-evolution framework top-level design |
| MemRL | Runtime RL | Factory runtime self-improvement |
| DSPy | Meta-Learning | Pipeline declarative optimization |
| OPRO | Meta-Learning | Strategy/prompt self-optimization |
| SWE-agent | Verification System | Verification system ACI design |
| Plan-and-Solve | Task Planning | Factory Designer intent decomposition |
| DEPS | Interactive Planning | Factory execution feedback loop |
| Transparency Paradox | Cognitive Load | P012 Cognitive Load Manager |
| Learning to Trust | Trust Calibration | P010 Trust Score Model |
| Adaptive Info Modulation | Dynamic Permissions | P011 Dynamic Permission System |
| HMT Survey | Human-AI Collaboration | Human-AI collaboration pattern taxonomy |
| Voyager Skill Library | Knowledge Transfer | Factory skill library design prototype |
| Decompose & Recompose | Knowledge Transfer | Factory DNA crossover mechanism |

### P1 - Important Reference (Key Optimization)

| Paper | Domain | Heya Application |
|-------|--------|-----------------|
| RETRO/Atlas | Memory Systems | Semantic memory enhancement |
| TarMAC | Multi-Agent | Targeted message passing |
| DPO | Safety Alignment | Preference learning efficiency |
| Chain-of-Thought | Interpretability | Decision transparency |
| Tree/Graph of Thoughts | Task Planning | Factory design exploration |
| HuggingGPT | Task Planning | Factory capability assembly pattern |
| ToolLLM | Task Planning | Factory tool selection and orchestration |
| EvoPrompt | Evolutionary Computing | Factory DNA evolution |
| FunSearch/AlphaEvolve | Evolutionary Computing | MetaFactory technical blueprint |
| SPIN/ReST | Self-Play | Factory self-improvement |
| Persuasion Paradox | Trust Calibration | Avoiding over-trust design |
| ChatDev | Workflow Orchestration | Pipeline-style collaboration evidence |
| WorkflowLLM | Workflow Orchestration | Workflow orchestration reference |
| SkillGraph | Skill Library | Factory skill graph |
| Model Merging | Knowledge Distillation | Factory DNA merging |
| MetaClaw | Cross-Domain Adaptation | Factory cross-domain adaptation |

---

## Paper to EVOLVE Proposal Mapping

| EVOLVE Proposal | Core Academic Basis | Paper Category |
|----------------|--------------------|---------------|
| P001 Adaptive Triggering | Self-Evolving Agents Survey, MemRL | 05-Self-Evolution |
| P002 Sandbox Deployment | SWE-agent, MetaGPT, Continuous Delivery | 07-Workflow Orchestration, 11-Software Engineering |
| P003 Core/Extension Separation | MemRL stability-plasticity decomposition | 05-Self-Evolution |
| P004 Memory Lifecycle | CoALA, MemGPT | 01-Memory Systems |
| P005 Intelligent Compression | MemGPT virtual context management | 01-Memory Systems |
| P006 Hybrid Retrieval | FAISS/HNSW, RETRO | 01-Memory Systems |
| P007 Dynamic Topology | TarMAC, MetaGPT, Microservices Patterns | 02-Multi-Agent, 11-Software Engineering |
| P008 Communication Protocol | TarMAC, CAMEL | 02-Multi-Agent |
| P009 Self-Healing Mesh | QMIX/MAPPO, MAPE-K, Self-Healing Systems | 02-Multi-Agent, 11-Software Engineering |
| P010 Trust Model | Learning to Trust, Reflexion, HITL ML Survey | 06-Human-AI Collaboration, 11-Software Engineering |
| P011 Dynamic Permissions | Adaptive Info Modulation, Weak-to-Strong | 06-Human-AI Collaboration |
| P012 Cognitive Load | Transparency Paradox, Bloom's Revision | 06-Human-AI Collaboration |
| P013 MetaFactory | Self-Evolving Survey, AlphaEvolve, Evolutionary Architecture | 05-Self-Evolution, 11-Software Engineering |
| P014 Knowledge Transfer | SkillGraph, Model Merging, MetaClaw | 08-Knowledge Transfer |
| P015 Factory DNA | EvoPrompt, Decompose & Recompose | 05-Self-Evolution, 08-Knowledge Transfer |

---

## Related Documentation

- [RESEARCH/SYNTHESIS.md](../RESEARCH/SYNTHESIS.md) - Research synthesis and architecture mapping
- [DECISIONS/006-adopted-design-patterns.md](../DECISIONS/006-adopted-design-patterns.md) - Adopted design pattern decisions
- [EVOLVE/MASTER-SUMMARY.md](../EVOLVE/MASTER-SUMMARY.md) - Evolution proposals global overview
- [ROADMAP.md](../ROADMAP.md) - Phase 3 R&D roadmap

---

## Usage Guide

### Paper Tag Legend

- **Direct Adoption** - Core technology, planned for direct implementation
- **Adaptive Adoption** - Requires adaptation before use
- **Reference Only** - Conceptual reference, not directly implemented

### Priority Legend

- **P0** - Must: Core infrastructure, no alternatives
- **P1** - Important: Significantly enhances capabilities, worth implementing
- **P2** - Reference: Good to understand, may adopt in the future

---

## Update Log

- **2026-05-20** - Created SUMMARY folder: complete catalog, P0 quick reference, research synthesis (English), gap analysis, implementation priority matrix, JSON data; optimized README with quick navigation and tag system
- **2026-05-19** - Deep supplementation of existing categories: 01 Memory Systems (+11 RAG/long-context papers), 02 Multi-Agent (+8 game theory papers), 03 Safety (+10 red-teaming papers), 05 Self-Evolution (+10 meta-learning/NAS papers), 06 Human-AI Collaboration (+8 UX/HCI papers); paper library expanded from 228+ to 280+
- **2026-05-19** - Added 09-Neuroscience Foundations (52 papers), paper library expanded from 176+ to 228+; updated EVOLVE mapping table with neuroscience support
- **2026-05-18** - Added 11-Software Engineering and System Architecture (38 papers), paper library expanded from 138+ to 176+
- **2026-05-17** - Added 5 categories (04-08), paper library expanded from 46+ to 138+
- **2026-05-15** - Initialized paper library, curated 46+ core papers

---

*This paper library is continuously updated. Contributions of relevant research are welcome.*
