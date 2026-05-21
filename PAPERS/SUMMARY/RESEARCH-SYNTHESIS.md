# HeyaLab Research Synthesis

> Maps research findings to architectural decisions and implementation priorities
> Generated: 2026-05-19 | 252 papers across 10 categories

---

## 1. Executive Summary

This document synthesizes insights from 252 research papers collected across AI, neuroscience, software engineering, and cognitive science to inform the design of the Heya AI Factory architecture. Each section maps paper findings directly to architectural decisions, component designs, and EVOLVE proposals.

---

## 2. Memory Layer (P004-P006)

### 2.1 Core Insight: Memory as a Hierarchical System

**Key Finding**: Neuroscience research (Baddeley 1974, Squire 2004) establishes that biological memory operates as a multi-tier system with distinct encoding, consolidation, and retrieval mechanisms. This directly validates Heya's four-layer memory architecture.

| Biological System | Heya Component | Supporting Papers |
|-------------------|---------------|-------------------|
| Working Memory (central executive) | Working Memory Layer | Baddeley (1974), Miller (1956) |
| Hippocampal episodic memory | Episodic Memory Layer | O'Keefe & Nadel (1978), Squire (2004) |
| Neocortical semantic memory | Semantic Memory Layer | Squire & Kandel (1999), Sleep Consolidation (Walker 2004) |
| Procedural memory | Artifact Memory Layer | Sutton & Barto (1998) |

### 2.2 Architectural Decisions

**Decision 1: Hierarchical Memory with Virtual Context Management**
- **Source**: MemGPT (Packer 2023) — paging mechanism for unlimited context
- **Decision**: Adopt MemGPT's virtual context management as the foundation for Memory Layer
- **Implementation**: Working Memory as "main context", Episodic/Semantic as "external context" with paging

**Decision 2: Memory Lifecycle Management (P004)**
- **Source**: Nader & Hardt (2009) — reconsolidation; Frankland & Josselyn (2022) — active forgetting
- **Decision**: Memory is not passive storage. Implement active lifecycle: create → index → retrieve → decay → forget → archive
- **Rationale**: Forgetting is an active process, not passive decay. Factory must actively manage memory to prevent overload

**Decision 3: Intelligent Compression (P005)**
- **Source**: Sleep-dependent memory consolidation (Walker 2004); Compressive Transformer (Rae 2019)
- **Decision**: Four-level compression: summary → semantic extraction → knowledge merging → selective discard
- **Rationale**: During sleep, the brain consolidates episodic memories into semantic knowledge. Factory should perform similar consolidation during idle periods

**Decision 4: Hybrid Retrieval (P006)**
- **Source**: DPR (Karpukhin 2020), ColBERT (Khattab 2020), GraphRAG (Microsoft 2024)
- **Decision**: Multi-dimensional retrieval: semantic + keyword + temporal + importance, with three-level cache
- **Rationale**: No single retrieval method is sufficient. Hybrid approach combines sparse (BM25) and dense (DPR/ColBERT) retrieval with graph structure (GraphRAG)

### 2.3 Research Gaps

| Gap | Impact | Priority |
|-----|--------|----------|
| No paper on memory conflict resolution | P004 needs conflict resolution strategy | HIGH |
| Limited research on memory decay curves for LLMs | P004 decay parameters need empirical validation | MEDIUM |
| GraphRAG scalability for real-time retrieval | P006 performance optimization | MEDIUM |

---

## 3. Agent Mesh (P007-P009)

### 3.1 Core Insight: Brain-Inspired Network Topology

**Key Finding**: Brain networks exhibit small-world topology (Sporns 2004) with high clustering and short path lengths, enabling both local specialization and global integration.

| Brain Network Property | Agent Mesh Design | Supporting Papers |
|----------------------|-------------------|-------------------|
| Small-world topology | Dynamic topology (P007) | Sporns et al. (2004), Bassett & Bullmore (2006) |
| Dynamic reconfiguration | Task-based network reorganization | van den Heuvel (2009) |
| Default mode network | Idle-time self-reflection | Raichle (2005) |
| Global workspace | Critical information broadcast | Baars (1988), Dehaene (2014) |

### 3.2 Architectural Decisions

**Decision 1: Small-World Network Topology**
- **Source**: Sporns et al. (2004) — small-world brain networks; Microservices (Lewis & Fowler 2014)
- **Decision**: Agent Mesh should adopt small-world topology — dense local clusters with sparse global connections
- **Implementation**: Hub agents for cross-cluster communication, local agents for specialized tasks

**Decision 2: Standardized Communication Protocol (P008)**
- **Source**: TarMAC (Das 2019) — targeted communication; CAMEL (Li 2023) — role-playing
- **Decision**: 13 message types, 4 priority levels, backpressure mechanism, 3-channel communication
- **Rationale**: Neural oscillations synchronize brain regions; Agent Mesh needs similar synchronization protocols

**Decision 3: Self-Healing Mechanism (P009)**
- **Source**: MAPE-K (Kephart & Chess 2003); Self-Healing Systems Survey
- **Decision**: Health check → fault detection → task migration → state recovery
- **Rationale**: Biological neural networks continuously repair and reorganize. Agent Mesh must be self-healing

### 3.3 Research Gaps

| Gap | Impact | Priority |
|-----|--------|----------|
| No empirical study on optimal Agent Mesh size | P007 topology parameters | HIGH |
| Limited research on multi-agent communication overhead | P008 protocol efficiency | MEDIUM |
| Self-healing for LLM-based agents is unexplored | P009 novel contribution | HIGH |

---

## 4. Human-AI Collaboration (P010-P012)

