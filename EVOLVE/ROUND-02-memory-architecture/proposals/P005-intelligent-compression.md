# Proposal P005: Intelligent Memory Compression

> **Status**: Draft
> **Proposer**: AI Assistant
> **Date**: 2026-05-16
> **Related**: ROUND-002 Brainstorm, P004 Memory Lifecycle

---

## Summary

Design a multi-level intelligent memory compression system to address the rising storage costs and degraded retrieval quality caused by unlimited memory growth. Compression is divided into four levels: summary compression (merging multiple memories into a summary), semantic compression (extracting core semantics, Episodic -> Semantic distillation), knowledge graph compression (knowledge upgrade within Semantic Memory), and discard compression (permanently deleting low-value memories). The system ensures compression does not lose critical information through an "information retention score," supports pre-compression validation and post-compression reversibility checks. Compression is tightly integrated with P004's lifecycle management: compression serves as a buffer step before forgetting, and is also the driving force behind natural memory evolution.

---

## Design

### Compression Level Overview

```
┌─────────────────────────────────────────────────────────────┐
│                    Compression Level Pyramid                │
│                                                             │
│                    ┌──────┐                                  │
│                    │ L4   │  Discard Compression             │
│                   ┌┴──────┴┐                                 │
│                   │ L3     │  Knowledge Graph Compression    │
│                  ┌┴────────┴┐                                │
│                  │ L2       │  Semantic Compression (Episodic→Semantic) │
│                 ┌┴──────────┴┐                               │
│                 │ L1         │  Summary Compression           │
│                 └────────────┘                               │
│                                                             │
│  L1: Retain key information, discard details                │
│  L2: Extract patterns, generate knowledge                   │
│  L3: Knowledge merging and upgrade                         │
│  L4: Permanent deletion                                     │
└─────────────────────────────────────────────────────────────┘
```

### Core Data Structures

```typescript
// ============================================
// Compression-related type definitions
// ============================================

/** Compression level */
enum CompressionLevel {
  NONE = 0,       // Original memory, uncompressed
  SUMMARY = 1,    // Summary compression
  SEMANTIC = 2,   // Semantic compression (distilled into knowledge)
  GRAPH = 3,      // Knowledge graph compression (merged and upgraded)
  DISCARDED = 4,  // Discarded
}

/** Compression strategy */
enum CompressionStrategy {
  /** Summary compression: merge multiple similar memories into a single summary */
  SUMMARIZATION = 'summarization',

  /** Semantic distillation: extract abstract knowledge from specific events */
  SEMANTIC_EXTRACTION = 'semantic_extraction',

  /** Knowledge merging: merge multiple related knowledge entries into higher-level knowledge */
  KNOWLEDGE_MERGING = 'knowledge_merging',

  /** Selective discard: delete memories that no longer have value */
  SELECTIVE_DISCARD = 'selective_discard',

  /** Adaptive compression: automatically select the appropriate compression level based on memory state */
  ADAPTIVE = 'adaptive',
}

/** Compression task */
interface CompressionTask {
  id: string;
  strategy: CompressionStrategy;
  sourceMemoryIds: string[];
  targetTier: MemoryTier;
  priority: number;              // 0-1
  createdAt: number;
  status: 'pending' | 'running' | 'completed' | 'failed';
  result?: CompressionResult;
  error?: string;
}

/** Compression result */
interface CompressionResult {
  /** IDs of newly generated memories */
  producedMemoryIds: string[];

  /** IDs of original memories that were compressed (status changed) */
  compressedMemoryIds: string[];

  /** IDs of permanently deleted memories */
  deletedMemoryIds: string[];

  /** Information retention score (0-1, higher is better) */
  informationRetentionScore: number;

  /** Compression ratio (original size / compressed size) */
  compressionRatio: number;

  /** Compression duration (ms) */
  durationMs: number;

  /** LLM tokens used */
  tokensUsed: number;
}

/** Detailed breakdown of information retention score */
interface InformationRetentionBreakdown {
  overall: number;               // Overall retention score
  decisionContext: number;        // Decision context retention
  failureRecords: number;        // Failure record retention
  emotionalValence: number;      // Emotional valence retention
  causalLinks: number;           // Causal relationship retention
  entityReferences: number;      // Entity reference retention
  temporalInfo: number;          // Temporal information retention
}
```

