# HeyaLab Complete Paper Summary

> This document summarizes all academic papers involved in the HeyaLab project, organized by 8 research directions.
> Data source: `Heya-Papers-Library.xlsx`

---

## Statistical Overview

| Metric | Count |
|--------|-------|
| **Total Papers** | 94 |
| **P0 (Core/Direct Adoption)** | 46 |
| **P1 (Important/Adaptive Adoption)** | 44 |
| **P2 (Reference)** | 4 |

### Category Statistics

| Category | Total | P0 | P1 | P2 |
|----------|-------|----|----|-----|
| 01-Memory Systems | 9 | 4 | 4 | 1 |
| 02-Multi-Agent Coordination | 25 | 11 | 12 | 2 |
| 03-AI Safety and Tools | 18 | 10 | 7 | 1 |
| 04-Intent Understanding and Task Planning | 11 | 5 | 6 | 0 |
| 05-Self-Evolution and Meta-Learning | 20 | 6 | 14 | 0 |
| 06-Human-AI Collaboration and Cognitive Load | 20 | 7 | 12 | 1 |
| 07-Workflow Orchestration and Pipeline | 12 | 5 | 7 | 0 |
| 08-Knowledge Transfer and Cross-Domain Learning | 14 | 3 | 11 | 0 |

---

## 01-Memory Systems

| # | Paper Title | Authors | Year | Institution | Core Contribution | Priority | Heya Application |
|---|-------------|---------|------|-------------|-------------------|----------|-----------------|
| 1 | MemGPT: Towards LLMs as Operating Systems | Packer et al. | 2023 | CMU | Virtual context management, transforming LLMs into OS-style memory management | P0-Direct Adoption | Memory Layer core architecture |
| 2 | RETRO: Retrieval-Enhanced Transformer | Borgeaud et al. | 2022 | DeepMind | Long-range dependency modeling through retrieval enhancement | P1-Important | Semantic memory enhancement |
| 3 | Atlas: Few-shot Learning with Retrieval Augmented LM | Izacard et al. | 2022 | Meta FAIR | Retrieval-augmented few-shot learning | P1-Important | Retrieval-augmented learning |
| 4 | FAISS: A Library for Efficient Similarity Search | Johnson et al. | 2019 | Meta FAIR | Efficient similarity search library | P0-Direct Adoption | Vector retrieval engine |
| 5 | Cognitive Architectures for Language Agents (CoALA) | Sumers et al. | 2023 | Princeton | Cognitive architecture for language agents, modular memory components | P0-Core Theory | Factory cognitive architecture blueprint |
| 6 | Generative Agents: Interactive Simulacra of Human Behavior | Park et al. | 2023 | Stanford | Memory stream, reflection, recursive planning | P1-Important | Memory-driven planning |
| 7 | LLM Memory Management Survey | Multiple authors | 2024 | Multiple institutions | LLM memory management survey | P1-Important | Memory system design reference |
| 8 | RAG: Retrieval-Augmented Generation | Lewis et al. | 2020 | Meta FAIR | Retrieval-augmented generation framework | P0-Direct Adoption | Knowledge retrieval foundation |
| 9 | Real-time Knowledge Graph Construction | Multiple authors | 2023 | Multiple institutions | Real-time knowledge graph construction | P2-Reference | Knowledge structuring |

---

## 02-Multi-Agent Coordination

