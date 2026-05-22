# Mantle — The Artifact Home

> The mantle is the stone shelf above the hearth — where what has been created is displayed, preserved, and cherished.

---

## What is Mantle?

Mantle is the first-class home for all artifacts produced by the Heya ecosystem. It is not a file system, not a database, not a version control system — it is an **artifact-native storage layer** that understands the lineage, context, and lifecycle of every creation.

```
Traditional Systems:
  File System → Flat files, no context, no lineage
  Git → Code-centric, no semantic understanding
  Database → Structured data, no creative context

Mantle:
  Artifact-Native Storage → Every artifact knows where it came from,
  what components created it, what decisions shaped it,
  and what it can evolve into
```

### Key Insight

Artifacts are not just outputs — they are **living records of the creative process**. A book chapter is not just text; it is the result of specific components interacting, specific decisions being made, and specific human feedback being given. Mantle preserves this entire context.

---

## Core Concepts

### 1. Artifact

The fundamental unit of Mantle. An artifact is any output from the ecosystem.

```typescript
interface Artifact {
  id: ArtifactId;
  type: ArtifactType;
  name: string;
  description: string;

  // Content
  content: ArtifactContent;
  format: ArtifactFormat;

  // Lineage
  lineage: ArtifactLineage;

  // Lifecycle
  status: ArtifactStatus;
  version: SemVer;
  createdAt: Date;
  updatedAt: Date;

  // Relationships
  derivedFrom: ArtifactId[];     // What this was based on
  derivedInto: ArtifactId[];     // What was created from this
  relatedTo: ArtifactId[];       // Semantic connections

  // Metadata
  tags: string[];
  quality: QualityScore;
  humanApproval?: ApprovalRecord;
}

type ArtifactType =
  | 'document'      // Text, books, articles
  | 'code'          // Software, scripts, components
  | 'design'        // Visual designs, UI/UX
  | 'data'          // Datasets, analysis results
  | 'media'         // Images, audio, video
  | 'strategy'      // Plans, roadmaps, decisions
  | 'knowledge'     // Insights, patterns, learnings
  | 'composite';    // Multi-type artifacts

type ArtifactStatus =
  | 'draft'         // In progress, not yet reviewed
  | 'approved'      // Human-approved
  | 'published'     // Shared (via Grove)
  | 'archived'      // No longer active
  | 'deprecated';   // Superseded by newer version
```

### 2. ArtifactLineage

Full provenance of an artifact — trace any output back to its origins.

```typescript
interface ArtifactLineage {
  // Hearth context
  hearthSpaceId: SpaceId;
  componentsUsed: ComponentId[];
  interactionsInvolved: InteractionId[];

  // Decision trail
  decisions: DecisionRecord[];
  checkpoints: CheckpointRecord[];

  // Human involvement
  humanFeedback: FeedbackRecord[];
  approvalChain: ApprovalRecord[];

  // Evolution
  parentArtifact?: ArtifactId;
  evolutionPath: EvolutionStep[];
}

interface DecisionRecord {
  id: string;
  decision: string;
  rationale: string;
  madeBy: 'agent' | 'hey' | 'user';
  timestamp: Date;
  context: string;
}

interface EvolutionStep {
  from: ArtifactId;
  to: ArtifactId;
  type: 'mutation' | 'combination' | 'refinement' | 'fork';
  description: string;
}
```

### 3. ArtifactCollection

Logical groupings of related artifacts.

```typescript
interface ArtifactCollection {
  id: CollectionId;
  name: string;
  description: string;
  artifacts: ArtifactId[];
  structure: CollectionStructure;
  createdAt: Date;
  updatedAt: Date;
}

type CollectionStructure =
  | 'flat'           // Simple list
  | 'hierarchical'   // Tree structure (e.g., book chapters)
  | 'graph'          // Network of related artifacts
  | 'timeline'       // Chronological ordering
  | 'project';       // Project-scoped collection
```

---