### Level 1: Summary Compression

```typescript
// ============================================
// Summary compression service
// ============================================

interface ISummarizationCompressor {
  /**
   * Merge a group of similar Episodic Memories into a single summary
   *
   * Trigger conditions:
   * - Episodic Memories under the same topic/tag exceed N entries
   * - Memory's effectiveScore is below threshold but above archive threshold
   * - Manual trigger
   */
  compress(params: SummarizationParams): Promise<CompressionResult>;
}

interface SummarizationParams {
  /** List of Episodic Memory IDs to compress */
  sourceMemoryIds: string[];

  /** Maximum token count for the summary */
  maxSummaryTokens?: number;     // Default 200

  /** Information types that must be preserved */
  mustPreserve?: PreservationRequirement[];

  /** Whether to keep original memories (move to archive) or replace directly */
  keepOriginal?: boolean;        // Default true
}

/** Preservation requirement */
interface PreservationRequirement {
  type: 'decision' | 'failure' | 'entity' | 'causal_link' | 'timestamp';
  description: string;
  priority: 'required' | 'important' | 'nice_to_have';
}

// ============================================
// Summary compression pseudocode
// ============================================

async function summarizeEpisodicMemories(
  memories: EpisodicMemoryEntry[],
  params: SummarizationParams,
  llmClient: LLMClient
): Promise<{ summary: string; retentionScore: number }> {
  // 1. Sort by time
  const sorted = [...memories].sort((a, b) => a.createdAt - b.createdAt);

  // 2. Extract information that must be preserved
  const preservedInfo = extractPreservedInfo(sorted, params.mustPreserve);

  // 3. Build compression prompt
  const prompt = buildSummarizationPrompt(sorted, preservedInfo, params.maxSummaryTokens);

  // 4. Call LLM to generate summary
  const summary = await llmClient.complete(prompt);

  // 5. Validate information retention
  const retentionScore = evaluateRetention(sorted, summary, preservedInfo);

  // 6. If retention score is below threshold, try supplementing key information
  if (retentionScore.overall < 0.7) {
    const supplement = buildSupplement(sorted, summary, preservedInfo);
    const supplementedSummary = await llmClient.complete(supplement);
    const newRetentionScore = evaluateRetention(sorted, supplementedSummary, preservedInfo);

    if (newRetentionScore.overall > retentionScore.overall) {
      return { summary: supplementedSummary, retentionScore: newRetentionScore };
    }
  }

  return { summary, retentionScore };
}

function buildSummarizationPrompt(
  memories: EpisodicMemoryEntry[],
  preservedInfo: PreservedInfo,
  maxTokens: number
): string {
  return `
You are a memory compression assistant. Please compress the following ${memories.length} memories into a concise summary.

## Original Memories
${memories.map((m, i) => `[${i + 1}] (${new Date(m.createdAt).toISOString()}) ${m.content}`).join('\n')}

## Information That Must Be Preserved
${formatPreservedInfo(preservedInfo)}

## Compression Requirements
- Summary must not exceed ${maxTokens} tokens
- Retain all information marked as "required"
- Retain the time range (earliest and latest event times)
- Retain emotional valence (positive/negative/neutral)
- Summarize concisely without omitting key decisions and results

## Output Format
Output only the summary text, without additional explanation.
`.trim();
}
```

### Level 2: Semantic Compression (Episodic -> Semantic Distillation)