| # | Paper Title | Authors | Year | Institution | Core Contribution | Priority | Heya Application |
|---|-------------|---------|------|-------------|-------------------|----------|-----------------|
| 1 | Multi-Agent RL: A Selective Overview | Busoniu et al. | 2008 | Multiple institutions | Comprehensive MARL survey | P0-Theoretical Foundation | Agent Mesh coordination theory |
| 2 | QMIX: Monotonic Value Function Factorisation | Rashid et al. | 2018 | Oxford | Multi-agent value function decomposition, CTDE architecture | P1-Adaptive Adoption | Agent collaborative training |
| 3 | MAPPO: The Surprising Effectiveness of PPO | Yu et al. | 2021 | Tsinghua/UC Berkeley | Effectiveness of PPO in multi-agent settings | P1-Reference | Agent policy optimization |
| 4 | Emergence of Grounded Compositional Language | Mordatch & Abbeel | 2017 | OpenAI/UC Berkeley | Agents spontaneously develop compositional communication language | P0-Conceptual Reference | Agent communication language design |
| 5 | Learning Multi-Agent Communication | Sukhbaatar et al. | 2016 | NYU/Meta FAIR | Learnable multi-agent communication mechanism | P0-Direct Reference | Agent Mesh message passing |
| 6 | TarMAC: Targeted Multi-Agent Communication | Das et al. | 2019 | Meta FAIR/Georgia Tech | Targeted multi-agent communication | P0-Direct Adoption | Agent targeted message passing |
| 7 | Multi-Robot Task Allocation | Gerkey & Mataric | 2004 | Multiple institutions | Taxonomy of multi-robot task allocation systems | P0-Theoretical Framework | Agent task allocation mechanism |
| 8 | RoBO: Multi-Agent Coordination Protocol | Multiple authors | 2020 | Multiple institutions | Role-based multi-agent coordination protocol | P1-Adaptive Adoption | Agent role management |
| 9 | Coalition Formation in Multi-Agent Systems | Multiple authors | 2018 | Multiple institutions | Agent coalition formation algorithms | P1-Reference | Dynamic agent teaming |
| 10 | Self-Organizing Multi-Agent Systems | Multiple authors | 2019 | Multiple institutions | Self-organizing multi-agent systems survey | P0-Core Concept | Agent Mesh self-organization |
| 11 | Swarm Intelligence | Bonabeau et al. | 1999 | Multiple institutions | Theoretical foundations of swarm intelligence | P1-Conceptual Reference | Large-scale agent coordination |
| 12 | Federated Multi-Agent Actor-Critic | Multiple authors | 2021 | Multiple institutions | Multi-agent collaboration under federated learning | P0-Directly Related | Distributed agent learning |
| 13 | Human-Robot Collaboration: A Survey | Multiple authors | 2020 | Multiple institutions | Comprehensive human-robot collaboration survey | P0-Directly Related | Heya human-AI collaboration patterns |
| 14 | Learning from Human Feedback in MAS | Multiple authors | 2022 | Multiple institutions | Human feedback learning in multi-agent systems | P0-Direct Adoption | Human-in-the-loop design |
| 15 | AutoGPT & BabyAGI | Open-source projects | 2023 | Community | LLM-driven autonomous agent practice | P0-Direct Reference | Autonomous agent implementation |
| 16 | MetaGPT | Hong et al. | 2023 | DeepWisdom/PKU | SOP-standardized multi-agent collaboration framework | P0-Direct Adoption | Agent standardized collaboration |
| 17 | CAMEL | Li et al. | 2023 | KAUST | Role-playing for agent collaboration | P1-Adaptive Adoption | Agent role-playing mechanism |
| 18 | AgentVerse | Chen et al. | 2023 | Tsinghua | Multi-agent collaboration framework and simulation environment | P1-Reference | Agent Mesh environment design |
| 19 | A Course in Game Theory | Osborne & Rubinstein | 1994 | MIT Press | Classic game theory textbook | P0-Theoretical Foundation | Agent strategic interaction theory |
| 20 | MARL in Cooperative and Competitive Environments | Bowling & Veloso | 2002 | CMU | MARL classification framework | P1-Adaptive Adoption | Cooperative/competitive scenario classification |
| 21 | Mechanism Design for MAS | Multiple authors | 2018 | Multiple institutions | Mechanism design theory applied to MAS | P1-Reference | Task allocation mechanism design |
| 22 | Social Choice Theory and MAS | Multiple authors | 2019 | Multiple institutions | Social choice theory in multi-agent decision-making | P1-Reference | Multi-agent decision aggregation |
| 23 | Nash Q-Learning for General-Sum Games | Hu & Wellman | 2003 | U Michigan | Nash Q-learning algorithm | P1-Adaptive Adoption | Non-cooperative scenario strategy learning |
| 24 | Mean Field MARL | Yang et al. | 2018 | UCL/SJTU | Mean field multi-agent reinforcement learning | P1-Reference | Large-scale agent approximate solving |
| 25 | Theory of Games and Economic Behavior | von Neumann & Morgenstern | 1944 | Princeton | Foundational work on game theory | P2-Conceptual Reference | Competitive agent interaction theory |

---

## 03-AI Safety and Tools

