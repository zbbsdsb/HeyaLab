# Grove — The Shared Space

> A grove is a gathering place around the hearth — where multiple fires can be seen, where stories are shared, where community forms.

---

## What is Grove?

Grove is the collaboration layer of the Heya ecosystem. It enables multiple users (and their respective Heys) to co-create, share artifacts, discover inspiration, and build upon each other's work. Grove connects individual ecosystems into a **creative community**.

```
Traditional Systems:
  Git Fork/PR → Code-centric, formal, high friction
  Google Docs → Real-time but flat, no creative context
  Social Media → Sharing without creation, no depth

Grove:
  Creative Community → Share artifacts with full lineage,
  co-create in shared Hearth spaces,
  discover and learn from others' creative processes.
```

### Key Insight

Creation is not solitary. The best ideas emerge from the collision of different perspectives. Grove extends the single-user Hearth ecosystem into a multi-user creative community — without compromising the boundary principle or the emergent nature of creation.

---

## Core Concepts

### 1. Presence

A user's identity and their Hey within Grove.

```typescript
interface Presence {
  userId: UserId;
  displayName: string;
  avatar?: Avatar;

  // Their Hey
  hey: {
    name: string;
    personality: string;
    capabilities: string[];
  };

  // Their ecosystem
  publicArtifacts: number;
  sharedSpaces: number;
  reputation: ReputationScore;

  // Status
  status: 'active' | 'away' | 'offline';
  lastSeen: Date;
}
```

### 2. Shared Space

A collaborative environment where multiple users co-create.

```typescript
interface SharedSpace {
  id: SpaceId;
  name: string;
  description: string;

  // Ownership
  owner: UserId;
  members: SpaceMember[];
  membershipPolicy: MembershipPolicy;

  // Content
  sharedMantle: CollectionId;       // Shared artifact collection
  hearthInstances: HearthInstance[]; // Active Hearth spaces
  sharedEmbers: SeedId[];           // Shared creative seeds

  // Governance
  permissions: SpacePermissions;
  guidelines: SpaceGuidelines;

  // Metadata
  createdAt: Date;
  updatedAt: Date;
  activityLevel: number;
}

interface SpaceMember {
  userId: UserId;
  role: SpaceRole;
  joinedAt: Date;
  contributionScore: number;
}

type SpaceRole =
  | 'owner'         // Full control
  | 'admin'         // Manage members and settings
  | 'creator'       // Can create and modify artifacts
  | 'contributor'   // Can contribute to existing work
  | 'observer';     // Read-only access

type MembershipPolicy =
  | 'invite_only'   // Owner/admin invites
  | 'request'       // Users can request to join
  | 'open'          // Anyone can join
  | 'token';        // Join via shared token/link
```

### 3. Artifact Sharing

How artifacts flow between individual ecosystems and shared spaces.

```typescript
interface ArtifactSharing {
  // Share to Grove
  share(artifactId: ArtifactId, target: ShareTarget, options: ShareOptions): SharedArtifact;

  // Discover
  discover(query: DiscoveryQuery): DiscoveryResult[];

  // Interact with shared artifacts
  fork(artifactId: ArtifactId): Artifact;          // Create personal copy
  reference(artifactId: ArtifactId): Reference;     // Link without copying
  buildUpon(artifactId: ArtifactId): Derivation;    // Create derivative work
  collaborate(artifactId: ArtifactId): CollabSpace; // Open co-editing space
}

interface ShareTarget {
  type: 'user' | 'space' | 'public' | 'grove';
  id?: string;
}

interface ShareOptions {
  permissions: SharePermission;
  lineageVisibility: 'full' | 'partial' | 'none';  // How much provenance to show
  allowDerivation: boolean;
  allowFork: boolean;
  expiration?: Date;
}

type SharePermission =
  | 'view'          // Can see
  | 'reference'     // Can link to
  | 'derive'        // Can build upon
  | 'remix'         // Can modify and share
  | 'co_edit';      // Can edit in place
```

### 4. Discovery

Finding inspiration and learning from others.

```typescript
interface Discovery {
  // Explore
  explore(filters: DiscoveryFilter): ArtifactGallery;
  trending(): TrendingArtifacts;
  recommended(userId: UserId): RecommendedContent;

  // Learn
  inspectCreationProcess(artifactId: ArtifactId): CreationStory;
  analyzeEvolution(artifactId: ArtifactId): EvolutionStory;

  // Connect
  findSimilarCreators(userId: UserId): Creator[];
  findCollaborators(interest: string[]): Creator[];
}

interface CreationStory {
  artifact: Artifact;
  seed?: Seed;                    // Original Ember seed
  hearthSpace: SpaceId;           // Where it was created
  componentJourney: Component[];  // Components involved
  decisionHighlights: Decision[]; // Key decisions
  humanTouchpoints: HumanInput[]; // Where the human guided
  timeline: CreationTimeline;     // Step-by-step creation
}
```

---

