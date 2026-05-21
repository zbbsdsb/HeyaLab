# HeyaLab Implementation Priority Matrix

> Organizes paper reading priorities by development phase and team role, guiding the R&D team to efficiently leverage research findings
> Generated: 2026-05-19

---

## I. Organized by Development Phase

### Phase 2: Foundation Architecture Design (Current Phase)

**Required Reading (12 papers)** -- Architects and Tech Leads must read before design

| # | Paper | Category | Why Read Now |
|---|-------|----------|-------------|
| 1 | Microservices (Lewis & Fowler) | 11-Software Engineering | Factory Runtime modular design blueprint |
| 2 | Continuous Delivery (Humble) | 11-Software Engineering | Verification Pipeline L0-L4 design |
| 3 | MAPE-K (Kephart & Chess) | 11-Software Engineering | Metacognition Layer core model |
| 4 | GoF Design Patterns | 11-Software Engineering | Architecture design pattern foundation |
| 5 | Hebb (1949) | 09-Neuroscience | Biological foundation of learning mechanisms |
| 6 | Baddeley Working Memory Model | 09-Neuroscience | Memory Layer four-layer architecture |
| 7 | MemGPT | 01-Memory Systems | Virtual context management |
| 8 | MetaGPT | 02-Multi-Agent | SOP standardized collaboration |
| 9 | Constitutional AI | 03-AI Safety | Safety Layer core |
| 10 | ReAct | 03-AI Safety | Reasoning-acting loop |
| 11 | Plan-and-Solve / DEPS | 04-Intent Planning | Interactive planning |
| 12 | Guidelines for Human-AI Interaction | 06-Human-AI Collaboration | 18 AI interaction design guidelines |

---

### Phase 3: Core Component Implementation

#### 3A: Memory Layer Team

**Required Reading (8 papers)**:
| Paper | Category | Related Component |
|-------|----------|-------------------|
| Squire Memory Classification | 09-Neuroscience | P004 Memory Lifecycle |
| O'Keefe Hippocampal Cognitive Map | 09-Neuroscience | P004 Episodic Memory |
| Nader Memory Reconsolidation | 09-Neuroscience | P004 Memory Update |
| Sleep Memory Consolidation | 09-Neuroscience | P005 Intelligent Compression |
| Compressive Transformer | 01-Memory Systems | P005 Compression Algorithm |
| DPR (Karpukhin) | 01-Memory Systems | P006 Semantic Retrieval |
| ColBERT (Khattab) | 01-Memory Systems | P006 Fine-Grained Matching |
| GraphRAG | 01-Memory Systems | P006 Graph-Structured Retrieval |

**Recommended Reading (6 papers)**:
| Paper | Category | Related Component |
|-------|----------|-------------------|
| Miller Working Memory Capacity | 09-Neuroscience | P012 Cognitive Load |
| Frankland Forgetting Neuroscience | 09-Neuroscience | P004 Forgetting Mechanism |
| Memorizing Transformers | 01-Memory Systems | P006 kNN Retrieval |
| LongLoRA | 01-Memory Systems | Long Context Processing |
| FAISS/HNSW | 01-Memory Systems | Vector Retrieval Engine |
| RAGAS | 01-Memory Systems | Retrieval Quality Evaluation |

#### 3B: Agent Mesh Team

**Required Reading (7 papers)**:
| Paper | Category | Related Component |
|-------|----------|-------------------|
| Sporns Small-World Brain Networks | 09-Neuroscience | P007 Dynamic Topology |
| Power Large-Scale Brain Networks | 09-Neuroscience | P007 Network Architecture |
| TarMAC | 02-Multi-Agent | P008 Targeted Communication |
| QMIX/MAPPO | 02-Multi-Agent | Collaborative Strategies |
| MAPE-K | 11-Software Engineering | P009 Self-Management Model |
| Self-Healing Systems | 11-Software Engineering | P009 Self-Healing Mechanism |
| Building Microservices | 11-Software Engineering | Service Orchestration Patterns |