| # | Paper Title | Authors | Year | Institution | Core Contribution | Priority | Heya Application |
|---|-------------|---------|------|-------------|-------------------|----------|-----------------|
| 1 | Weak-to-Strong Generalization | Burns et al. | 2023 | OpenAI | How weak supervisors can supervise strong models | P0-Directly Related | Safety Layer supervision mechanism |
| 2 | Constitutional AI: Harmlessness from AI Feedback | Bai et al. | 2022 | Anthropic | Harmlessness training through AI feedback | P0-Direct Adoption | Safety Layer core method |
| 3 | InstructGPT: Training LLMs to Follow Instructions | Ouyang et al. | 2022 | OpenAI | Instruction fine-tuning based on human feedback | P0-Foundational Method | Human preference learning |
| 4 | Direct Preference Optimization (DPO) | Rafailov et al. | 2023 | Stanford/CZ Biohub | Direct preference optimization without reward model | P0-Direct Adoption | More efficient preference learning |
| 5 | Chain-of-Thought Prompting | Wei et al. | 2022 | Google Research | Improving reasoning and interpretability through chain-of-thought | P0-Direct Adoption | Agent decision transparency |
| 6 | ToolFormer: LLMs Can Teach Themselves to Use Tools | Schick et al. | 2023 | Meta FAIR | Language models self-learning to use external tools | P0-Direct Adoption | Tool Use Layer core method |
| 7 | Gorilla: LLM Connected with Massive APIs | Patil et al. | 2023 | UC Berkeley/Microsoft | Large-scale API-calling language model | P0-Direct Adoption | API calling capability |
| 8 | GPT-4o Function Calling | OpenAI | 2024 | OpenAI | Native function calling capability | P0-Direct Adoption | Tool calling standard |
| 9 | Model Context Protocol (MCP) | Anthropic | 2024 | Anthropic | Standardized context protocol for AI models | P0-Direct Adoption | Tool Use standardization protocol |
| 10 | ReAct: Synergizing Reasoning and Acting | Yao et al. | 2022 | Princeton/Google | Synergistic framework for reasoning and acting | P0-Direct Adoption | Agent reasoning-action loop |
| 11 | Reflexion: Self-Reflective Agents | Shinn et al. | 2023 | Northeastern/MIT | Agents with self-reflection capability | P0-Direct Adoption | Agent self-improvement mechanism |
| 12 | Voyager: An Open-Ended Embodied Agent | Wang et al. | 2023 | NVIDIA/Caltech | Open-ended embodied agent with lifelong learning | P1-Reference | Skill accumulation and reuse |
| 13 | Red Teaming Language Models | Casper et al. | 2023 | UC Berkeley/Stanford/MIT | LLM red-teaming methods survey | P0-Direct Adoption | Safety Layer security testing |
| 14 | Jailbreaking Large Language Models | Shen et al. | 2024 | Rutgers/PKU | LLM jailbreak attack methods research | P1-Adaptive Adoption | Safety Layer attack defense |
| 15 | Adversarial Examples for Neural Networks | Goodfellow et al. | 2014 | Google | Discovery of neural network vulnerability to adversarial perturbations | P1-Reference | Understanding adversarial vulnerability |
| 16 | Certified Robustness to Adversarial Examples | Cohen et al. | 2019 | Stanford/CMU | Provable adversarial robustness methods | P1-Reference | Safety Layer robustness guarantee |
| 17 | Prompt Injection Attacks and Defenses | Multiple authors | 2023 | Multiple institutions | Systematic study of prompt injection attacks | P0-Direct Adoption | Tool Use Layer input security |
| 18 | TruthfulQA | Lin et al. | 2021 | OpenAI/Oxford | Truthfulness evaluation benchmark | P1-Adaptive Adoption | Safety Layer factuality verification |
| 19 | MMLU | Hendrycks et al. | 2020 | UC Berkeley | Large-scale multitask language understanding benchmark | P1-Reference | Factory capability evaluation reference |

---

## 04-Intent Understanding and Task Planning

| # | Paper Title | Authors | Year | Institution | Core Contribution | Priority | Heya Application |
|---|-------------|---------|------|-------------|-------------------|----------|-----------------|
| 1 | Plan-and-Solve Prompting | Wang et al. | 2023 | SMU/Alibaba | Plan-first-then-execute prompting strategy | P0-Direct Adoption | Factory Designer intent decomposition |
| 2 | Tree of Thoughts | Yao et al. | 2023 | Princeton/DeepMind | Tree-structured search reasoning | P1-Adaptive Adoption | Factory design multi-path exploration |
| 3 | Graph of Thoughts | Besta et al. | 2023 | ETH Zurich | DAG thought modeling | P1-Adaptive Adoption | Complex Factory graph reasoning |
| 4 | Understanding the Planning of LLM Agents: A Survey | Huang et al. | 2024 | USTC/Alibaba | LLM agent planning capability survey | P0-Theoretical Framework | Factory planning system reference |
| 5 | HuggingGPT | Shen et al. | 2023 | ZJU/Microsoft | LLM as controller connecting AI models | P0-Direct Reference | Factory capability assembly pattern |
| 6 | ToolLLM | Qin et al. | 2023 | Tsinghua/ModelBest | ToolBench dataset + DFS reasoning | P1-Adaptive Adoption | Factory tool selection and orchestration |
| 7 | ViperGPT | Suris et al. | 2023 | Columbia | Code generation as reasoning intermediate representation | P1-Reference | Code as reasoning intermediate representation |
| 8 | DEPS: Interactive Planning with LLMs | Wang et al. | 2023 | HKU/UCLA | Interactive planning: describe execution, explain failures | P0-Direct Adoption | Factory execution feedback loop |
| 9 | ProgPrompt | Singh et al. | 2022 | USC/NVIDIA | Programmatic LLM prompt structure | P1-Adaptive Adoption | Factory environment-constrained plan generation |
| 10 | Inner Monologue | Huang et al. | 2022 | Google/UC Berkeley | Multi-source feedback closed-loop planning | P1-Reference | Factory closed-loop execution feedback |
| 11 | Voyager | Wang et al. | 2023 | NVIDIA/Caltech/Stanford | Open-ended lifelong learning agent | P0-Direct Reference | Skill accumulation and self-improvement |
| 12 | Generative Agents | Park et al. | 2023 | Stanford/Google | Memory stream + reflection + recursive planning | P1-Adaptive Adoption | Memory-driven planning |

