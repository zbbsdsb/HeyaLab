# Intent Understanding and Task Planning Paper Collection

> This category collects academic papers related to AI intent understanding, task decomposition, and planning reasoning
> Related to Heya architecture: Factory Designer core capabilities, intent-driven design (ADR-004)

---

## 1. Task Planning and Decomposition

### 1.1 Plan-and-Solve Prompting: Improving Zero-Shot Chain-of-Thought Reasoning
- **Authors**: Lei Wang, Wanyu Xu, Yihuai Lan, Zhiqiang Hu, Yunshi Lan, Roy Ka-Wei Lee, Ee-Peng Lim
- **Year**: 2023 (ACL 2023)
- **Institution**: Singapore Management University, Alibaba Group
- **Core Contribution**: Proposes the Plan-and-Solve (PS) prompting strategy, which first decomposes the entire task into a subtask plan and then executes step by step, effectively addressing the "missing steps" error in Zero-shot-CoT
- **Key Technologies**: Two-stage prompting (plan first, execute later), zero-shot chain-of-thought reasoning, PS+ detailed instruction extension
- **Heya Relevance**: **Direct Adoption** - Factory Designer intent decomposition process
- **Link**: [arXiv:2305.04091](https://arxiv.org/abs/2305.04091)

### 1.2 Tree of Thoughts: Deliberate Problem Solving with Large Language Models
- **Authors**: Shunyu Yao, Dian Yu, Jeffrey Zhao, Izhak Shafran, Thomas L. Griffiths, Yuan Cao, Karthik Narasimhan
- **Year**: 2023 (NeurIPS 2023)
- **Institution**: Princeton University, Google DeepMind
- **Core Contribution**: Extends LLM reasoning from linear chains to tree-structured search, allowing the model to explore multiple possibilities at each reasoning step with self-evaluation, lookahead, and backtracking
- **Key Technologies**: Tree-of-thought search, BFS/DFS traversal, self-evaluation
- **Heya Relevance**: **Adaptive Adoption** - Multi-path exploration for Factory design alternatives
- **Link**: [arXiv:2305.10601](https://arxiv.org/abs/2305.10601)

### 1.3 Graph of Thoughts: Solving Elaborate Problems with Large Language Models
- **Authors**: Maciej Besta, Nils Blach, Ales Kubicek, Robert Gerstenberger, Michal Podstawski et al.
- **Year**: 2023 (AAAI 2024)
- **Institution**: ETH Zurich
- **Core Contribution**: Generalizes ToT's tree structure into a directed acyclic graph (DAG), supporting thought merging, refinement, and feedback loops
- **Key Technologies**: Graph-structured thought modeling, thought merging, thought refinement, loop enhancement
- **Heya Relevance**: **Adaptive Adoption** - Graph-based reasoning for complex Factory design
- **Link**: [arXiv:2308.09687](https://arxiv.org/abs/2308.09687)

### 1.4 Understanding the Planning of LLM Agents: A Survey
- **Authors**: Xu Huang, Weiwen Liu, Xiaolong Chen, Xingmei Wang, Hao Wang, Defu Lian, Yasheng Wang, Ruiming Tang, Enhong Chen
- **Year**: 2024
- **Institution**: University of Science and Technology of China, Alibaba Group
- **Core Contribution**: First systematic survey of LLM Agent planning capabilities, categorizing existing work into five dimensions: task decomposition, plan selection, external modules, reflection, and memory
- **Key Technologies**: Five-dimensional planning taxonomy, task decomposition strategies, plan selection mechanisms, reflection and memory enhancement
- **Heya Relevance**: **Theoretical Framework** - Systematic reference for Factory planning capabilities
- **Link**: [arXiv:2402.02716](https://arxiv.org/abs/2402.02716)

---

## 2. Natural Language Intent to Structured Action

### 2.1 HuggingGPT: Solving AI Tasks with ChatGPT and its Friends in Hugging Face
- **Authors**: Yongliang Shen, Kaitao Song, Xu Tan, Dongsheng Li, Weiming Lu, Yueting Zhuang
- **Year**: 2023
- **Institution**: Zhejiang University, Microsoft
- **Core Contribution**: Uses LLM as a controller to connect various AI models, enabling automatic planning and execution of complex cross-modal, cross-domain tasks
- **Key Technologies**: LLM as central controller, task decomposition and dependency ordering, description-based model selection
- **Heya Relevance**: **Direct Reference** - Factory capability assembly pattern
- **Link**: [arXiv:2303.17580](https://arxiv.org/abs/2303.17580)

### 2.2 ToolLLM: Facilitating Large Language Models to Master 16000+ Real-world APIs
- **Authors**: Yujia Qin, Shihao Liang, Yining Ye et al.
- **Year**: 2023
- **Institution**: Tsinghua University, ModelBest
- **Core Contribution**: Constructs the ToolBench dataset (16,464 real APIs) and proposes a DFS decision tree reasoning algorithm
- **Key Technologies**: ToolBench dataset, DFS decision tree reasoning, neural API retriever
- **Heya Relevance**: **Adaptive Adoption** - Factory tool selection and orchestration
- **Link**: [arXiv:2307.16789](https://arxiv.org/abs/2307.16789)

### 2.3 ViperGPT: Visual Inference via Python Execution for Reasoning
- **Authors**: Didac Suris, Sachit Menon, Carl Vondrick
- **Year**: 2023
- **Institution**: Columbia University
- **Core Contribution**: Combines vision-language models into Python subroutines using code generation models, completing complex reasoning tasks through code execution
- **Key Technologies**: Code generation as reasoning intermediate representation, zero-training inference
- **Heya Relevance**: **Reference** - Concept of code as reasoning intermediate representation
- **Link**: [arXiv:2303.08128](https://arxiv.org/abs/2303.08128)

---

## 3. Hierarchical Task Networks and Interactive Planning

### 3.1 DEPS: Describe, Explain, Plan and Select -- Interactive Planning with LLMs
- **Authors**: Zihao Wang, Shaofei Cai, Guanzhou Chen, Anji Liu, Xiaojian Ma, Yitao Liang
- **Year**: 2023 (NeurIPS 2023)
- **Institution**: The University of Hong Kong, UCLA
- **Core Contribution**: Proposes an interactive planning method that corrects LLM initial plans by describing execution processes and explaining failure causes
- **Key Technologies**: Hierarchical task decomposition, execution feedback description, failure self-explanation, trainable objective ranker
- **Heya Relevance**: **Direct Adoption** - Factory execution feedback and plan correction loop
- **Link**: [arXiv:2302.01560](https://arxiv.org/abs/2302.01560)

### 3.2 ProgPrompt: Generating Situated Robot Task Plans using Large Language Models
- **Authors**: Ishika Singh, Valts Blukis, Arsalan Mousavian, Ankit Goyal et al.
- **Year**: 2022 (ICRA 2023)
- **Institution**: USC, NVIDIA, University of Washington
- **Core Contribution**: Proposes programmatic LLM prompt structure, injecting available actions and objects in program specification form into prompts
- **Key Technologies**: Programmatic prompt design, environment-aware action constraints, code-style plan generation
- **Heya Relevance**: **Adaptive Adoption** - Factory plan generation under environment constraints
- **Link**: [arXiv:2209.11302](https://arxiv.org/abs/2209.11302)

### 3.3 Inner Monologue: Embodied Reasoning through Planning with Language Models
- **Authors**: Wenlong Huang, Fei Xia, Ted Xiao et al.
- **Year**: 2022
- **Institution**: Google Research, UC Berkeley
- **Core Contribution**: Enables LLMs to form an "inner monologue" using multiple feedback sources in embodied environments, achieving closed-loop planning
- **Key Technologies**: Closed-loop language feedback, multi-source feedback integration, high-level instruction to low-level action mapping
- **Heya Relevance**: **Reference** - Factory closed-loop execution and feedback mechanism
- **Link**: [arXiv:2207.05608](https://arxiv.org/abs/2207.05608)

---

## 4. Autonomous Planning Agents

### 4.1 Voyager: An Open-Ended Embodied Agent with Large Language Models
- **Authors**: Guanzhi Wang, Yuqi Xie, Yunfan Jiang, Ajay Mandlekar et al.
- **Year**: 2023
- **Institution**: NVIDIA, Caltech, UT Austin, Stanford
- **Core Contribution**: First LLM-driven Minecraft open-ended lifelong learning agent, featuring automatic curriculum, skill library, and iterative prompting mechanisms
- **Key Technologies**: Automatic curriculum, composable skill library, iterative prompting
- **Heya Relevance**: **Direct Reference** - Skill accumulation and Factory self-improvement
- **Link**: [arXiv:2305.16291](https://arxiv.org/abs/2305.16291)

### 4.2 Generative Agents: Interactive Simulacra of Human Behavior
- **Authors**: Joon Sung Park, Joseph C. O'Brien, Carrie J. Cai et al.
- **Year**: 2023 (UIST 2023)
- **Institution**: Stanford University, Google Research
- **Core Contribution**: Extends LLMs to store complete experience records, synthesize memories into high-level reflections, and dynamically retrieve memories to plan behavior
- **Key Technologies**: Memory stream, reflection, recursive planning
- **Heya Relevance**: **Adaptive Adoption** - Memory-driven planning mechanism
- **Link**: [arXiv:2304.03442](https://arxiv.org/abs/2304.03442)

---

## Heya Application Summary

| Paper/Technology | Application Scenario | Priority |
|-----------------|---------------------|----------|
| Plan-and-Solve | Factory Designer intent decomposition | P0 - Must |
| DEPS | Factory execution feedback loop | P0 - Must |
| HuggingGPT | Factory capability assembly pattern | P1 - Important |
| ToolLLM | Factory tool selection and orchestration | P1 - Important |
| Tree/Graph of Thoughts | Factory design alternative exploration | P1 - Important |
| Voyager | Skill accumulation and self-improvement | P1 - Important |
| LLM Agent Planning Survey | Systematic reference for planning capabilities | P1 - Important |

---

*Last updated: 2026-05-17*