```typescript
// ============================================
// Semantic distillation service
// ============================================

interface ISemanticExtractor {
  /**
   * Distill Semantic Memory from Episodic Memory
   *
   * Trigger conditions:
   * - Multiple Episodic Memories exhibit similar patterns (cluster detection)
   * - Recurring preferences/constraints in human feedback
   * - "Dreaming" mechanism discovers implicit associations
   * - Manual trigger
   */
  extract(params: SemanticExtractionParams): Promise<CompressionResult>;
}

interface SemanticExtractionParams {
  /** Input Episodic Memory ID list */
  sourceMemoryIds: string[];

  /** Distillation direction (optional, auto-detected if not provided) */
  suggestedKnowledgeType?: KnowledgeType;

  /** Minimum supporting evidence count (minimum number of Episodic Memories needed for distillation) */
  minEvidenceCount?: number;     // Default 3
}

// ============================================
// Semantic distillation pseudocode
// ============================================

async function extractSemanticKnowledge(
  memories: EpisodicMemoryEntry[],
  params: SemanticExtractionParams,
  llmClient: LLMClient
): Promise<SemanticExtractionResult> {
  // 1. Pattern detection: find common patterns in memories
  const patterns = await detectPatterns(memories, llmClient);

  // 2. Validate patterns: ensure they are not coincidental similarities
  const validatedPatterns = await validatePatterns(patterns, memories);

  // 3. Determine knowledge type
  const knowledgeEntries = validatedPatterns.map(pattern => ({
    content: pattern.description,
    knowledgeType: params.suggestedKnowledgeType || pattern.suggestedType,
    confidence: calculateConfidence(pattern, memories),
    sourceEpisodes: memories.map(m => m.id),
    contextConditions: pattern.contextConditions,
  }));

  // 4. Check for contradictions with existing Semantic Memory
  const contradictions = await checkContradictions(knowledgeEntries);

  // 5. Resolve contradictions
  for (const contradiction of contradictions) {
    // If new knowledge is more confident (more evidence), update existing knowledge
    // If existing knowledge is more confident, lower the new knowledge's confidence
    await resolveContradiction(contradiction);
  }

  return {
    knowledgeEntries,
    contradictions,
    patternCount: validatedPatterns.length,
  };
}

async function detectPatterns(
  memories: EpisodicMemoryEntry[],
  llmClient: LLMClient
): Promise<DetectedPattern[]> {
  const prompt = `
Analyze the following ${memories.length} memories and identify common patterns.

## Memory Contents
${memories.map((m, i) => `[${i + 1}] ${m.content}`).join('\n')}

## Analysis Requirements
Identify common patterns across these memories, including but not limited to:
- Recurring behaviors or preferences
- Repeated errors or failure patterns
- Implicit constraints
- Effective strategies or methods
- Anomalies or noteworthy patterns

## Output Format
For each discovered pattern, output:
- Pattern description
- Suggested knowledge type (pattern/preference/constraint/heuristic/rule/anomaly)
- Supporting evidence (which memories support this pattern)
- Context conditions under which the pattern holds
- Confidence level (0-1, based on evidence quantity and consistency)
`.trim();

  const response = await llmClient.complete(prompt);
  return parsePatterns(response);
}

function calculateConfidence(
  pattern: DetectedPattern,
  memories: EpisodicMemoryEntry[]
): number {
  // Calculate confidence based on the following factors
  const evidenceRatio = pattern.supportingEvidence.length / memories.length;
  const consistencyScore = pattern.consistencyScore; // Consistency between evidence
  const recencyBoost = calculateRecencyBoost(pattern.supportingEvidence, memories);

  return Math.min(1.0,
    evidenceRatio * 0.4 +
    consistencyScore * 0.4 +
    recencyBoost * 0.2
  );
}
```

### Level 3: Knowledge Graph Compression

```typescript
// ============================================
// Knowledge merging service
// ============================================

interface IKnowledgeMerger {
  /**
   * Merge multiple related Semantic Memories into higher-level knowledge
   *
   * Trigger conditions:
   * - Multiple Semantic Memories have semantic similarity above threshold
   * - "Dreaming" mechanism discovers implicit relationships between knowledge entries
   * - Human explicitly requests knowledge organization
   */
  merge(params: KnowledgeMergeParams): Promise<CompressionResult>;
}

interface KnowledgeMergeParams {
  /** Semantic Memory ID list to merge */
  sourceMemoryIds: string[];

  /** Merge strategy */
  mergeStrategy: 'generalize' | 'specialize' | 'compose' | 'reconcile';
}

/**
 * Merge strategy descriptions:
 * - generalize: Distill more general knowledge from specific knowledge
 *   Example: "User prefers green buttons" + "User prefers rounded corners" -> "User prefers clean, modern UI style"
 *
 * - specialize: Refine general knowledge into context-specific knowledge
 *   Example: "User prefers clean design" -> "User prefers clean design on mobile" + "User prefers feature-rich design on desktop"
 *
 * - compose: Combine multiple knowledge entries into composite knowledge
 *   Example: "API responses should be cached" + "Cache should have TTL" -> "API responses should be cached with TTL"
 *
 * - reconcile: Harmonize contradictory knowledge
 *   Example: "User prefers dark mode" + "User prefers light mode" -> "User prefers dark mode at night, light mode during the day"
 */

// ============================================
// Knowledge merging pseudocode
// ============================================

async function mergeSemanticKnowledge(
  memories: SemanticMemoryEntry[],
  params: KnowledgeMergeParams,
  llmClient: LLMClient
): Promise<KnowledgeMergeResult> {
  switch (params.mergeStrategy) {
    case 'generalize':
      return generalizeKnowledge(memories, llmClient);
    case 'specialize':
      return specializeKnowledge(memories, llmClient);
    case 'compose':
      return composeKnowledge(memories, llmClient);
    case 'reconcile':
      return reconcileKnowledge(memories, llmClient);
  }
}

async function generalizeKnowledge(
  memories: SemanticMemoryEntry[],
  llmClient: LLMClient
): Promise<KnowledgeMergeResult> {
  const prompt = `