---

## 05-Self-Evolution and Meta-Learning

| # | Paper Title | Authors | Year | Institution | Core Contribution | Priority | Heya Application |
|---|-------------|---------|------|-------------|-------------------|----------|-----------------|
| 1 | Self-Evolving AI Agents Survey | Fang et al. | 2025 | U Glasgow/Tencent | Unified framework for self-evolving AI agents | P0-Core Theory | Self-evolution framework top-level design |
| 2 | MemRL | Zhang et al. | 2026 | SJTU/Xidian/MemTensor | Runtime RL on episodic memory without weight updates | P0-Direct Adoption | Factory runtime self-improvement |
| 3 | SEIF | Fudan University team | 2026 | Fudan University | Hard-problem-driven self-evolving RL | P1-Reference | Factory instruction-following improvement |
| 4 | OPRO: LLMs as Optimizers | Yang et al. | 2023 | Google DeepMind | Using LLMs as optimizers | P0-Direct Adoption | Factory self-optimization strategy |
| 5 | DSPy | Khattab et al. | 2023 | Stanford NLP | Declarative LM pipeline programming model | P0-Direct Adoption | Pipeline declarative optimization |
| 6 | Self-Instruct | Wang et al. | 2022 | U Washington | Model self-generating instructions to improve capability | P1-Reference | Self-improvement data generation |
| 7 | SPIN: Self-Play Fine-Tuning | Chen et al. | 2024 | UCLA | LLM self-play to improve capability | P1-Adaptive Adoption | Factory self-play improvement |
| 8 | ReST: Self-Training for Problem-Solving | Singh et al. | 2023 | Google DeepMind | EM self-training method | P1-Adaptive Adoption | Factory self-training strategy |
| 9 | EvoPrompt | Guo et al. | 2023 | Tsinghua/Xiamen U | EA + LLM discrete prompt optimization | P0-Direct Adoption | Factory DNA evolution mechanism |
| 10 | FunSearch | Romera-Paredes et al. | 2023 | Google DeepMind | LLM + evolutionary search for mathematical discovery | P0-Direct Reference | Factory DNA evolution evidence |
| 11 | AlphaEvolve | DeepMind team | 2025 | Google DeepMind | General-purpose science agent, code genome evolution | P0-Core Reference | MetaFactory technical blueprint |
| 12 | Continual Learning for LLMs Survey | Wu et al. | 2024 | Monash University | LLM continual learning techniques survey | P1-Adaptive Adoption | Factory continual learning strategy |
| 13 | MAML | Finn et al. | 2017 | UC Berkeley | Model-agnostic meta-learning | P1-Adaptive Adoption | Factory rapid adaptation to new tasks |
| 14 | Meta-SGD | Li et al. | 2017 | Huawei Noah's Ark | Learning adaptive learning rates | P1-Reference | Meta-learning optimization strategy |
| 15 | Reptile | Nichol et al. | 2018 | OpenAI | Simplified meta-learning algorithm | P1-Reference | Simplified meta-learning implementation |
| 16 | Neural Architecture Search with RL | Zoph & Le | 2016 | Google Brain | RL for automatic neural architecture search | P1-Reference | Factory DNA architecture search |
| 17 | DARTS | Liu et al. | 2018 | CMU/Google Brain | Differentiable architecture search | P1-Reference | Efficient architecture search |
| 18 | ENAS | Pham et al. | 2018 | Google Brain | Efficient NAS with parameter sharing | P1-Reference | Factory DNA efficient search |
| 19 | Matching Networks for One Shot Learning | Vinyals et al. | 2016 | DeepMind | Attention-based one-shot learning | P1-Adaptive Adoption | Learning new capabilities from few examples |
| 20 | Prototypical Networks | Snell et al. | 2017 | U Toronto/MIT | Prototypical networks for few-shot classification | P1-Reference | Few-shot learning method |

