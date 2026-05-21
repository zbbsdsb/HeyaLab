# ROUND-002: Heya Memory System In-Depth Design Brainstorm

> **Topic**: In-depth design of the Heya memory system
> **Date**: 2026-05-16
> **Related**: ROADMAP Phase 3 - Memory Layer, RESEARCH/SYNTHESIS.md Section 3

---

## Core Questions

Based on the "Project-Level Memory" principle in VISION.md and the research synthesis of MemGPT, CoALA, MemRL, and Generative Agents in RESEARCH/SYNTHESIS.md, we need to answer:

1. **How should the four-layer memory architecture (Working / Episodic / Semantic / Artifact) be specifically implemented?**
2. **What is the complete lifecycle of a memory, from creation to death?**
3. **How can we compress memories without losing critical information?**
4. **How can the retrieval engine simultaneously satisfy semantic precision and temporal completeness?**
5. **How should the forgetting mechanism be designed -- what to forget and what to keep?**
6. **How should contradictory memories be handled?**
7. **Can memories be shared across Factories?**
8. **Can memories "dream" -- spontaneously reorganize and make creative connections during idle time?**

---

## I. Specific Implementation of the Four-Layer Memory Architecture

### 1.1 Working Memory

**Essence**: The current task context window, corresponding to MemGPT's "main context."

**Implementation ideas**:

- **Capacity**: Dynamically managed, not a fixed token count but "information density" management
- **Content composition**:
  - Current WorkOrder's goals, constraints, and progress
  - Recently retrieved Episodic/Semantic memory fragments
  - Human's most recent instructions and preferences
  - Input/output of the current Pipeline step
- **Eviction strategy**: LRU + importance weighting. Not simply "oldest first," but "least important first"
- **Difference from MemGPT**: MemGPT's working memory is a plain-text context window; Heya's working memory is structured, with type annotations (this is a goal, this is a memory fragment, this is a human instruction)

**Bold idea**: Can Working Memory have a "focus of attention"? Just as humans selectively attend in noisy environments, Working Memory could have a "focus zone" (high resolution) and a "periphery zone" (low-resolution summaries). The focus zone holds the most critical current information, while the periphery zone holds relevant but non-critical context.

### 1.2 Episodic Memory

**Essence**: Records of specific events, decisions, and failures, corresponding to CoALA's episodic memory and Generative Agents' memory stream.

**Implementation ideas**:

- **Storage format**: Each memory is a MemoryEntry, containing:
  - `id`: Unique identifier
  - `timestamp`: Creation time
  - `content`: Raw content (can be text, decision records, error logs)
  - `embedding`: Semantic vector
  - `importance`: Importance score (0-1)
  - `accessCount`: Number of times retrieved
  - `lastAccessed`: Last access time
  - `tags`: Tags (automatic + manual)
  - `provenance`: Source (which Factory, which Pipeline step, which WorkOrder)
  - `emotionalValence`: Emotional valence (positive/negative/neutral) -- used for forgetting decisions
- **Indexing**: Vector index (FAISS/HNSW) + temporal index + tag index
- **Storage backend**: Vector database (hot data) + object storage (cold data)

**Bold idea**: Can Episodic Memory have "memory chains"? Just as human memories have causal relationships between events, each memory can link to "cause" and "effect" memories. This way, retrieval can trace along causal chains.

### 1.3 Semantic Memory

**Essence**: Knowledge, patterns, and preferences distilled from experience, corresponding to CoALA's semantic memory.

**Implementation ideas**:

- **Source**: Not directly stored, but distilled from Episodic Memory
- **Distillation trigger**: Automatically triggered when multiple Episodic Memories exhibit similar patterns
- **Storage format**: Knowledge entries, containing:
  - `id`: Unique identifier
  - `type`: Knowledge type (pattern/preference/constraint/heuristic/rule)
  - `content`: Knowledge content
  - `confidence`: Confidence level (based on the quantity and quality of supporting evidence)
  - `sourceEpisodes`: List of Episodic Memory IDs supporting this knowledge
  - `lastValidated`: Last validation time
  - `contradictions`: Records that contradict this knowledge
- **Validation mechanism**: Semantic Memory is "slow memory" and requires validation before updating. Derived from MemRL's stability-plasticity decomposition.
- **Layering**: High-confidence knowledge can directly influence Factory behavior; low-confidence knowledge serves only as reference

**Bold idea**: Can Semantic Memory have a "knowledge graph" structure? Not just a flat list of entries, but with explicit relationships between concepts (A causes B, A takes priority over B, A contradicts B). This would enable multi-hop reasoning during inference.

### 1.4 Artifact Memory

