# Proposal P006: Hybrid Retrieval Engine

> **Status**: Draft
> **Proposer**: AI Assistant
> **Date**: 2026-05-16
> **Related**: ROUND-002 Brainstorm, P004 Memory Lifecycle, Generative Agents (Three-Dimensional Retrieval)

---

## Summary

Design a multi-dimensional hybrid retrieval engine to provide efficient, precise memory retrieval for Heya's four-layer memory system. The engine fuses four retrieval dimensions -- semantic similarity (vector retrieval), keyword matching (BM25), temporal relevance (time weighting), and importance scoring (quality weighting) -- producing a final ranking through a learnable weight fusion algorithm. The system includes a query optimizer (query expansion, intent identification, query decomposition), a multi-level caching strategy (semantic cache + result cache + prefetch cache), and a MemRL-inspired adaptive learning mechanism for retrieval strategies. Retrieval results are reranked and deduplicated before being injected into Working Memory, providing precise context for the current task.

---

## Design

### Retrieval Architecture Overview

```
┌──────────────────────────────────────────────────────────────────┐
│                    User Query / System Query                     │
└──────────────────────────────┬───────────────────────────────────┘
                               ▼
                    ┌─────────────────────┐
                    │   Query Optimizer   │
                    │ - Intent detection  │
                    │ - Query expansion   │
                    │ - Query decompose   │
                    │ - Cache lookup      │
                    └──────────┬──────────┘
                               ▼
              ┌────────────────┼────────────────┐
              ▼                ▼                ▼
     ┌────────────┐   ┌────────────┐   ┌────────────┐
     │  Semantic   │   │  Keyword   │   │  Temporal  │
     │  Retrieval  │   │  Retrieval │   │  Retrieval │
     │ (HNSW/FAISS)│   │ (BM25)     │   │ (B-Tree)   │
     └─────┬──────┘   └─────┬──────┘   └─────┬──────┘
           │                │                │
           └────────────────┼────────────────┘
                          ▼
                ┌──────────────────┐
                │  Weighted Fusion │
                │ - Dynamic weight │
                │ - RRF / Weighted │
                └────────┬─────────┘
                         ▼
                ┌──────────────────┐
                │  Reranking       │
                │ - Cross-encoder  │
                │ - Context-aware │
                └────────┬─────────┘
                         ▼
                ┌──────────────────┐
                │  Dedup + Truncate│
                │ - Similar dedup │
                │ - Diversity     │
                └────────┬─────────┘
                         ▼
                ┌──────────────────┐
                │  Result Inject   │
                │ - Working Memory │
                │ - Cache update  │
                │ - Access record  │
                └──────────────────┘
```

### Core Data Structures

```typescript
// ============================================
// Retrieval-related type definitions
// ============================================

/** Retrieval dimension */
enum RetrievalDimension {
  SEMANTIC = 'semantic',       // Semantic similarity
  KEYWORD = 'keyword',         // Keyword matching
  TEMPORAL = 'temporal',       // Temporal relevance
  IMPORTANCE = 'importance',   // Importance
}

/** Query intent */
enum QueryIntent {
  /** Find specific events or facts */
  FACTUAL = 'factual',
  /** Find patterns or trends */
  PATTERN = 'pattern',
  /** Find decisions or causes */
  CAUSAL = 'causal',
  /** Find preferences or constraints */
  PREFERENCE = 'preference',
  /** Open-ended exploration */
  EXPLORATORY = 'exploratory',
  /** Find artifacts */
  ARTIFACT = 'artifact',
}

/** Query source */
enum QuerySource {
  PIPELINE_STEP = 'pipeline_step',   // Automatic query from Pipeline step
  HUMAN_QUERY = 'human_query',       // Direct human query
  REFLECTION = 'reflection',         // Reflection/dreaming mechanism query
  RETRIEVAL_AUGMENTATION = 'rag',    // RAG retrieval augmentation
}

// ============================================
// Query definition
// ============================================

interface MemoryQuery {
  /** Query text */
  text: string;

  /** Query intent (optional, auto-detected if not provided) */
  intent?: QueryIntent;

  /** Query source */
  source: QuerySource;

  /** Restrict memory tiers */
  tiers?: MemoryTier[];

  /** Tag filtering */
  tags?: {
    include?: string[];          // Must include these tags
    exclude?: string[];          // Must exclude these tags
  };

  /** Time range filter */
  timeRange?: {
    start: number;               // Start timestamp
    end: number;                 // End timestamp
    preference?: 'recent' | 'chronological' | 'any';
  };

  /** Factory restriction */
  factoryId?: string;

  /** WorkOrder restriction */
  workOrderId?: string;

  /** Maximum results to return */
  maxResults?: number;           // Default 10

  /** Minimum score threshold */
  minScore?: number;             // Default 0.3

  /** Retrieval weight override (optional, uses adaptive weights if not provided) */
  weights?: RetrievalWeights;

  /** Whether to include archived memories */
  includeArchived?: boolean;     // Default false

  /** Deduplication threshold */
  dedupThreshold?: number;       // Default 0.85
}

/** Retrieval weights */
interface RetrievalWeights {
  semantic: number;              // Semantic similarity weight
  keyword: number;               // Keyword matching weight
  temporal: number;              // Temporal relevance weight
  importance: number;            // Importance weight
}

// ============================================
// Retrieval results
// ============================================

interface MemoryRetrievalResult {
  /** Memory entry */
  memory: BaseMemoryEntry;

  /** Composite score */
  score: number;

  /** Per-dimension score breakdown */
  dimensionScores: {
    semantic: number;
    keyword: number;
    temporal: number;
    importance: number;
  };

  /** Matched query variant (produced by query expansion) */
  matchedQueryVariant?: string;

  /** Retrieval time (ms) */
  retrievalTimeMs: number;
}

/** Retrieval session (a complete retrieval process) */
interface RetrievalSession {
  id: string;
  query: MemoryQuery;
  optimizedQuery: OptimizedQuery;
  results: MemoryRetrievalResult[];
  totalRetrievalTimeMs: number;
  cacheHit: boolean;
  dimensionResults: {
    semantic: DimensionResult;
    keyword: DimensionResult;
    temporal: DimensionResult;
  };
  weightsUsed: RetrievalWeights;
}
```