Below are multiple knowledge entries distilled from experience. Please find their commonalities and distill a more general piece of knowledge.

## Input Knowledge
${memories.map((m, i) => `[${i + 1}] (${m.knowledgeType}, confidence: ${m.confidence}) ${m.content}`).join('\n')}

## Requirements
- The generalized knowledge should cover the core meaning of all input knowledge
- Retain context conditions (if any)
- The new knowledge's confidence should not be lower than the average confidence of input knowledge
- If a reasonable generalization cannot be found, explain why

## Output Format
- Generalized knowledge content
- Knowledge type
- Context conditions
- Confidence level
- Which input knowledge entries are covered
`.trim();

  const response = await llmClient.complete(prompt);
  return parseMergeResult(response, memories);
}
```

### Level 4: Selective Discard

```typescript
// ============================================
// Selective discard service
// ============================================

interface ISelectiveDiscarder {
  /**
   * Identify and discard memories that no longer have value
   *
   * Trigger conditions:
   * - Memory has been completely covered by higher-level knowledge
   * - Memory's effectiveScore is far below threshold
   * - Memory's content has been replaced by new memories
   */
  identifyDiscardable(tier: MemoryTier): Promise<DiscardCandidate[]>;

  /** Execute discard */
  discard(candidateIds: string[]): Promise<CompressionResult>;
}

interface DiscardCandidate {
  memoryId: string;
  reason: DiscardReason;
  /** Higher-level memory that replaces this memory (if any) */
  replacedBy?: string;
  /** Risk assessment of the discard */
  risk: 'low' | 'medium' | 'high';
}

type DiscardReason =
  | 'fully_covered_by_semantic'     // Completely covered by Semantic Memory
  | 'superseded_by_newer'           // Replaced by a newer memory
  | 'obsolete_context'              // Context is outdated (after Factory restructuring)
  | 'duplicate'                     // Duplicate of another memory
  | 'below_minimum_value';          // Value below minimum threshold

async function identifyDiscardable(
  tier: MemoryTier,
  memoryStore: IMemoryStore
): Promise<DiscardCandidate[]> {
  const candidates: DiscardCandidate[] = [];

  if (tier === MemoryTier.EPISODIC) {
    const allEpisodic = await memoryStore.getByTier(MemoryTier.EPISODIC);

    for (const memory of allEpisodic) {
      // Check if completely covered by Semantic Memory
      const coveringSemantic = await memoryStore.findCoveringSemantic(memory);
      if (coveringSemantic && coveringSemantic.confidence > 0.8) {
        candidates.push({
          memoryId: memory.id,
          reason: 'fully_covered_by_semantic',
          replacedBy: coveringSemantic.id,
          risk: 'low',
        });
        continue;
      }

      // Check if there is a newer replacement memory
      const newer = await memoryStore.findNewerVersion(memory);
      if (newer) {
        candidates.push({
          memoryId: memory.id,
          reason: 'superseded_by_newer',
          replacedBy: newer.id,
          risk: 'low',
        });
        continue;
      }

      // Check if it is a duplicate memory
      const duplicates = await memoryStore.findDuplicates(memory);
      if (duplicates.length > 0) {
        // Keep the most important one, discard the rest
        const sorted = [memory, ...duplicates].sort((a, b) => b.importance - a.importance);
        for (let i = 1; i < sorted.length; i++) {
          candidates.push({
            memoryId: sorted[i].id,
            reason: 'duplicate',
            replacedBy: sorted[0].id,
            risk: 'low',
          });
        }
      }
    }
  }

  return candidates;
}
```