**Essence**: Heya's unique fourth layer, recording all artifacts produced by Factories and their metadata.

**Implementation ideas**:

- **Stored content**: Does not store the artifacts themselves (artifacts are in the file system/object storage), but stores:
  - Artifact metadata (type, size, creation time, creator)
  - Artifact verification results (which checks were passed, scores)
  - Artifact dependencies (based on which inputs, influenced which subsequent artifacts)
  - Artifact evaluations (human evaluations, automated evaluations)
  - Artifact version history
- **Purposes**:
  - Avoiding redundant generation of similar artifacts
  - Providing reference templates for new artifacts
  - Tracking artifact quality trends
  - Supporting queries like "has this project done something similar before?"

**Bold idea**: Can Artifact Memory support "artifact variants"? Recording multiple variants of an artifact and their effectiveness differences, so that when similar requirements arise in the future, the best variant can be selected as a starting point.

### 1.5 Relationships Between the Four Layers

```
┌─────────────────────────────────────────────────────────┐
│                  Working Memory                          │
│         (Current task context, limited capacity, fast access) │
│    ┌──────────────────────────────────────────────┐     │
│    │  Retrieved memory fragments + current task info   │     │
│    └──────────────────────────────────────────────┘     │
└──────────────────────┬──────────────────────────────────┘
                       │ Retrieve / Distill
        ┌──────────────┼──────────────┐
        ▼              ▼              ▼
┌──────────────┐ ┌──────────┐ ┌──────────────┐
│  Episodic    │ │ Semantic │ │  Artifact    │
│  Memory      │ │ Memory   │ │  Memory      │
│  (Specific events) │ (Abstract knowledge) │ (Output records) │
└──────────────┘ └──────────┘ └──────────────┘
        │              ▲
        │   Distill    │
        └──────────────┘
```

---

## II. Memory Lifecycle

### 2.1 Creation

**Trigger conditions**:
- When a Pipeline step completes -> create Episodic Memory
- When human gives feedback -> create Episodic Memory (marked as feedback type)
- When a pattern is detected -> create Semantic Memory
- When an artifact is produced -> create Artifact Memory
- When relevant memories are retrieved -> update accessCount

**Creation process**:
1. Raw content extraction (from Pipeline logs, human conversations, artifact metadata)
2. Structured processing (add timestamp, tags, provenance)
3. Semantic vectorization (call embedding model)
4. Importance scoring (based on content type, source, context)
5. Store in the corresponding memory layer
6. Update indexes

### 2.2 Indexing

**Multi-dimensional indexing**:
- **Semantic index**: Vector index (FAISS/HNSW), supports similarity retrieval
- **Temporal index**: B-Tree index, supports time range queries
- **Tag index**: Inverted index, supports tag filtering
- **Importance index**: Sorted index, supports sorting by importance
- **Causal index**: Graph index, supports causal chain tracing (if memory chains are implemented)

### 2.3 Retrieval

See Section IV "Retrieval Optimization" for details.

### 2.4 Decay

**Decay model**: Not simply "decay over time," but multi-dimensional decay:

```
effectiveScore = baseImportance
  * recencyFactor(time)        // newer is better
  * accessFactor(accessCount)  // more frequently retrieved is better
  * relevanceFactor(queries)   // relevance to recent queries
  * emotionalFactor(valence)   // negative memories decay slower (lessons should not be forgotten)
```

- `recencyFactor`: Exponential decay, but different memory types decay at different rates
  - Episodic: Fast decay (specific events fade)
  - Semantic: Slow decay (knowledge should persist)
  - Artifact: No decay (artifact records are permanent archives)
- `emotionalFactor`: Negative memories (failures, errors) decay slower, because lessons are more valuable than successes

### 2.5 Forgetting

See Section V "Forgetting Mechanism" for details.

### 2.6 Archiving

**Archiving conditions**:
- Memory's effectiveScore is below threshold, but content still has potential value
- Original version of a memory after compression
- All Episodic Memories of a completed WorkOrder

**Archive storage**:
- Move from hot storage (vector database) to cold storage (object storage)
- Retain indexes but mark as archived
- Archived memories are still retrievable, but with higher latency
- Archived memories do not participate in daily Working Memory retrieval

---

## III. Compression Strategies

### 3.1 Why Is Compression Needed?

- Episodic Memory grows indefinitely (every event is recorded)
- Working Memory has limited capacity and needs compression to fit more context
- Semantic redundancy: Multiple similar memories can be merged into a single summary
- Cost control: Vector database storage and retrieval costs grow with data volume

### 3.2 Compression Levels

