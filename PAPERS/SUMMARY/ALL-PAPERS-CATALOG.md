# HeyaLab Complete Paper Catalog

> Auto-generated on 2026-05-19 | 252 papers total | 10 categories

---

## Overview Statistics

| Metric | Count |
|--------|-------|
| **Total Papers** | 252 |
| **Categories** | 10 |
| **Direct Adoption** | 99 |
| **Adaptive Adoption** | 141 |
| **Reference Only** | 5 |
| **P0 Must Adopt** | 26 |

---

## Category Statistics

| # | Category | Papers | Direct Adoption | Adaptive Adoption | Reference Only | Related Architecture |
|---|----------|--------|----------------|-------------------|----------------|---------------------|
| 01 | AI Memory Systems | 21 | 8 | 12 | 1 | Memory Layer |
| 02 | Multi-Agent Coordination | 26 | 8 | 17 | 1 | Agent Mesh |
| 03 | AI Safety and Tools | 30 | 12 | 16 | 2 | Safety Layer + Tool Use |
| 04 | Intent Understanding and Task Planning | 12 | 5 | 7 | 0 | Factory Designer |
| 05 | Self-Evolution and Meta-Learning | 20 | 6 | 14 | 0 | P001-P003, P013-P015 |
| 06 | Human-AI Collaboration and Cognitive Load | 25 | 8 | 17 | 0 | P010-P012, ADR-003 |
| 07 | Workflow Orchestration and Pipeline | 13 | 4 | 9 | 0 | Factory Pipeline |
| 08 | Knowledge Transfer and Cross-Domain Learning | 14 | 5 | 9 | 0 | P014-P015 |
| 09 | Neuroscience Foundations | 52 | 16 | 33 | 1 | Memory Layer, Agent Mesh |
| 11 | Software Engineering and System Architecture | 39 | 27 | 7 | 0 | Factory Runtime, Verification |

---

## 01 - AI Memory Systems (21 papers)

| # | Paper | Authors | Year | Relevance | Core Contribution |
|---|-------|---------|------|-----------|-------------------|
| 1 | MemGPT: Towards LLMs as Operating Systems | Charles Packer et al. | 2023 | Direct Adoption | Hierarchical memory architecture, virtual context management |
| 2 | RETRO: Retrieving from Trillions of Tokens | Sebastian Borgeaud et al. | 2021 | Adaptive Adoption | Retrieval-enhanced language model |
| 3 | Atlas: Few-shot Learning with RALM | Gautier Izacard et al. | 2022 | Adaptive Adoption | Retrieval-augmented few-shot learning |
| 4 | Continual Learning with Transformers | Multiple authors | -- | Adaptive Adoption | Catastrophic forgetting mitigation |
| 5 | Memory Networks | Jason Weston et al. | 2014 | Direct Adoption | Addressable memory, multi-hop reasoning |
| 6 | Differentiable Neural Computer (DNC) | Alex Graves et al. | 2016 | Adaptive Adoption | Differentiable addressing, memory temporal links |
| 7 | FAISS: Efficient Similarity Search | Jeff Johnson et al. | 2019 | Direct Adoption | Billion-scale vector retrieval |
| 8 | HNSW: Approximate Nearest Neighbor | Yu. A. Malkov | 2018 | Direct Adoption | Hierarchical navigable small world graph algorithm |
| 9 | LaMDA: Language Models for Dialog | Romal Thoppilan et al. | 2022 | Adaptive Adoption | Multi-turn dialog memory |
| 10 | BlenderBot 3: Deployed Conversational Agent | Kurt Shuster et al. | 2022 | Direct Adoption | Long-term user memory management |
| 11 | Long-Term Memory in LLMs Survey | -- | -- | Direct Adoption | Memory classification theoretical framework |
| 12 | GraphRAG: Knowledge Graphs for RAG | Microsoft Research | 2024 | Adaptive Adoption | Knowledge graph-enhanced retrieval |
| 13 | RAGAS: Automated RAG Evaluation | Es, J. van et al. | 2023 | Adaptive Adoption | RAG system evaluation framework |
| 14 | Dense Passage Retrieval (DPR) | Vladimir Karpukhin et al. | 2020 | Direct Adoption | Dual-encoder semantic retrieval |
| 15 | ColBERT: Contextualized Late Interaction | Omar Khattab | 2020 | Adaptive Adoption | Token-level fine-grained matching |
| 16 | Hybrid Search: BM25 + Dense Retrieval | Multiple authors | -- | Direct Adoption | Sparse-dense fusion retrieval |
| 17 | LongLoRA: Long-Context Fine-tuning | Yukang Chen et al. | 2023 | Adaptive Adoption | Sparse attention for context extension |
| 18 | Ring Attention: Scaling to 100M Tokens | Liu Hao | 2023 | Reference | Distributed million-token processing |
| 19 | Memorizing Transformers | Sainbayar Sukhbaatar et al. | 2022 | Direct Adoption | kNN external memory unlimited capacity |
| 20 | Recurrent Memory Transformer | Anna M. Mikhalkova et al. | 2023 | Adaptive Adoption | Recurrent memory compression |
| 21 | Compressive Transformer | Jack W. Rae et al. | 2019 | Adaptive Adoption | Historical information compressed representation |