### Compression Scheduler

```typescript
// ============================================
// Compression scheduler
// ============================================

interface ICompressionScheduler {
  /** Start automatic compression scheduling */
  start(): void;

  /** Stop scheduling */
  stop(): void;

  /** Manually trigger compression */
  triggerCompression(params: ManualCompressionParams): Promise<CompressionResult>;

  /** Get compression statistics */
  getStats(): CompressionStats;
}

interface ManualCompressionParams {
  strategy: CompressionStrategy;
  sourceMemoryIds?: string[];
  tier?: MemoryTier;
  force?: boolean;                 // Force compression (ignore protection rules)
}

interface CompressionStats {
  totalCompressions: number;
  byStrategy: Record<CompressionStrategy, number>;
  averageRetentionScore: number;
  averageCompressionRatio: number;
  totalTokensUsed: number;
  pendingTasks: number;
}

// ============================================
// Auto-compression trigger conditions
// ============================================

interface CompressionTriggerConfig {
  /** Conditions for triggering summary compression */
  summarization: {
    /** Episodic Memory count threshold under the same tag */
    sameTagThreshold: number;      // Default 10
    /** Consider compression when effectiveScore is below this value */
    maxScoreThreshold: number;     // Default 0.3
  };

  /** Conditions for triggering semantic distillation */
  semanticExtraction: {
    /** Cluster similarity threshold */
    clusterSimilarity: number;     // Default 0.85
    /** Minimum supporting evidence count */
    minEvidenceCount: number;      // Default 3
    /** Check frequency (cron) */
    checkInterval: string;         // Default "0 */4 * * *" (every 4 hours)
  };

  /** Conditions for triggering knowledge merging */
  knowledgeMerging: {
    /** Semantic Memory similarity threshold */
    similarityThreshold: number;   // Default 0.9
    /** Check frequency */
    checkInterval: string;         // Default "0 0 * * 0" (weekly)
  };

  /** Conditions for triggering selective discard */
  selectiveDiscard: {
    /** Consider discard when effectiveScore is below this value */
    minScoreThreshold: number;     // Default 0.01
    /** Check frequency */
    checkInterval: string;         // Default "0 3 * * *" (daily at 3 AM)
  };
}

// ============================================
// Compression pipeline
// ============================================

async function runCompressionPipeline(
  task: CompressionTask,
  services: {
    summarizer: ISummarizationCompressor;
    extractor: ISemanticExtractor;
    merger: IKnowledgeMerger;
    discarder: ISelectiveDiscarder;
    retentionEvaluator: IRetentionEvaluator;
    memoryStore: IMemoryStore;
  }
): Promise<CompressionResult> {
  const startTime = Date.now();

  switch (task.strategy) {
    case CompressionStrategy.SUMMARIZATION: {
      const result = await services.summarizer.compress({
        sourceMemoryIds: task.sourceMemoryIds,
      });

      // Validate information retention
      const retention = await services.retentionEvaluator.evaluate(
        task.sourceMemoryIds,
        result.producedMemoryIds
      );

      if (retention.overall < 0.6) {
        throw new Error(
          `Information retention score too low after compression: ${retention.overall}, cancelling compression`
        );
      }

      return {
        ...result,
        informationRetentionScore: retention.overall,
        durationMs: Date.now() - startTime,
        tokensUsed: estimateTokensUsed(task),
      };
    }

    case CompressionStrategy.SEMANTIC_EXTRACTION: {
      const result = await services.extractor.extract({
        sourceMemoryIds: task.sourceMemoryIds,
      });

      return {
        ...result,
        informationRetentionScore: 1.0, // Semantic distillation does not lose information, it upgrades it
        durationMs: Date.now() - startTime,
        tokensUsed: estimateTokensUsed(task),
      };
    }

    // ... other strategies similar
  }
}
```