### Query Optimizer

```typescript
// ============================================
// Query optimizer interface
// ============================================

interface IQueryOptimizer {
  /** Optimize query (intent detection + expansion + decomposition) */
  optimize(query: MemoryQuery): Promise<OptimizedQuery>;

  /** Identify query intent */
  identifyIntent(text: string): Promise<QueryIntent>;
}

interface OptimizedQuery {
  /** Original query */
  original: MemoryQuery;

  /** Detected intent */
  intent: QueryIntent;

  /** Expanded query variant list */
  expandedVariants: QueryVariant[];

  /** Decomposed sub-query list (if complex query) */
  subQueries?: MemoryQuery[];

  /** Recommended retrieval weights */
  recommendedWeights: RetrievalWeights;

  /** Recommended memory tiers */
  recommendedTiers: MemoryTier[];
}

interface QueryVariant {
  text: string;
  expansionType: 'synonym' | 'related_concept' | 'broader' | 'narrower';
  weight: number;               // Weight of this variant
}

// ============================================
// Intent detection pseudocode
// ============================================

async function identifyIntent(text: string, llmClient: LLMClient): Promise<QueryIntent> {
  // Fast rule matching (no LLM tokens consumed)
  const rules: Array<{ pattern: RegExp; intent: QueryIntent }> = [
    { pattern: /when|what time|date|last week|yesterday/i, intent: QueryIntent.FACTUAL },
    { pattern: /why|reason|cause|because|led to/i, intent: QueryIntent.CAUSAL },
    { pattern: /prefer|like|dislike|habit|style/i, intent: QueryIntent.PREFERENCE },
    { pattern: /pattern|trend|always|often|frequent/i, intent: QueryIntent.PATTERN },
    { pattern: /artifact|file|code|design|document|output/i, intent: QueryIntent.ARTIFACT },
    { pattern: /have we|is there|can we|whether/i, intent: QueryIntent.EXPLORATORY },
  ];

  for (const rule of rules) {
    if (rule.pattern.test(text)) {
      return rule.intent;
    }
  }

  // When rules cannot determine, use LLM
  const prompt = `
Determine the intent of the following query, selecting from these options:
- factual: Find specific events or facts
- pattern: Find patterns or trends
- causal: Find decisions or causes
- preference: Find preferences or constraints
- exploratory: Open-ended exploration
- artifact: Find artifacts

Query: "${text}"

Output only the intent type, without any additional content.
`.trim();

  const response = await llmClient.complete(prompt, { maxTokens: 20 });
  return response.trim() as QueryIntent;
}

// ============================================
// Query expansion pseudocode
// ============================================

async function expandQuery(
  text: string,
  intent: QueryIntent,
  llmClient: LLMClient
): Promise<QueryVariant[]> {
  const variants: QueryVariant[] = [];

  // 1. Synonym expansion
  const synonyms = await generateSynonyms(text, llmClient);
  for (const synonym of synonyms) {
    variants.push({
      text: synonym,
      expansionType: 'synonym',
      weight: 0.8,
    });
  }

  // 2. Related concept expansion
  const relatedConcepts = await generateRelatedConcepts(text, intent, llmClient);
  for (const concept of relatedConcepts) {
    variants.push({
      text: concept,
      expansionType: 'related_concept',
      weight: 0.6,
    });
  }

  // 3. Intent-specific expansion
  if (intent === QueryIntent.PATTERN) {
    // Pattern query: expand to broader phrasing
    variants.push({
      text: generalizeQuery(text),
      expansionType: 'broader',
      weight: 0.5,
    });
  }

  if (intent === QueryIntent.FACTUAL) {
    // Factual query: extract key entities as exact match variants
    const entities = extractEntities(text);
    for (const entity of entities) {
      variants.push({
        text: entity,
        expansionType: 'narrower',
        weight: 0.9,
      });
    }
  }

  return variants;
}

// ============================================
// Intent-to-weight mapping
// ============================================

const INTENT_WEIGHT_MAP: Record<QueryIntent, RetrievalWeights> = {
  [QueryIntent.FACTUAL]: {
    semantic: 0.3,
    keyword: 0.4,      // Factual queries rely more on exact matching
    temporal: 0.2,
    importance: 0.1,
  },
  [QueryIntent.PATTERN]: {
    semantic: 0.5,      // Pattern queries rely more on semantic understanding
    keyword: 0.1,
    temporal: 0.2,
    importance: 0.2,
  },
  [QueryIntent.CAUSAL]: {
    semantic: 0.4,
    keyword: 0.2,
    temporal: 0.3,      // Causal queries need temporal information
    importance: 0.1,
  },
  [QueryIntent.PREFERENCE]: {
    semantic: 0.4,
    keyword: 0.1,
    temporal: 0.1,
    importance: 0.4,    // Preference queries rely more on importance
  },
  [QueryIntent.EXPLORATORY]: {
    semantic: 0.6,      // Exploratory queries rely most on semantics
    keyword: 0.1,
    temporal: 0.1,
    importance: 0.2,
  },
  [QueryIntent.ARTIFACT]: {
    semantic: 0.3,
    keyword: 0.3,
    temporal: 0.2,
    importance: 0.2,
  },
};
```

