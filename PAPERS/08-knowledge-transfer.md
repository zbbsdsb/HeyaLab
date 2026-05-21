# Knowledge Transfer and Cross-Domain Learning Paper Collection

> This category collects academic papers related to knowledge transfer, skill libraries, cross-domain adaptation, and knowledge distillation
> Related to Heya architecture: Cross-Factory knowledge transfer (P014), Factory DNA inheritance (P015)

---

## 1. Skill Libraries and Compositional Learning

### 1.1 Voyager: An Open-Ended Embodied Agent with Large Language Models
- **Authors**: Guanzhi Wang, Yuqi Xie, Yunfan Jiang et al.
- **Year**: 2023
- **Institution**: NVIDIA, Caltech, UT Austin, Stanford
- **Core Contribution**: First LLM-driven embodied lifelong learning Agent, with the core innovation being a continuously growing executable code skill library
- **Key Technologies**: Automatic curriculum generation, executable code skill library, iterative prompting, skill composition and reuse
- **Heya Relevance**: **Direct Adoption** - Design prototype for Factory skill library
- **Link**: [arXiv:2305.16291](https://arxiv.org/abs/2305.16291)

### 1.2 SkillGraph: Skill-Augmented Reinforcement Learning for Agents via Evolving Skill Graphs
- **Authors**: Xiaoyuan Li, Moxin Li, Keqin Bao et al.
- **Year**: 2026
- **Core Contribution**: Enhances RL Agents through continuously evolving skill graphs, achieving structured organization and efficient retrieval and reuse of skills
- **Key Technologies**: Skill graph construction and evolution, graph-structured skill retrieval, skill reuse in reinforcement learning
- **Heya Relevance**: **Direct Reference** - Construction method for Factory skill graph
- **Link**: [arXiv](https://arxiv.org/) (submitted May 2026)

### 1.3 Evolving Programmatic Skill Networks
- **Authors**: Haochen Shi, Xingdi Yuan, Bang Liu
- **Year**: 2026
- **Institution**: Microsoft
- **Core Contribution**: Studies continual skill acquisition in open-ended embodied environments, proposing evolution methods for programmatic skill networks
- **Key Technologies**: Programmatic skill representation, skill network evolution, open-environment continual learning
- **Heya Relevance**: **Adaptive Adoption** - Continuous evolution of Factory skill networks
- **Link**: [arXiv](https://arxiv.org/) (submitted January 2026)

### 1.4 Agent Skills for Large Language Models: Architecture, Acquisition, Security, and the Path Forward
- **Authors**: Renjun Xu, Yang Yan
- **Year**: 2026
- **Core Contribution**: Systematic exploration of the transition from monolithic language models to modular, skilled Agents
- **Key Technologies**: Modular skill architecture, skill acquisition and orchestration, skill security audit
- **Heya Relevance**: **Reference** - Factory skill security audit
- **Link**: [arXiv](https://arxiv.org/) (submitted February 2026)

### 1.5 Skill Expansion and Composition in Parameter Space
- **Authors**: Tenglong Liu, Jianxiong Li, Yinan Zheng et al.
- **Year**: 2025
- **Core Contribution**: Achieves skill expansion and composition in parameter space, enabling Agents to reuse prior knowledge to solve new challenges
- **Key Technologies**: Parameter space skill composition, knowledge reuse, autonomous skill development
- **Heya Relevance**: **Reference** - Parameter space composition for Factory DNA
- **Link**: [arXiv](https://arxiv.org/) (submitted February 2025)

### 1.6 Odyssey: Empowering Minecraft Agents with Open-World Skills
- **Authors**: Shunyu Liu, Yaoru Li, Kongcheng Zhang et al.
- **Year**: 2024
- **Core Contribution**: Extends Voyager to equip Agents with open-world skills, breaking beyond basic programmatic task limitations
- **Key Technologies**: Open-world skill learning, complex task decomposition, skill transfer and generalization
- **Heya Relevance**: **Reference** - Cross-domain transfer of Factory skills
- **Link**: [arXiv](https://arxiv.org/) (submitted July 2024)

---

## 2. Cross-Domain Adaptation

### 2.1 MetaClaw: Just Talk -- An Agent That Meta-Learns and Evolves in the Wild
- **Authors**: Peng Xia, Jianwen Chen, Xinyu Yang et al.
- **Year**: 2026
- **Institution**: UC Santa Cruz et al.
- **Core Contribution**: Proposes an Agent that can meta-learn and continuously evolve in real-world environments, achieving cross-domain adaptation through natural language interaction
- **Key Technologies**: Meta-learning, natural language-driven adaptation, online evolution
- **Heya Relevance**: **Direct Reference** - Core method for Factory cross-domain adaptation
- **Link**: [arXiv](https://arxiv.org/) (submitted March 2026)

### 2.2 Decompose and Recompose: Reasoning New Skills from Existing Abilities
- **Authors**: Multi-institution collaboration
- **Year**: 2026
- **Core Contribution**: Reasons new skills by decomposing and recomposing existing abilities, achieving cross-task generalization without parameter updates
- **Key Technologies**: Skill decomposition and recomposition, in-context learning, cross-task generalization
- **Heya Relevance**: **Direct Adoption** - Core mechanism for Factory DNA crossover (P015)
- **Link**: [arXiv](https://arxiv.org/) (submitted May 2026)

---

## 3. Knowledge Distillation

### 3.1 Implicit Chain of Thought Reasoning via Knowledge Distillation
- **Authors**: Yuntian Deng, Kiran Prasad, Roland Fernandez et al.
- **Year**: 2023
- **Institution**: Microsoft, Harvard University
- **Core Contribution**: Achieves implicit chain-of-thought reasoning through knowledge distillation, distilling reasoning "vertically" into hidden states across different model layers
- **Key Technologies**: Implicit reasoning distillation, inter-layer knowledge transfer, vertical distillation strategy
- **Heya Relevance**: **Adaptive Adoption** - Implicit distillation and compression of Factory experience
- **Link**: [arXiv:2311.01460](https://arxiv.org/abs/2311.01460)

### 3.2 Model Merging in the Era of Large Language Models
- **Authors**: Multi-institution collaboration
- **Year**: 2026
- **Core Contribution**: Surveys model merging techniques in the LLM era, merging multiple neural network parameters into a single model to achieve capability combination
- **Key Technologies**: Model weight merging, Task Vector operations, TIES/DARE merging
- **Heya Relevance**: **Direct Reference** - Factory DNA merging and capability combination
- **Link**: [arXiv](https://arxiv.org/) (submitted March 2026)

### 3.3 A Survey on Symbolic Knowledge Distillation of Large Language Models
- **Authors**: Kamal Acharya, Alvaro Velasquez, Houbing Herbert Song
- **Year**: 2024
- **Core Contribution**: Surveys symbolic knowledge distillation methods for LLMs, extracting large model knowledge into symbolic, interpretable rules
- **Key Technologies**: Symbolic knowledge extraction, neuro-symbolic distillation, interpretable knowledge transfer
- **Heya Relevance**: **Adaptive Adoption** - Symbolic extraction of Factory experience
- **Link**: [arXiv](https://arxiv.org/) (submitted August 2024)

---

## 4. Continual Learning

### 4.1 Continual Learning for Large Language Models: A Survey
- **Authors**: Tongtong Wu, Linhao Luo, Yuan-Fang Li et al.
- **Year**: 2024
- **Institution**: Monash University
- **Core Contribution**: Systematic survey of LLM continual learning techniques: continual pre-training, instruction fine-tuning, alignment
- **Key Technologies**: Continual pre-training, PEFT, catastrophic forgetting mitigation
- **Heya Relevance**: **Adaptive Adoption** - Factory continual learning strategy
- **Link**: [arXiv:2402.01364](https://arxiv.org/abs/2402.01364)

### 4.2 The Rise and Potential of Large Language Model Based Agents: A Survey
- **Authors**: Zhiheng Xi, Wenxiang Chen, Xin Guo et al.
- **Year**: 2023
- **Institution**: Fudan University
- **Core Contribution**: First systematic LLM Agent survey, proposing the Brain-Perception-Action general framework
- **Key Technologies**: LLM as Agent core reasoning engine, modular architecture design
- **Heya Relevance**: **Theoretical Reference** - General framework for Agent architecture
- **Link**: [arXiv:2309.07864](https://arxiv.org/abs/2309.07864)

---

## Heya Application Summary

| Paper/Technology | Application Scenario | Priority |
|-----------------|---------------------|----------|
| Voyager Skill Library | Factory skill library design prototype | P0 - Must |
| Decompose and Recompose | Factory DNA crossover mechanism | P0 - Must |
| MetaClaw | Factory cross-domain adaptation | P1 - Important |
| SkillGraph | Factory skill graph | P1 - Important |
| Model Merging | Factory DNA merging | P1 - Important |
| Evolving Skill Networks | Skill network continuous evolution | P1 - Important |
| Implicit CoT Distillation | Experience implicit distillation | P2 - Reference |
| Symbolic KD | Experience symbolic extraction | P2 - Reference |

---

## Key Insights

### Knowledge Transfer Design Principles (Based on Paper Research)

1. **Executable skills**: Skills are stored as executable code with temporal extensibility and composability (Voyager)
2. **Structured organization**: Skills are organized through graph/network structures, supporting efficient retrieval and reuse (SkillGraph)
3. **Decompose-recompose**: New skills are acquired by decomposing existing capabilities and recombining them, without learning from scratch
4. **Parameter space operations**: Model merging techniques allow direct combination of different capabilities in parameter space (Model Merging)
5. **Symbolic extraction**: Implicit knowledge can be distilled into explicit, interpretable symbolic rules

---

*Last updated: 2026-05-17*