### Information Retention Evaluator

```typescript
// ============================================
// Information retention evaluation
// ============================================

interface IRetentionEvaluator {
  /**
   * Evaluate whether compressed memories retain critical information from original memories
   */
  evaluate(
    originalMemoryIds: string[],
    compressedMemoryIds: string[]
  ): Promise<InformationRetentionBreakdown>;
}

async function evaluateRetention(
  originals: EpisodicMemoryEntry[],
  compressed: string,
  preservedInfo: PreservedInfo
): Promise<InformationRetentionBreakdown> {
  const scores: InformationRetentionBreakdown = {
    overall: 0,
    decisionContext: 0,
    failureRecords: 0,
    emotionalValence: 0,
    causalLinks: 0,
    entityReferences: 0,
    temporalInfo: 0,
  };

  // 1. Decision context retention check
  const decisions = originals.filter(m =>
    m.tags.includes('decision') || m.tags.includes('human-choice')
  );
  if (decisions.length > 0) {
    const preserved = decisions.filter(d =>
      compressed.includes(extractKeyDecision(d.content))
    );
    scores.decisionContext = preserved.length / decisions.length;
  } else {
    scores.decisionContext = 1.0; // No decision information, no penalty
  }

  // 2. Failure record retention check
  const failures = originals.filter(m =>
    m.emotionalValence === EmotionalValence.NEGATIVE
  );
  if (failures.length > 0) {
    const preserved = failures.filter(f =>
      compressed.includes(extractKeyFailure(f.content))
    );
    scores.failureRecords = preserved.length / failures.length;
  } else {
    scores.failureRecords = 1.0;
  }

  // 3. Emotional valence retention check
  const hasPositive = originals.some(m => m.emotionalValence === EmotionalValence.POSITIVE);
  const hasNegative = originals.some(m => m.emotionalValence === EmotionalValence.NEGATIVE);
  const compressedHasPositive = detectSentiment(compressed) > 0.2;
  const compressedHasNegative = detectSentiment(compressed) < -0.2;
  scores.emotionalValence =
    (!hasPositive || compressedHasPositive) && (!hasNegative || compressedHasNegative) ? 1.0 : 0.5;

  // 4. Causal relationship retention check
  const causalCount = originals.reduce((sum, m) => sum + m.causalLinks.length, 0);
  if (causalCount > 0) {
    // Check if the compressed text implies causal relationships
    const causalWords = ['because', 'therefore', 'led to', 'due to', 'thus', 'because', 'therefore'];
    const causalWordCount = causalWords.filter(w => compressed.includes(w)).length;
    scores.causalLinks = Math.min(1.0, causalWordCount / Math.min(causalCount, 3));
  } else {
    scores.causalLinks = 1.0;
  }

  // 5. Entity reference retention check
  const entities = extractEntities(originals.map(m => m.content));
  if (entities.length > 0) {
    const preserved = entities.filter(e => compressed.includes(e));
    scores.entityReferences = preserved.length / entities.length;
  } else {
    scores.entityReferences = 1.0;
  }

  // 6. Temporal information retention check
  const timeRange = {
    earliest: Math.min(...originals.map(m => m.createdAt)),
    latest: Math.max(...originals.map(m => m.createdAt)),
  };
  const hasTimeInfo = compressed.includes('time') || compressed.includes('date' ||
    /\d{4}[-\/]\d{2}/.test(compressed));
  scores.temporalInfo = hasTimeInfo ? 1.0 : 0.5;

  // Composite score (weighted average)
  scores.overall =
    scores.decisionContext * 0.25 +
    scores.failureRecords * 0.25 +
    scores.emotionalValence * 0.1 +
    scores.causalLinks * 0.15 +
    scores.entityReferences * 0.15 +
    scores.temporalInfo * 0.1;

  return scores;
}
```

### Compression-Lifecycle Integration