**Level 1: Summary Compression**
- Merge multiple similar Episodic Memories into a single summary
- Retain key information: time range, participating entities, results, lessons learned
- Discard details: specific conversation content, intermediate states
- Trigger condition: More than N memories under the same topic

**Level 2: Semantic Compression**
- Extract core semantics from memories and replace with more concise expressions
- Example: "User requested changing button color from blue to green for the third time" -> "User prefers green buttons"
- This is essentially the Episodic -> Semantic distillation process

**Level 3: Knowledge Graph Compression**
- Merge multiple Semantic Memories into higher-level knowledge
- Example: "User prefers green buttons" + "User prefers rounded corners" -> "User prefers clean, modern UI style"
- This is an internal upgrade within Semantic Memory

**Level 4: Discard Compression**
- Completely delete memories that no longer have value
- Only retain statistical information that "this once existed"

### 3.3 Fidelity Guarantees for Compression

**Key principle**: Compression can lose details, but must not lose decision-relevant information.

- Retain all context related to human decisions
- Retain all records of failures and errors (at least retain summaries)
- Retain emotional valence (positive/negative)
- Retain causal chains (if memory chains are implemented)

**Validation mechanism**: Compressed memories must pass a "reversibility check" -- if the compressed memory replaces the original memory, would the Factory's behavior differ significantly?

---

## IV. Retrieval Optimization

### 4.1 Semantic Retrieval

- **Method**: Vectorize the query text and find nearest neighbors in the vector index
- **Advantage**: Understands query intent, does not rely on exact keywords
- **Disadvantage**: May miss exact matches; insufficient precision for proper nouns, IDs, etc.
- **Applicable scenario**: "Have we handled a similar problem before?"

### 4.2 Keyword Retrieval

- **Method**: BM25 or similar algorithm, based on word frequency matching
- **Advantage**: Exact matching, fast, friendly to proper nouns
- **Disadvantage**: Does not understand semantics, cannot match synonyms
- **Applicable scenario**: "What was the outcome of WorkOrder #123?"

### 4.3 Temporal Retrieval

- **Method**: Range queries and sorting based on timestamps
- **Advantage**: Can track the progression of events
- **Disadvantage**: Does not consider content relevance
- **Applicable scenario**: "What changes did we make last week?"

### 4.4 Importance Retrieval

- **Method**: Sorting based on importance score
- **Advantage**: Ensures the most important information is retrieved first
- **Disadvantage**: Importance is static, does not consider relevance to the current query
- **Applicable scenario**: "What are the most critical decisions in this project?"

### 4.5 Hybrid Retrieval

**Core idea**: Fuse the results of the above four retrieval methods using weighted ranking.

```
finalScore = α * semanticScore
           + β * keywordScore
           + γ * temporalScore
           + δ * importanceScore
```

Weights can be dynamically adjusted:
- Query contains proper nouns -> increase keywordScore weight
- Query is an open-ended question -> increase semanticScore weight
- Query contains a time range -> increase temporalScore weight
- Query is a summary question -> increase importanceScore weight

**Bold idea**: Can we use "learned retrieval strategies"? Instead of fixed weights, dynamically adjust weights based on historical retrieval effectiveness (whether the user used the retrieval results, whether the retrieval results helped solve the problem). This is the MemRL approach -- runtime reinforcement learning on memory retrieval.

### 4.6 Query Optimization

- **Query expansion**: Automatically expand the user's query into multiple variants (synonyms, related concepts)
- **Query decomposition**: Break complex queries into multiple sub-queries
- **Query caching**: Cache results of common queries
- **Query intent identification**: Determine whether the user wants to find specific events, patterns, or artifacts

---

## V. Forgetting Mechanism

### 5.1 Why Is Forgetting Needed?

- Indefinitely growing memories lead to degraded retrieval quality (increased noise)
- Storage costs grow linearly with data volume
- Outdated memories may mislead current decisions
- Humans also forget -- forgetting is part of intelligence, not a defect

### 5.2 Passive Forgetting

**Definition**: Memories naturally decay and are moved to archive or deleted when they fall below a threshold.

- Natural decay based on effectiveScore
- No active decision required
- Similar to human "natural forgetting"

**What gets passively forgotten**:
- Low-importance daily operation records
- Specific events that have been replaced by higher-level knowledge
- Memories that have not been retrieved for a long time

### 5.3 Active Forgetting

**Definition**: The system actively identifies and deletes memories that are no longer needed.

**Trigger conditions**:
- Memory is marked as "error" and has been corrected
- Memory is no longer relevant to the current Factory configuration (after Factory restructuring)
- The source artifact of the memory has been deleted
- Human explicitly requests forgetting ("forget this")