## Architecture

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                              MANTLE                                          │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                              │
│  ┌─────────────────────────────────────────────────────────────────────┐    │
│  │                        ARTIFACT STORE                                │    │
│  │  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌──────────┐           │    │
│  │  │ Documents│  │   Code   │  │ Designs  │  │  Media   │  ...      │    │
│  │  └──────────┘  └──────────┘  └──────────┘  └──────────┘           │    │
│  └─────────────────────────────────────────────────────────────────────┘    │
│                                                                              │
│  ┌─────────────────────────────────────────────────────────────────────┐    │
│  │                      LINEAGE ENGINE                                  │    │
│  │  Provenance Tracking → Decision Trail → Evolution Graph              │    │
│  └─────────────────────────────────────────────────────────────────────┘    │
│                                                                              │
│  ┌─────────────────────────────────────────────────────────────────────┐    │
│  │                      COLLECTION MANAGER                              │    │
│  │  Grouping → Hierarchy → Cross-Reference → Search                     │    │
│  └─────────────────────────────────────────────────────────────────────┘    │
│                                                                              │
│  ┌─────────────────────────────────────────────────────────────────────┐    │
│  │                      VERSION CONTROL                                 │    │
│  │  Branching → Merging → Diffing → Rollback                            │    │
│  └─────────────────────────────────────────────────────────────────────┘    │
│                                                                              │
│  ┌─────────────────────────────────────────────────────────────────────┐    │
│  │                      QUALITY GATE                                    │    │
│  │  Verification Status → Human Approval → Quality Score                 │    │
│  └─────────────────────────────────────────────────────────────────────┘    │
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## Core Operations

### Ingestion

```typescript
interface MantleIngestion {
  // Receive artifact from Hearth
  ingest(artifact: ArtifactDraft, lineage: ArtifactLineage): Artifact;

  // Batch ingestion (e.g., a complete project output)
  ingestBatch(artifacts: ArtifactDraft[], lineage: BatchLineage): ArtifactCollection;
}
```

### Retrieval

```typescript
interface MantleRetrieval {
  // Get artifact by ID
  get(id: ArtifactId): Artifact;

  // Search
  search(query: ArtifactQuery): Artifact[];

  // Browse collections
  browseCollection(collectionId: CollectionId): CollectionView;

  // Trace lineage
  getLineage(artifactId: ArtifactId): ArtifactLineage;
  getEvolutionGraph(artifactId: ArtifactId): EvolutionGraph;
}
```

### Evolution

```typescript
interface MantleEvolution {
  // Create new version
  evolve(artifactId: ArtifactId, changes: ArtifactChanges): Artifact;

  // Fork artifact
  fork(artifactId: ArtifactId, newName?: string): Artifact;

  // Combine artifacts
  combine(sourceIds: ArtifactId[], strategy: CombineStrategy): Artifact;

  // Compare versions
  diff(versionA: ArtifactId, versionB: ArtifactId): ArtifactDiff;
}
```

---

## Relationship to Other Components

```
Hearth ──produces──► Mantle
  │                     │
  │                     ├──► Veil (renders artifacts for display)
  │                     ├──► Grove (shares artifacts with others)
  │                     └──► Ember (seeds can reference artifacts)
  │
Hey ──curates──► Mantle
  │                │
  │                └──► Hey can organize, tag, and present artifacts
```

**Key Principle**: Mantle is a **receiver**, not a **creator**. It does not modify artifacts — it stores, organizes, and presents them. All creation happens in Hearth.

---

## Design Principles

1. **Lineage First**: Every artifact carries its full history. No orphaned outputs.

2. **Format Agnostic**: Mantle stores any artifact type. It does not interpret content — it preserves it.

3. **Evolution Native**: Artifacts are not static files. They can branch, merge, and evolve like living documents.

4. **Quality Tracked**: Every artifact has a quality score and approval status. Nothing is "done" until verified.

5. **Collection Aware**: Artifacts naturally group into projects, series, and collections. Mantle supports this natively.

---

## Storage Model

```
Mantle Storage
├── Artifacts/
│   ├── {artifact-id}/
│   │   ├── content          (raw content, format-specific)
│   │   ├── metadata.json    (type, status, version, tags)
│   │   ├── lineage.json     (full provenance)
│   │   └── versions/
│   │       ├── v1.0.0/
│   │       ├── v1.1.0/
│   │       └── ...
│
├── Collections/
│   ├── {collection-id}/
│   │   ├── manifest.json    (name, structure, artifact refs)
│   │   └── ...
│
└── Index/
    ├── lineage-graph        (artifact relationships)
    ├── evolution-graph      (version history)
    └── search-index         (full-text + semantic)
```

---

## MVP Scope

For the initial implementation:

- [x] Artifact storage and retrieval
- [x] Basic lineage tracking (originating Hearth space, components used)
- [x] Collection support (flat and hierarchical)
- [x] Version control (branch, compare, rollback)
- [ ] Full decision trail (post-MVP)
- [ ] Semantic search (post-MVP)
- [ ] Cross-artifact evolution graph (post-MVP)

---

*Last Updated: 2026-05-22*
*Status: Core Design Defined*
*See also: [ADR-008](../../DECISIONS/008-four-complementary-components.md)*
