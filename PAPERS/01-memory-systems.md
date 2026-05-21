# AI Memory Systems Paper Collection

> This category collects academic papers related to AI long-term memory, knowledge storage, and retrieval augmentation
> Related to Heya architecture: Memory Layer design in Phase 3

---

## 1. Foundational Architecture

### 1.1 MemGPT: Towards LLMs as Operating Systems
- **Authors**: Charles Packer et al.
- **Year**: 2023
- **Institution**: UC Berkeley
- **Core Contribution**: Proposes treating LLMs as operating systems, achieving unlimited context windows through virtual context management
- **Key Technologies**: Hierarchical memory architecture (main context/external context), paging mechanism, interrupt handling
- **Heya Relevance**: **Direct Adoption** - Provides theoretical foundation for the Memory Layer's hierarchical architecture
- **Link**: [arXiv:2310.08560](https://arxiv.org/abs/2310.08560)

### 1.2 RETRO: Improving Language Models by Retrieving from Trillions of Tokens
- **Authors**: Sebastian Borgeaud et al.
- **Year**: 2021
- **Institution**: DeepMind
- **Core Contribution**: Retrieval-enhanced language model that retrieves relevant information from large-scale corpora
- **Key Technologies**: Chunked retrieval, neighbor encoder, retrieval-generation joint training
- **Heya Relevance**: **Adaptive Adoption** - Provides technical reference for semantic memory retrieval
- **Link**: [arXiv:2112.04426](https://arxiv.org/abs/2112.04426)

### 1.3 Atlas: Few-shot Learning with Retrieval Augmented Language Models
- **Authors**: Gautier Izacard et al.
- **Year**: 2022
- **Institution**: Meta AI
- **Core Contribution**: Few-shot learning framework combining retrieval augmentation
- **Key Technologies**: Non-parametric memory, end-to-end training, knowledge injection
- **Heya Relevance**: **Adaptive Adoption** - Provides reference for knowledge retrieval mechanisms
- **Link**: [arXiv:2208.03299](https://arxiv.org/abs/2208.03299)

---

## 2. Long-Term Memory and Continual Learning

### 2.1 Continual Learning with Transformers
- **Authors**: Collection of related papers
- **Core Domain**: Catastrophic forgetting mitigation, knowledge accumulation, progressive learning
- **Heya Relevance**: **Adaptive Adoption** - Agents need continual learning without forgetting

### 2.2 Memory Networks
- **Authors**: Jason Weston et al.
- **Year**: 2014-2015
- **Institution**: Facebook AI Research
- **Core Contribution**: Early memory-augmented neural network architecture
- **Key Technologies**: Addressable memory, multi-hop reasoning, memory read/write mechanisms
- **Heya Relevance**: **Conceptual Reference** - Foundational ideas of memory networks

### 2.3 Differentiable Neural Computer (DNC)
- **Authors**: Alex Graves et al.
- **Year**: 2016
- **Institution**: DeepMind
- **Core Contribution**: Differentiable neural computer combining neural networks with external memory
- **Key Technologies**: Differentiable addressing, content/location addressing, memory temporal links
- **Heya Relevance**: **Reference** - Complex memory management mechanisms

---

## 3. Vector Databases and Retrieval

### 3.1 FAISS: A Library for Efficient Similarity Search
- **Authors**: Jeff Johnson et al.
- **Year**: 2019
- **Institution**: Facebook AI Research
- **Core Contribution**: Efficient similarity search library supporting billion-scale vectors
- **Key Technologies**: Quantization, clustering indexes, GPU acceleration
- **Heya Relevance**: **Direct Adoption** - As the underlying retrieval engine for the Memory Layer
- **Link**: [GitHub: facebookresearch/faiss](https://github.com/facebookresearch/faiss)

### 3.2 HNSW: Efficient and Robust Approximate Nearest Neighbor Search
- **Authors**: Yu. A. Malkov, D. A. Yashunin
- **Year**: 2018
- **Core Contribution**: Hierarchical navigable small world graph algorithm
- **Key Technologies**: Multi-layer graph structure, approximate nearest neighbor, efficient indexing
- **Heya Relevance**: **Direct Adoption** - Core algorithm for vector retrieval

---

## 4. Personalization and Contextual Memory

### 4.1 LaMDA: Language Models for Dialog Applications
- **Authors**: Romal Thoppilan et al.
- **Year**: 2022
- **Institution**: Google
- **Core Contribution**: Language models for dialog applications, emphasizing factuality and safety
- **Key Technologies**: Multi-turn dialog memory, knowledge retrieval, safety filtering
- **Heya Relevance**: **Adaptive Adoption** - Dialog memory management

### 4.2 BlenderBot 3: A Deployed Conversational Agent
- **Authors**: Kurt Shuster et al.
- **Year**: 2022
- **Institution**: Meta AI
- **Core Contribution**: Continually learning dialog system
- **Key Technologies**: Long-term memory, user profiles, continuous improvement
- **Heya Relevance**: **Direct Reference** - Long-term user memory management

---

## 5. Memory Type Theory

### 5.1 A Survey on Long-Term Memory in Large Language Models
- **Core Content**: Comprehensive survey of long-term memory in LLMs
- **Memory Classification**:
  - **Episodic Memory**: Specific events and experiences
  - **Semantic Memory**: Facts and conceptual knowledge
  - **Procedural Memory**: Skills and operational knowledge
- **Heya Relevance**: **Theoretical Framework** - Directly guides the three-layer design of the Memory Layer

---

## 6. Advanced RAG Techniques

### 6.1 GraphRAG: Using Knowledge Graphs for Retrieval-Augmented Generation
- **Authors**: Microsoft Research Team
- **Year**: 2024
- **Institution**: Microsoft Research
- **Core Contribution**: Combines knowledge graphs with RAG, capturing relationships between entities through graph structures to improve retrieval quality for complex reasoning tasks
- **Key Technologies**: Knowledge graph construction, community detection, graph traversal retrieval, summary generation
- **Heya Relevance**: **Adaptive Adoption** - Graph structure enhancement for P006 hybrid retrieval, supporting relational reasoning
- **Link**: [arXiv:2404.16130](https://arxiv.org/abs/2404.16130)

### 6.2 RAGAS: Automated Evaluation of Retrieval Augmented Generation
- **Authors**: Es, J. van, et al.
- **Year**: 2023
- **Institution**: Exploding Gradients
- **Core Contribution**: Proposes an automated evaluation framework for RAG systems, measuring retrieval faithfulness, answer relevance, context precision, etc.
- **Key Technologies**: Faithfulness metrics, answer relevance, context precision/recall
- **Heya Relevance**: **Adaptive Adoption** - Evaluation framework for Memory Layer retrieval quality
- **Link**: [arXiv:2309.15217](https://arxiv.org/abs/2309.15217)

### 6.3 Dense Passage Retrieval (DPR)
- **Authors**: Vladimir Karpukhin, Barlas Oguz, Sewon Min, Patrick Lewis, Ledell Wu, Sergey Edunov, Danqi Chen, Wen-tau Yih
- **Year**: 2020
- **Institution**: Facebook AI Research
- **Core Contribution**: Proposes dense passage retrieval using dual-encoder architecture for semantic retrieval, surpassing traditional BM25
- **Key Technologies**: Dual encoder, dense vector retrieval, negative sampling, contrastive learning
- **Heya Relevance**: **Direct Adoption** - Core method for semantic memory retrieval
- **Link**: [EMNLP 2020](https://arxiv.org/abs/2004.04906)

### 6.4 ColBERT: Efficient and Effective Passage Search via Contextualized Late Interaction
- **Authors**: Omar Khattab, Matei Zaharia
- **Year**: 2020
- **Institution**: Stanford University
- **Core Contribution**: Proposes late interaction mechanism, preserving token-level fine-grained matching while maintaining retrieval efficiency
- **Key Technologies**: MaxSim operation, late interaction, token-level embeddings, vector quantization
- **Heya Relevance**: **Adaptive Adoption** - Improves fine-grained matching capability for retrieval
- **Link**: [SIGIR 2020](https://arxiv.org/abs/2004.12832)

### 6.5 Hybrid Search: Combining BM25 and Dense Retrieval
- **Authors**: Synthesis of multiple studies
- **Core Content**: Combines advantages of sparse retrieval (BM25) and dense retrieval through weighted fusion or learned ranking optimization
- **Key Technologies**: Sparse-dense fusion, learned ranking, query expansion, reranking
- **Heya Relevance**: **Direct Adoption** - Core strategy for P006 hybrid retrieval

---

## 7. Long Context Processing

### 7.1 LongLoRA: Efficient Fine-tuning of Long-Context Large Language Models
- **Authors**: Yukang Chen, Shengju Qian, Haotian Tang, Xiangyu Zhang, Yongjian Chen, Yuhui Yuan, Zejian Wang, Junyu Luo, Yongqiang Li, Kai Chen, Yujun Cai, Zhongyuan Zhang, Yunfei Lv, Yibo Yang, Wenqi Shao, Yehui Tang, Zheyuan Hu, Jiaheng Liu, Yang You, Ping Luo
- **Year**: 2023
- **Institution**: The Chinese University of Hong Kong, Shanghai AI Laboratory
- **Core Contribution**: Proposes efficient LLM long-context fine-tuning method, extending context windows through sparse attention mechanisms
- **Key Technologies**: Shifted Sparse Attention (S2-Attn), LoRA fine-tuning, context extension
- **Heya Relevance**: **Adaptive Adoption** - Supports memory processing for long conversations and long documents
- **Link**: [arXiv:2309.12307](https://arxiv.org/abs/2309.12307)

### 7.2 Ring Attention: Scaling Context to 100M Tokens
- **Authors**: Liu Hao, Pieter Abbeel
- **Year**: 2023
- **Institution**: UC Berkeley
- **Core Contribution**: Proposes ring attention mechanism, achieving million-token context processing through distributed computation
- **Key Technologies**: Ring communication, block attention, distributed computation, memory optimization
- **Heya Relevance**: **Reference** - Ultra-long context processing solution
- **Link**: [arXiv:2310.01889](https://arxiv.org/abs/2310.01889)

### 7.3 Memorizing Transformers
- **Authors**: Sainbayar Sukhbaatar, Jason Weston, Piotr Bojanowski, Armand Joulin, Lucas Cetto, Edouard Grave
- **Year**: 2022
- **Institution**: Meta AI, FAIR
- **Core Contribution**: Proposes memory-augmented Transformer, achieving unlimited memory capacity through kNN retrieval of external memory
- **Key Technologies**: kNN retrieval, memory attention, trainable memory read/write
- **Heya Relevance**: **Direct Adoption** - Core design reference for the Memory Layer
- **Link**: [ICLR 2022](https://arxiv.org/abs/2203.08945)

---

## 8. Memory Compression and Summarization

### 8.1 Recurrent Memory Transformer
- **Authors**: Anna M. Mikhalkova, Yu. A. Malkov, Ivan V. Mazurenko, Alexey P. Sinitca, Liliya M. Zintsova, Sergey O. Kuznetsov, Leonid E. Zhukov
- **Year**: 2023
- **Institution**: HSE University, Yandex
- **Core Contribution**: Proposes recurrent memory Transformer, processing infinite-length sequences through compressed memory states
- **Key Technologies**: Recurrent connections, memory compression, state transfer
- **Heya Relevance**: **Adaptive Adoption** - Technical reference for P005 intelligent compression
- **Link**: [arXiv:2305.02541](https://arxiv.org/abs/2305.02541)

### 8.2 Compressive Transformer
- **Authors**: Jack W. Rae, Leonard Bartlett, Tim Cooijmans, Anna Harutyunyan, Abram L. Fyshe, Matthew W. Hoffman, Janos K. Pal, Doina Precup, Adria Puigdomenech, Jonathan Schwarz, Sindy R. Lowe, Danilo J. Rezende, Leonard Blier, Corey Lynch, Irina Rish, Remi Tachet des Combes, Jelena Luketina, Razvan Pascanu, Sander Dieleman, Yori Zwols, Michal J. Johnson, Alexander Pritzel, Will Dabney, Thomas P. Minka, Ian Osband, Neil C. Rabinowitz, Greg Thornton, Nicolas Sonnerat, Francis Song, Chris J. Maddison, George E. Dahl, Ivo Kweon, Mohammad Gheshlaghi Azar, Neil Houlsby, Allan Jaffe, Razvan Pascanu, Nando de Freitas, Timothy P. Lillicrap, Shane Legg, Demis Hassabis, Murray Shanahan, David Silver
- **Year**: 2019
- **Institution**: DeepMind
- **Core Contribution**: Proposes compressive Transformer, compressing historical information into compact representations to extend effective context length
- **Key Technologies**: Memory compression, compression network, segmented compression
- **Heya Relevance**: **Adaptive Adoption** - Core technology for P005 intelligent compression
- **Link**: [ICLR 2019](https://arxiv.org/abs/1911.05507)

---

## Heya Application Summary

| Paper/Technology | Application Scenario | Priority |
|-----------------|---------------------|----------|
| MemGPT | Memory Layer core architecture | P0 - Must |
| FAISS/HNSW | Vector retrieval engine | P0 - Must |
| DPR/ColBERT | Semantic retrieval methods | P0 - Must |
| GraphRAG | Graph structure-enhanced retrieval | P1 - Important |
| Memorizing Transformers | Unlimited memory capacity | P1 - Important |
| Compressive Transformer | Memory compression | P1 - Important |
| LongLoRA/Ring Attention | Long context processing | P1 - Important |
| RAGAS | Retrieval quality evaluation | P2 - Reference |
| RETRO/Atlas | Semantic memory enhancement | P1 - Important |
| DNC | Complex memory management | P2 - Reference |
| BlenderBot 3 | User profile memory | P1 - Important |

---

*Last updated: 2026-05-19*