**Recommended Reading (5 papers)**:
| Paper | Category | Related Component |
|-------|----------|-------------------|
| van den Heuvel Dynamic Network Reconfiguration | 09-Neuroscience | P007 Topology Switching |
| Baars Global Workspace | 09-Neuroscience | Information Broadcasting |
| Mean Field MARL | 02-Multi-Agent | Large-Scale Approximation |
| Nash Q-Learning | 02-Multi-Agent | Non-Cooperative Scenarios |
| A Course in Game Theory | 02-Multi-Agent | Game Theory Foundation |

#### 3C: Safety & Tools Team

**Required Reading (7 papers)**:
| Paper | Category | Related Component |
|-------|----------|-------------------|
| Constitutional AI | 03-AI Safety | Safety Core |
| DPO | 03-AI Safety | Preference Learning |
| ToolFormer | 03-AI Safety | Tool Use |
| Gorilla | 03-AI Safety | API Calling |
| MCP (Anthropic) | 03-AI Safety | Standardized Protocol |
| Red Teaming LMs | 03-AI Safety | Security Testing |
| Prompt Injection Defense | 03-AI Safety | Input Security |

**Recommended Reading (5 papers)**:
| Paper | Category | Related Component |
|-------|----------|-------------------|
| Adversarial Examples (Goodfellow) | 03-AI Safety | Adversarial Robustness |
| TruthfulQA | 03-AI Safety | Factuality Verification |
| MMLU | 03-AI Safety | Capability Benchmark |
| ReAct | 03-AI Safety | Reasoning-Acting Loop |
| Reflexion | 03-AI Safety | Self-Reflection |

#### 3D: Human-AI Collaboration Team

**Required Reading (6 papers)**:
| Paper | Category | Related Component |
|-------|----------|-------------------|
| Kahneman Dual-System Theory | 09-Neuroscience | P010 Trust Model |
| Sweller Cognitive Load Theory | 09-Neuroscience | P012 Cognitive Load |
| Transparency Paradox | 06-Human-AI Collaboration | P012 Information Modulation |
| Learning to Trust | 06-Human-AI Collaboration | P010 Trust Calibration |
| Adaptive Information Modulation | 06-Human-AI Collaboration | P011 Dynamic Permissions |
| Microsoft HCI Guidelines | 06-Human-AI Collaboration | AI Interaction Design |

**Recommended Reading (5 papers)**:
| Paper | Category | Related Component |
|-------|----------|-------------------|
| Zak Trust Neuroscience | 09-Neuroscience | P010 Biological Foundation |
| Design of Everyday Things | 06-Human-AI Collaboration | UX Design Principles |
| HITL ML Survey (Amershi) | 11-Software Engineering | HITL Theory |
| Persuasion Paradox | 06-Human-AI Collaboration | Avoiding Over-Trust |
| Don't Make Me Think | 06-Human-AI Collaboration | Simple Design |

---

### Phase 4: MVP Validation

**Required Reading (5 papers)** -- All team members
| Paper | Category | Why |
|-------|----------|-----|
| Accelerate (Forsgren) | 11-Software Engineering | DORA metrics framework |
| Testing in Production | 11-Software Engineering | Sandbox deployment strategy |
| Lehman Software Evolution Laws | 09-Neuroscience | Evolution constraints |
| MAML (Finn) | 05-Self-Evolution | Fast adaptation |
| Self-Evolving Agents Survey | 05-Self-Evolution | Self-evolution framework |

---

### Phase 5: Long-Term Evolution (P013-P015)

**Exploratory Reading (8 papers)** -- Research-oriented
| Paper | Category | Related |
|-------|----------|---------|
| AlphaEvolve | 05-Self-Evolution | P013 MetaFactory |
| FunSearch | 05-Self-Evolution | P013 Algorithm Discovery |
| DARTS/ENAS | 05-Self-Evolution | P013 Architecture Search |
| Connectome (Seung) | 09-Neuroscience | P015 Factory DNA |
| Neuroevolution Survey | 05-Self-Evolution | P015 Genetic Algorithms |
| Evolutionary Architecture | 11-Software Engineering | P013 Architecture Evolution |
| Voyager Skill Library | 08-Knowledge Transfer | P014 Knowledge Transfer |
| Model Merging | 08-Knowledge Transfer | P014 Model Merging |