### Multi-Dimensional Retrieval Engine

```typescript
// ============================================
// Hybrid retrieval engine interface
// ============================================

interface IHybridRetrievalEngine {
  /** Execute hybrid retrieval */
  retrieve(query: MemoryQuery): Promise<RetrievalSession>;

  /** Streaming retrieval (return results as they come) */
  retrieveStream(query: MemoryQuery): AsyncIterable<MemoryRetrievalResult>;
}

// ============================================
// Per-dimension retrievers
// ============================================

/** Semantic retriever */
interface ISemanticRetriever {
  /** Vector similarity retrieval */
  search(params: {
    queryVector: number[];
    tier?: MemoryTier[];
    tags?: string[];
    maxResults: number;
    minSimilarity: number;
  }): Promise<DimensionResult>;
}

interface DimensionResult {
  memoryIds: string[];
  scores: Map<string, number>;   // memoryId -> score
  retrievalTimeMs: number;
}

/** Keyword retriever */
interface IKeywordRetriever {
  /** BM25 keyword retrieval */
  search(params: {
    queryText: string;
    tier?: MemoryTier[];
    tags?: string[];
    maxResults: number;
    minScore: number;
  }): Promise<DimensionResult>;
}

/** Temporal retriever */
interface ITemporalRetriever {
  /** Time range retrieval */
  search(params: {
    timeRange: { start: number; end: number };
    tier?: MemoryTier[];
    tags?: string[];
    maxResults: number;
    preference?: 'recent' | 'chronological';
  }): Promise<DimensionResult>;
}

// ============================================
// Weighted fusion ranking
// ============================================

interface IFusionRanker {
  /** Fuse multi-dimensional retrieval results */
  fuse(params: FusionParams): FusionResult;
}

interface FusionParams {
  semanticResults: DimensionResult;
  keywordResults: DimensionResult;
  temporalResults: DimensionResult;
  weights: RetrievalWeights;
  importanceScores: Map<string, number>; // memoryId -> importance
}

interface FusionResult {
  rankedMemoryIds: string[];
  scores: Map<string, number>;
  /** Which dimensions contributed */
  dimensionContributions: Map<string, Set<RetrievalDimension>>;
}

// ============================================
// Fusion algorithm: Reciprocal Rank Fusion (RRF)
// ============================================

function reciprocalRankFusion(
  params: FusionParams
): FusionResult {
  const k = 60; // RRF constant (empirical value)
  const scoreMap = new Map<string, number>();
  const contributionMap = new Map<string, Set<RetrievalDimension>>();

  // 1. Calculate RRF score for each dimension's results
  const dimensions: Array<{
    results: DimensionResult;
    dimension: RetrievalDimension;
    weight: number;
  }> = [
    { results: params.semanticResults, dimension: RetrievalDimension.SEMANTIC, weight: params.weights.semantic },
    { results: params.keywordResults, dimension: RetrievalDimension.KEYWORD, weight: params.weights.keyword },
    { results: params.temporalResults, dimension: RetrievalDimension.TEMPORAL, weight: params.weights.temporal },
  ];

  for (const { results, dimension, weight } of dimensions) {
    results.memoryIds.forEach((memoryId, rank) => {
      const rrfScore = weight / (k + rank + 1);

      if (!scoreMap.has(memoryId)) {
        scoreMap.set(memoryId, 0);
        contributionMap.set(memoryId, new Set());
      }

      scoreMap.set(memoryId, scoreMap.get(memoryId)! + rrfScore);
      contributionMap.get(memoryId)!.add(dimension);
    });
  }

  // 2. Add importance scores
  for (const [memoryId, score] of scoreMap) {
    const importance = params.importanceScores.get(memoryId) ?? 0.5;
    const importanceBoost = importance * params.weights.importance;
    scoreMap.set(memoryId, score + importanceBoost);
  }

  // 3. Sort by score
  const ranked = [...scoreMap.entries()]
    .sort((a, b) => b[1] - a[1])
    .map(([id]) => id);

  return {
    rankedMemoryIds: ranked,
    scores: scoreMap,
    dimensionContributions: contributionMap,
  };
}

// ============================================
// Fusion algorithm: Weighted Score Fusion (alternative)
// ============================================

function weightedScoreFusion(
  params: FusionParams
): FusionResult {
  const scoreMap = new Map<string, number>();
  const contributionMap = new Map<string, Set<RetrievalDimension>>();

  // Calculate weighted composite score for each memory
  const allMemoryIds = new Set([
    ...params.semanticResults.memoryIds,
    ...params.keywordResults.memoryIds,
    ...params.temporalResults.memoryIds,
  ]);

  for (const memoryId of allMemoryIds) {
    let totalScore = 0;
    const contributions = new Set<RetrievalDimension>();

    const semanticScore = params.semanticResults.scores.get(memoryId) ?? 0;
    const keywordScore = params.keywordResults.scores.get(memoryId) ?? 0;
    const temporalScore = params.temporalResults.scores.get(memoryId) ?? 0;
    const importanceScore = params.importanceScores.get(memoryId) ?? 0.5;

    if (semanticScore > 0) {
      totalScore += semanticScore * params.weights.semantic;
      contributions.add(RetrievalDimension.SEMANTIC);
    }
    if (keywordScore > 0) {
      totalScore += keywordScore * params.weights.keyword;
      contributions.add(RetrievalDimension.KEYWORD);
    }
    if (temporalScore > 0) {
      totalScore += temporalScore * params.weights.temporal;
      contributions.add(RetrievalDimension.TEMPORAL);
    }

    totalScore += importanceScore * params.weights.importance;

    // Multi-dimension coverage bonus (memories hit by multiple dimensions get a boost)
    const coverageBonus = contributions.size > 1
      ? 0.05 * (contributions.size - 1)
      : 0;
    totalScore += coverageBonus;

    scoreMap.set(memoryId, totalScore);
    contributionMap.set(memoryId, contributions);
  }

  const ranked = [...scoreMap.entries()]
    .sort((a, b) => b[1] - a[1])
    .map(([id]) => id);

  return {
    rankedMemoryIds: ranked,
    scores: scoreMap,
    dimensionContributions: contributionMap,
  };
}
```