## Architecture

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                               GROVE                                          │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                              │
│  ┌─────────────────────────────────────────────────────────────────────┐    │
│  │                      IDENTITY LAYER                                  │    │
│  │  User Profiles → Hey Profiles → Reputation → Presence               │    │
│  └─────────────────────────────────────────────────────────────────────┘    │
│                                                                              │
│  ┌─────────────────────────────────────────────────────────────────────┐    │
│  │                      SHARED SPACES                                   │    │
│  │  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌──────────┐           │    │
│  │  │  Team A  │  │ Project B│  │ Community│  │  Open    │  ...      │    │
│  │  │  Space   │  │  Space   │  │  Space   │  │  Space   │           │    │
│  │  └──────────┘  └──────────┘  └──────────┘  └──────────┘           │    │
│  │                                                                      │    │
│  │  Each space has: shared Mantle, shared Ember, collaborative Hearth   │    │
│  └─────────────────────────────────────────────────────────────────────┘    │
│                                                                              │
│  ┌─────────────────────────────────────────────────────────────────────┐    │
│  │                      SHARING ENGINE                                  │    │
│  │  Share → Fork → Reference → Derive → Collaborate                    │    │
│  │  Permission Management → Lineage Preservation → Attribution          │    │
│  └─────────────────────────────────────────────────────────────────────┘    │
│                                                                              │
│  ┌─────────────────────────────────────────────────────────────────────┐    │
│  │                      DISCOVERY ENGINE                                │    │
│  │  Explore → Trending → Recommended → Similar Creators                │    │
│  │  Creation Stories → Evolution Analysis → Learning                    │    │
│  └─────────────────────────────────────────────────────────────────────┘    │
│                                                                              │
│  ┌─────────────────────────────────────────────────────────────────────┐    │
│  │                      GOVERNANCE                                      │    │
│  │  Permissions → Guidelines → Moderation → Dispute Resolution          │    │
│  └─────────────────────────────────────────────────────────────────────┘    │
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## Collaborative Hearth

When multiple users share a Hearth space:

```typescript
interface CollaborativeHearth {
  // Multi-user Hearth session
  space: SharedSpace;
  participants: Participant[];

  // Each participant's interface
  interface Participant {
    user: Presence;
    hey: HeyInstance;
    permissions: ParticipantPermission;
    focus: FocusArea;      // What they're working on
    cursor?: CursorPosition; // For shared Veil views
  }

  // Coordination
  coordination: {
    // Real-time awareness
    presence: PresenceStream;
    activity: ActivityFeed;

    // Conflict resolution
    conflictPolicy: ConflictPolicy;  // 'first_come' | 'merge' | 'negotiate'

    // Checkpoint coordination
    checkpointPolicy: CheckpointPolicy;  // Who approves what
  };
}

type ConflictPolicy =
  | 'first_come'     // First to modify wins
  | 'merge'          // Automatic merge when possible
  | 'negotiate'      // Heys negotiate on behalf of users
  | 'human_resolve'; // Escalate to humans
```

---

## Relationship to Other Components

```
Grove ──connects──► Multiple Users (each with their own Hey)
Grove ──shares──► Mantle artifacts (with permissions and lineage)
Grove ──shares──► Ember seeds (inspiration exchange)
Grove ──hosts──► Collaborative Hearth spaces (multi-user creation)
Grove ──exposes──► Veil (community dashboards, discovery views)

Grove does NOT modify:
  - How Hearth works internally (boundary principle preserved)
  - How Hey's memory works (companion autonomy preserved)
  - How components evolve (natural selection preserved)
```

**Key Principle**: Grove provides the **social context** for creation. It connects ecosystems without compromising their internal integrity. A shared Hearth space still follows the same boundary principle — it just has multiple human guides and multiple Heys coordinating.

---

## Design Principles

1. **Respect Autonomy**: Every user's ecosystem is their own. Grove facilitates sharing, never forces it.

2. **Preserve Lineage**: When an artifact is shared, forked, or derived, its lineage is preserved. Attribution is automatic, not optional.

3. **Creation Stories**: The most valuable thing to share is not just the artifact, but the story of how it was created. Grove makes creation processes visible and learnable.

4. **Permission Granularity**: Sharing is not binary. Users control exactly what others can do — view, reference, derive, remix, or co-edit.

5. **Community Scales**: Grove works for a team of two and a community of thousands. The same primitives (shared spaces, permissions, discovery) scale.

---

## Privacy Model

```typescript
interface PrivacyModel {
  // Levels of visibility
  levels: {
    private: {
      description: 'Only you can see';
      scope: 'personal';
    };
    shared: {
      description: 'Specific people you invite';
      scope: 'invited_users';
    };
    space: {
      description: 'Members of a shared space';
      scope: 'space_members';
    };
    public: {
      description: 'Anyone in Grove can discover';
      scope: 'grove_wide';
    };
  };

  // What's shared at each level
  visibility: {
    private: ['artifact_content', 'full_lineage', 'seed'];
    shared: ['artifact_content', 'partial_lineage', 'attribution'];
    space: ['artifact_content', 'space_lineage', 'collaboration_context'];
    public: ['artifact_preview', 'creation_story', 'attribution'];
  };
}
```

---

## MVP Scope

For the initial implementation:

- [ ] Basic user profiles and presence
- [ ] Simple shared spaces (invite-only)
- [ ] Artifact sharing (view and fork)
- [ ] Basic discovery (browse public artifacts)
- [ ] Collaborative Hearth (post-MVP)
- [ ] Creation stories (post-MVP)
- [ ] Reputation system (post-MVP)
- [ ] Advanced discovery/recommendation (post-MVP)

**Note**: Grove is the lowest priority component for MVP. The initial product is single-user. Grove becomes essential when the community grows.

---

*Last Updated: 2026-05-22*
*Status: Core Design Defined*
*See also: [ADR-008](../../DECISIONS/008-four-complementary-components.md)*