---

## 06-Human-AI Collaboration and Cognitive Load

| # | Paper Title | Authors | Year | Institution | Core Contribution | Priority | Heya Application |
|---|-------------|---------|------|-------------|-------------------|----------|-----------------|
| 1 | Advancing Human-Machine Teaming | Chen et al. | 2025 | Multiple institutions | Comprehensive HMT classification system | P0-Theoretical Framework | Human-AI collaboration pattern taxonomy |
| 2 | Human-AI Collaborative RL Systems | Li | 2024 | Multiple institutions | Systematic CRL survey | P1-Adaptive Adoption | Human-AI feedback learning mechanism |
| 3 | Interaction Profiles in Human-AI Collaboration | Hao et al. | 2026 | Multiple institutions | Three collaboration modes: DR/CI/DE | P0-Direct Adoption | Checkpoint type design |
| 4 | HMS-HI Framework | Melih et al. | 2025 | Multiple institutions | Human-machine social hybrid intelligence framework | P0-Directly Related | High-risk decision collaboration |
| 5 | Interface Framework for Human-AI Collaboration | Andru & Saksena | 2026 | Multiple institutions | AI autonomy dimension framework | P1-Adaptive Adoption | Checkpoint UI design |
| 6 | Transparency Paradox in XAI | Margondai & Mouloua | 2026 | Multiple institutions | Transparency paradox: explanations are not universally beneficial | P0-Direct Adoption | P012 Cognitive Load Manager |
| 7 | Revising Bloom's Taxonomy for Human-AI | Ayodele et al. | 2026 | Multiple institutions | Revised Bloom's taxonomy for human-AI systems | P1-Reference | Human-AI distributed cognition assessment |
| 8 | Human-AI Epistemic Partnership | Zhai | 2026 | Multiple institutions | Epistemic partnership framework | P1-Reference | Human-AI cognitive division of labor |
| 9 | Learning to Trust | Li & Steyvers | 2026 | Multiple institutions | Human mental recalibration of AI confidence signals | P0-Direct Adoption | P010 Trust Score Model |
| 10 | Persuasion Paradox | Cohen et al. | 2026 | Multiple institutions | LLM explanations systematically increase trust without improving accuracy | P0-Directly Related | Avoiding over-trust design |
| 11 | From Accuracy to Readiness | Lee | 2026 | Multiple institutions | Team readiness evaluation framework | P1-Adaptive Adoption | Human-AI team effectiveness evaluation |
| 12 | Gaze-informed Trust in HAT | Ries et al. | 2024 | Multiple institutions | Impact of AI behavior on trust | P1-Reference | AI behavior impact on trust |
| 13 | Adaptive Information Modulation for Agentic AI | Chen et al. | 2024 | Multiple institutions | Adaptive information modulation governance | P0-Direct Adoption | P011 Dynamic Permission System |
| 14 | User Agency and System Automation | Langerak | 2025 | Multiple institutions | Balancing user agency and system automation | P1-Reference | AI propose/human decide balance |
| 15 | AI Agents with Human-Like Collaborative Tools | Reed et al. | 2025 | Multiple institutions | Human-like collaborative tools improve performance 15-40% | P1-Reference | Agent collaborative tool design |
| 16 | Adaptive XAI in High Stakes | Fernando et al. | 2025 | Multiple institutions | Adaptive XAI in high-pressure scenarios | P1-Reference | High-risk scenario trust modeling |
| 17 | Cognitive Biases in LLM-Assisted Dev | Zhou et al. | 2026 | Multiple institutions | Cognitive biases in LLM-assisted development | P1-Reference | Human-AI collaboration bias mitigation |
| 18 | The Design of Everyday Things | Norman | 1988 | Basic Books | Classic HCI work | P0-Direct Adoption | Checkpoint UI design principles |
| 19 | Don't Make Me Think | Krug | 2000 | New Riders | Core usability design principles | P1-Adaptive Adoption | Checkpoint interface simplicity design |
| 20 | Guidelines for Human-AI Interaction | Amershi et al. | 2019 | Microsoft Research | 18 human-AI interaction design guidelines | P0-Direct Adoption | Checkpoint Layer design guidelines |
| 21 | About Face: Interaction Design | Cooper et al. | 2014 | Wiley | Authoritative interaction design guide | P1-Important | Human-AI interaction design methods |

---

## 07-Workflow Orchestration and Pipeline