### Reranker

```typescript
// ============================================
// Reranker
// ============================================

interface IReranker {
  /** Rerank preliminary retrieval results */
  rerank(
    query: string,
    results: MemoryRetrievalResult[],
    context?: RerankContext
  ): Promise<MemoryRetrievalResult[]>;
}

interface RerankContext {
  /** Current WorkOrder context */
  currentTask?: string;
  /** Current Working Memory content */
  workingMemoryContent?: string;
  /** Human's recent preferences */
  recentPreferences?: string[];
}

// ============================================
// Cross-encoder reranking pseudocode
// ============================================

async function crossEncoderRerank(
  query: string,
  results: MemoryRetrievalResult[],
  context: RerankContext,
  crossEncoder: ICrossEncoder
): Promise<MemoryRetrievalResult[]> {
  // 1. Build context-enhanced query
  const enhancedQuery = context.currentTask
    ? `${query}\nCurrent task: ${context.currentTask}`
    : query;

  // 2. Calculate cross-encoder score for each result
  const rerankScores = new Map<string, number>();
  for (const result of results) {
    const crossScore = await crossEncoder.score(enhancedQuery, result.memory.content);
    rerankScores.set(result.memory.id, crossScore);
  }

  // 3. Fuse original scores with cross-encoder scores
  const alpha = 0.6; // Cross-encoder weight
  const reranked = results.map(result => {
    const crossScore = rerankScores.get(result.memory.id) ?? 0;
    const fusedScore = alpha * crossScore + (1 - alpha) * result.score;
    return { ...result, score: fusedScore };
  });

  // 4. Re-sort by fused score
  reranked.sort((a, b) => b.score - a.score);

  return reranked;
}

// ============================================
// Cross-encoder interface
// ============================================

interface ICrossEncoder {
  /** Calculate relevance score between query and document */
  score(query: string, document: string): Promise<number>;
}
```

### Deduplication and Diversity

