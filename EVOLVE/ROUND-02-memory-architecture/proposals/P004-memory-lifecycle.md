# Proposal P004: Memory Lifecycle Management

> **Status**: Draft
> **Proposer**: AI Assistant
> **Date**: 2026-05-16
> **Related**: ROUND-002 Brainstorm, RESEARCH/SYNTHESIS.md Section 3

---

## Summary

Design a complete lifecycle management mechanism for Heya's four-layer memory system (Working / Episodic / Semantic / Artifact). Memories progress through six stages from creation: creation, indexing, retrieval (cyclic), decay, forgetting, and archiving. Each memory layer has different lifecycle strategies: Working Memory is managed at second/minute granularity, Episodic Memory at day/week granularity, Semantic Memory at month granularity, and Artifact Memory is permanently retained. The system employs a multi-dimensional decay model (time x access frequency x relevance x emotional valence), supports both active and passive forgetting, and preserves the "existence trace" of memories through progressive forgetting.

---

## Design

### Lifecycle Overview

```
Creation ──→ Indexing ──→ Retrieval (loop) ──→ Decay ──→ Forgetting/Archiving
  │                                      │
  │         ┌────────────────────────────┘
  │         │  (can compress before forgetting)
  │         ▼
  │     Compression ──→ New memory (summary/knowledge)
  │
  └──→ Semantic distillation (Episodic → Semantic)
```

### Core Data Structures

```typescript
// ============================================
// Base type definitions
// ============================================

/** Memory tier */
enum MemoryTier {
  WORKING = 'working',
  EPISODIC = 'episodic',
  SEMANTIC = 'semantic',
  ARTIFACT = 'artifact',
}

/** Memory status */
enum MemoryStatus {
  ACTIVE = 'active',       // Active, retrievable
  DECAYING = 'decaying',   // Decaying, reduced retrieval priority
  ARCHIVED = 'archived',   // Archived, cold storage
  DELETED = 'deleted',     // Deleted
}

/** Emotional valence */
enum EmotionalValence {
  POSITIVE = 1,    // Positive (success, praise)
  NEUTRAL = 0,     // Neutral
  NEGATIVE = -1,   // Negative (failure, criticism)
}

/** Memory source type */
enum MemorySourceType {
  PIPELINE_STEP = 'pipeline_step',   // Pipeline step output
  HUMAN_FEEDBACK = 'human_feedback', // Human feedback
  SYSTEM_INFERENCE = 'system_inference', // System inference
  COMPRESSION = 'compression',       // Compression product
  EXTERNAL = 'external',             // External import
}

// ============================================
// Core memory entry
// ============================================

/** Memory entry base class */
interface BaseMemoryEntry {
  id: string;
  tier: MemoryTier;
  status: MemoryStatus;
  content: string;
  embedding: number[];          // Semantic vector
  importance: number;           // 0-1 base importance
  effectiveScore: number;       // Current effective score (after decay)
  tags: string[];               // Tags
  provenance: MemoryProvenance; // Source information
  createdAt: number;            // Creation timestamp (ms)
  updatedAt: number;            // Update timestamp (ms)
  lastAccessedAt: number;       // Last access timestamp
  accessCount: number;          // Number of times retrieved
  version: number;              // Version number (for optimistic concurrency control)
}

/** Memory provenance information */
interface MemoryProvenance {
  sourceType: MemorySourceType;
  factoryId: string;
  workOrderId?: string;
  pipelineStepId?: string;
  sourceMemoryIds?: string[];   // If a compression/distillation product, record source memories
  humanAuthorId?: string;       // If directly input by human
}

/** Episodic Memory extension */
interface EpisodicMemoryEntry extends BaseMemoryEntry {
  tier: MemoryTier.EPISODIC;
  emotionalValence: EmotionalValence;
  causalLinks: CausalLink[];    // Causal links
  compressionLevel: number;     // 0=original, 1=summary, 2=existence trace
  originalContent?: string;     // Original content before compression
}

/** Semantic Memory extension */
interface SemanticMemoryEntry extends BaseMemoryEntry {
  tier: MemoryTier.SEMANTIC;
  knowledgeType: KnowledgeType;
  confidence: number;           // 0-1 confidence level
  sourceEpisodes: string[];     // Episodic Memory IDs supporting this knowledge
  lastValidatedAt: number;      // Last validation time
  contradictions: string[];     // Semantic Memory IDs that contradict this
  contextConditions: string;    // Context conditions under which the knowledge holds
}

/** Artifact Memory extension */
interface ArtifactMemoryEntry extends BaseMemoryEntry {
  tier: MemoryTier.ARTIFACT;
  artifactType: string;         // Artifact type (code/design/document/...)
  artifactPath: string;         // Artifact storage path
  artifactHash: string;         // Artifact content hash
  verificationResult: VerificationResult;
  dependencies: string[];       // Dependent Artifact Memory IDs
  variants: ArtifactVariant[];  // Artifact variants
}

/** Working Memory entry (lightweight) */
interface WorkingMemoryEntry {
  id: string;
  tier: MemoryTier.WORKING;
  type: 'task_context' | 'retrieved_memory' | 'human_instruction' | 'pipeline_state';
  content: string;
  priority: number;             // 0-1 priority
  insertedAt: number;
  expiresAt?: number;           // Optional expiration time
  sourceMemoryId?: string;      // If retrieved from another layer
}

// ============================================
// Causal links
// ============================================

interface CausalLink {
  targetMemoryId: string;
  relationType: 'cause' | 'effect' | 'prerequisite' | 'contradiction';
  strength: number;             // 0-1 relationship strength
}

// ============================================
// Knowledge types
// ============================================

enum KnowledgeType {
  PATTERN = 'pattern',           // Observed pattern
  PREFERENCE = 'preference',     // User preference
  CONSTRAINT = 'constraint',     // Constraint
  HEURISTIC = 'heuristic',       // Heuristic rule
  RULE = 'rule',                 // Deterministic rule
  ANOMALY = 'anomaly',           // Anomaly detection
}

// ============================================
// Verification result
// ============================================

interface VerificationResult {
  passed: boolean;
  checks: {
    name: string;
    passed: boolean;
    score?: number;
    details?: string;
  }[];
  humanApproved: boolean;
  humanFeedback?: string;
}

// ============================================
// Artifact variant
// ============================================

interface ArtifactVariant {
  variantId: string;
  description: string;
  artifactPath: string;
  effectiveness: number;        // 0-1 effectiveness score
  context: string;              // Applicable context
}
```