| # | Paper Title | Authors | Year | Institution | Core Contribution | Priority | Heya Application |
|---|-------------|---------|------|-------------|-------------------|----------|-----------------|
| 1 | AutoGen | Wu et al. | 2023 | Microsoft/Penn State | Multi-agent conversation framework | P1-Adaptive Adoption | Agent Mesh communication reference |
| 2 | ChatDev | Qian et al. | 2023 | Tsinghua/OpenBMB | LLM virtual software company framework | P1-Important | Pipeline-style collaboration evidence |
| 3 | MetaGPT | Hong et al. | 2023 | DeepWisdom/PKU | SOP encoded as prompt sequences | P0-Direct Adoption | Factory SOP encoding format |
| 4 | AgentVerse | Chen et al. | 2023 | Tsinghua/OpenBMB | Group dynamics multi-agent framework | P1-Reference | Agent Mesh dynamic topology |
| 5 | CAMEL | Li et al. | 2023 | Oxford/KAUST | Role-playing multi-agent collaboration | P1-Adaptive Adoption | Agent role specialization |
| 6 | TaskWeaver | Qiao et al. | 2023 | Microsoft Research | Code-first agent framework | P1-Reference | Code execution sandbox |
| 7 | WorkflowLLM | Fan et al. | 2024 | Tsinghua/OpenBMB | LLM workflow orchestration enhancement | P1-Important | Workflow orchestration reference |
| 8 | SWE-agent | Yang et al. | 2024 | Princeton | Agent-Computer Interface | P0-Direct Adoption | Verification system ACI design |
| 9 | CoALA | Sumers et al. | 2023 | Princeton | Language agent cognitive architecture | P0-Core Theory | Factory cognitive architecture |
| 10 | LLM Agent Survey | Wang et al. | 2023 | RUC | LLM agent unified framework | P1-Important | Agent modular design |
| 11 | ReAct | Yao et al. | 2022 | Princeton/Google | Interleaved reasoning and acting framework | P0-Direct Adoption | Agent reasoning-action loop |
| 12 | Reflexion | Shinn et al. | 2023 | Northeastern/Princeton | Language reflection iterative optimization | P0-Direct Adoption | Self-reflection and improvement loop |

---

## 08-Knowledge Transfer and Cross-Domain Learning

| # | Paper Title | Authors | Year | Institution | Core Contribution | Priority | Heya Application |
|---|-------------|---------|------|-------------|-------------------|----------|-----------------|
| 1 | Voyager (Skill Library) | Wang et al. | 2023 | NVIDIA/Caltech/Stanford | Executable code skill library | P0-Direct Adoption | Factory skill library design prototype |
| 2 | SkillGraph | Li et al. | 2026 | Multiple institutions | Skill graph-augmented RL agent | P1-Important | Factory skill graph |
| 3 | Evolving Programmatic Skill Networks | Shi et al. | 2026 | Microsoft | Evolving programmatic skill networks | P1-Adaptive Adoption | Skill network continuous evolution |
| 4 | Agent Skills for LLMs | Xu & Yan | 2026 | Multiple institutions | Transition to modular, skilled agents | P1-Reference | Factory skill security audit |
| 5 | Skill Expansion in Parameter Space | Liu et al. | 2025 | Multiple institutions | Skill expansion and composition in parameter space | P1-Reference | Factory DNA parameter space composition |
| 6 | Odyssey | Liu et al. | 2024 | Multiple institutions | Open-world skill learning | P1-Reference | Factory skill cross-domain transfer |
| 7 | MetaClaw | Xia et al. | 2026 | UC Santa Cruz | Meta-learning and continuously evolving agent | P1-Important | Factory cross-domain adaptation |
| 8 | Decompose and Recompose | Multiple institutions | 2026 | Multiple institutions | Decomposing and recomposing existing capabilities to reason new skills | P0-Direct Adoption | Factory DNA crossover mechanism |
| 9 | Implicit CoT via KD | Deng et al. | 2023 | Microsoft/Harvard | Implicit chain-of-thought distillation | P1-Adaptive Adoption | Experience implicit distillation |
| 10 | Model Merging Survey | Multiple institutions | 2026 | Multiple institutions | LLM model merging techniques survey | P1-Important | Factory DNA merging |
| 11 | Symbolic KD Survey | Acharya et al. | 2024 | Multiple institutions | Symbolic knowledge distillation methods | P1-Adaptive Adoption | Experience symbolic extraction |
| 12 | Continual Learning for LLMs | Wu et al. | 2024 | Monash University | LLM continual learning techniques | P1-Adaptive Adoption | Factory continual learning |
| 13 | LLM Agent Survey (Rise & Potential) | Xi et al. | 2023 | Fudan | LLM agent survey, Brain-Perception-Action | P1-Theoretical Reference | Agent general framework |

---

## P0 Core Papers Quick Reference

> Below are all P0 priority papers, totaling **46**, grouped by category.