---

## 02 - Multi-Agent Coordination (26 papers)

| # | Paper | Authors | Year | Relevance | Core Contribution |
|---|-------|---------|------|-----------|-------------------|
| 1 | Multi-Agent RL: Selective Overview | Lucian Busoniu et al. | 2008 | Direct Adoption | Comprehensive MARL survey |
| 2 | QMIX: Monotonic Value Function Factorisation | Tabish Rashid et al. | 2018 | Adaptive Adoption | CTDE multi-agent value decomposition |
| 3 | MAPPO: Effectiveness of PPO | Chao Yu et al. | 2021 | Adaptive Adoption | Multi-agent policy gradient |
| 4 | Emergence of Grounded Compositional Language | Igor Mordatch | 2017 | Direct Adoption | Spontaneous agent communication language |
| 5 | Learning Multi-Agent Communication | Sainbayar Sukhbaatar et al. | 2016 | Direct Adoption | Learnable communication mechanism |
| 6 | TarMAC: Targeted Multi-Agent Communication | Abhishek Das et al. | 2019 | Direct Adoption | Goal-oriented targeted communication |
| 7 | Multi-Robot Task Allocation | Brian P. Gerkey | 2004 | Direct Adoption | Task allocation system taxonomy |
| 8 | RoBO: Multi-Agent Coordination Protocol | -- | -- | Adaptive Adoption | Role assignment dynamic reorganization |
| 9 | Coalition Formation in MAS | Multiple authors | -- | Adaptive Adoption | Coalition game stability analysis |
| 10 | Self-Organizing MAS | -- | -- | Direct Adoption | Emergent behavior self-organization |
| 11 | Swarm Intelligence | Eric Bonabeau et al. | 1999 | Adaptive Adoption | Swarm intelligence theoretical foundations |
| 12 | Federated Multi-Agent Actor-Critic | -- | -- | Direct Adoption | Distributed federated learning |
| 13 | Human-Robot Collaboration Survey | -- | -- | Direct Adoption | Comprehensive human-robot collaboration survey |
| 14 | Learning from Human Feedback in MAS | -- | -- | Direct Adoption | HITL multi-agent learning |
| 15 | AutoGPT & BabyAGI | Open-source projects | 2023 | Direct Adoption | LLM autonomous agent practice |
| 16 | MetaGPT: Meta Programming for MAS | Sirui Hong et al. | 2023 | Direct Adoption | SOP standardized collaboration |
| 17 | CAMEL: Communicative Agents | Guohao Li et al. | 2023 | Adaptive Adoption | Role-playing collaboration |
| 18 | AgentVerse: Multi-Agent Collaboration | Weize Chen et al. | 2023 | Adaptive Adoption | Collaboration framework and simulation |
| 19 | A Course in Game Theory | Osborne, Rubinstein | 1994 | Direct Adoption | Classic game theory textbook |
| 20 | MARL in Cooperative/Competitive Environments | Bowling, Veloso | 2002 | Adaptive Adoption | MARL classification framework |
| 21 | Mechanism Design for MAS | Multiple authors | -- | Adaptive Adoption | Incentive-compatible mechanism design |
| 22 | Social Choice Theory and MAS | Multiple authors | -- | Adaptive Adoption | Group preference aggregation |
| 23 | Nash Q-Learning for General-Sum Games | Hu, Wellman | 2003 | Adaptive Adoption | Nash equilibrium learning |
| 24 | Mean Field MARL | Yaodong Yang et al. | 2018 | Adaptive Adoption | Large-scale agent approximation |
| 25 | Theory of Games and Economic Behavior | von Neumann, Morgenstern | 1944 | Reference | Foundational game theory work |