```typescript
// ============================================
// Deduplication and diversity manager
// ============================================

interface IDeduplicationManager {
  /** Deduplicate retrieval results */
  deduplicate(
    results: MemoryRetrievalResult[],
    threshold: number
  ): MemoryRetrievalResult[];

  /** Ensure result diversity */
  ensureDiversity(
    results: MemoryRetrievalResult[],
    config: DiversityConfig
  ): MemoryRetrievalResult[];
}

interface DiversityConfig {
  /** Maximum memories per tag */
  maxPerTag: number;             // Default 3

  /** Maximum memories per source */
  maxPerSource: number;          // Default 3

  /** Temporal diversity: ensure results cover different time periods */
  temporalBuckets?: number;      // Default 3 (recent/mid/long-term)

  /** Tier diversity: ensure results cover different memory tiers */
  tierBalance: boolean;          // Default true
}

async function deduplicateResults(
  results: MemoryRetrievalResult[],
  threshold: number,
  embeddingService: IEmbeddingService
): Promise<MemoryRetrievalResult[]> {
  const kept: MemoryRetrievalResult[] = [];

  for (const result of results) {
    let isDuplicate = false;

    for (const existing of kept) {
      // Calculate semantic similarity with already-kept results
      const similarity = await embeddingService.computeSimilarity(
        result.memory.embedding,
        existing.memory.embedding
      );

      if (similarity > threshold) {
        isDuplicate = true;
        // Keep the one with the higher score
        if (result.score > existing.score) {
          kept.splice(kept.indexOf(existing), 1, result);
        }
        break;
      }
    }

    if (!isDuplicate) {
      kept.push(result);
    }
  }

  return kept;
}

function ensureDiversity(
  results: MemoryRetrievalResult[],
  config: DiversityConfig
): MemoryRetrievalResult[] {
  const filtered: MemoryRetrievalResult[] = [];
  const tagCount = new Map<string, number>();
  const sourceCount = new Map<string, number>();

  for (const result of results) {
    // Tag diversity check
    const primaryTag = result.memory.tags[0] || 'untagged';
    if ((tagCount.get(primaryTag) ?? 0) >= config.maxPerTag) {
      continue;
    }

    // Source diversity check
    const source = result.memory.provenance.sourceType;
    if ((sourceCount.get(source) ?? 0) >= config.maxPerSource) {
      continue;
    }

    filtered.push(result);
    tagCount.set(primaryTag, (tagCount.get(primaryTag) ?? 0) + 1);
    sourceCount.set(source, (sourceCount.get(source) ?? 0) + 1);
  }

  return filtered;
}
```

### Multi-Level Caching Strategy

```typescript
// ============================================
// Cache manager
// ============================================

interface IRetrievalCacheManager {
  /** Query cache */
  get(query: MemoryQuery): RetrievalSession | null;
  set(query: MemoryQuery, session: RetrievalSession): void;

  /** Semantic cache (reuse similar queries) */
  getSemantic(queryVector: number[], threshold: number): RetrievalSession | null;

  /** Prefetch cache (prefetch potentially needed memories based on current task) */
  prefetch(context: PrefetchContext): Promise<void>;

  /** Cache statistics */
  getStats(): CacheStats;
}

interface PrefetchContext {
  currentTask: string;
  currentWorkOrderId: string;
  recentQueries: string[];
  pipelineStepId: string;
}

interface CacheStats {
  hitRate: number;
  semanticHitRate: number;
  prefetchHitRate: number;
  cacheSize: number;
  evictionCount: number;
}

// ============================================
// Three-level cache design
// ============================================

interface CacheConfig {
  /** L1: Exact match cache (query text exact match) */
  l1: {
    enabled: boolean;
    maxSize: number;             // Default 1000 entries
    ttl: number;                 // Default 5 minutes
  };

  /** L2: Semantic cache (similar query reuse) */
  l2: {
    enabled: boolean;
    similarityThreshold: number; // Default 0.95
    maxSize: number;             // Default 500 entries
    ttl: number;                 // Default 30 minutes
  };

  /** L3: Prefetch cache (task-context-based prefetch) */
  l3: {
    enabled: boolean;
    prefetchCount: number;       // Default 20 entries
    ttl: number;                 // Default 10 minutes
  };
}

// ============================================
// Semantic cache pseudocode
// ============================================

class SemanticCache {
  private cache: Map<string, CacheEntry> = new Map();
  private vectorIndex: IVectorIndex; // For fast similarity search

  get(queryVector: number[], threshold: number): RetrievalSession | null {
    // Find the most similar query in cache
    const nearest = this.vectorIndex.search(queryVector, { topK: 1 });

    if (nearest.length > 0 && nearest[0].score >= threshold) {
      const entry = this.cache.get(nearest[0].id);
      if (entry && !this.isExpired(entry)) {
        return entry.session;
      }
    }

    return null;
  }

  set(queryVector: number[], session: RetrievalSession): void {
    const id = generateCacheId();
    this.cache.set(id, {
      session,
      createdAt: Date.now(),
      queryVector,
    });
    this.vectorIndex.add(id, queryVector);
  }
}

// ============================================
// Prefetch strategy pseudocode
// ============================================

async function prefetchMemories(
  context: PrefetchContext,
  retriever: IHybridRetrievalEngine,
  cache: IRetrievalCacheManager
): Promise<void> {
  // 1. Prefetch based on current task description
  const taskQuery: MemoryQuery = {
    text: context.currentTask,
    source: QuerySource.PIPELINE_STEP,
    maxResults: 10,
    tiers: [MemoryTier.EPISODIC, MemoryTier.SEMANTIC],
  };

  const taskResults = await retriever.retrieve(taskQuery);
  cache.set(taskQuery, taskResults);

  // 2. Prefetch based on historical query patterns
  if (context.recentQueries.length > 0) {
    const patternQuery: MemoryQuery = {
      text: context.recentQueries.join(' '),
      source: QuerySource.PIPELINE_STEP,
      maxResults: 5,
      tiers: [MemoryTier.SEMANTIC],
    };

    const patternResults = await retriever.retrieve(patternQuery);
    cache.set(patternQuery, patternResults);
  }

  // 3. Prefetch based on current Pipeline step
  const stepQuery: MemoryQuery = {
    text: `pipeline step ${context.pipelineStepId} related experience`,
    source: QuerySource.PIPELINE_STEP,
    maxResults: 5,
    tiers: [MemoryTier.EPISODIC],
    tags: { include: ['pipeline', 'experience'] },
  };

  const stepResults = await retriever.retrieve(stepQuery);
  cache.set(stepQuery, stepResults);
}
```