### Stage 1: Memory Creation

```typescript
// ============================================
// Memory creation service
// ============================================

interface IMemoryCreationService {
  /** Create Episodic Memory */
  createEpisodic(params: CreateEpisodicParams): Promise<EpisodicMemoryEntry>;

  /** Create Semantic Memory (distilled from Episodic) */
  createSemantic(params: CreateSemanticParams): Promise<SemanticMemoryEntry>;

  /** Create Artifact Memory */
  createArtifact(params: CreateArtifactParams): Promise<ArtifactMemoryEntry>;

  /** Add Working Memory entry */
  addToWorking(entry: WorkingMemoryEntry): void;

  /** Batch creation */
  batchCreate(entries: CreateMemoryBatch[]): Promise<MemoryCreationResult>;
}

interface CreateEpisodicParams {
  content: string;
  tags?: string[];
  emotionalValence: EmotionalValence;
  importance?: number;           // Optional, auto-evaluated if not provided
  provenance: MemoryProvenance;
  causalLinks?: CausalLink[];
}

interface CreateSemanticParams {
  content: string;
  knowledgeType: KnowledgeType;
  sourceEpisodes: string[];      // Must provide supporting evidence
  contextConditions?: string;
  confidence?: number;           // Optional, calculated from evidence if not provided
}

interface CreateArtifactParams {
  artifactType: string;
  artifactPath: string;
  artifactHash: string;
  verificationResult: VerificationResult;
  dependencies?: string[];
  content: string;               // Summary description of the artifact
}

type CreateMemoryBatch =
  | { type: 'episodic'; params: CreateEpisodicParams }
  | { type: 'semantic'; params: CreateSemanticParams }
  | { type: 'artifact'; params: CreateArtifactParams };

interface MemoryCreationResult {
  created: string[];             // Successfully created memory IDs
  failed: { params: CreateMemoryBatch; error: string }[];
}

// ============================================
// Importance auto-evaluator
// ============================================

interface IImportanceEvaluator {
  /** Evaluate the base importance of a memory */
  evaluate(params: {
    content: string;
    sourceType: MemorySourceType;
    emotionalValence?: EmotionalValence;
    context: {
      isHumanDirectInput: boolean;
      isFailureRelated: boolean;
      isDecisionRelated: boolean;
      workOrderPriority?: number;
    };
  }): number; // 0-1
}

// Importance evaluation pseudocode
function evaluateImportance(params: EvaluateParams): number {
  let score = 0.5; // Baseline

  // Source weighting
  if (params.context.isHumanDirectInput) score += 0.2;
  if (params.sourceType === MemorySourceType.HUMAN_FEEDBACK) score += 0.15;

  // Emotional weighting (negative memories are more important -- lessons should not be forgotten)
  if (params.context.isFailureRelated) score += 0.2;
  if (params.emotionalValence === EmotionalValence.NEGATIVE) score += 0.1;

  // Decision weighting
  if (params.context.isDecisionRelated) score += 0.15;

  // WorkOrder priority weighting
  if (params.context.workOrderPriority) {
    score += params.context.workOrderPriority * 0.1;
  }

  return Math.min(1.0, Math.max(0.0, score));
}
```