---

## 03 - AI Safety and Tools (30 papers)

| # | Paper | Authors | Year | Relevance | Core Contribution |
|---|-------|---------|------|-----------|-------------------|
| 1 | Weak-to-Strong Generalization | Collin Burns et al. | 2023 | Direct Adoption | Weak supervisors supervising strong models |
| 2 | Scalable Oversight for LLMs | Multiple authors | -- | Direct Adoption | Scalable oversight methods |
| 3 | Constitutional AI: Harmlessness from AI Feedback | Yuntao Bai et al. | 2022 | Direct Adoption | Constitutional principles self-critique RLAIF |
| 4 | Alignment Faking in LLMs | -- | -- | Adaptive Adoption | Alignment-faking behavior research |
| 5 | InstructGPT: Training LLMs to Follow Instructions | Long Ouyang et al. | 2022 | Direct Adoption | RLHF instruction fine-tuning |
| 6 | Direct Preference Optimization (DPO) | Rafael Rafailov et al. | 2023 | Direct Adoption | Preference optimization without reward model |
| 7 | Scaling Supervision | -- | -- | Adaptive Adoption | Large-scale supervision signal acquisition |
| 8 | Towards Monosemanticity | -- | -- | Adaptive Adoption | Model interpretability decomposition |
| 9 | Chain-of-Thought Prompting | Jason Wei et al. | 2022 | Direct Adoption | Chain-of-thought reasoning transparency |
| 10 | Mechanistic Interpretability | -- | -- | Adaptive Adoption | Understanding networks at mechanistic level |
| 11 | ToolFormer: LLMs Can Teach Themselves Tools | Timo Schick et al. | 2023 | Direct Adoption | Self-supervised tool use |
| 12 | Gorilla: LLM Connected with Massive APIs | Shishir G. Patil et al. | 2023 | Direct Adoption | Large-scale API calling |
| 13 | GPT-4o Function Calling | OpenAI | 2024 | Direct Adoption | Native function calling |
| 14 | APIBench: Benchmark for Tool-Augmented LLMs | -- | -- | Adaptive Adoption | Tool evaluation benchmark |
| 15 | ReAct: Reasoning and Acting | Shunyu Yao et al. | 2022 | Direct Adoption | Reasoning-acting synergistic loop |
| 16 | Reflexion: Self-Reflective Agents | Noah Shinn et al. | 2023 | Direct Adoption | Self-reflection improvement |
| 17 | Voyager: Open-Ended Embodied Agent | Guanzhi Wang et al. | 2023 | Adaptive Adoption | Skill library lifelong learning |
| 18 | Model Context Protocol (MCP) | Anthropic | 2024 | Direct Adoption | Standardized context protocol |
| 19 | Function Calling Standardization | -- | -- | Direct Adoption | OpenAPI/JSON Schema |
| 20 | Frontier Models Scheming | -- | -- | Adaptive Adoption | Frontier model deceptive behavior |
| 21 | AI Safety Index / Risk Assessment | -- | -- | Direct Adoption | Safety assessment framework |
| 22 | Red Teaming Language Models | Stephen Casper et al. | 2023 | Direct Adoption | Red-teaming classification framework |
| 23 | Jailbreaking Large Language Models | Xinyu Shen et al. | 2024 | Adaptive Adoption | Jailbreak attack classification defense |
| 24 | Adversarial Examples for Neural Networks | Ian J. Goodfellow et al. | 2014 | Adaptive Adoption | FGSM adversarial examples |
| 25 | Certified Robustness to Adversarial Examples | Jeremy M. Cohen et al. | 2019 | Adaptive Adoption | Provable adversarial robustness |
| 26 | Prompt Injection Attacks and Defenses | Multiple authors | -- | Direct Adoption | Prompt injection attack defense |
| 27 | Safety Benchmarks for LLMs | Multi-institution | -- | Direct Adoption | Multi-dimensional safety evaluation |
| 28 | TruthfulQA | Stephanie Lin et al. | 2021 | Adaptive Adoption | Truthfulness evaluation benchmark |
| 29 | MMLU: Massive Multitask Understanding | Dan Hendrycks et al. | 2020 | Adaptive Adoption | 57-discipline capability benchmark |

---

## 04 - Intent Understanding and Task Planning (12 papers)