**What should NOT be actively forgotten**:
- Memories explicitly marked as "important" by humans
- Records of failures and errors (at least retain summaries)
- Memories related to currently active WorkOrders
- High-confidence Semantic Memories

### 5.4 Boundaries of Forgetting

**Bold idea**: Can we implement "progressive forgetting"? Instead of sudden deletion, gradually reduce resolution:
1. Complete memory -> retain all details
2. Summary memory -> retain only key information
3. Existence memory -> only know "this happened," no details remembered
4. Archived -> moved to cold storage, can be restored if needed
5. Deleted -> completely gone

This mirrors the human memory blurring process -- first forgetting details, then the event, and finally even "this happened" is forgotten.

---

## VI. Memory Consistency

### 6.1 Types of Contradictions

- **Temporal contradiction**: The same thing has different states at different times (this is normal, not a true contradiction)
- **Factual contradiction**: Two memories claim opposite facts ("user likes blue" vs "user doesn't like blue")
- **Preference contradiction**: Human has expressed different preferences in different scenarios
- **Strategy contradiction**: A previously successful strategy now fails

### 6.2 Handling Strategies

**Temporal contradiction**: Distinguish by timestamp, return the latest when retrieving, or return all versions with time annotations.

**Factual contradiction**:
- Check chronological order: later occurrence takes priority
- Check source weight: human directly expressed > AI inferred
- If indeterminate, mark as "pending confirmation" and prompt the human

**Preference contradiction**:
- Record the contextual conditions under which the preference holds
- "User prefers blue in formal settings, green in casual settings"
- When retrieving, select the most matching preference based on current context

**Strategy contradiction**:
- Record the conditions under which the strategy succeeds/fails
- Strategy effectiveness is not absolute but context-dependent
- This is the core idea of MemRL -- strategies need to be evaluated at runtime

### 6.3 Consistency Checking

- When new memories are written, check for contradictions with existing Semantic Memories
- If contradictory, create a "contradiction record" and mark as pending resolution
- Periodically (or when evolution is triggered) batch-check consistency

---

## VII. Cross-Factory Memory Sharing

### 7.1 Motivation for Sharing

- Experience accumulated by one Factory in a domain may be valuable to another Factory
- For example: debugging strategies learned by a "game development Factory" may also be useful for a "software Factory"
- Avoid every Factory starting learning from scratch

### 7.2 Challenges of Sharing

- **Noise problem**: Irrelevant memories degrade retrieval quality
- **Context difference**: Strategies effective in one Factory may be ineffective in another
- **Privacy problem**: Some memories may contain project-specific sensitive information
- **Consistency problem**: Two Factories may develop contradictory Semantic Memories

### 7.3 Sharing Strategies

**Option A: Share only Semantic Memory**
- Episodic Memory is project-specific and not shared
- Semantic Memory is abstract knowledge and can be shared
- Need to verify knowledge applicability in the target Factory before sharing

**Option B: Memory Marketplace**
- Each Factory can "publish" knowledge it considers valuable
- Other Factories can "subscribe" to knowledge they are interested in
- Has a rating mechanism: knowledge adopted by multiple Factories gets higher ratings

**Option C: Federated Learning-style Sharing**
- Do not share memories directly, but share "summaries" or "patterns" of memories
- Similar to federated learning: each Factory learns local patterns, then aggregates global patterns

**Bold idea**: Can there be a "Meta Factory" that does not produce artifacts but is specifically responsible for managing and sharing all Factories' memories? Like a "librarian," it knows what knowledge each Factory has and recommends when needed.

---

## VIII. Wild Idea: Can Memories "Dream"?

### 8.1 Inspiration

Humans dream during sleep -- the brain spontaneously reorganizes memories, tries new connections, and consolidates learning during idle time. This phenomenon has substantial evidence in neuroscience (memory consolidation theory).

### 8.2 What "Dreaming" Means in Heya

**Definition**: When a Factory is idle (no active WorkOrder), it spontaneously reorganizes, connects, and creatively reasons about its memories.

### 8.3 Specific Mechanisms

**Mechanism 1: Memory Reconsolidation**
- Randomly select multiple Episodic Memories
- Try to find implicit associations between them
- If interesting associations are discovered, create new Semantic Memories
- Example: Discover "every time the user submits requirements in the afternoon, they pay more attention to detail" -> create knowledge "user pays more attention to detail in the afternoon"

**Mechanism 2: Counterfactual Reasoning**
- Select a failed Episodic Memory
- Reason: "What would have happened if a different strategy had been adopted?"
- Store the reasoning result as "hypothetical knowledge"
- When encountering similar situations in the future, these hypotheses can be referenced