### Retrieval Strategy Adaptive Learning

```typescript
// ============================================
// MemRL-inspired retrieval strategy learning
// ============================================

interface IRetrievalStrategyLearner {
  /** Record retrieval result usage feedback */
  recordFeedback(feedback: RetrievalFeedback): void;

  /** Get current learned optimal weights */
  getLearnedWeights(intent: QueryIntent): RetrievalWeights;

  /** Train/update weights (called periodically) */
  train(): Promise<TrainingResult>;
}

interface RetrievalFeedback {
  /** Retrieval session ID */
  sessionId: string;

  /** Original query */
  query: MemoryQuery;

  /** Returned results */
  results: MemoryRetrievalResult[];

  /** Weights used */
  weightsUsed: RetrievalWeights;

  /** Feedback type */
  feedbackType: 'result_used' | 'result_ignored' | 'result_helpful' | 'result_unhelpful';

  /** IDs of used/helpful results */
  relevantMemoryIds?: string[];

  /** Whether the final task succeeded */
  taskSuccess?: boolean;
}

interface TrainingResult {
  previousWeights: Record<QueryIntent, RetrievalWeights>;
  updatedWeights: Record<QueryIntent, RetrievalWeights>;
  improvement: number;           // Retrieval quality improvement percentage
  sampleCount: number;           // Training sample count
}

// ============================================
// Retrieval strategy learning pseudocode
// ============================================

class RetrievalStrategyLearner implements IRetrievalStrategyLearner {
  private feedbackBuffer: RetrievalFeedback[] = [];
  private currentWeights: Record<QueryIntent, RetrievalWeights>;
  private learningRate = 0.05;

  recordFeedback(feedback: RetrievalFeedback): void {
    this.feedbackBuffer.push(feedback);

    // Trigger online learning when buffer is full
    if (this.feedbackBuffer.length >= 50) {
      this.onlineUpdate();
    }
  }

  private onlineUpdate(): void {
    // Group by intent
    const grouped = groupByIntent(this.feedbackBuffer);

    for (const [intent, feedbacks] of Object.entries(grouped)) {
      const weights = this.currentWeights[intent as QueryIntent];

      for (const feedback of feedbacks) {
        if (feedback.feedbackType === 'result_helpful' && feedback.relevantMemoryIds) {
          // Analyze which dimensions contributed most to helpful results
          for (const memoryId of feedback.relevantMemoryIds) {
            const result = feedback.results.find(r => r.memory.id === memoryId);
            if (result) {
              // Increase the weight of the top contributing dimension
              const topDimension = getTopContributingDimension(result.dimensionScores);
              weights[topDimension] += this.learningRate;
            }
          }
        }

        if (feedback.feedbackType === 'result_unhelpful') {
          // Decrease the weights of dimensions that may have led to poor results
          // (Simplified: uniformly decrease all weights, then normalize)
        }
      }

      // Normalize weights (sum to 1)
      this.normalizeWeights(weights);
    }

    this.feedbackBuffer = [];
  }

  private normalizeWeights(weights: RetrievalWeights): void {
    const total = weights.semantic + weights.keyword + weights.temporal + weights.importance;
    weights.semantic /= total;
    weights.keyword /= total;
    weights.temporal /= total;
    weights.importance /= total;
  }
}

function getTopContributingDimension(
  scores: { semantic: number; keyword: number; temporal: number; importance: number }
): keyof RetrievalWeights {
  const entries = Object.entries(scores) as [keyof RetrievalWeights, number][];
  entries.sort((a, b) => b[1] - a[1]);
  return entries[0][0];
}
```

### Complete Retrieval Flow