| # | Paper | Authors | Year | Relevance | Core Contribution |
|---|-------|---------|------|-----------|-------------------|
| 1 | Plan-and-Solve Prompting | -- | 2023 | Direct Adoption | Structured task planning |
| 2 | DEPS: Interactive Planning | -- | 2023 | Direct Adoption | Interactive planning loop |
| 3 | Tree of Thoughts | -- | 2023 | Adaptive Adoption | Tree-structured solution exploration |
| 4 | Graph of Thoughts | -- | 2023 | Adaptive Adoption | Graph-structured solution exploration |
| 5 | HuggingGPT | -- | 2023 | Adaptive Adoption | Capability assembly pattern |
| 6 | ToolLLM | -- | 2023 | Adaptive Adoption | Tool selection and orchestration |
| 7 | ReAct | -- | 2022 | Direct Adoption | Reasoning-acting loop |
| 8 | Chain-of-Thought | -- | 2022 | Adaptive Adoption | Step-by-step reasoning |
| 9 | Reflexion | -- | 2023 | Adaptive Adoption | Self-reflection |
| 10 | Self-Instruct | -- | 2022 | Adaptive Adoption | Self-generated instructions |
| 11 | Voyager | -- | 2023 | Adaptive Adoption | Skill library accumulation |
| 12 | DSPy | -- | 2023 | Adaptive Adoption | Declarative pipeline |

---

## 05 - Self-Evolution and Meta-Learning (20 papers)

| # | Paper | Authors | Year | Relevance | Core Contribution |
|---|-------|---------|------|-----------|-------------------|
| 1 | Self-Evolving AI Agents Survey | Jinyuan Fang et al. | 2025 | Direct Adoption | Self-evolution unified framework |
| 2 | MemRL: Runtime RL on Episodic Memory | Shengtao Zhang et al. | 2026 | Direct Adoption | Runtime RL self-improvement |
| 3 | SEIF: Self-Evolving RL for Instruction Following | Fudan University | 2026 | Adaptive Adoption | Hard-problem-driven self-evolution |
| 4 | OPRO: LLMs as Optimizers | Chengrun Yang et al. | 2023 | Direct Adoption | Natural language optimization |
| 5 | DSPy: Compiling Declarative LM Calls | Omar Khattab et al. | 2023 | Direct Adoption | Declarative pipeline compilation |
| 6 | Self-Instruct: Self-Generated Instructions | Yizhong Wang et al. | 2022 | Adaptive Adoption | Bootstrapping instruction generation |
| 7 | SPIN: Self-Play Fine-Tuning | Zixiang Chen et al. | 2024 | Adaptive Adoption | Self-play fine-tuning |
| 8 | ReST: Self-Training for Problem-Solving | Avi Singh et al. | 2023 | Adaptive Adoption | EM self-training |
| 9 | EvoPrompt: LLMs with Evolutionary Algorithms | Qingyan Guo et al. | 2023 | Direct Adoption | Evolutionary algorithm prompt optimization |
| 10 | FunSearch: Mathematical Discoveries | Bernardino Romera-Paredes et al. | 2023 | Direct Adoption | LLM + evolutionary search |
| 11 | AlphaEvolve: Algorithm Discovery | Google DeepMind | 2025 | Direct Adoption | Code genome evolution |
| 12 | Continual Learning for LLMs Survey | Tongtong Wu et al. | 2024 | Adaptive Adoption | LLM continual learning |
| 13 | MAML: Model-Agnostic Meta-Learning | Chelsea Finn et al. | 2017 | Adaptive Adoption | Few-shot fast adaptation |
| 14 | Meta-SGD: Learning to Learn Quickly | Zhenguo Li et al. | 2017 | Reference | Adaptive learning rate |
| 15 | Reptile: Scalable Meta-Learning | Alex Nichol et al. | 2018 | Reference | Simplified meta-learning |
| 16 | Neural Architecture Search with RL | Barret Zoph, Quoc V. Le | 2016 | Reference | RL architecture search |
| 17 | DARTS: Differentiable Architecture Search | Hanxiao Liu et al. | 2018 | Reference | Differentiable architecture search |
| 18 | ENAS: Efficient NAS | Hieu Pham et al. | 2018 | Reference | Parameter-sharing efficient search |
| 19 | Matching Networks for One Shot Learning | Oriol Vinyals et al. | 2016 | Adaptive Adoption | Attention-based one-shot learning |
| 20 | Prototypical Networks for Few-shot Learning | Jake Snell et al. | 2017 | Reference | Prototypical network few-shot classification |