---

## II. Organized by Team Role

### Architect

**Core Focus**: System design, modularization, scalability
| Priority | Paper | Category |
|----------|-------|----------|
| P0 | Microservices (Lewis & Fowler) | 11 |
| P0 | GoF Design Patterns | 11 |
| P0 | MAPE-K (Kephart) | 11 |
| P0 | Continuous Delivery (Humble) | 11 |
| P0 | Self-Adaptive Architecture (Cheng) | 11 |
| P1 | Small-World Brain Networks | 09 |
| P1 | Large-Scale Brain Networks | 09 |
| P1 | Evolutionary Architecture | 11 |

### Memory Engineer

**Core Focus**: Memory architecture, retrieval, compression
| Priority | Paper | Category |
|----------|-------|----------|
| P0 | MemGPT | 01 |
| P0 | Baddeley Working Memory | 09 |
| P0 | Squire Memory Classification | 09 |
| P0 | DPR | 01 |
| P1 | GraphRAG | 01 |
| P1 | Compressive Transformer | 01 |
| P1 | Sleep Consolidation | 09 |

### Agent Engineer

**Core Focus**: Multi-Agent coordination, communication, self-healing
| Priority | Paper | Category |
|----------|-------|----------|
| P0 | MetaGPT | 02 |
| P0 | TarMAC | 02 |
| P0 | MAPE-K | 11 |
| P1 | QMIX/MAPPO | 02 |
| P1 | Self-Healing Systems | 11 |
| P1 | Mean Field MARL | 02 |

### Safety Engineer

**Core Focus**: AI safety, tool safety, red teaming
| Priority | Paper | Category |
|----------|-------|----------|
| P0 | Constitutional AI | 03 |
| P0 | Red Teaming LMs | 03 |
| P0 | Prompt Injection Defense | 03 |
| P1 | Adversarial Examples | 03 |
| P1 | TruthfulQA | 03 |
| P1 | DPO | 03 |

### UX Designer

**Core Focus**: Human-AI interaction, cognitive load, trust design
| Priority | Paper | Category |
|----------|-------|----------|
| P0 | Microsoft HCI Guidelines | 06 |
| P0 | Design of Everyday Things | 06 |
| P0 | Transparency Paradox | 06 |
| P1 | Kahneman Dual-System | 09 |
| P1 | Don't Make Me Think | 06 |
| P1 | About Face | 06 |

### Researcher

**Core Focus**: Self-evolution, meta-learning, frontier exploration
| Priority | Paper | Category |
|----------|-------|----------|
| P0 | Self-Evolving Agents Survey | 05 |
| P0 | MemRL | 05 |
| P0 | Friston Free Energy Principle | 09 |
| P1 | MAML | 05 |
| P1 | AlphaEvolve | 05 |
| P1 | DARTS | 05 |

---

## III. Quick Reference Card

### Minimum Reading per Role

| Role | Required (P0) | Recommended (P1) | Total |
|------|---------------|-------------------|-------|
| Architect | 5 papers | 3 papers | 8 papers |
| Memory Engineer | 4 papers | 3 papers | 7 papers |
| Agent Engineer | 3 papers | 3 papers | 6 papers |
| Safety Engineer | 3 papers | 3 papers | 6 papers |
| UX Designer | 3 papers | 3 papers | 6 papers |
| Researcher | 3 papers | 3 papers | 6 papers |

### Suggested Reading Timeline

| Week | All-Team Shared Reading | Role-Specific Reading |
|------|------------------------|----------------------|
| Week 1 | Microservices, MAPE-K, Baddeley, MemGPT | Role P0 papers |
| Week 2 | GoF Patterns, ReAct, Kahneman, MetaGPT | Role P0 papers |
| Week 3 | Constitutional AI, Sweller, Sporns | Role P1 papers |
| Week 4 | Continuous Delivery, Friston, O'Keefe | Role P1 papers |

---

*Generated: 2026-05-19 | Based on 252 papers analysis*
