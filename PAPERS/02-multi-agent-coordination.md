# Multi-Agent Coordination Paper Collection

> This category collects academic papers related to multi-agent systems, coordination mechanisms, and communication protocols
> Related to Heya architecture: Agent Mesh design in Phase 3

---

## 1. Multi-Agent Communication and Coordination

### 1.1 Multi-Agent Reinforcement Learning: A Selective Overview
- **Authors**: Lucian Busoniu et al.
- **Year**: 2008/2010 (Survey)
- **Core Contribution**: Comprehensive survey of multi-agent reinforcement learning
- **Key Technologies**: Independent learning, joint learning, communication protocols, coordination games
- **Heya Relevance**: **Theoretical Foundation** - Theoretical framework for Agent Mesh coordination mechanisms

### 1.2 QMIX: Monotonic Value Function Factorisation
- **Authors**: Tabish Rashid et al.
- **Year**: 2018
- **Institution**: University of Oxford
- **Core Contribution**: Multi-agent value function decomposition, enabling centralized training with decentralized execution
- **Key Technologies**: Monotonicity constraint, mixing network, CTDE architecture
- **Heya Relevance**: **Adaptive Adoption** - Provides training methods for inter-agent collaboration
- **Link**: [arXiv:1803.11485](https://arxiv.org/abs/1803.11485)

### 1.3 MAPPO: The Surprising Effectiveness of PPO
- **Authors**: Chao Yu et al.
- **Year**: 2021
- **Institution**: Tsinghua University, UC Berkeley
- **Core Contribution**: Demonstrates the effectiveness of PPO in multi-agent settings
- **Key Technologies**: Multi-agent policy gradient, parameter sharing, value function centralization
- **Heya Relevance**: **Reference** - Agent policy optimization methods
- **Link**: [arXiv:2103.01955](https://arxiv.org/abs/2103.01955)

---

## 2. Agent Communication Protocols

### 2.1 Emergence of Grounded Compositional Language
- **Authors**: Igor Mordatch, Pieter Abbeel
- **Year**: 2017
- **Institution**: OpenAI, UC Berkeley
- **Core Contribution**: Agents spontaneously develop compositional communication languages
- **Key Technologies**: Emergent communication, discrete symbols, end-to-end training
- **Heya Relevance**: **Conceptual Reference** - Agent communication language design

### 2.2 Learning Multi-Agent Communication
- **Authors**: Sainbayar Sukhbaatar et al.
- **Year**: 2016
- **Institution**: NYU, Facebook AI Research
- **Core Contribution**: Learnable multi-agent communication mechanism
- **Key Technologies**: Communication channel, differentiable message passing, attention mechanism
- **Heya Relevance**: **Direct Reference** - Agent Mesh message passing mechanism

### 2.3 TarMAC: Targeted Multi-Agent Communication
- **Authors**: Abhishek Das et al.
- **Year**: 2019
- **Institution**: Facebook AI Research, Georgia Tech
- **Core Contribution**: Targeted multi-agent communication
- **Key Technologies**: Targeted communication, attention selection, on-demand communication
- **Heya Relevance**: **Direct Adoption** - Targeted message passing between Agents
- **Link**: [arXiv:1810.11187](https://arxiv.org/abs/1810.11187)

---

## 3. Task Allocation and Collaboration

### 3.1 Multi-Robot Task Allocation
- **Authors**: Brian P. Gerkey, Maja J. Mataric
- **Year**: 2004 (Survey)
- **Core Contribution**: Systematic taxonomy of multi-robot task allocation problems
- **Key Technologies**: Auction algorithms, market mechanisms, contract net protocol
- **Heya Relevance**: **Theoretical Framework** - Agent task allocation mechanism

### 3.2 RoBO: A Protocol for Multi-Agent Coordination
- **Core Content**: Role-based multi-agent coordination protocol
- **Key Technologies**: Role assignment, dynamic reorganization, protocol standardization
- **Heya Relevance**: **Adaptive Adoption** - Agent role management

### 3.3 Coalition Formation in Multi-Agent Systems
- **Authors**: Multiple related studies
- **Core Content**: Agent coalition formation algorithms
- **Key Technologies**: Coalition games, stability analysis, payoff distribution
- **Heya Relevance**: **Reference** - Dynamic Agent teaming

---

## 4. Decentralization and Self-Organization

### 4.1 Self-Organizing Multi-Agent Systems
- **Core Content**: Survey of self-organizing multi-agent systems
- **Key Technologies**: Emergent behavior, local rules, global patterns
- **Heya Relevance**: **Core Concept** - Agent Mesh self-organization properties

### 4.2 Swarm Intelligence: From Natural to Artificial Systems
- **Authors**: Eric Bonabeau et al.
- **Year**: 1999 (Classic work)
- **Core Contribution**: Theoretical foundations of swarm intelligence
- **Key Technologies**: Ant colony algorithms, particle swarm, emergent computing
- **Heya Relevance**: **Conceptual Reference** - Large-scale Agent coordination

### 4.3 Federated Multi-Agent Actor-Critic
- **Authors**: Related federated learning research
- **Core Content**: Multi-agent collaboration under federated learning frameworks
- **Key Technologies**: Privacy protection, distributed training, knowledge sharing
- **Heya Relevance**: **Directly Related** - Distributed Agent learning

---

## 5. Human-Machine Collaboration and Hybrid Systems

### 5.1 Human-Robot Collaboration: A Survey
- **Core Content**: Comprehensive survey of human-robot collaboration
- **Key Technologies**: Shared control, intent recognition, safe interaction
- **Heya Relevance**: **Directly Related** - Heya human-AI collaboration patterns

### 5.2 Learning from Human Feedback in Multi-Agent Systems
- **Core Content**: Human feedback learning in multi-agent systems
- **Key Technologies**: Human-in-the-loop, preference learning, corrective feedback
- **Heya Relevance**: **Direct Adoption** - Human-in-the-loop design

---

## 6. Multi-Agent in the LLM Era

### 6.1 AutoGPT & BabyAGI (Open-source Projects)
- **Core Contribution**: LLM-driven autonomous agent practice
- **Key Technologies**: Goal decomposition, tool use, autonomous execution
- **Heya Relevance**: **Direct Reference** - Autonomous Agent implementation

### 6.2 MetaGPT: Meta Programming for Multi-Agent Collaborative Framework
- **Authors**: Sirui Hong et al.
- **Year**: 2023
- **Institution**: Multi-institution collaboration
- **Core Contribution**: Multi-agent collaboration framework based on standard operating procedures
- **Key Technologies**: SOP standardization, role specialization, collaborative workflows
- **Heya Relevance**: **Direct Adoption** - Agent standardized collaboration workflows
- **Link**: [arXiv:2308.00352](https://arxiv.org/abs/2308.00352)

### 6.3 CAMEL: Communicative Agents for "Mind" Exploration
- **Authors**: Guohao Li et al.
- **Year**: 2023
- **Institution**: KAUST, others
- **Core Contribution**: Agent collaboration through role-playing
- **Key Technologies**: Role-playing, conversational collaboration, task decomposition
- **Heya Relevance**: **Adaptive Adoption** - Agent role-playing mechanism
- **Link**: [arXiv:2303.17760](https://arxiv.org/abs/2303.17760)

### 6.4 AgentVerse: Facilitating Multi-Agent Collaboration
- **Authors**: Weize Chen et al.
- **Year**: 2023
- **Institution**: Tsinghua University
- **Core Contribution**: Multi-agent collaboration framework and simulation environment
- **Key Technologies**: Environment design, collaboration patterns, capability evaluation
- **Heya Relevance**: **Reference** - Agent Mesh environment design
- **Link**: [arXiv:2308.10848](https://arxiv.org/abs/2308.10848)

---

## 7. Game Theory Foundations

### 7.1 A Course in Game Theory
- **Authors**: Martin J. Osborne, Ariel Rubinstein
- **Year**: 1994
- **Publisher**: MIT Press
- **Core Contribution**: Classic game theory textbook, systematically covering core concepts such as Nash equilibrium, subgame perfect equilibrium, and Bayesian equilibrium
- **Key Technologies**: Nash equilibrium, strategic-form games, extensive-form games, incomplete information games
- **Heya Relevance**: **Theoretical Foundation** - Theoretical framework for strategic interaction between Agents
- **Link**: [MIT Press](https://mitpress.mit.edu/books/course-game-theory)

### 7.2 Multi-Agent Reinforcement Learning in Cooperative and Competitive Environments
- **Authors**: Michael Bowling, Manuela Veloso
- **Year**: 2002
- **Institution**: Carnegie Mellon University
- **Core Contribution**: Proposes a classification framework for multi-agent reinforcement learning, distinguishing cooperative, competitive, and mixed-motive game scenarios
- **Key Technologies**: Markov games, Nash Q-learning, equilibrium selection, opponent modeling
- **Heya Relevance**: **Adaptive Adoption** - Classification basis for Agent Mesh cooperative/competitive scenarios
- **Link**: [AAAI 2002](https://www.aaai.org/Library/AAAI/2002/aaai02-018.php)

### 7.3 Mechanism Design for Multi-Agent Systems
- **Authors**: Synthesis of multiple studies
- **Core Content**: Application of mechanism design theory in multi-agent systems, designing incentive-compatible collaboration mechanisms
- **Key Technologies**: Incentive compatibility, VCG mechanism, auction theory, social choice functions
- **Heya Relevance**: **Reference** - Mechanism design for Agent task allocation and resource competition

### 7.4 Social Choice Theory and Multi-Agent Systems
- **Authors**: Synthesis of multiple studies
- **Core Content**: Application of social choice theory in multi-agent decision-making, studying group preference aggregation
- **Key Technologies**: Voting rules, Arrow's impossibility theorem, preference aggregation, strategic voting
- **Heya Relevance**: **Reference** - Theoretical foundation for multi-agent decision aggregation

---

## 8. Multi-Agent Games and Equilibria

### 8.1 Nash Q-Learning for General-Sum Games
- **Authors**: Junling Hu, Michael P. Wellman
- **Year**: 2003
- **Institution**: University of Michigan
- **Core Contribution**: Proposes Nash Q-learning algorithm, learning Nash equilibria in general-sum games
- **Key Technologies**: Nash equilibrium learning, game-theoretic reinforcement learning, opponent modeling
- **Heya Relevance**: **Adaptive Adoption** - Agent strategy learning in non-cooperative scenarios
- **Link**: [Machine Learning](https://doi.org/10.1023/A:1024045601088)

### 8.2 Mean Field Multi-Agent Reinforcement Learning
- **Authors**: Yaodong Yang, Rui Luo, Minne Li, Weinan Zhang, Jun Wang
- **Year**: 2018
- **Institution**: University College London, Shanghai Jiao Tong University
- **Core Contribution**: Proposes mean field multi-agent reinforcement learning, addressing scalability of large-scale agent systems
- **Key Technologies**: Mean field approximation, mean field games, scalable MARL
- **Heya Relevance**: **Reference** - Approximate solving for large-scale Agent Mesh
- **Link**: [ICML 2018](https://arxiv.org/abs/1802.05483)

### 8.3 Zero-Sum Games and Minimax Theorem
- **Authors**: John von Neumann, Oskar Morgenstern
- **Year**: 1944 (Classic work)
- **Publisher**: Princeton University Press
- **Core Contribution**: Foundational work on game theory, proving the minimax theorem
- **Key Technologies**: Minimax theorem, zero-sum games, mixed strategies
- **Heya Relevance**: **Conceptual Reference** - Theoretical foundation for competitive Agent interaction
- **Link**: [Princeton University Press](https://press.princeton.edu/books/paperback/9780691130611/theory-of-games-and-economic-behavior)

---

## Heya Application Summary

| Paper/Technology | Application Scenario | Priority |
|-----------------|---------------------|----------|
| MetaGPT | Agent collaboration standard workflow | P0 - Must |
| TarMAC | Targeted message passing | P0 - Must |
| QMIX/MAPPO | Collaborative strategy learning | P1 - Important |
| CAMEL | Role-playing mechanism | P1 - Important |
| Task allocation algorithms | Dynamic task routing | P1 - Important |
| Self-organization theory | Mesh self-organization | P2 - Reference |
| Game theory foundations | Agent strategic interaction | P1 - Important |
| Nash Q-Learning | Non-cooperative scenario learning | P2 - Reference |
| Mean Field MARL | Large-scale Agent approximation | P2 - Reference |
| Mechanism design | Task allocation mechanism | P2 - Reference |

---

## Key Insights

### Agent Mesh Design Principles (Based on Paper Research)

1. **Decentralization**: Avoid single points of failure; each Agent decides independently
2. **On-demand communication**: Communicate only when needed (TarMAC concept)
3. **Role specialization**: Agents specialize by capability (MetaGPT concept)
4. **Standardized protocols**: Define clear communication protocols and collaboration workflows
5. **Human-AI collaboration**: Human intervention and guidance at critical nodes
6. **Game theory perspective**: Agent interactions can be modeled as games, requiring consideration of equilibrium and incentive compatibility

---

*Last updated: 2026-05-19*