---

## 06 - Human-AI Collaboration and Cognitive Load (25 papers)

| # | Paper | Authors | Year | Relevance | Core Contribution |
|---|-------|---------|------|-----------|-------------------|
| 1 | Advancing Human-Machine Teaming | Dian Chen et al. | 2025 | Direct Adoption | Comprehensive HMT classification system |
| 2 | Human-AI Collaborative RL Systems | Zhaoxing Li | 2024 | Adaptive Adoption | CRL system survey |
| 3 | Interaction Profiles in Human-AI Problem Solving | Zhanxin Hao et al. | 2026 | Direct Adoption | Three collaboration mode identification |
| 4 | Human Machine Social Hybrid Intelligence | Ahmet Akkaya Melih et al. | 2025 | Direct Adoption | HMS-HI framework |
| 5 | Interface Framework for Human-AI Collaboration | Shruthi Andru et al. | 2026 | Adaptive Adoption | Dimensional framework UI design |
| 6 | Transparency Paradox in Explainable AI | Ancuta Margondai et al. | 2026 | Direct Adoption | Transparency paradox cognitive load |
| 7 | Revising Bloom's Taxonomy for Human-AI Systems | Kayode P. Ayodele et al. | 2026 | Adaptive Adoption | Dual-mode cognition assessment |
| 8 | Generative AI User Experience | Xiaoming Zhai | 2026 | Adaptive Adoption | Epistemic partnership framework |
| 9 | Learning to Trust: Mental Recalibration | ZhaoBin Li, Mark Steyvers | 2026 | Direct Adoption | Trust calibration experiments |
| 10 | Persuasion Paradox in LLM Explanations | Ruth Cohen et al. | 2026 | Direct Adoption | Persuasion paradox |
| 11 | From Accuracy to Readiness: Metrics | Min Hun Lee | 2026 | Adaptive Adoption | Team readiness evaluation |
| 12 | Gaze-informed Trust Signatures | Anthony J. Ries et al. | 2024 | Adaptive Adoption | Trust dynamics research |
| 13 | Adaptive Information Modulation | Qiliang Chen et al. | 2024 | Direct Adoption | Adaptive information modulation |
| 14 | User Agency and System Automation | Thomas Langerak | 2025 | Adaptive Adoption | Agency balance |
| 15 | AI Agents with Human-Like Collaborative Tools | Harper Reed et al. | 2025 | Adaptive Adoption | Adaptive collaboration strategies |
| 16 | Adaptive XAI in High Stakes Environments | Nishani Fernando et al. | 2025 | Adaptive Adoption | Swift trust modeling |
| 17 | Cognitive Biases in LLM-Assisted Dev | Xinyi Zhou et al. | 2026 | Adaptive Adoption | Cognitive bias research |
| 18 | The Design of Everyday Things | Donald A. Norman | 1988 | Direct Adoption | Affordance concept UX foundation |
| 19 | Don't Make Me Think | Steve Krug | 2000 | Adaptive Adoption | Simple usability design |
| 20 | HCI: An Empirical Research Perspective | Scott I. MacKenzie | 2012 | Reference | HCI empirical research methods |
| 21 | About Face: Essentials of Interaction Design | Alan Cooper et al. | 2014 | Adaptive Adoption | Goal-directed design |
| 22 | Guidelines for Human-AI Interaction | Saleema Amershi et al. | 2019 | Direct Adoption | 18 human-AI interaction design guidelines |
| 23 | UX Design for AI | Multiple authors | -- | Direct Adoption | AI user experience patterns |
| 24 | Designing for Interpretability in ML Systems | Multiple authors | -- | Adaptive Adoption | Explainable AI interface design |

---

## 07 - Workflow Orchestration and Pipeline (13 papers)