### 01-Memory Systems (4 papers)

| # | Paper Title | Authors | Year | Priority | Heya Application |
|---|-------------|---------|------|----------|-----------------|
| 1 | MemGPT: Towards LLMs as Operating Systems | Packer et al. | 2023 | P0-Direct Adoption | Memory Layer core architecture |
| 2 | FAISS: A Library for Efficient Similarity Search | Johnson et al. | 2019 | P0-Direct Adoption | Vector retrieval engine |
| 3 | Cognitive Architectures for Language Agents (CoALA) | Sumers et al. | 2023 | P0-Core Theory | Factory cognitive architecture blueprint |
| 4 | RAG: Retrieval-Augmented Generation | Lewis et al. | 2020 | P0-Direct Adoption | Knowledge retrieval foundation |

### 02-Multi-Agent Coordination (11 papers)

| # | Paper Title | Authors | Year | Priority | Heya Application |
|---|-------------|---------|------|----------|-----------------|
| 1 | Multi-Agent RL: A Selective Overview | Busoniu et al. | 2008 | P0-Theoretical Foundation | Agent Mesh coordination theory |
| 2 | Emergence of Grounded Compositional Language | Mordatch & Abbeel | 2017 | P0-Conceptual Reference | Agent communication language design |
| 3 | Learning Multi-Agent Communication | Sukhbaatar et al. | 2016 | P0-Direct Reference | Agent Mesh message passing |
| 4 | TarMAC: Targeted Multi-Agent Communication | Das et al. | 2019 | P0-Direct Adoption | Agent targeted message passing |
| 5 | Multi-Robot Task Allocation | Gerkey & Mataric | 2004 | P0-Theoretical Framework | Agent task allocation mechanism |
| 6 | Self-Organizing Multi-Agent Systems | Multiple authors | 2019 | P0-Core Concept | Agent Mesh self-organization |
| 7 | Federated Multi-Agent Actor-Critic | Multiple authors | 2021 | P0-Directly Related | Distributed agent learning |
| 8 | Human-Robot Collaboration: A Survey | Multiple authors | 2020 | P0-Directly Related | Heya human-AI collaboration patterns |
| 9 | Learning from Human Feedback in MAS | Multiple authors | 2022 | P0-Direct Adoption | Human-in-the-loop design |
| 10 | AutoGPT & BabyAGI | Open-source projects | 2023 | P0-Direct Reference | Autonomous agent implementation |
| 11 | MetaGPT | Hong et al. | 2023 | P0-Direct Adoption | Agent standardized collaboration |
| 12 | A Course in Game Theory | Osborne & Rubinstein | 1994 | P0-Theoretical Foundation | Agent strategic interaction theory |

### 03-AI Safety and Tools (10 papers)

| # | Paper Title | Authors | Year | Priority | Heya Application |
|---|-------------|---------|------|----------|-----------------|
| 1 | Weak-to-Strong Generalization | Burns et al. | 2023 | P0-Directly Related | Safety Layer supervision mechanism |
| 2 | Constitutional AI: Harmlessness from AI Feedback | Bai et al. | 2022 | P0-Direct Adoption | Safety Layer core method |
| 3 | InstructGPT: Training LLMs to Follow Instructions | Ouyang et al. | 2022 | P0-Foundational Method | Human preference learning |
| 4 | Direct Preference Optimization (DPO) | Rafailov et al. | 2023 | P0-Direct Adoption | More efficient preference learning |
| 5 | Chain-of-Thought Prompting | Wei et al. | 2022 | P0-Direct Adoption | Agent decision transparency |
| 6 | ToolFormer: LLMs Can Teach Themselves to Use Tools | Schick et al. | 2023 | P0-Direct Adoption | Tool Use Layer core method |
| 7 | Gorilla: LLM Connected with Massive APIs | Patil et al. | 2023 | P0-Direct Adoption | API calling capability |
| 8 | GPT-4o Function Calling | OpenAI | 2024 | P0-Direct Adoption | Tool calling standard |
| 9 | Model Context Protocol (MCP) | Anthropic | 2024 | P0-Direct Adoption | Tool Use standardization protocol |
| 10 | ReAct: Synergizing Reasoning and Acting | Yao et al. | 2022 | P0-Direct Adoption | Agent reasoning-action loop |
| 11 | Reflexion: Self-Reflective Agents | Shinn et al. | 2023 | P0-Direct Adoption | Agent self-improvement mechanism |
| 12 | Red Teaming Language Models | Casper et al. | 2023 | P0-Direct Adoption | Safety Layer security testing |
| 13 | Prompt Injection Attacks and Defenses | Multiple authors | 2023 | P0-Direct Adoption | Tool Use Layer input security |