### Stage 2: Memory Indexing

```typescript
// ============================================
// Multi-dimensional index manager
// ============================================

interface IMemoryIndexManager {
  /** Build all dimension indexes for a memory entry */
  index(entry: BaseMemoryEntry): Promise<void>;

  /** Batch index building */
  batchIndex(entries: BaseMemoryEntry[]): Promise<void>;

  /** Remove index */
  removeIndex(memoryId: string, tier: MemoryTier): Promise<void>;

  /** Update index (when memory content changes) */
  updateIndex(entry: BaseMemoryEntry): Promise<void>;
}

/** Index configuration */
interface IndexConfig {
  semantic: {
    enabled: boolean;
    vectorDimension: number;     // Embedding dimension
    indexType: 'HNSW' | 'IVF' | 'FLAT';
    similarityMetric: 'cosine' | 'euclidean' | 'dot';
  };
  temporal: {
    enabled: boolean;
    granularity: 'second' | 'minute' | 'hour' | 'day';
  };
  tag: {
    enabled: boolean;
  };
  importance: {
    enabled: boolean;
  };
  causal: {
    enabled: boolean;            // Causal chain index (optional)
  };
}
```

### Stage 3: Memory Retrieval

```typescript
// ============================================
// Retrieval interface (detailed design in P006)
// ============================================

interface IMemoryRetrievalService {
  /** Hybrid retrieval */
  retrieve(query: MemoryQuery): Promise<MemoryRetrievalResult[]>;
}

interface MemoryQuery {
  text: string;                  // Query text
  tier?: MemoryTier[];           // Restrict memory tiers
  tags?: string[];               // Tag filtering
  timeRange?: { start: number; end: number }; // Time range
  maxResults?: number;           // Maximum results to return
  minScore?: number;             // Minimum score threshold
  weights?: RetrievalWeights;    // Retrieval weights
  factoryId?: string;            // Restrict Factory
}

interface RetrievalWeights {
  semantic: number;              // Semantic similarity weight
  keyword: number;               // Keyword matching weight
  temporal: number;              // Temporal relevance weight
  importance: number;            // Importance weight
}

interface MemoryRetrievalResult {
  memory: BaseMemoryEntry;
  score: number;                 // Composite score
  matchDetails: {
    semanticScore: number;
    keywordScore: number;
    temporalScore: number;
    importanceScore: number;
  };
}
```

### Stage 4: Memory Decay