```typescript
// ============================================
// Hybrid retrieval engine complete implementation
// ============================================

class HybridRetrievalEngine implements IHybridRetrievalEngine {
  constructor(
    private queryOptimizer: IQueryOptimizer,
    private semanticRetriever: ISemanticRetriever,
    private keywordRetriever: IKeywordRetriever,
    private temporalRetriever: ITemporalRetriever,
    private fusionRanker: IFusionRanker,
    private reranker: IReranker,
    private dedupManager: IDeduplicationManager,
    private cacheManager: IRetrievalCacheManager,
    private embeddingService: IEmbeddingService,
    private strategyLearner: IRetrievalStrategyLearner,
    private memoryStore: IMemoryStore,
  ) {}

  async retrieve(query: MemoryQuery): Promise<RetrievalSession> {
    const startTime = Date.now();
    const sessionId = generateSessionId();

    // 1. Check cache
    const cached = this.cacheManager.get(query);
    if (cached) {
      return { ...cached, id: sessionId };
    }

    // 2. Check semantic cache
    const queryVector = await this.embeddingService.embed(query.text);
    const semanticCached = this.cacheManager.getSemantic(queryVector, 0.95);
    if (semanticCached) {
      return { ...semanticCached, id: sessionId, cacheHit: true };
    }

    // 3. Query optimization
    const optimized = await this.queryOptimizer.optimize(query);

    // 4. Determine retrieval weights
    const weights = query.weights
      ?? optimized.recommendedWeights
      ?? this.strategyLearner.getLearnedWeights(optimized.intent);

    // 5. Determine retrieval scope
    const tiers = query.tiers ?? optimized.recommendedTiers;

    // 6. Execute multi-dimensional retrieval in parallel
    const [semanticResults, keywordResults, temporalResults] = await Promise.all([
      this.semanticRetriever.search({
        queryVector,
        tier: tiers,
        tags: query.tags?.include,
        maxResults: query.maxResults ?? 10 * 2, // Retrieve more for fusion
        minSimilarity: 0.3,
      }),
      this.keywordRetriever.search({
        queryText: query.text,
        tier: tiers,
        tags: query.tags?.include,
        maxResults: query.maxResults ?? 10 * 2,
        minScore: 0.1,
      }),
      query.timeRange
        ? this.temporalRetriever.search({
            timeRange: query.timeRange,
            tier: tiers,
            tags: query.tags?.include,
            maxResults: query.maxResults ?? 10 * 2,
            preference: query.timeRange.preference,
          })
        : Promise.resolve({ memoryIds: [], scores: new Map(), retrievalTimeMs: 0 }),
    ]);

    // 7. Get importance scores
    const allMemoryIds = new Set([
      ...semanticResults.memoryIds,
      ...keywordResults.memoryIds,
      ...temporalResults.memoryIds,
    ]);
    const importanceScores = await this.memoryStore.getImportanceScores([...allMemoryIds]);

    // 8. Weighted fusion ranking
    const fusionResult = this.fusionRanker.fuse({
      semanticResults,
      keywordResults,
      temporalResults,
      weights,
      importanceScores,
    });

    // 9. Get complete memory entries
    const memories = await this.memoryStore.getByIds(fusionResult.rankedMemoryIds);

    // 10. Build preliminary results
    const preliminaryResults: MemoryRetrievalResult[] = memories.map(memory => ({
      memory,
      score: fusionResult.scores.get(memory.id) ?? 0,
      dimensionScores: {
        semantic: semanticResults.scores.get(memory.id) ?? 0,
        keyword: keywordResults.scores.get(memory.id) ?? 0,
        temporal: temporalResults.scores.get(memory.id) ?? 0,
        importance: importanceScores.get(memory.id) ?? 0.5,
      },
      retrievalTimeMs: Date.now() - startTime,
    }));

    // 11. Reranking
    const reranked = await this.reranker.rerank(query.text, preliminaryResults);

    // 12. Deduplication
    const deduped = this.dedupManager.deduplicate(
      reranked,
      query.dedupThreshold ?? 0.85
    );

    // 13. Diversity guarantee
    const diversified = this.dedupManager.ensureDiversity(deduped, {
      maxPerTag: 3,
      maxPerSource: 3,
      tierBalance: true,
    });

    // 14. Truncation
    const finalResults = diversified.slice(0, query.maxResults ?? 10);

    // 15. Filter results below threshold
    const filtered = finalResults.filter(r => r.score >= (query.minScore ?? 0.3));

    // 16. Update access records
    await this.memoryStore.updateAccessCounts(filtered.map(r => r.memory.id));

    // 17. Build session result
    const session: RetrievalSession = {
      id: sessionId,
      query,
      optimizedQuery: optimized,
      results: filtered,
      totalRetrievalTimeMs: Date.now() - startTime,
      cacheHit: false,
      dimensionResults: {
        semantic: semanticResults,
        keyword: keywordResults,
        temporal: temporalResults,
      },
      weightsUsed: weights,
    };

    // 18. Update cache
    this.cacheManager.set(query, session);

    return session;
  }
}
```

### Retrieval Result Injection into Working Memory