### 4.1 Core Insight: Trust as a Dynamic, Multi-Dimensional Signal

**Key Finding**: Trust is not binary but a continuous, dynamically calibrated signal influenced by cognitive biases, emotional states, and social context (Kahneman 2011, Zak 2017).

| Cognitive Science Finding | Heya Component | Supporting Papers |
|--------------------------|---------------|-------------------|
| Dual-system theory (fast/slow) | Trust Model (P010) | Kahneman (2011) |
| Cognitive load theory | Cognitive Load Manager (P012) | Sweller (1988), Miller (1956) |
| Trust neuroscience (oxytocin) | Trust calibration | Zak (2017) |
| Transparency paradox | Information modulation | Margondai (2026) |
| HITL interaction patterns | Checkpoint types | Amershi (2019, 2023) |

### 4.2 Architectural Decisions

**Decision 1: Trust Score Model (P010)**
- **Source**: Learning to Trust (Li & Steyvers 2026); Zak (2017)
- **Decision**: Multi-dimensional trust score with dynamic calibration based on performance history
- **Implementation**: Trust score = f(accuracy, consistency, transparency, human feedback patterns)

**Decision 2: Dynamic Permission System (P011)**
- **Source**: Adaptive Information Modulation (Chen 2024); Miller (1956)
- **Decision**: L0-L4 permission levels, 11 operations × 10 tasks permission matrix
- **Rationale**: Human working memory capacity is 7±2 items. Information presented must be chunked and prioritized

**Decision 3: Cognitive Load Manager (P012)**
- **Source**: Transparency Paradox (Margondai 2026); Sweller (1988); Microsoft HCI Guidelines (Amershi 2019)
- **Decision**: Evaluate human decision load, intelligently batch and defer non-urgent decisions
- **Rationale**: More information ≠ better decisions. The transparency paradox shows excessive information can harm trust

### 4.3 Research Gaps

| Gap | Impact | Priority |
|-----|--------|----------|
| No trust model specifically for LLM-based agents | P010 novel contribution | HIGH |
| Limited research on cognitive load measurement for AI assistants | P012 metric design | MEDIUM |
| Cultural differences in trust calibration | P010 generalization | LOW |

---

## 5. Self-Evolution (P001-P003, P013-P015)

### 5.1 Core Insight: Bounded Evolution with Stable Core

**Key Finding**: Biological evolution operates within constraints — critical period plasticity (Hensch 2005) and Lehman's software evolution laws (1980) both indicate that systems need stable cores with evolvable peripheries.

| Evolution Principle | Heya Component | Supporting Papers |
|--------------------|---------------|-------------------|
| Hebbian plasticity | Experience-based learning | Hebb (1949), Bliss & Collingridge (1993) |
| Critical period plasticity | Core/Extension separation (P003) | Hensch (2005) |
| Software evolution laws | Factory evolution constraints | Lehman (1980) |
| MAPE-K feedback loop | Metacognition monitoring | Kephart & Chess (2003) |
| MAML meta-learning | Fast adaptation | Finn et al. (2017) |

### 5.2 Architectural Decisions

**Decision 1: Core/Extension Separation (P003)**
- **Source**: Critical period plasticity (Hensch 2005); Lehman's Laws (1980)
- **Decision**: Define immutable Core Layer and evolvable Extension Layer
- **Rationale**: Biological systems lose plasticity after critical periods. Factory Core should stabilize after initial training

**Decision 2: Adaptive Trigger System (P001)**
- **Source**: MemRL (Zhang 2026); Self-Evolving Agents Survey (Fang 2025)
- **Decision**: Trigger evolution based on dynamic baselines, not fixed thresholds
- **Rationale**: Fixed thresholds become obsolete as the system improves. Dynamic baselines ensure continuous challenge

**Decision 3: Sandbox Deployment (P002)**
- **Source**: Continuous Delivery (Humble 2010); Testing in Production (Rosenthal 2019)
- **Decision**: Shadow → Canary → Full three-stage deployment
- **Rationale**: CI/CD best practices apply to AI system evolution

### 5.3 Research Gaps

| Gap | Impact | Priority |
|-----|--------|----------|
| Self-evolution safety guarantees | P001-P003 safety constraints | HIGH |
| MetaFactory feasibility (P013) | Long-term research direction | MEDIUM |
| Factory DNA encoding scheme (P015) | Novel contribution | HIGH |

---

## 6. Cross-Cutting Concerns

### 6.1 Software Engineering Foundations

| Pattern | Application | Source |
|---------|------------|--------|
| Microservices | Factory Runtime modularization | Lewis & Fowler (2014) |
| CI/CD Pipeline | Verification L0-L4 | Humble (2010) |
| MAPE-K | Metacognition Layer | Kephart & Chess (2003) |
| GoF Design Patterns | Architecture patterns | Gamma et al. (1994) |
| Plugin Architecture | Tool System SPI | OSGi Alliance |
| HITL Systems | Human Checkpoint | Amershi (2019) |
| Evolutionary Architecture | MetaFactory | Ford & Parsons (2017) |

### 6.2 Verification System (L0-L4)

| Level | Name | Analog | Papers |
|-------|------|--------|--------|
| L0 | Self-Check | Unit Test | Continuous Delivery (Humble) |
| L1 | Automated Validation | Integration Test | Accelerate (Forsgren) |
| L2 | Testing | E2E Test | TruthfulQA (Lin) |
| L3 | Benchmarking | Performance Test | MMLU (Hendrycks) |
| L4 | Human Review | Manual Review | HITL Survey (Amershi) |

---

*Generated: 2026-05-19 | Source: PAPERS/ directory, 252 papers, 10 categories*