```typescript
// ============================================
// Decay engine
// ============================================

interface IDecayEngine {
  /** Calculate the current effective score of a memory */
  calculateEffectiveScore(entry: BaseMemoryEntry): number;

  /** Batch update effective scores for all memories */
  updateAllScores(tier: MemoryTier): Promise<void>;

  /** Get decay curve configuration */
  getDecayConfig(tier: MemoryTier): DecayConfig;
}

/** Decay configuration */
interface DecayConfig {
  /** Half-life (ms) -- time for importance to halve */
  halfLife: number;

  /** Decay function type */
  decayFunction: 'exponential' | 'logarithmic' | 'step';

  /** Impact of emotional valence on decay rate */
  emotionalModifier: {
    [EmotionalValence.POSITIVE]: number;  // Multiplier, >1 means faster decay
    [EmotionalValence.NEUTRAL]: number;
    [EmotionalValence.NEGATIVE]: number;  // <1 means slower decay
  };

  /** Access frequency boost */
  accessBoost: number;           // Effective score increase per access

  /** Minimum effective score (below this, enters DECAYING status) */
  decayThreshold: number;

  /** Archive threshold (below this, enters ARCHIVED status) */
  archiveThreshold: number;
}

// ============================================
// Decay configuration per tier
// ============================================

const DECAY_CONFIGS: Record<MemoryTier, DecayConfig> = {
  [MemoryTier.WORKING]: {
    halfLife: 5 * 60 * 1000,              // 5 minutes
    decayFunction: 'exponential',
    emotionalModifier: { 1: 1.0, 0: 1.0, '-1': 0.8 },
    accessBoost: 0.05,
    decayThreshold: 0.3,
    archiveThreshold: 0.1,
  },

  [MemoryTier.EPISODIC]: {
    halfLife: 7 * 24 * 60 * 60 * 1000,    // 7 days
    decayFunction: 'exponential',
    emotionalModifier: {
      1: 1.2,    // Positive memories decay slightly faster
      0: 1.0,
      '-1': 0.5, // Negative memories decay very slowly (lessons)
    },
    accessBoost: 0.02,
    decayThreshold: 0.2,
    archiveThreshold: 0.05,
  },

  [MemoryTier.SEMANTIC]: {
    halfLife: 30 * 24 * 60 * 60 * 1000,   // 30 days
    decayFunction: 'logarithmic',          // Logarithmic decay, more gradual
    emotionalModifier: { 1: 1.0, 0: 1.0, '-1': 1.0 },
    accessBoost: 0.01,
    decayThreshold: 0.15,
    archiveThreshold: 0.02,
  },

  [MemoryTier.ARTIFACT]: {
    halfLife: Infinity,                    // No decay
    decayFunction: 'step',                 // Step function (no decay)
    emotionalModifier: { 1: 1.0, 0: 1.0, '-1': 1.0 },
    accessBoost: 0,
    decayThreshold: 0,
    archiveThreshold: 0,
  },
};

// ============================================
// Effective score calculation
// ============================================

function calculateEffectiveScore(
  entry: BaseMemoryEntry,
  config: DecayConfig,
  now: number
): number {
  const age = now - entry.createdAt;

  // 1. Time decay factor
  let timeFactor: number;
  switch (config.decayFunction) {
    case 'exponential':
      timeFactor = Math.pow(0.5, age / config.halfLife);
      break;
    case 'logarithmic':
      timeFactor = 1 / (1 + Math.log(1 + age / config.halfLife));
      break;
    case 'step':
      timeFactor = 1.0;
      break;
  }

  // 2. Emotional modifier (Episodic Memory only)
  let emotionalFactor = 1.0;
  if ('emotionalValence' in entry) {
    const valence = (entry as EpisodicMemoryEntry).emotionalValence;
    emotionalFactor = config.emotionalModifier[valence];
  }

  // 3. Access frequency boost
  const accessFactor = 1 + Math.min(entry.accessCount * config.accessBoost, 0.5);

  // 4. Composite calculation
  const effectiveScore = entry.importance * timeFactor * emotionalFactor * accessFactor;

  return Math.min(1.0, Math.max(0.0, effectiveScore));
}
```

### Stage 5: Forgetting