**Mechanism 3: Creative Connection**
- Select memories from different domains
- Try applying a solution from one domain to another
- Example: Apply "difficulty curve in game level design" to "learning path design"
- This is the advanced form of cross-Factory sharing

**Mechanism 4: Knowledge Grooming**
- Check Semantic Memory consistency
- Merge redundant knowledge
- Delete knowledge that is no longer applicable
- Update knowledge confidence levels

### 8.4 Trigger Conditions for "Dreaming"

- Factory has been idle for more than N minutes
- Memory count exceeds threshold and needs organizing
- Periodic trigger (e.g., every day at midnight)
- Human explicit request ("help me organize my memories")

### 8.5 Outputs of "Dreaming"

- New Semantic Memories (knowledge discovered during memory reconsolidation)
- Updated importance scores (based on new association discoveries)
- Memory compression (cleaning up redundancy)
- Consistency reports (contradictions found and resolutions)
- **Dream Log**: Records all interesting associations discovered during the "dreaming" process for human review

### 8.6 Connection to Academic Research

- **Generative Agents**' Reflection mechanism: Agents periodically reflect on memories and distill high-level cognition. Heya's "dreaming" is an enhanced version of Reflection.
- **MemRL**'s Runtime RL: Reinforcement learning on memories. "Dreaming" can be seen as an unsupervised pre-training phase, providing better initialization for subsequent RL.
- **Human sleep research**: The association between REM sleep and memory consolidation. Heya's "dreaming" corresponds to the REM phase.

---

## IX. Deep Connections to Academic Systems

### 9.1 MemGPT / Letta

**What Heya adopts**:
- Layered memory architecture (fast/slow memory)
- Virtual context management (context window as working memory)
- Paging mechanism (paged loading and swapping of memories)

**Where Heya goes beyond**:
- MemGPT's memory is flat; Heya has a four-layer structure
- MemGPT has no forgetting mechanism; Heya has complete decay and forgetting
- MemGPT has no memory compression; Heya has multi-level compression
- MemGPT has no cross-agent memory sharing; Heya has cross-Factory sharing

### 9.2 CoALA (Cognitive Architectures for Language Agents)

**What Heya adopts**:
- Modular memory architecture (episodic/semantic/procedural)
- Cognitive architecture framework (perception, memory, decision, action loop)

**Where Heya goes beyond**:
- CoALA is a theoretical framework; Heya is a concrete implementation
- CoALA has no Artifact Memory; Heya adds this layer
- CoALA has no memory lifecycle management; Heya has a complete creation-to-archive flow
- CoALA has no forgetting mechanism; Heya has active/passive forgetting

### 9.3 MemRL (Memory-based Reinforcement Learning)

**What Heya adopts**:
- Stability-Plasticity decomposition: stable reasoning vs plastic memory
- Runtime reinforcement learning on memories

**Where Heya goes beyond**:
- MemRL focuses on RL; Heya integrates RL into broader memory management
- MemRL has no compression and forgetting; Heya has both
- MemRL's memory is a single type; Heya has four layers

### 9.4 Generative Agents

**What Heya adopts**:
- Memory Stream concept (memory stream with timestamps and importance scores)
- Three-dimensional retrieval (recency + relevance + importance)
- Reflection mechanism (periodic memory reflection)

**Where Heya goes beyond**:
- Generative Agents' memory has only one layer; Heya has four layers
- Generative Agents' Reflection is simple; Heya's "dreaming" is more complex
- Generative Agents are autonomous; Heya emphasizes human participation
- Generative Agents have no forgetting; Heya does

---

## X. Key Decision Points

The following questions need to be further refined during the proposal phase:

1. **Working Memory capacity management**: Fixed token limit vs dynamic information density management?
2. **Implementation cost of memory chains**: Is causal indexing worth the initial engineering investment?
3. **Compression trigger timing**: Real-time compression vs batch compression?
4. **Forgetting threshold**: How to determine the effectiveScore forgetting threshold?
5. **Scope of cross-Factory sharing**: Initially share only Semantic Memory, or also include compressed Episodic?
6. **Computational cost of "dreaming"**: How much computational resource does idle-time memory reorganization require?
7. **Vector database selection**: FAISS (self-hosted) vs Pinecone/Weaviate (managed service)?
8. **Embedding model selection**: OpenAI embeddings vs open-source models vs fine-tuned models?

---

*This brainstorm record serves as input for ROUND-002 and will produce three proposals: P004 (Memory Lifecycle), P005 (Intelligent Compression), and P006 (Hybrid Retrieval).*