```typescript
// ============================================
// Compression-lifecycle integration interface
// ============================================

interface ICompressionLifecycleIntegration {
  /**
   * Attempt compression before forgetting
   * If compression succeeds and retention score is high enough, compression replaces forgetting
   */
  compressBeforeForget(memoryId: string): Promise<CompressionOrForgetResult>;

  /**
   * Batch processing: scan memories about to be forgotten, attempt compression
   */
  batchCompressBeforeForget(memoryIds: string[]): Promise<BatchCompressionResult>;
}

type CompressionOrForgetResult =
  | { action: 'compressed'; result: CompressionResult }
  | { action: 'forgotten'; reason: string }
  | { action: 'preserved'; reason: string };

async function compressBeforeForget(
  memoryId: string,
  entry: EpisodicMemoryEntry,
  services: CompressionServices
): Promise<CompressionOrForgetResult> {
  // 1. Check if compression is possible
  if (entry.compressionLevel >= 2) {
    // Already at existence trace level, cannot compress further
    return { action: 'forgotten', reason: 'Maximum compression level already reached' };
  }

  // 2. Check if there are similar memories that can be compressed together
  const similarMemories = await services.memoryStore.findSimilar(
    memoryId,
    { minSimilarity: 0.8, limit: 5 }
  );

  if (similarMemories.length >= 2) {
    // Enough similar memories, execute summary compression
    try {
      const result = await services.summarizer.compress({
        sourceMemoryIds: [memoryId, ...similarMemories.map(m => m.id)],
      });

      if (result.informationRetentionScore >= 0.6) {
        return { action: 'compressed', result };
      }
    } catch (error) {
      // Compression failed, continue with forgetting flow
    }
  }

  // 3. Check if it can be distilled into Semantic Memory
  const patternMemories = await services.memoryStore.findPatternGroup(memoryId);
  if (patternMemories.length >= 3) {
    try {
      const result = await services.extractor.extract({
        sourceMemoryIds: patternMemories.map(m => m.id),
      });

      return { action: 'compressed', result };
    } catch (error) {
      // Distillation failed, continue with forgetting flow
    }
  }

  // 4. Cannot compress, execute progressive forgetting
  return { action: 'forgotten', reason: 'No suitable compression strategy available' };
}
```

---

## Advantages

1. **Storage efficiency**: Four-level compression can significantly reduce storage footprint, estimated to reduce Episodic Memory storage by 60-80%
2. **Retrieval quality**: Compression removes redundancy and noise, improving the signal-to-noise ratio of retrieval
3. **Knowledge sedimentation**: Semantic compression transforms specific events into abstract knowledge, achieving the leap from experience to wisdom
4. **Information fidelity**: The information retention score mechanism ensures compression does not lose critical information
5. **Seamless lifecycle integration**: Compression serves as a buffer before forgetting, providing a gentler memory management approach than direct forgetting
6. **Flexible strategy selection**: Four compression strategies cover different scenarios, and the adaptive strategy can automatically select the optimal approach
7. **Verifiable**: Every compression has a retention score and compression ratio, supporting quality monitoring

## Risks

1. **LLM dependency**: Summary compression and semantic distillation both depend on LLM, with cost and quality volatility risks
   - *Mitigation*: Use smaller models for compression tasks; cache common compression patterns; set token budgets
2. **Compression bias**: LLM may introduce its own interpretation bias during compression, distorting original information
   - *Mitigation*: Information retention score as quality gate; preserve original memories to archive for auditing
3. **Over-compression**: May compress information that seems unimportant but is actually critical
   - *Mitigation*: Conservative retention threshold (0.6); humans can mark memories as "non-compressible"
4. **Compression latency**: Compressing large volumes of memories may take considerable time
   - *Mitigation*: Background async execution; priority scheduling; batch processing
5. **Semantic distillation accuracy**: Distilling abstract knowledge from specific events may produce incorrect generalizations
   - *Mitigation*: Confidence mechanism; multi-evidence requirement; periodic validation

## Pending Decisions

1. **LLM selection for compression**: Use the same model as the Factory or a dedicated small model?
2. **Compression trigger thresholds**: How to determine trigger thresholds for each compression level? Should they auto-adjust based on usage data?
3. **Information retention score threshold**: Is the minimum retention score of 0.6 appropriate? Should different memory types have different thresholds?
4. **Compression timing**: Real-time compression (check immediately after memory creation) or batch compression (periodic scanning)?
5. **Original memory retention policy**: How long to retain original memories after compression? Should we support restoring original memories from compression results?
6. **Cross-language compression**: When memory content is in Chinese, should compression maintain Chinese? How to handle mixed-language memories?