```typescript
// ============================================
// Forgetting manager
// ============================================

interface IForgettingManager {
  /** Passive forgetting: scan and process low-score memories */
  runPassiveForgetting(tier: MemoryTier): Promise<ForgettingReport>;

  /** Active forgetting: delete specified memories */
  activeForget(memoryIds: string[], reason: string): Promise<void>;

  /** Progressive forgetting: gradually reduce memory resolution */
  progressiveForget(memoryId: string): Promise<ProgressiveForgettingResult>;

  /** Check if a memory can be forgotten */
  canForget(memoryId: string): ForgetEligibility;
}

interface ForgettingReport {
  scanned: number;
  decayed: number;           // Moved to DECAYING status
  archived: number;          // Moved to ARCHIVED status
  deleted: number;           // Permanently deleted
  preserved: number;         // Preserved due to protection rules
}

interface ProgressiveForgettingResult {
  previousLevel: number;     // Previous compression level
  newLevel: number;          // New compression level
  contentBefore: string;
  contentAfter: string;
}

interface ForgetEligibility {
  canForget: boolean;
  reason?: string;           // If cannot forget, why
  protectionRules: string[]; // Hit protection rules
}

// ============================================
// Forgetting protection rules
// ============================================

const FORGET_PROTECTION_RULES = [
  {
    name: 'human_marked_important',
    check: (entry: BaseMemoryEntry) => entry.tags.includes('important:user'),
    reason: 'Memories marked as important by humans will not be forgotten',
  },
  {
    name: 'active_workorder_reference',
    check: async (entry: BaseMemoryEntry) => {
      // Check if any active WorkOrder references this memory
      return await hasActiveWorkOrderReference(entry.id);
    },
    reason: 'Memories referenced by active WorkOrders will not be forgotten',
  },
  {
    name: 'high_confidence_semantic',
    check: (entry: BaseMemoryEntry) =>
      entry.tier === MemoryTier.SEMANTIC &&
      (entry as SemanticMemoryEntry).confidence > 0.8,
    reason: 'High-confidence semantic memories will not be forgotten',
  },
  {
    name: 'recent_failure',
    check: (entry: BaseMemoryEntry) =>
      'emotionalValence' in entry &&
      (entry as EpisodicMemoryEntry).emotionalValence === EmotionalValence.NEGATIVE &&
      Date.now() - entry.createdAt < 30 * 24 * 60 * 60 * 1000, // Failure within 30 days
    reason: 'Failure records from the past 30 days will not be forgotten',
  },
  {
    name: 'sole_evidence_for_semantic',
    check: async (entry: BaseMemoryEntry) => {
      // Check if any Semantic Memory depends solely on this Episodic Memory
      return await isSoleEvidenceForSemantic(entry.id);
    },
    reason: 'Memories that are the sole evidence for a Semantic Memory will not be forgotten',
  },
];

// ============================================
// Progressive forgetting flow
// ============================================

async function progressiveForget(
  memoryId: string,
  entry: EpisodicMemoryEntry,
  compressionService: ICompressionService
): Promise<ProgressiveForgettingResult> {
  const previousLevel = entry.compressionLevel;

  switch (previousLevel) {
    case 0: // Complete memory -> Summary
      const summary = await compressionService.summarize(entry.content);
      entry.content = summary;
      entry.compressionLevel = 1;
      entry.originalContent = entry.content; // Preserve original content to archive
      break;

    case 1: // Summary -> Existence trace
      entry.content = `[Memory Summary] ${entry.content.substring(0, 50)}...`;
      entry.compressionLevel = 2;
      break;

    case 2: // Existence trace -> Archived
      entry.status = MemoryStatus.ARCHIVED;
      break;

    default:
      // Already archived, permanently delete
      entry.status = MemoryStatus.DELETED;
      break;
  }

  return {
    previousLevel,
    newLevel: entry.compressionLevel,
    contentBefore: entry.originalContent || '',
    contentAfter: entry.content,
  };
}
```

### Stage 6: Archiving

```typescript
// ============================================
// Archive manager
// ============================================

interface IArchiveManager {
  /** Archive memory (move from hot storage to cold storage) */
  archive(memoryId: string): Promise<void>;

  /** Batch archiving */
  batchArchive(memoryIds: string[]): Promise<ArchiveReport>;

  /** Restore from archive */
  restore(memoryId: string): Promise<BaseMemoryEntry>;

  /** Search archived memories */
  searchArchive(query: MemoryQuery): Promise<MemoryRetrievalResult[]>;

  /** Purge expired archives (permanently delete those past retention period) */
  purgeExpiredArchive(retentionDays: number): Promise<number>;
}

interface ArchiveReport {
  archived: number;
  failed: number;
  errors: { memoryId: string; error: string }[];
}

// ============================================
// Archive policy configuration
// ============================================

interface ArchivePolicy {
  /** Retention period per tier (days) */
  retentionDays: {
    [MemoryTier.EPISODIC]: number;
    [MemoryTier.SEMANTIC]: number;
    [MemoryTier.ARTIFACT]: number;
  };

  /** Archive storage backend */
  storageBackend: {
    hot: StorageBackendConfig;   // Hot storage (vector database)
    cold: StorageBackendConfig;  // Cold storage (object storage)
  };

  /** Whether to retain vector index for archived memories */
  keepVectorIndex: boolean;
}

interface StorageBackendConfig {
  type: 'vector_db' | 'object_storage' | 'file_system';
  connectionString?: string;
  bucket?: string;
  options?: Record<string, unknown>;
}

const DEFAULT_ARCHIVE_POLICY: ArchivePolicy = {
  retentionDays: {
    [MemoryTier.EPISODIC]: 90,    // Episodic archive retained for 90 days
    [MemoryTier.SEMANTIC]: 365,   // Semantic archive retained for 1 year
    [MemoryTier.ARTIFACT]: Infinity, // Artifact permanently retained
  },
  storageBackend: {
    hot: { type: 'vector_db' },
    cold: { type: 'object_storage' },
  },
  keepVectorIndex: true,          // Archived memories still searchable via vector retrieval
};
```