### 04-Intent Understanding and Task Planning (5 papers)

| # | Paper Title | Authors | Year | Priority | Heya Application |
|---|-------------|---------|------|----------|-----------------|
| 1 | Plan-and-Solve Prompting | Wang et al. | 2023 | P0-Direct Adoption | Factory Designer intent decomposition |
| 2 | Understanding the Planning of LLM Agents: A Survey | Huang et al. | 2024 | P0-Theoretical Framework | Factory planning system reference |
| 3 | HuggingGPT | Shen et al. | 2023 | P0-Direct Reference | Factory capability assembly pattern |
| 4 | DEPS: Interactive Planning with LLMs | Wang et al. | 2023 | P0-Direct Adoption | Factory execution feedback loop |
| 5 | Voyager | Wang et al. | 2023 | P0-Direct Reference | Skill accumulation and self-improvement |

### 05-Self-Evolution and Meta-Learning (6 papers)

| # | Paper Title | Authors | Year | Priority | Heya Application |
|---|-------------|---------|------|----------|-----------------|
| 1 | Self-Evolving AI Agents Survey | Fang et al. | 2025 | P0-Core Theory | Self-evolution framework top-level design |
| 2 | MemRL | Zhang et al. | 2026 | P0-Direct Adoption | Factory runtime self-improvement |
| 3 | OPRO: LLMs as Optimizers | Yang et al. | 2023 | P0-Direct Adoption | Factory self-optimization strategy |
| 4 | DSPy | Khattab et al. | 2023 | P0-Direct Adoption | Pipeline declarative optimization |
| 5 | EvoPrompt | Guo et al. | 2023 | P0-Direct Adoption | Factory DNA evolution mechanism |
| 6 | FunSearch | Romera-Paredes et al. | 2023 | P0-Direct Reference | Factory DNA evolution evidence |
| 7 | AlphaEvolve | DeepMind team | 2025 | P0-Core Reference | MetaFactory technical blueprint |

### 06-Human-AI Collaboration and Cognitive Load (7 papers)

| # | Paper Title | Authors | Year | Priority | Heya Application |
|---|-------------|---------|------|----------|-----------------|
| 1 | Advancing Human-Machine Teaming | Chen et al. | 2025 | P0-Theoretical Framework | Human-AI collaboration pattern taxonomy |
| 2 | Interaction Profiles in Human-AI Collaboration | Hao et al. | 2026 | P0-Direct Adoption | Checkpoint type design |
| 3 | HMS-HI Framework | Melih et al. | 2025 | P0-Directly Related | High-risk decision collaboration |
| 4 | Transparency Paradox in XAI | Margondai & Mouloua | 2026 | P0-Direct Adoption | P012 Cognitive Load Manager |
| 5 | Learning to Trust | Li & Steyvers | 2026 | P0-Direct Adoption | P010 Trust Score Model |
| 6 | Persuasion Paradox | Cohen et al. | 2026 | P0-Directly Related | Avoiding over-trust design |
| 7 | Adaptive Information Modulation for Agentic AI | Chen et al. | 2024 | P0-Direct Adoption | P011 Dynamic Permission System |
| 8 | The Design of Everyday Things | Norman | 1988 | P0-Direct Adoption | Checkpoint UI design principles |
| 9 | Guidelines for Human-AI Interaction | Amershi et al. | 2019 | P0-Direct Adoption | Checkpoint Layer design guidelines |

### 07-Workflow Orchestration and Pipeline (5 papers)

| # | Paper Title | Authors | Year | Priority | Heya Application |
|---|-------------|---------|------|----------|-----------------|
| 1 | MetaGPT | Hong et al. | 2023 | P0-Direct Adoption | Factory SOP encoding format |
| 2 | SWE-agent | Yang et al. | 2024 | P0-Direct Adoption | Verification system ACI design |
| 3 | CoALA | Sumers et al. | 2023 | P0-Core Theory | Factory cognitive architecture |
| 4 | ReAct | Yao et al. | 2022 | P0-Direct Adoption | Agent reasoning-action loop |
| 5 | Reflexion | Shinn et al. | 2023 | P0-Direct Adoption | Self-reflection and improvement loop |

### 08-Knowledge Transfer and Cross-Domain Learning (3 papers)

| # | Paper Title | Authors | Year | Priority | Heya Application |
|---|-------------|---------|------|----------|-----------------|
| 1 | Voyager (Skill Library) | Wang et al. | 2023 | P0-Direct Adoption | Factory skill library design prototype |
| 2 | Decompose and Recompose | Multiple institutions | 2026 | P0-Direct Adoption | Factory DNA crossover mechanism |

---

> Document generated: 2026-05-19 | Data source: `papers` list in `generate_papers_excel.py`
