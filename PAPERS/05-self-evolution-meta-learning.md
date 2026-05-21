# Self-Evolution and Meta-Learning Paper Collection

> This category collects academic papers related to AI self-evolution, meta-learning, and continuous improvement
> Related to Heya architecture: Self-Evolution Framework (P001-P003, P013-P015)

---

## 1. Self-Evolving Agents

### 1.1 A Comprehensive Survey of Self-Evolving AI Agents
- **Authors**: Jinyuan Fang, Yanwen Peng, Xi Zhang, Yingxu Wang et al.
- **Year**: 2025
- **Institution**: University of Glasgow, Tencent et al.
- **Core Contribution**: First systematic survey framework for self-evolving AI agents, proposing a unified feedback loop: system input -> agent system -> environment -> optimizer
- **Key Technologies**: Unified feedback loop framework, component-level evolution strategies, domain-specific optimization
- **Heya Relevance**: **Core Theory** - Top-level design basis for Heya's self-evolution framework
- **Link**: [arXiv:2508.07407](https://arxiv.org/abs/2508.07407)

### 1.2 MemRL: Self-Evolving Agents via Runtime Reinforcement Learning on Episodic Memory
- **Authors**: Shengtao Zhang, Jiaqian Wang, Ruiwen Zhou et al.
- **Year**: 2026
- **Institution**: Shanghai Jiao Tong University, Xidian University, MemTensor
- **Core Contribution**: Achieves agent self-evolution through runtime reinforcement learning on episodic memory without weight updates. Core innovation: decoupling stable inference from plastic memory
- **Key Technologies**: Non-parametric reinforcement learning, two-stage retrieval mechanism, stability-plasticity dilemma resolution
- **Heya Relevance**: **Direct Adoption** - Core method for Factory runtime self-improvement
- **Link**: [arXiv:2601.03192](https://arxiv.org/abs/2601.03192)

### 1.3 SEIF: Self-Evolving Reinforcement Learning for Instruction Following
- **Authors**: Fudan University team
- **Year**: 2026
- **Institution**: Fudan University
- **Core Contribution**: Hard-problem-driven self-evolving reinforcement learning framework, enabling language models to continuously improve instruction-following capability through self-evolution
- **Key Technologies**: Hard-problem-driven training sample generation, self-evolving reinforcement learning
- **Heya Relevance**: **Reference** - Continuous improvement of Factory instruction following

---

## 2. Meta-Learning and Prompt Optimization

### 2.1 Large Language Models as Optimizers (OPRO)
- **Authors**: Chengrun Yang, Xuezhi Wang, Yifeng Lu, Hanxiao Liu, Quoc V. Le, Denny Zhou, Xinyun Chen
- **Year**: 2023 (ICLR 2024)
- **Institution**: Google DeepMind
- **Core Contribution**: Proposes Optimization by PROmpting (OPRO), using LLMs as optimizers. The optimized best prompt surpasses human-designed prompts by 8% on GSM8K
- **Key Technologies**: OPRO optimization paradigm, natural language description of optimization tasks, iterative prompt optimization
- **Heya Relevance**: **Direct Adoption** - Factory self-optimizing prompts and strategies
- **Link**: [arXiv:2309.03409](https://arxiv.org/abs/2309.03409)

### 2.2 DSPy: Compiling Declarative Language Model Calls into Self-Improving Pipelines
- **Authors**: Omar Khattab, Arnav Singhvi, Paridhi Maheshwari et al.
- **Year**: 2023 (ICLR 2024)
- **Institution**: Stanford University NLP Lab
- **Core Contribution**: Proposes a declarative LM pipeline programming model with a compiler that automatically optimizes pipelines to maximize given metrics. "Programming language models, not prompting them"
- **Key Technologies**: Declarative LM pipeline, automatic compilation optimization, Signature/Teleprompter
- **Heya Relevance**: **Direct Adoption** - Factory Pipeline declarative definition and automatic optimization
- **Link**: [arXiv:2310.03714](https://arxiv.org/abs/2310.03714)

### 2.3 Self-Instruct: Aligning Language Models with Self-Generated Instructions
- **Authors**: Yizhong Wang, Yeganeh Kordi, Swaroop Mishra et al.
- **Year**: 2022 (ACL 2023)
- **Institution**: University of Washington
- **Core Contribution**: Improves instruction-following capability through model self-generated instructions, achieving 33% absolute improvement on GPT-3
- **Key Technologies**: Self-generated instructions, bootstrapping, instruction filtering
- **Heya Relevance**: **Reference** - Data generation strategy for Factory self-improvement
- **Link**: [arXiv:2212.10560](https://arxiv.org/abs/2212.10560)

---

## 3. Runtime Reinforcement Learning

### 3.1 Self-Play Fine-Tuning Converts Weak Language Models to Strong Language Models (SPIN)
- **Authors**: Zixiang Chen, Yihe Deng, Huizhuo Yuan, Kaixuan Ji, Quanquan Gu
- **Year**: 2024 (ICML 2024)
- **Institution**: UCLA
- **Core Contribution**: Inspired by AlphaGo Zero, LLMs improve capability through self-play without additional human annotation data
- **Key Technologies**: Self-play fine-tuning, adversarial training, theoretical convergence proof
- **Heya Relevance**: **Adaptive Adoption** - Factory self-play improvement strategy
- **Link**: [arXiv:2401.01335](https://arxiv.org/abs/2401.01335)

### 3.2 ReST: Beyond Human Data - Scaling Self-Training for Problem-Solving with Language Models
- **Authors**: Avi Singh, John D. Co-Reyes, Rishabh Agarwal et al. (Google DeepMind)
- **Year**: 2023 (TMLR 2024)
- **Institution**: Google DeepMind
- **Core Contribution**: EM-based self-training method: generate samples -> binary feedback filtering -> fine-tuning -> repeat iteration
- **Key Technologies**: EM self-training, binary feedback filtering, verifiable tasks
- **Heya Relevance**: **Adaptive Adoption** - Factory self-training data strategy
- **Link**: [arXiv:2312.06585](https://arxiv.org/abs/2312.06585)

---

## 4. Evolutionary Computing Combined with LLMs

### 4.1 EvoPrompt: Connecting LLMs with Evolutionary Algorithms Yields Powerful Prompt Optimizers
- **Authors**: Qingyan Guo, Rui Wang, Junliang Guo et al.
- **Year**: 2023 (ICLR 2024)
- **Institution**: Tsinghua University, Xiamen University et al.
- **Core Contribution**: Combines evolutionary algorithms (EA) with LLMs for discrete prompt optimization, significantly outperforming human prompts on 31 datasets
- **Key Technologies**: EA+LLM collaboration, mutation/crossover operators, population evolution
- **Heya Relevance**: **Direct Adoption** - Evolution mechanism for Factory DNA (P015)
- **Link**: [arXiv:2309.08532](https://arxiv.org/abs/2309.08532)

### 4.2 FunSearch: Making New Discoveries in Mathematical Sciences Using Large Language Models
- **Authors**: Bernardino Romera-Paredes, Alhussein Fawzi et al. (Google DeepMind)
- **Year**: 2023 (Nature 2024)
- **Institution**: Google DeepMind
- **Core Contribution**: Combines LLMs with evolutionary search for mathematical discovery, first proving that LLMs can generate code to discover new knowledge for open scientific problems
- **Key Technologies**: LLM code generation + evolutionary search, function space search, population evolution
- **Heya Relevance**: **Direct Reference** - Empirical foundation for Factory DNA evolution
- **Link**: [Nature](https://www.nature.com/articles/s41586-023-06924-6)

### 4.3 AlphaEvolve: Evolutionary Algorithm Discovery with Large Language Models
- **Authors**: Google DeepMind team (in collaboration with Terence Tao)
- **Year**: 2025 (Nature 2025)
- **Institution**: Google DeepMind
- **Core Contribution**: Upgraded general-purpose scientific agent from FunSearch, using algorithm source code as "genome," breaking the 56-year efficiency benchmark for matrix multiplication
- **Key Technologies**: Code genome evolution, Gemini as genetic operator, general-purpose algorithm discovery
- **Heya Relevance**: **Core Reference** - Technical blueprint for MetaFactory (P013)
- **Link**: [Nature](https://www.nature.com/articles/s41586-025-08948-7)

---

## 5. Open-Ended Learning

### 5.1 Continual Learning for Large Language Models: A Survey
- **Authors**: Tongtong Wu, Linhao Luo, Yuan-Fang Li, Shirui Pan et al.
- **Year**: 2024
- **Institution**: Monash University
- **Core Contribution**: Systematic survey of LLM continual learning techniques, proposing multi-stage classification: continual pre-training, instruction fine-tuning, alignment
- **Key Technologies**: Continual pre-training, parameter-efficient fine-tuning (PEFT), catastrophic forgetting mitigation
- **Heya Relevance**: **Adaptive Adoption** - Factory continual learning without forgetting
- **Link**: [arXiv:2402.01364](https://arxiv.org/abs/2402.01364)

---

## 6. Classic Meta-Learning Methods

### 6.1 MAML: Model-Agnostic Meta-Learning for Fast Adaptation of Deep Networks
- **Authors**: Chelsea Finn, Pieter Abbeel, Sergey Levine
- **Year**: 2017
- **Institution**: UC Berkeley
- **Core Contribution**: Proposes model-agnostic meta-learning, achieving few-shot fast adaptation by learning good initialization parameters
- **Key Technologies**: Meta-learning, second-order gradients, task distribution, fast adaptation
- **Heya Relevance**: **Adaptive Adoption** - Meta-learning foundation for Factory rapid adaptation to new tasks
- **Link**: [ICML 2017](https://arxiv.org/abs/1703.03400)

### 6.2 Meta-SGD: Learning to Learn Quickly
- **Authors**: Zhenguo Li, Fengwei Zhou, Fei Chen, Hang Li
- **Year**: 2017
- **Institution**: Huawei Noah's Ark Lab
- **Core Contribution**: Extends MAML to learn adaptive learning rates, improving meta-learning efficiency
- **Key Technologies**: Meta learning rate, adaptive learning, fast adaptation
- **Heya Relevance**: **Reference** - Optimization strategy for Factory meta-learning
- **Link**: [arXiv:1707.09835](https://arxiv.org/abs/1707.09835)

### 6.3 Reptile: A Scalable Meta-Learning Algorithm
- **Authors**: Alex Nichol, Joshua Achiam, John Schulman
- **Year**: 2018
- **Institution**: OpenAI
- **Core Contribution**: Proposes a simplified meta-learning algorithm through multi-step SGD and parameter interpolation
- **Key Technologies**: First-order meta-learning, task sampling, parameter interpolation
- **Heya Relevance**: **Reference** - Simplified meta-learning implementation
- **Link**: [arXiv:1803.03635](https://arxiv.org/abs/1803.03635)

---

## 7. Neural Architecture Search

### 7.1 Neural Architecture Search with Reinforcement Learning
- **Authors**: Barret Zoph, Quoc V. Le
- **Year**: 2016
- **Institution**: Google Brain
- **Core Contribution**: First use of reinforcement learning for automatic neural architecture search, achieving SOTA on CIFAR-10 and PTB
- **Key Technologies**: Architecture search space, RNN controller, reinforcement learning optimization
- **Heya Relevance**: **Reference** - Theoretical foundation for Factory DNA architecture search
- **Link**: [ICLR 2017](https://arxiv.org/abs/1611.01578)

### 7.2 DARTS: Differentiable Architecture Search
- **Authors**: Hanxiao Liu, Karen Simonyan, Yiming Yang
- **Year**: 2018
- **Institution**: CMU, Google Brain
- **Core Contribution**: Proposes differentiable architecture search, continuous relaxation of discrete search space, dramatically improving search efficiency
- **Key Technologies**: Differentiable search, continuous relaxation, gradient optimization
- **Heya Relevance**: **Reference** - Efficient architecture search method
- **Link**: [ICLR 2019](https://arxiv.org/abs/1806.09036)

### 7.3 Efficient Neural Architecture Search (ENAS)
- **Authors**: Hieu Pham, Melody Y. Guan, Barret Zoph, Quoc V. Le, Jeff Dean
- **Year**: 2018
- **Institution**: Google Brain
- **Core Contribution**: Proposes efficient neural architecture search, dramatically reducing search cost through parameter sharing
- **Key Technologies**: Parameter sharing, search graph, sub-model sampling
- **Heya Relevance**: **Reference** - Efficient search for Factory DNA
- **Link**: [ICML 2018](https://arxiv.org/abs/1802.03268)

---

## 8. Few-Shot Learning

### 8.1 Matching Networks for One Shot Learning
- **Authors**: Oriol Vinyals, Charles Blundell, Timothy Lillicrap, Koray Kavukcuoglu, Daan Wierstra
- **Year**: 2016
- **Institution**: DeepMind
- **Core Contribution**: Proposes matching networks, achieving one-shot learning through attention mechanisms
- **Key Technologies**: Attention mechanism, support set encoding, one-shot classification
- **Heya Relevance**: **Adaptive Adoption** - Factory learning new capabilities from few examples
- **Link**: [NeurIPS 2016](https://arxiv.org/abs/1606.04080)

### 8.2 Prototypical Networks for Few-shot Learning
- **Authors**: Jake Snell, Kevin Swersky, Richard S. Zemel, Hugo Larochelle
- **Year**: 2017
- **Institution**: University of Toronto, MIT
- **Core Contribution**: Proposes prototypical networks, performing few-shot classification through category prototypes (means)
- **Key Technologies**: Prototype representation, distance metric, embedding learning
- **Heya Relevance**: **Reference** - Simple and effective few-shot learning method
- **Link**: [NeurIPS 2017](https://arxiv.org/abs/1703.05175)

---

## Heya Application Summary

| Paper/Technology | Application Scenario | Priority |
|-----------------|---------------------|----------|
| Self-Evolving Agents Survey | Self-evolution framework top-level design | P0 - Must |
| MemRL | Runtime self-improvement | P0 - Must |
| DSPy | Pipeline declarative optimization | P0 - Must |
| OPRO | Strategy/prompt self-optimization | P0 - Must |
| EvoPrompt | Factory DNA evolution | P1 - Important |
| FunSearch/AlphaEvolve | MetaFactory technical blueprint | P1 - Important |
| SPIN/ReST | Self-play improvement | P1 - Important |
| Continual Learning Survey | Continual learning strategy | P1 - Important |
| MAML | Meta-learning fast adaptation | P1 - Important |
| DARTS/ENAS | Architecture search | P2 - Reference |
| Prototypical Networks | Few-shot learning | P2 - Reference |

---

*Last updated: 2026-05-19*
