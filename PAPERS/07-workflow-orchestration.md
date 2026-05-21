# Workflow Orchestration and Pipeline Paper Collection

> This category collects academic papers related to AI workflow orchestration, pipeline design, SOP encoding, and multi-step reasoning
> Related to Heya architecture: Factory Pipeline design, SOP encoding format, verification pipeline

---

## 1. Multi-Agent Workflow Orchestration

### 1.1 AutoGen: Enabling Next-Gen LLM Applications via Multi-Agent Conversation
- **Authors**: Qingyun Wu, Gagan Bansal, Jieyu Zhang et al.
- **Year**: 2023
- **Institution**: Microsoft Research, Penn State University
- **Core Contribution**: Open-source multi-agent conversation framework supporting various combinations of LLMs, human input, and tools
- **Key Technologies**: Multi-agent conversation orchestration, human-AI collaboration loop, tool calling
- **Heya Relevance**: **Adaptive Adoption** - Reference for Agent Mesh communication patterns
- **Link**: [arXiv:2308.08155](https://arxiv.org/abs/2308.08155)

### 1.2 ChatDev: Communicative Agents for Software Development
- **Authors**: Chen Qian, Wei Liu, Hongzhang Liu et al.
- **Year**: 2023 (ACL 2024)
- **Institution**: Tsinghua University, OpenBMB
- **Core Contribution**: LLM-driven virtual software company framework, completing the full workflow through "Chat Chain" and "Communicative Dehallucination" mechanisms
- **Key Technologies**: Chat Chain communication protocol, Communicative Dehallucination, role-playing collaboration
- **Heya Relevance**: **Direct Reference** - Empirical foundation for Factory pipeline-style collaboration
- **Link**: [arXiv:2307.07924](https://arxiv.org/abs/2307.07924)

### 1.3 MetaGPT: Meta Programming for A Multi-Agent Collaborative Framework
- **Authors**: Sirui Hong, Mingchen Zhuge, Jiaqi Chen et al.
- **Year**: 2023 (ICLR 2024)
- **Institution**: DeepWisdom, Peking University, KAUST, IDSIA
- **Core Contribution**: Encodes human workflow SOPs as prompt sequences, enabling pipeline-style multi-role collaboration with significantly reduced cascading hallucinations
- **Key Technologies**: SOP encoding as prompt sequences, pipeline paradigm, intermediate result verification
- **Heya Relevance**: **Direct Adoption** - Core reference for Factory SOP encoding format
- **Link**: [arXiv:2308.00352](https://arxiv.org/abs/2308.00352)

### 1.4 AgentVerse: Facilitating Multi-Agent Collaboration and Exploring Emergent Behaviors
- **Authors**: Weize Chen, Yusheng Su, Jingwei Zuo et al.
- **Year**: 2023
- **Institution**: Tsinghua University, OpenBMB, Microsoft
- **Core Contribution**: Multi-agent framework inspired by human group dynamics, studying emergent behaviors
- **Key Technologies**: Dynamic multi-agent composition adjustment, emergent behavior exploration, group dynamics
- **Heya Relevance**: **Reference** - Behavioral reference for Agent Mesh dynamic topology
- **Link**: [arXiv:2308.10848](https://arxiv.org/abs/2308.10848)

### 1.5 CAMEL: Communicative Agents for "Mind" Exploration of Large Language Model Society
- **Authors**: Guohao Li et al.
- **Year**: 2023 (NeurIPS 2023)
- **Institution**: Oxford University, KAUST
- **Core Contribution**: First role-playing-based multi-agent collaboration framework, mitigating instruction-following errors through Inception Prompting
- **Key Technologies**: Role-playing framework, Inception Prompting, task-script-driven collaboration
- **Heya Relevance**: **Adaptive Adoption** - Reference for Agent role specialization
- **Link**: [arXiv:2303.08761](https://arxiv.org/abs/2303.08761)

### 1.6 TaskWeaver: A Code-First Agent Framework
- **Authors**: Bo Qiao, Liqun Li, Xu Zhang et al.
- **Year**: 2023
- **Institution**: Microsoft Research
- **Core Contribution**: Code-first Agent framework that converts user requests into executable code, supporting rich data structures and dynamic plugin selection
- **Key Technologies**: Code-first architecture, dynamic plugin selection, secure code execution sandbox
- **Heya Relevance**: **Reference** - Factory code generation and execution sandbox
- **Link**: [arXiv:2311.17541](https://arxiv.org/abs/2311.17541)

### 1.7 WorkflowLLM: Enhancing Workflow Orchestration Capability of Large Language Models
- **Authors**: Shengda Fan, Xin Cong, Yuepeng Fu et al.
- **Year**: 2024
- **Institution**: Tsinghua University, OpenBMB
- **Core Contribution**: Data-centric framework enhancing LLM workflow orchestration capability, building the large-scale WorkflowBench dataset with 106,763 samples
- **Key Technologies**: WorkflowBench dataset, hierarchical thought generation, WorkflowLlama
- **Heya Relevance**: **Direct Reference** - Data and model reference for Factory workflow orchestration
- **Link**: [arXiv:2411.05451](https://arxiv.org/abs/2411.05451)

---

## 2. AI Agent Software Engineering

### 2.1 SWE-agent: Agent-Computer Interfaces Enable Automated Software Engineering
- **Authors**: John Yang, Carlos E. Jimenez, Alexander Wettig et al.
- **Year**: 2024 (NeurIPS 2024)
- **Institution**: Princeton University
- **Core Contribution**: Proposes custom Agent-Computer Interface (ACI) enabling LM Agents to autonomously solve software engineering tasks
- **Key Technologies**: ACI design, code file creation/editing, repository navigation, test execution
- **Heya Relevance**: **Direct Adoption** - ACI design reference for Factory verification system
- **Link**: [arXiv:2405.15793](https://arxiv.org/abs/2405.15793)

---

## 3. Cognitive Architecture and Agent Unified Framework

### 3.1 Cognitive Architectures for Language Agents (CoALA)
- **Authors**: Theodore R. Sumers, Shunyu Yao, Karthik Narasimhan, Thomas L. Griffiths
- **Year**: 2023 (TMLR 2024)
- **Institution**: Princeton University
- **Core Contribution**: Drawing from cognitive science, proposes a cognitive architecture for language agents, describing modular memory components, structured action spaces, and generalized decision processes
- **Key Technologies**: Modular memory architecture, structured action space, cognitive science and AI cross-disciplinary integration
- **Heya Relevance**: **Core Theory** - Design blueprint for Factory cognitive architecture
- **Link**: [arXiv:2309.02427](https://arxiv.org/abs/2309.02427)

### 3.2 A Survey on Large Language Model based Autonomous Agents
- **Authors**: Lei Wang, Chen Ma, Xueyang Feng et al.
- **Year**: 2023 (continuously updated)
- **Institution**: Renmin University of China
- **Core Contribution**: Proposes a unified LLM Agent framework (Profile -> Memory -> Planning -> Action)
- **Key Technologies**: Agent unified architecture, modular memory, structured action space
- **Heya Relevance**: **Theoretical Framework** - Modular design reference for Factory Agents
- **Link**: [arXiv:2308.11432](https://arxiv.org/abs/2308.11432)

---

## 4. Multi-Step Reasoning Pipelines

### 4.1 ReAct: Synergizing Reasoning and Acting in Language Models
- **Authors**: Shunyu Yao, Jeffrey Zhao, Dian Yu et al.
- **Year**: 2022 (ICLR 2023)
- **Institution**: Princeton University, Google Research
- **Core Contribution**: Landmark framework for interleaved reasoning and acting, alternating Thought-Act-Observe cycles
- **Key Technologies**: Reasoning-acting interleaving, external knowledge base interaction, error propagation mitigation
- **Heya Relevance**: **Direct Adoption** - Core of Agent reasoning-action loop
- **Link**: [arXiv:2210.03629](https://arxiv.org/abs/2210.03629)

### 4.2 Reflexion: Language Agents with Verbal Reinforcement Learning
- **Authors**: Noah Shinn, Federico Cassano, Edward Berman et al.
- **Year**: 2023 (NeurIPS 2023)
- **Institution**: Northeastern University, Princeton University
- **Core Contribution**: Agents iteratively optimize behavior through linguistic reflection, achieving 91% pass@1 on HumanEval
- **Key Technologies**: Verbal reinforcement learning, episodic memory buffer, self-reflection mechanism
- **Heya Relevance**: **Direct Adoption** - Factory self-reflection and improvement loop
- **Link**: [arXiv:2303.11366](https://arxiv.org/abs/2303.11366)

---

## Heya Application Summary

| Paper/Technology | Application Scenario | Priority |
|-----------------|---------------------|----------|
| MetaGPT | Factory SOP encoding format | P0 - Must |
| CoALA | Factory cognitive architecture | P0 - Must |
| ReAct | Agent reasoning-action loop | P0 - Must |
| Reflexion | Self-reflection and improvement | P0 - Must |
| SWE-agent | Verification system ACI design | P0 - Must |
| ChatDev | Pipeline-style collaboration evidence | P1 - Important |
| WorkflowLLM | Workflow orchestration reference | P1 - Important |
| LLM Agent Survey | Agent modular design | P1 - Important |
| AutoGen | Agent Mesh communication reference | P1 - Important |
| TaskWeaver | Code execution sandbox | P2 - Reference |

---

## Key Insights

### Factory Pipeline Design Principles (Based on Paper Research)

1. **SOP encoding**: Encode standard operating procedures as executable prompt sequences (MetaGPT)
2. **Intermediate verification**: Each pipeline step has a verification gate to prevent error propagation (MetaGPT, SWE-agent)
3. **Structured communication**: Use structured documents between steps rather than free-form dialog (MetaGPT, ChatDev)
4. **Cognitive architecture**: Modular memory + Structured action space + Generalized decision process (CoALA)
5. **Closed-loop feedback**: Complete cycle of Reasoning -> Action -> Observation -> Reflection (ReAct, Reflexion)

---

*Last updated: 2026-05-17*