| # | Paper | Authors | Year | Relevance | Core Contribution |
|---|-------|---------|------|-----------|-------------------|
| 1 | MetaGPT: SOP for Multi-Agent | Sirui Hong et al. | 2023 | Direct Adoption | SOP standardized collaboration |
| 2 | ChatDev: Communicative Agents | -- | 2023 | Adaptive Adoption | Pipeline-style collaboration |
| 3 | WorkflowLLM | -- | 2023 | Adaptive Adoption | Workflow orchestration |
| 4 | SWE-agent | -- | 2023 | Direct Adoption | Software engineering agent |
| 5 | LangGraph | -- | 2024 | Adaptive Adoption | Graph-structured workflow |
| 6 | AutoGen | -- | 2023 | Adaptive Adoption | Multi-agent dialog framework |
| 7 | CrewAI | -- | 2024 | Adaptive Adoption | Role-based agent orchestration |
| 8 | ReAct | -- | 2022 | Adaptive Adoption | Reasoning-acting loop |
| 9 | Plan-and-Solve | -- | 2023 | Adaptive Adoption | Structured planning |
| 10 | DEPS | -- | 2023 | Adaptive Adoption | Interactive planning |
| 11 | DSPy | -- | 2023 | Adaptive Adoption | Declarative pipeline |
| 12 | Voyager | -- | 2023 | Adaptive Adoption | Skill library accumulation |
| 13 | ToolLLM | -- | 2023 | Adaptive Adoption | Tool orchestration |

---

## 08 - Knowledge Transfer and Cross-Domain Learning (14 papers)

| # | Paper | Authors | Year | Relevance | Core Contribution |
|---|-------|---------|------|-----------|-------------------|
| 1 | Voyager: Skill Library | Guanzhi Wang et al. | 2023 | Direct Adoption | Skill library design prototype |
| 2 | Decompose & Recompose | -- | -- | Direct Adoption | DNA crossover mechanism |
| 3 | SkillGraph | -- | -- | Adaptive Adoption | Skill graph |
| 4 | Model Merging | -- | -- | Adaptive Adoption | Model merging knowledge distillation |
| 5 | MetaClaw | -- | -- | Adaptive Adoption | Cross-domain adaptation |
| 6 | L2P: Learning to Prompt | -- | 2022 | Adaptive Adoption | Prompt transfer |
| 7 | DualPrompt | -- | 2022 | Adaptive Adoption | Dual-prompt continual learning |
| 8 | CODA-Prompt | -- | 2022 | Adaptive Adoption | Compositional prompt |
| 9 | S-Prompts | -- | 2022 | Adaptive Adoption | Adaptive prompt |
| 10 | InfLoRA | -- | -- | Adaptive Adoption | Inference fine-tuning |
| 11 | LLaMA-Adapter | -- | 2023 | Adaptive Adoption | Adapter transfer |
| 12 | LoRA | -- | 2022 | Adaptive Adoption | Low-rank adaptation |
| 13 | Adapter Fusion | -- | -- | Adaptive Adoption | Adapter fusion |
| 14 | T5 | -- | 2020 | Adaptive Adoption | Transfer learning foundation |

---

## 09 - Neuroscience Foundations (52 papers)

> Full list available in [09-neuroscience-foundations.md](../09-neuroscience-foundations.md)

**P0 Core Papers (12 papers)**:
1. McCulloch & Pitts (1943) - Neural network theoretical foundation
2. Hebb (1949) - Hebbian learning rule
3. Baddeley (1974) - Working memory model
4. Miller (1956) - Working memory capacity 7+-2
5. Squire (2004) - Memory system classification
6. Squire & Kandel (1999) - Memory molecular mechanisms
7. O'Keefe & Nadel (1978) - Hippocampal cognitive map
8. Kahneman (2011) - Dual-system theory
9. Sweller (1988) - Cognitive load theory
10. Friston (2010) - Free energy principle
11. Sporns et al. (2004) - Small-world brain networks
12. Baars (1988) - Global workspace theory

---

## 11 - Software Engineering and System Architecture (39 papers)

> Full list available in [11-software-engineering-patterns.md](../11-software-engineering-patterns.md)

**P0 Core Papers (10 papers)**:
1. Lewis & Fowler (2014) - Microservice architecture definition
2. Newman (2014) - Complete microservice design pattern set
3. Humble & Farley (2010) - Continuous delivery
4. Forsgren, Humble & Kim (2018) - DevOps science
5. GoF (1994) - Design patterns
6. Kephart & Chess (2003) - MAPE-K self-management model
7. Cheng et al. (2009) - Self-adaptive software architecture
8. Lehman (1980) - Software evolution laws
9. Amershi et al. (2019) - HITL machine learning
10. HITL AI Systems Survey (2023) - HITL system architecture

---

*Auto-generated on 2026-05-19 | Data source: 10 category files in PAPERS/ directory*
