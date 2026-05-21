# Neuroscience Foundations Paper Collection

> This category collects core neuroscience papers and works from 1950 onwards, covering neuron models, memory systems, cognitive decision-making, brain networks, neural plasticity, consciousness theory, and brain-inspired computing
> Related to Heya architecture: Memory Layer (P004-P006), Agent Mesh (P007-P009), Human-AI Collaboration (P010-P012), Self-Evolution (P001-P003, P013-P015)

---

## 1. Neurons and Neural Network Foundations (1950s-1980s)

### 1.1 A Logical Calculus of the Ideas Immanent in Nervous Activity
- **Authors**: Warren S. McCulloch, Walter Pitts
- **Year**: 1943
- **Institution**: University of Illinois, University of Chicago
- **Core Contribution**: Proposed the first formalized neuron model (M-P model), proving that neural networks can in principle compute any computable function
- **Key Technologies**: Formalized neurons, binary logic gates, neural network computational universality
- **Heya Relevance**: **Theoretical Foundation** - The theoretical starting point of neural networks and AI, the ultimate biological source of Heya's "AI organizes itself as a Factory"
- **Link**: [Bulletin of Mathematical Biophysics](https://doi.org/10.1007/BF02478259)

### 1.2 The Organization of Behavior: A Neuropsychological Theory
- **Authors**: Donald O. Hebb
- **Year**: 1949
- **Publisher**: Wiley
- **Core Contribution**: Proposed Hebbian Learning: "neurons that fire together wire stronger," providing the theoretical foundation for synaptic plasticity and learning
- **Key Technologies**: Hebbian learning rule, Cell Assembly, Phase Sequence
- **Heya Relevance**: **Direct Adoption** - The fundamental law of synaptic plasticity. Heya's "Factory learns from experience" corresponds to Hebbian learning; the "Cell Assembly" concept corresponds to self-organization of Factory capabilities
- **Link**: [Wiley](https://www.wiley.com/en-us/The+Organization+of+Behavior%3A+A+Neuropsychological+Theory-p-9780471367277)

### 1.3 The Perceptron: A Probabilistic Model for Information Storage and Organization in the Brain
- **Authors**: Frank Rosenblatt
- **Year**: 1958
- **Institution**: Cornell Aeronautical Laboratory
- **Core Contribution**: Proposed the Perceptron model, the first learnable artificial neural network, pioneering connectionist research
- **Key Technologies**: Perceptron learning rule, pattern classification, linear separability
- **Heya Relevance**: **Historical Reference** - The starting point of connectionism; Heya's "AI as Factory, not tool" concept can be traced back to connectionism's distributed processing philosophy
- **Link**: [Psychological Review](https://doi.org/10.1037/h0042519)

### 1.4 Perceptrons: An Introduction to Computational Geometry
- **Authors**: Marvin Minsky, Seymour Papert
- **Year**: 1969
- **Publisher**: MIT Press
- **Core Contribution**: Rigorously analyzed the limitations of perceptrons (XOR problem), revealing the fundamental constraints of single-layer networks, indirectly driving research into multi-layer networks and backpropagation
- **Key Technologies**: Linear separability proof, computational geometry, network expressiveness limitations
- **Heya Relevance**: **Conceptual Reference** - Understanding the boundaries of AI system expressiveness; Heya needs multi-layer, multi-module Factory architecture to surpass simple perceptron limitations
- **Link**: [MIT Press](https://mitpress.mit.edu/books/perceptrons-introduction-computational-geometry)

### 1.5 What the Frog's Eye Tells the Frog's Brain
- **Authors**: Jerome Y. Lettvin, Humberto R. Maturana, Warren S. McCulloch, Walter H. Pitts
- **Year**: 1959
- **Institution**: MIT
- **Core Contribution**: Discovered 5 types of feature-detecting cells in the frog retina (sustained edge detectors, moving edge detectors, changing contrast detectors, dimming detectors, edge detectors), founding computational neuroscience
- **Key Technologies**: Feature detection, hierarchical information processing, visual system functional specificity
- **Heya Relevance**: **Reference** - Hierarchical information processing corresponds to Heya's "Verification L0-L4" progressive verification
- **Link**: [Proceedings of the IRE](https://doi.org/10.1109/JRPROC.1959.257241)

### 1.6 Receptive Fields of Single Neurons in the Cat's Striate Cortex
- **Authors**: David H. Hubel, Torsten N. Wiesel
- **Year**: 1959
- **Institution**: Harvard Medical School
- **Core Contribution**: Discovered the receptive field organization of visual cortex neurons: simple cells (orientation selectivity) and complex cells (position invariance), founding the hierarchical processing theory of the visual system
- **Key Technologies**: Receptive fields, simple cells/complex cells, orientation selectivity, hierarchical processing
- **Heya Relevance**: **Reference** - The visual cortex's hierarchical processing pattern (simple -> complex) corresponds to Heya's "Reasoning Engine multi-strategy" design
- **Link**: [Journal of Physiology](https://doi.org/10.1113/jphysiol.1959.sp006308)

### 1.7 Neocognitron: A Self-organizing Neural Network Model for a Mechanism of Pattern Recognition Unaffected by Shift in Position
- **Authors**: Kunihiko Fukushima
- **Year**: 1980
- **Institution**: NHK Science & Technical Research Laboratories
- **Core Contribution**: Proposed Neocognitron, the first deep neural network with convolution and pooling structures, directly inspiring later Convolutional Neural Networks (CNNs)
- **Key Technologies**: Convolutional layers, pooling layers, hierarchical feature extraction, shift invariance
- **Heya Relevance**: **Reference** - Hierarchical feature extraction corresponds to Heya Factory's "intent -> capability -> verification" layer-by-layer decomposition
- **Link**: [Biological Cybernetics](https://doi.org/10.1007/BF00344251)

### 1.8 Parallel Distributed Processing: Explorations in the Microstructure of Cognition (Vols. 1 & 2)
- **Authors**: David E. Rumelhart, James L. McClelland, and the PDP Research Group
- **Year**: 1986
- **Publisher**: MIT Press
- **Core Contribution**: The manifesto of connectionism, proposing the Parallel Distributed Processing (PDP) framework, including the rediscovery of backpropagation, distributed representations, and semantic networks
- **Key Technologies**: Backpropagation, distributed representations, hidden units, pattern association, sequential processing
- **Heya Relevance**: **Core Reference** - PDP framework's "distributed representations" correspond to Heya's "semantic memory" storage; "parallel processing" corresponds to Agent Mesh's distributed coordination
- **Link**: [MIT Press](https://mitpress.mit.edu/books/parallel-distributed-processing-explorations-microstructure-cognition)

### 1.9 How Does the Cerebral Cortex Work? Development of Ideas and the Discovery of Columnar Organization
- **Authors**: Vernon B. Mountcastle
- **Year**: 1998
- **Institution**: Johns Hopkins University
- **Core Contribution**: Discovered the columnar organization of the cerebral cortex, proposing that the basic unit of cortical processing is the "minicolumn"
- **Key Technologies**: Cortical columns, minicolumn structure, functional columns, cortical uniformity hypothesis
- **Heya Relevance**: **Reference** - The modular, repetitive structure of cortical columns corresponds to Heya Factory's "standardized capability module" design
- **Link**: [Journal of the American Medical Association](https://doi.org/10.1001/jama.279.2.186)

---

## 2. Memory Systems Neuroscience

### 2.1 Hippocampus as a Cognitive Map
- **Authors**: John O'Keefe, Lynn Nadel
- **Year**: 1978
- **Publisher**: Behavioral and Brain Sciences
- **Core Contribution**: Discovered place cells in the hippocampus, proposing the hippocampus as a cognitive map theory; awarded the 2014 Nobel Prize in Physiology or Medicine
- **Key Technologies**: Place cells, cognitive map, spatial navigation, hippocampal-entorhinal cortex circuit
- **Heya Relevance**: **Direct Adoption** - The hippocampus's "cognitive map" corresponds to Heya's "episodic memory retrieval": through spatial analogy, Factory can efficiently retrieve relevant memories like the hippocampus
- **Link**: [Behavioral and Brain Sciences](https://doi.org/10.1017/S0140525X00046557)

### 2.2 Working Memory
- **Authors**: Alan D. Baddeley, Graham J. Hitch
- **Year**: 1974 (initial) / 2000 (revised)
- **Institution**: University of Cambridge, University of York
- **Core Contribution**: Proposed the multi-component working memory model, including the Central Executive, Phonological Loop, Visuospatial Sketchpad, and Episodic Buffer
- **Key Technologies**: Central executive, phonological loop, visuospatial sketchpad, episodic buffer, multi-component model
- **Heya Relevance**: **Direct Adoption** - Heya Memory System's four-layer architecture directly corresponds to the Baddeley model: Working Memory (Central Executive) -> Episodic Memory (Episodic Buffer) -> Semantic Memory (Long-term memory)
- **Link**: [Science](https://doi.org/10.1126/science.255.5043.556)

### 2.3 The Magical Number Seven, Plus or Minus Two: Some Limits on Our Capacity for Processing Information
- **Authors**: George A. Miller
- **Year**: 1956
- **Institution**: Harvard University
- **Core Contribution**: Discovered that human working memory capacity is approximately 7+-2 information chunks, proposing "chunking" strategies to overcome capacity limitations
- **Key Technologies**: Working memory capacity, chunking, information processing bottleneck, channel capacity
- **Heya Relevance**: **Direct Adoption** - Theoretical foundation for P012 Cognitive Load Manager: the amount of information Factory presents to humans should not exceed cognitive capacity limits, requiring intelligent grouping and summarization
- **Link**: [Psychological Review](https://doi.org/10.1037/h0043158)

### 2.4 The Cognitive Neuroscience of Memory
- **Authors**: Larry R. Squire
- **Year**: 2004 (2nd Ed.)
- **Publisher**: Oxford University Press
- **Core Contribution**: Authoritative textbook on memory neuroscience, systematically describing the classification of declarative and nondeclarative memory and their neural bases
- **Key Technologies**: Declarative/nondeclarative memory, episodic/semantic memory, procedural memory, priming, memory consolidation
- **Heya Relevance**: **Direct Adoption** - Heya's four-layer memory architecture (Working/Episodic/Semantic/Artifact) is directly based on Squire's memory classification system
- **Link**: [Oxford University Press](https://global.oup.com/academic/product/the-cognitive-neuroscience-of-memory-9780195155653)

### 2.5 Memory: From Mind to Molecules
- **Authors**: Larry R. Squire, Eric R. Kandel
- **Year**: 1999/2008 (2nd Ed.)
- **Publisher**: Scientific American Library
- **Core Contribution**: Multi-level memory analysis from molecules to systems, covering synaptic plasticity, hippocampal function, and molecular mechanisms of memory consolidation
- **Key Technologies**: LTP/LTD, CREB protein, hippocampal-dependent consolidation, systems-level consolidation, memory reconsolidation
- **Heya Relevance**: **Direct Adoption** - Molecular mechanisms of memory consolidation correspond to Heya's "Episodic -> Semantic memory transformation": experience is extracted into semantic knowledge through the consolidation process
- **Link**: [Scientific American](https://www.scientificamerican.com/book/memory-from-mind-to-molecules/)

### 2.6 Reconsolidation and the Dynamic Nature of Memory
- **Authors**: Karim Nader, Oliver Hardt
- **Year**: 2009
- **Institution**: McGill University, Leibniz Institute
- **Core Contribution**: Systematic review of memory reconsolidation: consolidated memories become unstable after reactivation and require reconsolidation
- **Key Technologies**: Memory reconsolidation, retrieval-instability-reconsolidation window, protein synthesis dependence, memory update mechanisms
- **Heya Relevance**: **Adaptive Adoption** - Heya's "memory update" mechanism: when Factory recalls past experiences, it can modify original memories based on new feedback, rather than only appending new memories
- **Link**: [Annual Review of Neuroscience](https://doi.org/10.1146/annurev.neuro.32.091607.095730)

### 2.7 The Hippocampus and Memory: Insights from a Neuroimaging Perspective
- **Authors**: Anthony D. Wagner, Lila Davachi
- **Year**: 2001
- **Institution**: Stanford University
- **Core Contribution**: Studying the hippocampus's role in memory encoding, consolidation, and retrieval through neuroimaging, proposing a hippocampal-neocortical interactive memory system model
- **Key Technologies**: fMRI memory encoding studies, hippocampal activation patterns, encoding-retrieval asymmetry, pattern separation/completion
- **Heya Relevance**: **Adaptive Adoption** - The hippocampus's "pattern separation" and "pattern completion" correspond to exact matching and fuzzy matching in Heya memory retrieval
- **Link**: [Nature Reviews Neuroscience](https://doi.org/10.1038/35094573)

### 2.8 Sleep, Memory, and Plasticity
- **Authors**: Matthew P. Walker, Robert Stickgold
- **Year**: 2004/2006
- **Institution**: Harvard Medical School
- **Core Contribution**: Systematic review of sleep's role in memory consolidation, distinguishing REM and NREM sleep functions for different types of memory consolidation
- **Key Technologies**: Sleep-dependent memory consolidation, REM/slow-wave sleep division, hippocampal-neocortical memory transfer, sleep spindles
- **Heya Relevance**: **Adaptive Adoption** - Biological basis for P005 intelligent compression: during sleep, the brain automatically performs memory compression (semantic extraction) and redundant information clearing
- **Link**: [Annual Review of Psychology](https://doi.org/10.1146/annurev.psych.57.102904.190156)

### 2.9 The Default Mode Network and the Self-Referential Thought
- **Authors**: Marcus E. Raichle, Debra A. Gusnard
- **Year**: 2005
- **Institution**: Washington University School of Medicine
- **Core Contribution**: Discovered the Default Mode Network (DMN), revealing the brain's spontaneous activity patterns during "rest" and their relationship to self-referential thinking
- **Key Technologies**: Default mode network, spontaneous low-frequency fluctuations, self-referential processing, mental time travel
- **Heya Relevance**: **Reference** - DMN's "self-referential processing" corresponds to Heya's "Metacognition Layer": Factory performs self-reflection and improvement when no external tasks are present
- **Link**: [Annual Review of Neuroscience](https://doi.org/10.1146/annurev.neuro.29.051605.112930)

### 2.10 The Neuroscience of Forgetting
- **Authors**: Paul W. Frankland, Sheena Josselyn
- **Year**: 2022
- **Institution**: Hospital for Sick Children, University of Toronto
- **Core Contribution**: Review of neural mechanisms of forgetting, proposing that forgetting is an active process rather than passive decay, including synaptic pruning, neurogenesis-driven forgetting, and interference
- **Key Technologies**: Active forgetting, synaptic pruning, adult hippocampal neurogenesis, interference theory, retrieval-induced forgetting
- **Heya Relevance**: **Adaptive Adoption** - The "forgetting" mechanism in P004 memory lifecycle management: forgetting is not memory loss but an active information management strategy
- **Link**: [Annual Review of Neuroscience](https://doi.org/10.1146/annurev-neuro-100321-114636)

---

## 3. Cognitive and Decision Neural Mechanisms

### 3.1 Thinking, Fast and Slow
- **Authors**: Daniel Kahneman
- **Year**: 2011
- **Publisher**: Farrar, Straus and Giroux
- **Core Contribution**: Proposed the dual-system theory of human thinking: System 1 (fast, intuitive, automatic) and System 2 (slow, rational, effortful); awarded the 2002 Nobel Prize in Economics
- **Key Technologies**: Dual-system theory, anchoring effect, availability heuristic, prospect theory, loss aversion
- **Heya Relevance**: **Direct Adoption** - Core theory for P010 Trust Score Model: humans use System 1 for quick trust/rejection and System 2 for rational evaluation. Heya's "AI proposes -> human decides" needs to understand human cognitive biases
- **Link**: [Farrar, Straus and Giroux](https://us.macmillan.com/books/9780374533557/thinking-fast-and-slow)

### 3.2 Cognitive Load Theory
- **Authors**: John Sweller
- **Year**: 1988
- **Institution**: University of New South Wales
- **Core Contribution**: Proposed cognitive load theory, distinguishing intrinsic, extraneous, and germane cognitive load, guiding instructional and interface design
- **Key Technologies**: Cognitive load classification, working memory capacity limitations, schema construction, automation
- **Heya Relevance**: **Direct Adoption** - Core theory for P012 Cognitive Load Manager: Factory needs to minimize extraneous load and optimize germane load when presenting information to humans
- **Link**: [Cognitive Science](https://doi.org/10.1207/s15516709cog1204_4)

### 3.3 The Neuroscience of Trust
- **Authors**: Paul J. Zak
- **Year**: 2017
- **Institution**: Claremont Graduate University
- **Core Contribution**: Systematic study of the neural mechanisms of trust, discovering that oxytocin is a key neurotransmitter for trust, proposing biological markers of trust
- **Key Technologies**: Oxytocin and trust, neural circuits of trust (amygdala-prefrontal), dynamic trust regulation, cross-cultural trust differences
- **Heya Relevance**: **Direct Adoption** - Biological foundation for P010 Trust Score Model: trust is not static but a neurochemical process that can be dynamically regulated through experience
- **Link**: [Harvard Business Review](https://hbr.org/2017/07/the-neuroscience-of-trust)

### 3.4 The Free-Energy Principle: A Unified Brain Theory?
- **Authors**: Karl Friston
- **Year**: 2010
- **Institution**: University College London
- **Core Contribution**: Proposed the Free Energy Principle (FEP), describing the brain as an active inference system: the brain understands the world by minimizing prediction error
- **Key Technologies**: Free energy principle, predictive coding, active inference, Bayesian brain hypothesis, hierarchical generative models
- **Heya Relevance**: **Direct Adoption** - Core theory for Factory Designer intent understanding: AI understands user intent through "predictive coding" and reduces uncertainty through "active inference"
- **Link**: [Nature Reviews Neuroscience](https://doi.org/10.1038/nrn2787)

### 3.5 The Prefrontal Cortex and Executive Functions
- **Authors**: Earl K. Miller, Jonathan D. Cohen
- **Year**: 2001
- **Institution**: MIT, Princeton University
- **Core Contribution**: Review of the prefrontal cortex's role in executive functions: attention control, decision-making, working memory, planning, inhibitory control
- **Key Technologies**: Prefrontal executive functions, attention networks, decision networks, top-down control, rule representation
- **Heya Relevance**: **Adaptive Adoption** - Heya's "Factory Designer" corresponds to prefrontal executive functions: intent understanding -> planning -> decision -> control
- **Link**: [Annual Review of Neuroscience](https://doi.org/10.1146/annurev.neuro.24.052008.092238)

### 3.6 The Neural Basis of Economic Decision-Making in the Ultimatum Game
- **Authors**: Alan G. Sanfey, James K. Rilling, Jessica A. Aronson, Leigh E. Nystrom, Jonathan D. Cohen
- **Year**: 2003
- **Institution**: Princeton University
- **Core Contribution**: Discovered that unfair offers activate the anterior insula (associated with disgust) and dorsolateral prefrontal cortex (associated with cognitive control), revealing emotion-rational interaction in economic decision-making
- **Key Technologies**: Ultimatum game, anterior insula activation, emotion-cognition interaction, neural basis of fairness
- **Heya Relevance**: **Reference** - Emotional factors in human decision-making: Heya's Human Checkpoint needs to consider that humans are not purely rational decision-makers but are also influenced by emotions and fairness
- **Link**: [Science](https://doi.org/10.1126/science.1085596)

### 3.7 Dopamine Neuron Activity Reflects the Difference Between Expected and Actual Rewards
- **Authors**: Wolfram Schultz, Peter Dayan, Read Montague
- **Year**: 1997
- **Institution**: University of Fribourg, Gatsby Computational Neuroscience Unit
- **Core Contribution**: Discovered that midbrain dopamine neurons encode reward prediction error (RPE), providing the neuroscience foundation for reinforcement learning
- **Key Technologies**: Reward prediction error, temporal difference learning, dopamine signals, reinforcement learning models
- **Heya Relevance**: **Adaptive Adoption** - Biological foundation for Heya's "feedback -> improvement" loop: dopamine RPE signals correspond to Factory's "success/failure" feedback signals
- **Link**: [Nature](https://doi.org/10.1038/38553)

### 3.8 The Role of the Amygdala in Decision-Making
- **Authors**: Elizabeth A. Phelps, Joseph E. LeDoux
- **Year**: 2005
- **Institution**: New York University
- **Core Contribution**: Review of the amygdala's role in emotional decision-making, revealing neural mechanisms of fear conditioning, risk assessment, and emotional memory
- **Key Technologies**: Amygdala function, fear conditioning, emotional memory, risk assessment, emotion regulation
- **Heya Relevance**: **Reference** - Reference for P011 Dynamic Permission System: human risk perception is modulated by the amygdala; Factory should adjust interaction based on human emotional states
- **Link**: [Neuron](https://doi.org/10.1016/j.neuron.2005.09.012)

### 3.9 A Mechanistic Account of Uncertainty in Perception and Action
- **Authors**: Angela J. Yu, Peter Dayan
- **Year**: 2005
- **Institution**: Gatsby Computational Neuroscience Unit
- **Core Contribution**: Proposed computational models of uncertainty perception and action, combining Bayesian inference with neural dynamics
- **Key Technologies**: Bayesian decision-making, uncertainty estimation, information seeking, computational optimal experimental design
- **Heya Relevance**: **Reference** - Factory Designer's "Clarification" phase: AI proactively asks questions to reduce uncertainty, corresponding to the brain's "information seeking" behavior
- **Link**: [Nature Neuroscience](https://doi.org/10.1038/nn1570)

---

## 4. Brain Networks and Systems Neuroscience

### 4.1 Small-World Brain Networks
- **Authors**: Olaf Sporns, Jonathan D. Zwi
- **Year**: 2004
- **Institution**: Indiana University
- **Core Contribution**: Discovered that brain structural connectivity networks have small-world properties (high clustering coefficient + short path length), revealing the topological basis for efficient information transmission in the brain
- **Key Technologies**: Small-world networks, clustering coefficient, characteristic path length, structural connectome, hub nodes
- **Heya Relevance**: **Direct Adoption** - Core reference for P007 dynamic topology: Agent Mesh should adopt small-world network topology with both tight local connections and efficient global communication
- **Link**: [PLoS Computational Biology](https://doi.org/10.1371/journal.pcbi.0020059)

### 4.2 Large-Scale Brain Networks
- **Authors**: Jonathan D. Power, Alexander L. Cohen, Steven M. Nelson, Gagan S. Wig, Kelly Anne Barnes, Jessica A. Church, Alecia D. Vogel, Timothy O. Laumann, Franco Pestilli, Peter L. Vincent, Steven E. Petersen, Marcus E. Raichle
- **Year**: 2011
- **Institution**: Washington University School of Medicine
- **Core Contribution**: Systematically mapped brain functional network atlases, identifying multiple large-scale functional networks (default mode network, dorsal attention network, frontoparietal control network, etc.)
- **Key Technologies**: Functional connectome, resting-state fMRI, large-scale functional networks, network segregation and integration
- **Heya Relevance**: **Direct Adoption** - Network architecture reference for Agent Mesh: the brain's multiple functional networks correspond to Factory's multiple capability modules, operating both independently and cooperatively
- **Link**: [Neuron](https://doi.org/10.1016/j.neuron.2011.09.006)

### 4.3 A Cognitive Theory of Consciousness
- **Authors**: Bernard J. Baars
- **Year**: 1988
- **Publisher**: Cambridge University Press
- **Core Contribution**: Proposed Global Workspace Theory (GWT): consciousness is the process of information being broadcast across the brain; conscious content becomes accessible to all modules in the global workspace
- **Key Technologies**: Global workspace, consciousness broadcasting, consciousness threshold, competitive access, context processing
- **Heya Relevance**: **Direct Adoption** - Heya's "Human Checkpoint Layer" corresponds to the global workspace: key decision information needs to be "broadcast" to all relevant modules, not processed locally
- **Link**: [Cambridge University Press](https://www.cambridge.org/core/books/cognitive-theory-of-consciousness/)

### 4.4 Integrated Information Theory of Consciousness
- **Authors**: Giulio Tononi
- **Year**: 2004
- **Institution**: University of Wisconsin-Madison
- **Core Contribution**: Proposed Integrated Information Theory (IIT): consciousness is a measure of integrated information (Phi); the stronger a system's information integration capability, the higher its consciousness level
- **Key Technologies**: Integrated information (Phi), information integration, causal structure, system decomposition, consciousness measurement
- **Heya Relevance**: **Reference** - Phi value can serve as a complexity metric for Factory systems: excessively high Phi means the system is too tightly coupled, which is detrimental to modularity
- **Link**: [BMC Neuroscience](https://doi.org/10.1186/1471-2202-5-42)

### 4.5 The Brain's Functional Architecture: A Network Perspective
- **Authors**: Danielle S. Bassett, Ed Bullmore
- **Year**: 2006
- **Institution**: University of California, Santa Barbara, University of Cambridge
- **Core Contribution**: Analyzed brain functional architecture from a network science perspective, discovering that brain functional networks have "economic small-world" properties: optimal balance between wiring cost and information processing efficiency
- **Key Technologies**: Economic small-world, wiring cost minimization, modularity, rich-club organization
- **Heya Relevance**: **Adaptive Adoption** - Optimization goal for Factory network design: balancing communication efficiency (information processing) and module independence (wiring cost)
- **Link**: [Trends in Cognitive Sciences](https://doi.org/10.1016/j.tics.2006.06.008)

### 4.6 Dynamic Reconfiguration of Functional Brain Networks During Cognition
- **Authors**: M. P. van den Heuvel, R. C. W. Mandl, R. S. Kahn, H. E. Hulshoff Pol
- **Year**: 2009
- **Institution**: University Medical Center Utrecht
- **Core Contribution**: Discovered that brain functional networks dynamically reconfigure during cognitive tasks: the default mode network decreases during tasks, task-related networks increase, and inter-network connectivity patterns change with task type
- **Key Technologies**: Dynamic functional connectivity, task-induced network reconfiguration, network flexibility, network switching
- **Heya Relevance**: **Adaptive Adoption** - Direct biological basis for P007 dynamic topology: Factory's Agent Mesh should dynamically adjust network topology based on task type
- **Link**: [Journal of Neuroscience](https://doi.org/10.1523/JNEUROSCI.3834-08.2009)

### 4.7 The Connectome: How the Brain's Wiring Makes Us Who We Are
- **Authors**: Sebastian Seung
- **Year**: 2012
- **Publisher**: Houghton Mifflin Harcourt
- **Core Contribution**: Proposed the "connectome" concept, arguing that the brain's connection patterns determine individual identity and capability differences
- **Key Technologies**: Connectome, neural circuits, synaptic connections, individual differences, connectomics
- **Heya Relevance**: **Reference** - Heya's "Factory DNA" (P015) concept: Factory capabilities are determined by its "connectome" (how capabilities are connected)
- **Link**: [Houghton Mifflin Harcourt](https://www.hmhbooks.com/the-connectome/)

---

## 5. Neural Plasticity and Learning

### 5.1 Long-Term Potentiation: A Debate About Current Issues
- **Authors**: T.V.P. Bliss, G. L. Collingridge
- **Year**: 1993
- **Institution**: National Institute for Medical Research, University of Bristol
- **Core Contribution**: Systematic review of Long-Term Potentiation (LTP) research; LTP is the primary candidate mechanism for synaptic plasticity, considered the molecular basis of learning and memory
- **Key Technologies**: LTP, NMDA receptors, Hebbian plasticity, synaptic enhancement, Long-Term Depression (LTD)
- **Heya Relevance**: **Direct Adoption** - Core mechanism of synaptic plasticity corresponds to Heya's "experience -> memory -> behavior change" learning loop: frequently used capability connections strengthen, infrequently used ones weaken
- **Link**: [Nature](https://doi.org/10.1038/361031a0)

### 5.2 Spike-Timing-Dependent Plasticity (STDP)
- **Authors**: Henry Markram, Joachim K. Lubke, Michael Frotscher, Bert Sakmann
- **Year**: 1997
- **Institution**: Max Planck Institute for Medical Research
- **Core Contribution**: Discovered Spike-Timing-Dependent Plasticity (STDP): connections strengthen when presynaptic spikes arrive before postsynaptic spikes, and weaken otherwise
- **Key Technologies**: STDP, precise timing, causal learning, competitive plasticity
- **Heya Relevance**: **Adaptive Adoption** - STDP's "causal learning" corresponds to Heya's "strengthen successful experiences, weaken failed ones": intent first then success -> strengthen; intent first then failure -> weaken
- **Link**: [Science](https://doi.org/10.1126/science.274.5295.2130)

### 5.3 Adult Hippocampal Neurogenesis
- **Authors**: Fred H. Gage
- **Year**: 2002
- **Institution**: The Salk Institute
- **Core Contribution**: Confirmed the existence of continuous neurogenesis in the adult hippocampus; newborn neurons can integrate into existing neural circuits and participate in learning
- **Key Technologies**: Adult neurogenesis, hippocampal dentate gyrus, neurogenesis, synaptic integration, learning enhancement
- **Heya Relevance**: **Adaptive Adoption** - Biological basis for P003 core/extension separation: adult neurogenesis corresponds to Factory's "extension layer" -- new capabilities can be added without affecting core structure
- **Link**: [Science](https://doi.org/10.1126/science.1076550)

### 5.4 Neuroplasticity: Changes in Gray Matter Induced by Training
- **Authors**: Arne May
- **Year**: 2011
- **Institution**: University of Hamburg
- **Core Contribution**: Review of training-induced gray matter structural plasticity changes, finding that learning can lead to structural changes in the cerebral cortex
- **Key Technologies**: Gray matter plasticity, use-dependent structural changes, training effects, MRI structural changes
- **Heya Relevance**: **Reference** - Biological validation of Factory's "continuous use leads to improvement": frequently used Factory capabilities become more efficient (structural optimization)
- **Link**: [Nature](https://doi.org/10.1038/nature09990)

### 5.5 Critical Period Plasticity in the Visual Cortex
- **Authors**: Takao K. Hensch
- **Year**: 2005
- **Institution**: Harvard Medical School
- **Core Contribution**: Review of molecular mechanisms of critical period plasticity in the visual cortex, revealing the role of inhibitory neural networks in opening and closing critical periods
- **Key Technologies**: Critical period, Otx2-Homeoprotein, PV interneurons, inhibitory maturation, plasticity window
- **Heya Relevance**: **Reference** - Reference for P003 core/extension separation: the core layer becomes stable (immutable) after the "critical period," while the extension layer maintains plasticity
- **Link**: [Nature Reviews Neuroscience](https://doi.org/10.1038/nrn1821)

### 5.6 The Reinforcement Learning Problem
- **Authors**: Richard S. Sutton, Andrew G. Barto
- **Year**: 1998
- **Publisher**: MIT Press
- **Core Contribution**: Foundational work on reinforcement learning, systematically describing Markov Decision Processes, temporal difference learning, Q-learning, and policy gradient methods
- **Key Technologies**: MDP, temporal difference (TD), Q-Learning, policy gradient, exploration-exploitation dilemma, credit assignment
- **Heya Relevance**: **Adaptive Adoption** - Algorithmic foundation for Heya's "feedback -> improvement" loop: Factory learns optimal strategies from human feedback, corresponding to RL's reward maximization
- **Link**: [MIT Press](https://mitpress.mit.edu/books/reinforcement-learning-introduction-second-edition)

---

## 6. Consciousness and Higher Cognition

### 6.1 Towards a Neurobiological Theory of Consciousness
- **Authors**: Francis Crick, Christof Koch
- **Year**: 1990
- **Institution**: Salk Institute, California Institute of Technology
- **Core Contribution**: Proposed the research program for finding Neural Correlates of Consciousness (NCC), focusing on neural mechanisms of visual consciousness
- **Key Technologies**: Neural correlates of consciousness, visual consciousness, neural synchrony, binding problem, 40Hz oscillations
- **Heya Relevance**: **Reference** - The "neural correlates of consciousness" concept corresponds to Heya's "key decision points": which information needs to enter the global workspace (consciousness) and which can be processed locally
- **Link**: [Seminars in the Neurosciences](https://doi.org/10.1016/S0091-679X(05)80012-2)

### 6.2 Consciousness and the Brain: Deciphering How the Brain Codes Our Thoughts
- **Authors**: Stanislas Dehaene
- **Year**: 2014
- **Publisher**: Viking
- **Core Contribution**: Proposed the "global neuronal workspace" model of consciousness, combining experimental evidence (masking paradigm, EEG) to elucidate neural mechanisms of consciousness
- **Key Technologies**: Global workspace model, consciousness access, P3b wave, masking paradigm, unconscious processing
- **Heya Relevance**: **Adaptive Adoption** - Heya's "AI never silently makes high-risk decisions" corresponds to consciousness's global broadcasting: key decisions must enter the "global workspace" (human-visible)
- **Link**: [Viking](https://www.penguinrandomhouse.com/books/294564/consciousness-and-the-brain-by-stanislas-dehaene/)

### 6.3 Theory of Mind
- **Authors**: Simon Baron-Cohen, Alan M. Leslie, Uta Frith
- **Year**: 1985
- **Institution**: University of London, MRC Cognitive Development Unit
- **Core Contribution**: Proposed the concept of "Theory of Mind" (ToM): the ability to understand that others have beliefs, desires, and intentions different from one's own
- **Key Technologies**: Theory of mind, false belief task, intent understanding, social cognition, autism and ToM deficits
- **Heya Relevance**: **Adaptive Adoption** - Factory Designer's "Clarification" phase requires ToM capability: understanding the user's true intent (rather than literal requests) and predicting human preferences
- **Link**: [Cognition](https://doi.org/10.1016/0010-0277(85)90022-8)

### 6.4 Predictive Processing as a Theory of Cognition
- **Authors**: Andy Clark
- **Year**: 2013
- **Institution**: University of Edinburgh
- **Core Contribution**: Elevated predictive coding theory to a unified theory of cognition, proposing that the brain is a "prediction machine" and perception is constrained prediction
- **Key Technologies**: Predictive processing, hierarchical Bayesian inference, prediction error minimization, attention as precision weighting
- **Heya Relevance**: **Adaptive Adoption** - Factory's "context management" corresponds to predictive processing: AI proactively prepares relevant information by predicting user needs, rather than passively waiting for requests
- **Link**: [Trends in Cognitive Sciences](https://doi.org/10.1016/j.tics.2013.06.003)

### 6.5 The Social Brain Hypothesis
- **Authors**: Robin I.M. Dunbar
- **Year**: 1998
- **Institution**: University of Oxford
- **Core Contribution**: Proposed the social brain hypothesis: primate brain (especially neocortex) size is determined by social group size; social complexity drove brain evolution
- **Key Technologies**: Social brain hypothesis, neocortex ratio, social group size, Dunbar's number (~150), social cognitive complexity
- **Heya Relevance**: **Reference** - Scale limit reference for Agent Mesh: Dunbar's number (~150) suggests the optimal scale of Agent Mesh may have similar limitations
- **Link**: [Evolutionary Anthropology](https://doi.org/10.1002/(SICI)1520-6505(1998)6:5<178::AID-EVAN5>3.0.CO;2-8)

---

## 7. Brain-Inspired Computing and Neuromorphic Engineering

### 7.1 Neural Engineering: Computation, Representation, and Dynamics in Neurobiological Systems
- **Authors**: Chris Eliasmith, Charles H. Anderson
- **Year**: 2003
- **Publisher**: MIT Press
- **Core Contribution**: Proposed the neural engineering framework (Nengo), applying neuroscience principles to computational system design and building large-scale biologically realistic neural network models
- **Key Technologies**: Neural encoding, population representation, Nengo simulator, biologically realistic models, computational neuroscience
- **Heya Relevance**: **Reference** - Heya's "AI is a Factory" concept can draw from neural engineering: using biological principles to guide AI system organization
- **Link**: [MIT Press](https://mitpress.mit.edu/books/neural-engineering)

### 7.2 Spiking Neural Networks: Principles and Challenges
- **Authors**: Wulfram Gerstner, Werner M. Kistler
- **Year**: 2002 (3rd Ed. 2014)
- **Publisher**: Cambridge University Press
- **Core Contribution**: Authoritative textbook on Spiking Neural Networks (SNN), systematically describing spike coding, synaptic dynamics, network computation, and plasticity rules
- **Key Technologies**: Spike coding, LIF model, STDP, spike timing coding, energy efficiency
- **Heya Relevance**: **Conceptual Reference** - SNN's event-driven computing corresponds to Heya's "on-demand activation": Factory capability modules activate only when needed, saving computational resources
- **Link**: [Cambridge University Press](https://www.cambridge.org/core/books/spiking-neuron-models/)

### 7.3 A Survey of Neuromorphic Computing
- **Authors**: Mike Davies, Narayan Srinivasa, Tsu-Jae King Liu, Gert Cauwenberghs
- **Year**: 2018
- **Institution**: Intel Labs, University of Michigan, UC San Diego
- **Core Contribution**: Survey of neuromorphic computing development, including Intel Loihi chip, IBM TrueNorth, etc., discussing applications of brain-inspired chips in perception, learning, and reasoning
- **Key Technologies**: Neuromorphic chips, spiking neural networks, on-chip learning, low-power computing, event-driven architecture
- **Heya Relevance**: **Conceptual Reference** - The event-driven, low-power characteristics of neuromorphic chips provide a future hardware direction for Heya's "resource efficiency optimization"
- **Link**: [Proceedings of the IEEE](https://doi.org/10.1109/JPROC.2018.2847686)

### 7.4 Brain-Inspired Computing: A Survey of Neuromorphic and Neurosymbolic Approaches
- **Authors**: Multi-institution collaboration
- **Year**: 2023+
- **Core Contribution**: Survey of the latest advances in brain-inspired computing, covering neuro-symbolic fusion, brain-inspired reasoning, hybrid AI architectures, and other frontier directions
- **Key Technologies**: Neuro-symbolic fusion, brain-inspired reasoning, hybrid architecture, explainable AI, knowledge enhancement
- **Heya Relevance**: **Reference** - Heya's "AI organizes itself as a Factory" is essentially a neuro-symbolic fusion: neural networks provide perception and generation, symbolic systems provide structure and rules
- **Link**: [arXiv:2306.01602](https://arxiv.org/abs/2306.01602)

### 7.5 The Hippocampus as a Predictive Map
- **Authors**: Kimberly L. Stachenfeld, Timothy J. Botvinick, Samuel J. Gershman
- **Year**: 2017
- **Institution**: Harvard University, Google DeepMind
- **Core Contribution**: Proposed the hippocampus as a predictive map theory: the hippocampus encodes not only spatial information but also structured predictive relationships, supporting generalization and planning
- **Key Technologies**: Predictive map, successor representation, generalization navigation, Tolman-Eichenbaum machine, relational reasoning
- **Heya Relevance**: **Adaptive Adoption** - The hippocampus's "predictive map" corresponds to Heya's "episodic memory retrieval": retrieving not only literally matching memories but also structurally related experiences
- **Link**: [Nature Neuroscience](https://doi.org/10.1038/nn.4658)

---

## Heya Application Summary

| Paper/Technology | Application Scenario | Priority |
|-----------------|---------------------|----------|
| Hebb (1949) | Synaptic plasticity foundation | P0 - Must |
| McCulloch & Pitts (1943) | Neural network theoretical foundation | P0 - Must |
| Baddeley Working Memory Model | Memory Layer four-layer architecture | P0 - Must |
| Squire Memory Classification | Memory hierarchical architecture | P0 - Must |
| O'Keefe Hippocampal Cognitive Map | Episodic memory retrieval | P0 - Must |
| Kahneman Dual-System Theory | P010 Trust Score Model | P0 - Must |
| Miller Working Memory Capacity | P012 Cognitive Load Management | P0 - Must |
| Sweller Cognitive Load Theory | P012 Cognitive Load Manager | P0 - Must |
| Friston Free Energy Principle | Intent understanding mechanism | P0 - Must |
| Sporns Small-World Brain Networks | P007 Dynamic Topology | P0 - Must |
| Power Large-Scale Brain Networks | Agent Mesh network architecture | P0 - Must |
| Baars Global Workspace Theory | Information integration mechanism | P0 - Must |
| Bliss & Collingridge LTP | Self-learning mechanism | P0 - Must |
| Hochreiter & Schmidhuber LSTM | Memory layer design | P0 - Must |
| Zak Trust Neuroscience | P010 Trust Score Model | P0 - Must |
| Kephart & Chess MAPE-K | Metacognition Layer | P0 - Must |
| Lehman Software Evolution Laws | Factory evolution theoretical foundation | P0 - Must |

---

## Key Insights

### Deep Correspondence Between Neuroscience and AI Factory

1. **Memory hierarchy <-> Hippocampal-neocortical system**: Hippocampus rapidly encodes episodic memory, slowly transfers to neocortical semantic memory during sleep -> Heya's Episodic -> Semantic transformation
2. **Working memory capacity <-> Cognitive load theory**: 7+-2 limit -> Heya must group and summarize information presented to humans
3. **Synaptic plasticity <-> Factory learning**: Hebb's rule + STDP -> High-frequency successful experiences strengthen connections, failures weaken them
4. **Small-world networks <-> Agent Mesh**: High clustering + short paths -> Tight local collaboration + efficient global communication
5. **Global workspace <-> Human Checkpoint**: Key information broadcast across the brain -> High-risk decisions must be human-visible
6. **Predictive coding <-> Intent understanding**: Brain predicts external input -> AI predicts user needs, proactively prepares
7. **Critical period plasticity <-> Core/extension separation**: Core layer stabilizes after critical period -> Factory Core Layer is immutable

---

## Cross-references with Existing Paper Categories

| Papers in this category | Cross-reference |
|------------------------|----------------|
| Hebb (1949) | -> 05-Self-Evolution: MemRL stability-plasticity decomposition |
| Baddeley Working Memory | -> 01-Memory Systems: MemGPT hierarchical memory, CoALA modular memory |
| Squire Memory Classification | -> 01-Memory Systems: Memory hierarchical architecture |
| Kahneman Dual-System | -> 06-Human-AI Collaboration: Learning to Trust, Transparency Paradox |
| Sweller Cognitive Load | -> 06-Human-AI Collaboration: Transparency Paradox, Bloom's Revision |
| Friston Free Energy Principle | -> 04-Intent Planning: DEPS interactive planning |
| Sporns Small-World Networks | -> 02-Multi-Agent: Agent Mesh dynamic topology |
| Baars Global Workspace | -> 07-Workflow Orchestration: MetaGPT intermediate verification |
| Bliss LTP | -> 05-Self-Evolution: Self-evolution framework |
| Sutton & Barto RL | -> 05-Self-Evolution: MemRL runtime RL |
| Zak Trust Neuroscience | -> 06-Human-AI Collaboration: P010 Trust Score Model |
| Dehaene Global Workspace | -> 06-Human-AI Collaboration: Human-AI collaboration pattern taxonomy |
| Baron-Cohen ToM | -> 04-Intent Planning: Factory Designer intent understanding |

---

*Last updated: 2026-05-19*