### Lifecycle Scheduler

```typescript
// ============================================
// Lifecycle scheduler
// ============================================

interface ILifecycleScheduler {
  /** Start background scheduling */
  start(): void;

  /** Stop scheduling */
  stop(): void;

  /** Manually trigger a full lifecycle check */
  runFullCycle(): Promise<LifecycleReport>;
}

interface LifecycleScheduleConfig {
  /** Decay score update frequency */
  decayUpdateInterval: string;    // Cron expression, e.g., "*/10 * * * *" (every 10 minutes)

  /** Passive forgetting scan frequency */
  passiveForgettingInterval: string; // "0 */6 * * *" (every 6 hours)

  /** Archive cleanup frequency */
  archiveCleanupInterval: string; // "0 2 * * *" (daily at 2 AM)

  /** Consistency check frequency */
  consistencyCheckInterval: string; // "0 0 * * 0" (every Sunday)
}

interface LifecycleReport {
  timestamp: number;
  decayUpdated: number;
  passiveForgotten: ForgettingReport;
  archived: number;
  restored: number;
  consistencyIssues: ConsistencyIssue[];
}

// ============================================
// Lifecycle events (for monitoring and auditing)
// ============================================

type LifecycleEvent =
  | { type: 'memory_created'; memoryId: string; tier: MemoryTier }
  | { type: 'memory_indexed'; memoryId: string; indexes: string[] }
  | { type: 'memory_accessed'; memoryId: string; queryId: string }
  | { type: 'memory_decayed'; memoryId: string; previousScore: number; newScore: number }
  | { type: 'memory_archived'; memoryId: string; reason: string }
  | { type: 'memory_restored'; memoryId: string }
  | { type: 'memory_deleted'; memoryId: string; reason: string }
  | { type: 'memory_compressed'; memoryId: string; level: number }
  | { type: 'consistency_issue'; issue: ConsistencyIssue };

interface ConsistencyIssue {
  type: 'contradiction' | 'orphan' | 'stale_reference';
  memoryIds: string[];
  description: string;
  severity: 'low' | 'medium' | 'high';
}
```

---

## Advantages

1. **Complete memory governance**: End-to-end management from creation to deletion, preventing unlimited memory growth from degrading the system
2. **Layered strategies**: Four memory layers each have different lifecycle strategies, aligning with the characteristics of different memory types in cognitive science
3. **Intelligent decay**: Multi-dimensional decay model (time x access x emotion x importance) is smarter than simple time-based decay
4. **Progressive forgetting**: Instead of sudden deletion, gradually reduces resolution, preserving the "existence trace" of memories
5. **Protection mechanisms**: Forgetting protection rules ensure critical memories (human-marked, actively referenced, high-confidence knowledge) are not mistakenly deleted
6. **Auditable**: All lifecycle events are recorded, supporting post-hoc review and debugging
7. **Configurable**: Decay rates, forgetting thresholds, and archiving policies can all be adjusted through configuration

## Risks

1. **Computational overhead**: Decay score updates require iterating over all memories, which may have performance issues at large scale
   - *Mitigation*: Incremental updates + batch processing + background scheduling
2. **Forgetting misjudgment**: May forget important memories when it should not
   - *Mitigation*: Conservative forgetting thresholds + rich protection rules + archive buffer
3. **Emotional modifier bias**: Negative memories decaying slower may make the system overly conservative
   - *Mitigation*: Set a maximum retention period for negative memories; after expiry, decay normally
4. **Configuration complexity**: Numerous decay parameters make tuning difficult
   - *Mitigation*: Provide reasonable default configurations + auto-tuning based on usage data

## Pending Decisions

1. **Working Memory management approach**: Fixed token limit or dynamic information density management?
2. **Whether to implement causal chain indexing in the initial version**: CausalLink has high engineering complexity; should it be deferred to a later version?
3. **Progressive forgetting compression levels**: Currently designed with 3 levels (complete -> summary -> trace); are more levels needed?
4. **Archive storage technology selection**: S3/MinIO/Azure Blob for object storage? Should vector indexes be retained after archiving?
5. **Auto-tuning of decay parameters**: Should we implement MemRL-inspired runtime reinforcement learning to automatically adjust decay parameters?
6. **Irreversibility of memory deletion**: Should archived memories be permanently deleted after the retention period? Is a "recycle bin" mechanism needed?
