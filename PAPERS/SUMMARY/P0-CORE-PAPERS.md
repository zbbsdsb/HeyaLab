# HeyaLab P0 Core Papers Quick Reference

> 26 core papers that the Heya AI Factory architecture must adopt

---

## AI Memory Systems (4 papers)

### 1. 6.1 GraphRAG: Using Knowledge Graphs for Retrieval-Augmented Generation
- **Authors**: Microsoft Research Team
- **Year**: 2024
- **Core Contribution**: Combines knowledge graphs with RAG, capturing relationships between entities through graph structures to improve retrieval quality for complex reasoning tasks
- **Heya Relevance**: **Adaptive Adoption** - Graph structure enhancement for P006 hybrid retrieval, supporting relational reasoning
- **Link**: [arXiv:2404.16130](https://arxiv.org/abs/2404.16130)

### 2. 6.5 Hybrid Search: Combining BM25 and Dense Retrieval
- **Authors**: Synthesis of multiple studies
- **Heya Relevance**: **Direct Adoption** - Core strategy for P006 hybrid retrieval

### 3. 8.1 Recurrent Memory Transformer
- **Authors**: Anna M. Mikhalkova, Yu. A. Malkov, Ivan V. Mazurenko, Alexey P. Sinitca, Liliya M. Zintsova, Sergey O. Kuznetsov, Leonid E. Zhukov
- **Year**: 2023
- **Core Contribution**: Proposes recurrent memory Transformer, processing infinite-length sequences through compressed memory states
- **Heya Relevance**: **Adaptive Adoption** - Technical reference for P005 intelligent compression
- **Link**: [arXiv:2305.02541](https://arxiv.org/abs/2305.02541)

### 4. 8.2 Compressive Transformer
- **Authors**: Jack W. Rae, Leonard Bartlett, Tim Cooijmans, Anna Harutyunyan, Abram L. Fyshe, Matthew W. Hoffman, Janos K. Pal, Doina Precup, Adria Puigdomenech, Jonathan Schwarz, Sindy R. Lowe, Danilo J. Rezende, Leonard Blier, Corey Lynch, Irina Rish, Remi Tachet des Combes, Jelena Luketina, Razvan Pascanu, Sander Dieleman, Yori Zwols, Michal J. Johnson, Alexander Pritzel, Will Dabney, Thomas P. Minka, Ian Osband, Neil C. Rabinowitz, Greg Thornton, Nicolas Sonnerat, Francis Song, Chris J. Maddison, George E. Dahl, Ivo Kweon, Mohammad Gheshlaghi Azar, Neil Houlsby, Allan Jaffe, Razvan Pascanu, Nando de Freitas, Timothy P. Lillicrap, Shane Legg, Demis Hassabis, Murray Shanahan, David Silver
- **Year**: 2019
- **Core Contribution**: Proposes compressive Transformer, compressing historical information into compact representations to extend effective context length
- **Heya Relevance**: **Adaptive Adoption** - Core technology for P005 intelligent compression
- **Link**: [ICLR 2019](https://arxiv.org/abs/1911.05507)

---

## Self-Evolution and Meta-Learning (2 papers)

### 1. 4.1 EvoPrompt: Connecting LLMs with Evolutionary Algorithms Yields Powerful Prompt Optimizers
- **Authors**: Qingyan Guo, Rui Wang, Junliang Guo et al.
- **Year**: 2023 (ICLR 2024)
- **Core Contribution**: Combines evolutionary algorithms (EA) with LLMs for discrete prompt optimization, significantly outperforming human prompts on 31 datasets
- **Heya Relevance**: **Direct Adoption** - Evolution mechanism for Factory DNA (P015)
- **Link**: [arXiv:2309.08532](https://arxiv.org/abs/2309.08532)

### 2. 4.3 AlphaEvolve: Evolutionary Algorithm Discovery with Large Language Models
- **Authors**: Google DeepMind team (in collaboration with Terence Tao)
- **Year**: 2025 (Nature 2025)
- **Core Contribution**: Upgraded general-purpose scientific agent from FunSearch, using algorithm source code as "genome," breaking the 56-year efficiency benchmark for matrix multiplication
- **Heya Relevance**: **Core Reference** - Technical blueprint for MetaFactory (P013)
- **Link**: [Nature](https://www.nature.com/articles/s41586-025-08948-7)

---

## Human-AI Collaboration and Cognitive Load (3 papers)

### 1. 2.1 The Transparency Paradox in Explainable AI: A Theory of Autonomy Depletion Through Cognitive Load
- **Authors**: Ancuta Margondai, Mustapha Mouloua
- **Year**: 2026
- **Core Contribution**: Discovers that transparency is not universally beneficial -- the same AI explanations improve decisions in some contexts but impair them in others. Models autonomy as a continuous random variable
- **Heya Relevance**: **Direct Adoption** - Theoretical foundation for P012 Cognitive Load Manager
- **Link**: [arXiv:2601.13973](https://arxiv.org/abs/2601.13973)

### 2. 3.1 Learning to Trust: How Humans Mentally Recalibrate AI Confidence Signals
- **Authors**: ZhaoBin Li, Mark Steyvers
- **Year**: 2026
- **Core Contribution**: Behavioral experiments (N=200) studying how humans mentally recalibrate AI confidence signals through repeated experience
- **Heya Relevance**: **Direct Adoption** - Empirical foundation for P010 Trust Score Model
- **Link**: [arXiv:2603.22634](https://arxiv.org/abs/2603.22634)

### 3. 4.1 Integrated Design and Governance of Agentic AI Systems through Adaptive Information Modulation
- **Authors**: Qiliang Chen, Sepehr Ilami, Nunzio Lore, Babak Heydari
- **Year**: 2024
- **Core Contribution**: For multi-agent architectures containing humans and LLM autonomous agents, proposes integrated design and governance through adaptive information modulation
- **Heya Relevance**: **Direct Adoption** - Governance framework for P011 Dynamic Permission System
- **Link**: [arXiv:2409.10372](https://arxiv.org/abs/2409.10372)

---

## Knowledge Transfer and Cross-Domain Learning (1 paper)

### 1. 2.2 Decompose and Recompose: Reasoning New Skills from Existing Abilities
- **Authors**: Multi-institution collaboration
- **Year**: 2026
- **Core Contribution**: Reasons new skills by decomposing and recomposing existing abilities, achieving cross-task generalization without parameter updates
- **Heya Relevance**: **Direct Adoption** - Core mechanism for Factory DNA crossover (P015)
- **Link**: [arXiv](https://arxiv.org/) (submitted May 2026)

---

## Neuroscience Foundations (12 papers)

### 1. 2.3 The Magical Number Seven, Plus or Minus Two: Some Limits on Our Capacity for Processing Information
- **Authors**: George A. Miller
- **Year**: 1956
- **Core Contribution**: Discovered that human working memory capacity is approximately 7+-2 information chunks, proposing "chunking" strategies to overcome capacity limitations
- **Heya Relevance**: **Direct Adoption** - Theoretical foundation for P012 Cognitive Load Manager: the amount of information Factory presents to humans should not exceed cognitive capacity limits, requiring intelligent grouping and summarization
- **Link**: [Psychological Review](https://doi.org/10.1037/h0043158)

### 2. 2.8 Sleep, Memory, and Plasticity
- **Authors**: Matthew P. Walker, Robert Stickgold
- **Year**: 2004/2006
- **Core Contribution**: Systematic review of sleep's role in memory consolidation, distinguishing REM and NREM sleep functions for different types of memory consolidation
- **Heya Relevance**: **Adaptive Adoption** - Biological basis for P005 intelligent compression: during sleep, the brain automatically performs memory compression (semantic extraction) and redundant information clearing
- **Link**: [Annual Review of Psychology](https://doi.org/10.1146/annurev.psych.57.102904.190156)

### 3. 2.10 The Neuroscience of Forgetting
- **Authors**: Paul W. Frankland, Sheena Josselyn
- **Year**: 2022
- **Core Contribution**: Review of neural mechanisms of forgetting, proposing that forgetting is an active process rather than passive decay, including synaptic pruning, neurogenesis-driven forgetting, and interference
- **Heya Relevance**: **Adaptive Adoption** - "Forgetting" mechanism in P004 memory lifecycle management: forgetting is not memory loss but an active information management strategy
- **Link**: [Annual Review of Neuroscience](https://doi.org/10.1146/annurev-neuro-100321-114636)

### 4. 3.1 Thinking, Fast and Slow
- **Authors**: Daniel Kahneman
- **Year**: 2011
- **Core Contribution**: Proposed the dual-system theory of human thinking: System 1 (fast, intuitive, automatic) and System 2 (slow, rational, effortful); awarded the 2002 Nobel Prize in Economics
- **Heya Relevance**: **Direct Adoption** - Core theory for P010 Trust Score Model: humans use System 1 for quick trust/rejection and System 2 for rational evaluation. Heya's "AI proposes -> human decides" needs to understand human cognitive biases
- **Link**: [Farrar, Straus and Giroux](https://us.macmillan.com/books/9780374533557/thinking-fast-and-slow)

### 5. 3.2 Cognitive Load Theory
- **Authors**: John Sweller
- **Year**: 1988
- **Core Contribution**: Proposed cognitive load theory, distinguishing intrinsic, extraneous, and germane cognitive load, guiding instructional and interface design
- **Heya Relevance**: **Direct Adoption** - Core theory for P012 Cognitive Load Manager: Factory needs to minimize extraneous load and optimize germane load when presenting information to humans
- **Link**: [Cognitive Science](https://doi.org/10.1207/s15516709cog1204_4)

### 6. 3.3 The Neuroscience of Trust
- **Authors**: Paul J. Zak
- **Year**: 2017
- **Core Contribution**: Systematic study of the neural mechanisms of trust, discovering that oxytocin is a key neurotransmitter for trust, proposing biological markers of trust
- **Heya Relevance**: **Direct Adoption** - Biological foundation for P010 Trust Score Model: trust is not static but a neurochemical process that can be dynamically regulated through experience
- **Link**: [Harvard Business Review](https://hbr.org/2017/07/the-neuroscience-of-trust)

### 7. 3.8 The Role of the Amygdala in Decision-Making
- **Authors**: Elizabeth A. Phelps, Joseph E. LeDoux
- **Year**: 2005
- **Core Contribution**: Review of the amygdala's role in emotional decision-making, revealing neural mechanisms of fear conditioning, risk assessment, and emotional memory
- **Heya Relevance**: **Reference** - Reference for P011 Dynamic Permission System: human risk perception is modulated by the amygdala; Factory should adjust interaction based on human emotional states
- **Link**: [Neuron](https://doi.org/10.1016/j.neuron.2005.09.012)

### 8. 4.1 Small-World Brain Networks
- **Authors**: Olaf Sporns, Jonathan D. Zwi
- **Year**: 2004
- **Core Contribution**: Discovered that brain structural connectivity networks have small-world properties (high clustering coefficient + short path length), revealing the topological basis for efficient information transmission in the brain
- **Heya Relevance**: **Direct Adoption** - Core reference for P007 dynamic topology: Agent Mesh should adopt small-world network topology with both tight local connections and efficient global communication
- **Link**: [PLoS Computational Biology](https://doi.org/10.1371/journal.pcbi.0020059)

### 9. 4.6 Dynamic Reconfiguration of Functional Brain Networks During Cognition
- **Authors**: M. P. van den Heuvel, R. C. W. Mandl, R. S. Kahn, H. E. Hulshoff Pol
- **Year**: 2009
- **Core Contribution**: Discovered that brain functional networks dynamically reconfigure during cognitive tasks: the default mode network decreases during tasks, task-related networks increase, and inter-network connectivity patterns change with task type
- **Heya Relevance**: **Adaptive Adoption** - Direct biological basis for P007 dynamic topology: Factory's Agent Mesh should dynamically adjust network topology based on task type
- **Link**: [Journal of Neuroscience](https://doi.org/10.1523/JNEUROSCI.3834-08.2009)

### 10. 4.7 The Connectome: How the Brain's Wiring Makes Us Who We Are
- **Authors**: Sebastian Seung
- **Year**: 2012
- **Core Contribution**: Proposed the "connectome" concept, arguing that the brain's connection patterns determine individual identity and capability differences
- **Heya Relevance**: **Reference** - Heya's "Factory DNA" (P015) concept: Factory capabilities are determined by its "connectome" (how capabilities are connected)
- **Link**: [Houghton Mifflin Harcourt](https://www.hmhbooks.com/the-connectome/)

### 11. 5.3 Adult Hippocampal Neurogenesis
- **Authors**: Fred H. Gage
- **Year**: 2002
- **Core Contribution**: Confirmed the existence of continuous neurogenesis in the adult hippocampus; newborn neurons can integrate into existing neural circuits and participate in learning
- **Heya Relevance**: **Adaptive Adoption** - Biological basis for P003 core/extension separation: adult neurogenesis corresponds to Factory's "extension layer" -- new capabilities can be added without affecting core structure
- **Link**: [Science](https://doi.org/10.1126/science.1076550)

### 12. 5.5 Critical Period Plasticity in the Visual Cortex
- **Authors**: Takao K. Hensch
- **Year**: 2005
- **Core Contribution**: Review of molecular mechanisms of critical period plasticity in the visual cortex, revealing the role of inhibitory neural networks in opening and closing critical periods
- **Heya Relevance**: **Reference** - Reference for P003 core/extension separation: the core layer becomes stable (immutable) after the "critical period," while the extension layer maintains plasticity
- **Link**: [Nature Reviews Neuroscience](https://doi.org/10.1038/nrn1821)

---

## Software Engineering and System Architecture (4 papers)

### 1. 2.6 Feature Toggles (Feature Flags): A Feature Management Pattern
- **Authors**: Pete Hodgson
- **Year**: 2014 (martinfowler.com)
- **Core Contribution**: Systematic exposition of the Feature Toggle pattern, decoupling feature release from code deployment
- **Heya Relevance**: **Reference** - Dynamic enable/disable of Factory capabilities, supporting canary releases in sandbox deployment (P002)
- **Link**: [martinfowler.com](https://martinfowler.com/articles/feature-toggles.html)

### 2. 2.7 Testing in Production: The Safe Way
- **Authors**: Casey Rosenthal, Nora Jones
- **Year**: 2019
- **Core Contribution**: Proposes safe production testing strategies, including shadow deployment, canary releases, and A/B testing
- **Heya Relevance**: **Reference** - Shadow/Canary/Full three-stage strategy in Factory sandbox deployment (P002)
- **Link**: [O'Reilly](https://www.oreilly.com/library/view/testing-in-production/9781492083103/)

### 3. 5.4 Self-Healing Systems: A Systematic Literature Review
- **Authors**: Multi-institution collaboration
- **Year**: 2010+
- **Core Contribution**: Systematic review of self-healing system methods, covering the complete process of fault detection, diagnosis, and recovery
- **Heya Relevance**: **Adaptive Adoption** - Design reference for P009 self-healing Mesh: health check -> fault detection -> task migration -> state recovery
- **Link**: [Journal of Systems and Software](https://www.sciencedirect.com/journal/journal-of-systems-and-software)

### 4. 6.4 Evolutionary Software Architecture: How to Evolve Your Architecture
- **Authors**: Patrick Kua, Evan Bottcher, Nick Tune
- **Year**: 2017
- **Core Contribution**: Proposes architecture evolution strategies, including incremental evolution, strategic technical debt management, and architecture fitness functions
- **Heya Relevance**: **Adaptive Adoption** - Architecture evolution strategy reference for P013 MetaFactory; the "fitness function" concept can be used to measure Factory design quality
- **Link**: [O'Reilly](https://www.oreilly.com/library/view/evolutionary-architecture/9781492054246/)

---

## EVOLVE Proposal to P0 Paper Mapping

| EVOLVE Proposal | P0 Paper | Category |
|----------------|----------|----------|
| P001 Adaptive Triggering | Self-Evolving AI Agents Survey | Self-Evolution and Meta-Learning |
| P001 Adaptive Triggering | MemRL | Self-Evolution and Meta-Learning |
| P002 Sandbox Deployment | SWE-agent | Workflow Orchestration |
| P002 Sandbox Deployment | Continuous Delivery | Software Engineering and System Architecture |
| P003 Core/Extension Separation | MemRL | Self-Evolution and Meta-Learning |
| P003 Core/Extension Separation | Hebb (1949) | Neuroscience Foundations |
| P004 Memory Lifecycle | MemGPT | AI Memory Systems |
| P004 Memory Lifecycle | Baddeley Working Memory | Neuroscience Foundations |
| P004 Memory Lifecycle | Squire Memory Classification | Neuroscience Foundations |
| P005 Intelligent Compression | MemGPT | AI Memory Systems |
| P005 Intelligent Compression | Sleep Memory Consolidation | Neuroscience Foundations |
| P006 Hybrid Retrieval | FAISS/HNSW | AI Memory Systems |
| P006 Hybrid Retrieval | DPR | AI Memory Systems |
| P006 Hybrid Retrieval | Hippocampal Pattern Separation | Neuroscience Foundations |
| P007 Dynamic Topology | TarMAC | Multi-Agent Coordination |
| P007 Dynamic Topology | Small-World Brain Networks | Neuroscience Foundations |
| P009 Self-Healing Mesh | MAPE-K | Software Engineering and System Architecture |
| P009 Self-Healing Mesh | Self-Healing Systems | Software Engineering and System Architecture |
| P010 Trust Model | Learning to Trust | Human-AI Collaboration |
| P010 Trust Model | Kahneman Dual-System | Neuroscience Foundations |
| P010 Trust Model | Zak Trust Neuroscience | Neuroscience Foundations |
| P011 Dynamic Permissions | Adaptive Information Modulation | Human-AI Collaboration |
| P011 Dynamic Permissions | Miller Working Memory Capacity | Neuroscience Foundations |
| P012 Cognitive Load | Transparency Paradox | Human-AI Collaboration |
| P012 Cognitive Load | Sweller Cognitive Load Theory | Neuroscience Foundations |
| P013 MetaFactory | AlphaEvolve | Self-Evolution and Meta-Learning |
| P013 MetaFactory | Lehman Software Evolution Laws | Neuroscience Foundations |

---

*Auto-generated on 2026-05-19*