```typescript
// ============================================
// Working Memory injector
// ============================================

interface IWorkingMemoryInjector {
  /**
   * Inject retrieval results into Working Memory
   *
   * Strategy:
   * - High-score results are injected directly into the focus zone
   * - Medium-score results are injected into the periphery zone (in summary form)
   * - Low-score results only have references recorded, not occupying space
   */
  inject(
    results: MemoryRetrievalResult[],
    workingMemory: IWorkingMemoryManager,
    config?: InjectionConfig
  ): InjectionResult;
}

interface InjectionConfig {
  /** Focus zone capacity (token count) */
  focusCapacity: number;         // Default 2000
  /** Periphery zone capacity (token count) */
  peripheryCapacity: number;     // Default 3000
  /** Minimum injection score */
  minInjectionScore: number;     // Default 0.5
}

interface InjectionResult {
  /** Number of results injected into focus zone */
  focusCount: number;
  /** Number of results injected into periphery zone */
  peripheryCount: number;
  /** Number of results with only references recorded */
  referenceCount: number;
  /** Total tokens used */
  totalTokensUsed: number;
}

async function injectToWorkingMemory(
  results: MemoryRetrievalResult[],
  workingMemory: IWorkingMemoryManager,
  config: InjectionConfig
): Promise<InjectionResult> {
  let focusTokens = 0;
  let peripheryTokens = 0;
  let focusCount = 0;
  let peripheryCount = 0;
  let referenceCount = 0;

  for (const result of results) {
    const contentTokens = estimateTokens(result.memory.content);

    if (result.score >= 0.8 && focusTokens + contentTokens <= config.focusCapacity) {
      // High-score result -> Focus zone (full content)
      workingMemory.add({
        id: `retrieved_${result.memory.id}`,
        tier: MemoryTier.WORKING,
        type: 'retrieved_memory',
        content: result.memory.content,
        priority: result.score,
        insertedAt: Date.now(),
        sourceMemoryId: result.memory.id,
      });
      focusTokens += contentTokens;
      focusCount++;
    } else if (result.score >= config.minInjectionScore && peripheryTokens + contentTokens <= config.peripheryCapacity) {
      // Medium-score result -> Periphery zone (summary form)
      const summary = await summarizeForPeriphery(result.memory.content);
      const summaryTokens = estimateTokens(summary);

      workingMemory.add({
        id: `retrieved_summary_${result.memory.id}`,
        tier: MemoryTier.WORKING,
        type: 'retrieved_memory',
        content: summary,
        priority: result.score * 0.8,
        insertedAt: Date.now(),
        sourceMemoryId: result.memory.id,
      });
      peripheryTokens += summaryTokens;
      peripheryCount++;
    } else {
      // Low-score result -> Only record reference
      referenceCount++;
    }
  }

  return {
    focusCount,
    peripheryCount,
    referenceCount,
    totalTokensUsed: focusTokens + peripheryTokens,
  };
}
```

---

## Advantages

1. **Multi-dimensional retrieval**: Fuses semantic, keyword, temporal, and importance dimensions, covering all query scenarios
2. **Query optimization**: Intent detection and query expansion significantly improve retrieval recall
3. **Intelligent weighting**: Intent-based weight recommendations + MemRL-style adaptive learning continuously optimize retrieval quality
4. **Multi-level caching**: Three-level cache (exact match + semantic match + prefetch) significantly reduces retrieval latency and cost
5. **Reranking**: Cross-encoder reranking provides more precise result ordering
6. **Diversity guarantee**: Deduplication and diversity mechanisms prevent results from concentrating on a single perspective
7. **Working Memory friendly**: Tiered injection with focus/periphery zones maximizes utilization of the limited context window

## Risks

1. **Retrieval latency**: Multi-dimensional parallel retrieval + reranking may increase latency
   - *Mitigation*: Multi-level caching, parallel execution, async prefetch
2. **Vector database performance**: Vector retrieval at large memory scale may become a bottleneck
   - *Mitigation*: HNSW indexing, sharding, hot/cold data separation
3. **Weight learning convergence**: MemRL-style online learning may converge slowly or unstably
   - *Mitigation*: Conservative learning rate + intent-grouped learning + periodic offline training
4. **Cross-encoder cost**: The reranking step requires additional LLM calls
   - *Mitigation*: Only rerank top-K results; use lightweight cross-encoders
5. **Cache consistency**: After memory updates, cache may return stale results
   - *Mitigation*: Short TTL + proactive cache invalidation on memory updates

## Pending Decisions

1. **Vector database selection**: FAISS (self-hosted, flexible) vs Pinecone/Weaviate (managed, hassle-free) vs Qdrant (open-source, feature-rich)?
2. **Embedding model selection**: OpenAI text-embedding-3 vs open-source models (BGE/E5) vs fine-tuned models?
3. **Cross-encoder selection**: Use LLM as cross-encoder vs dedicated models (Cohere Rerank/bge-reranker)?
4. **Fusion algorithm selection**: RRF vs weighted score fusion vs Learning-to-Rank (LTR)? Which for the initial version?
5. **Cache TTL strategy**: How to set cache TTL for different query types?
6. **Prefetch trigger timing**: Prefetch when Pipeline step starts vs prefetch on query miss vs both?
7. **Retrieval strategy learning cold start**: How to determine initial weights? Is a pre-training phase needed?
8. **Archived memory retrieval**: Should archived memories be included in retrieval scope? How to control retrieval latency?
