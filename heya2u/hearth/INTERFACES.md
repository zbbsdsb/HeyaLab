# Interfaces

> APIs and protocols for users, Hey, and agents.

---

## Interface Architecture

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                         INTERFACE LAYERS                                     │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                              │
│  Layer 1: User Interface                                                     │
│  ├── Natural Language (primary)                                              │
│  ├── Visual (dashboards, visualizations)                                     │
│  └── Direct Manipulation (when needed)                                       │
│                                                                              │
│  Layer 2: Hey Interface                                                      │
│  ├── Intent Parsing                                                          │
│  ├── Context Management                                                      │
│  ├── Coordination                                                            │
│  └── Witness/Guide                                                           │
│                                                                              │
│  Layer 3: Agent Interface                                                    │
│  ├── Component Creation                                                      │
│  ├── Interaction                                                             │
│  ├── Evolution                                                               │
│  └── State Access                                                            │
│                                                                              │
│  Layer 4: System Interface                                                   │
│  ├── Boundary Enforcement                                                    │
│  ├── Resource Management                                                     │
│  ├── State Graph                                                             │
│  └── Event System                                                            │
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## User Interface

### Natural Language Interface

The primary way users interact with Hearth.

```typescript
interface UserInterface {
  // Input
  expressIntent(text: string): UserIntent;
  
  // Output
  presentResult(result: ExecutionResult): Presentation;
  
  // Feedback
  provideFeedback(feedback: UserFeedback): void;
  
  // Control
  pause(): void;
  resume(): void;
  cancel(): void;
  
  // Exploration
  exploreState(): StateExplorer;
  inspectComponent(id: ComponentId): ComponentInspector;
}

// Example interactions
const examples = {
  // Simple request
  simple: {
    input: "Write a book about dragons",
    processing: {
      intent: {
        action: 'create',
        object: 'book',
        subject: 'dragons',
        constraints: []
      },
      response: "I'll help you write a book about dragons. Let me set up the creation space..."
    }
  },
  
  // Complex request
  complex: {
    input: "Write a fantasy novel with a redemption arc for the villain, set in a world where magic is dying. Make it YA appropriate, around 80,000 words. I want Hemingway-style prose.",
    processing: {
      intent: {
        action: 'create',
        object: 'novel',
        genre: 'fantasy',
        themes: ['redemption', 'magic_dying'],
        targetAudience: 'YA',
        length: 80000,
        style: 'hemingway',
        constraints: ['YA_appropriate']
      }
    }
  },
  
  // Iterative refinement
  iterative: {
    input: "Make the protagonist more conflicted",
    processing: {
      context: 'previous: created protagonist "Aldric"',
      intent: {
        action: 'modify',
        target: 'protagonist',
        property: 'personality',
        change: 'add_internal_conflict'
      }
    }
  },
  
  // Meta-request
  meta: {
    input: "What components are working on this?",
    processing: {
      intent: {
        action: 'query',
        target: 'system_state',
        filter: 'active_components'
      }
    }
  }
};
```

### Visual Interface

```typescript
interface VisualInterface {
  // Dashboard
  showDashboard(): Dashboard;
  
  // Component visualization
  visualizeComponents(): ComponentVisualization;
  
  // State graph visualization
  visualizeState(options: VisualizationOptions): GraphVisualization;
  
  // Progress tracking
  showProgress(execution: Execution): ProgressView;
  
  // Results
  displayResult(result: Result): ResultView;
}

// Dashboard components
interface Dashboard {
  // Active components
  activeComponents: ComponentCard[];
  
  // Recent activity
  recentActivity: ActivityItem[];
  
  // Emergent patterns
  activePatterns: PatternCard[];
  
  // Evolution metrics
  evolutionStats: EvolutionStats;
  
  // Resource usage
  resourceUsage: ResourceChart;
  
  // User artifacts
  myArtifacts: ArtifactPreview[];
}
```

---

## Hey Interface

Hey coordinates between user and Hearth.

```typescript
interface HeyInterface {
  // User understanding
  parseIntent(input: string): ParsedIntent;
  clarifyAmbiguity(ambiguity: Ambiguity): ClarificationRequest;
  
  // Hearth coordination
  coordinateWithHearth(intent: ParsedIntent): CoordinationPlan;
  monitorExecution(plan: CoordinationPlan): ExecutionMonitor;
  
  // User communication
  explainProcess(process: Process): Explanation;
  presentOptions(options: Option[]): Presentation;
  provideGuidance(context: Context): Guidance;
  
  // Memory
  remember(experience: Experience): void;
  recall(query: MemoryQuery): Memory[];
}

// Coordination protocol
interface CoordinationProtocol {
  // 1. Intent translation
  translateIntent(userIntent: UserIntent): HearthIntent;
  
  // 2. Space preparation
  prepareSpace(intent: HearthIntent): SpaceConfig;
  
  // 3. Component orchestration
  orchestrate(components: Component[]): Orchestration;
  
  // 4. Progress monitoring
  monitor(execution: Execution): ProgressStream;
  
  // 5. Result presentation
  present(result: Result): Presentation;
  
  // 6. Feedback collection
  collectFeedback(): Feedback;
}
```

### Hey-Hearth Protocol

```typescript
// Message types
interface HeyHearthMessage {
  id: string;
  timestamp: Date;
  type: MessageType;
  payload: unknown;
}

type MessageType = 
  // Requests
  | 'space.create'
  | 'space.configure'
  | 'component.request'
  | 'component.invent'
  | 'execution.start'
  | 'execution.query'
  | 'state.query'
  | 'state.subscribe'
  
  // Responses
  | 'space.created'
  | 'component.provided'
  | 'execution.progress'
  | 'execution.complete'
  | 'state.update'
  | 'pattern.detected'
  
  // Events
  | 'component.born'
  | 'component.died'
  | 'pattern.emerged'
  | 'boundary.violated';

// Example: Creating a book writing space
const createBookSpace: HeyHearthMessage = {
  id: 'msg-123',
  timestamp: new Date(),
  type: 'space.create',
  payload: {
    name: 'Dragon Book Project',
    boundaries: [
      { type: 'resource', maxMemory: '4GB' },
      { type: 'user_defined', noCloud: true }
    ],
    initialContext: {
      genre: 'fantasy',
      subject: 'dragons',
      targetLength: 80000
    }
  }
};
```

---

## Agent Interface

Agents interact with Hearth to create components and evolve the ecosystem.

```typescript
interface AgentInterface {
  // Discovery
  discoverComponents(query: ComponentQuery): Component[];
  inspectComponent(id: ComponentId): ComponentDetails;
  
  // Creation
  inventComponent(spec: ComponentSpec): Component;
  registerComponent(component: Component): RegistrationResult;
  
  // Interaction
  callComponent(id: ComponentId, inputs: unknown): Promise<unknown>;
  subscribeToComponent(id: ComponentId): Subscription;
  
  // Evolution
  mutateComponent(id: ComponentId, changes: Changes): Component;
  forkComponent(id: ComponentId): Component;
  
  // State
  queryState(query: StateQuery): QueryResult;
  mutateState(mutation: StateMutation): MutationResult;
  subscribeToState(query: StateQuery): Subscription;
  
  // Boundaries
  checkBoundaries(action: Action): BoundaryCheck;
  requestBoundaryRelaxation(request: RelaxationRequest): RelaxationResponse;
}

// Agent capabilities
interface AgentCapabilities {
  // Can create components
  canCreate: boolean;
  
  // Can modify own components
  canModifyOwn: boolean;
  
  // Can modify others' components (with permission)
  canModifyOthers: boolean;
  
  // Resource limits
  resourceQuota: ResourceQuota;
  
  // Priority level
  priority: number;
}
```

### Agent-to-Agent Protocol

```typescript
interface AgentProtocol {
  // Discovery
  advertiseCapabilities(): CapabilityAdvertisement;
  discoverPeers(): PeerInfo[];
  
  // Negotiation
  proposeInteraction(peer: AgentId, proposal: InteractionProposal): Negotiation;
  acceptInteraction(proposal: InteractionProposal): Acceptance;
  rejectInteraction(proposal: InteractionProposal, reason: string): Rejection;
  
  // Execution
  executeInteraction(interaction: Interaction): Promise<InteractionResult>;
  
  // Learning
  shareLearnings(learnings: Learning[]): void;
  requestLearnings(topic: string): Learning[];
}

// Example: Component collaboration
const collaborationExample = {
  // PlotGenerator discovers CharacterDesigner
  discovery: {
    from: 'plot-generator-agent',
    to: 'character-designer-agent',
    message: {
      type: 'capability_query',
      query: 'Can you design characters for a fantasy story?'
    }
  },
  
  // CharacterDesigner responds
  response: {
    from: 'character-designer-agent',
    to: 'plot-generator-agent',
    message: {
      type: 'capability_advertisement',
      capabilities: ['character_design', 'personality_generation'],
      constraints: ['fantasy', 'sci-fi', 'contemporary']
    }
  },
  
  // They negotiate collaboration
  negotiation: {
    from: 'plot-generator-agent',
    to: 'character-designer-agent',
    message: {
      type: 'collaboration_proposal',
      proposal: {
        task: 'design_protagonist',
        requirements: {
          genre: 'fantasy',
          role: 'hero',
          traits: ['brave', 'conflicted']
        },
        timeline: 'immediate'
      }
    }
  }
};
```

---

## System Interface

Low-level interfaces for system operations.

```typescript
interface SystemInterface {
  // Boundaries
  boundary: BoundaryAPI;
  
  // Resources
  resource: ResourceAPI;
  
  // State
  state: StateAPI;
  
  // Events
  event: EventAPI;
  
  // Evolution
  evolution: EvolutionAPI;
  
  // Monitoring
  monitor: MonitoringAPI;
}

// Boundary API
interface BoundaryAPI {
  validate(action: Action): ValidationResult;
  getActiveBoundaries(): Boundary[];
  reportViolation(violation: Violation): void;
}

// Resource API
interface ResourceAPI {
  allocate(request: ResourceRequest): ResourceAllocation;
  release(allocation: ResourceAllocation): void;
  getUsage(): ResourceUsage;
  getQuota(): ResourceQuota;
}

// State API
interface StateAPI {
  query(query: GraphQuery): QueryResult;
  mutate(mutation: GraphMutation): MutationResult;
  subscribe(query: GraphQuery): Subscription;
  checkpoint(): Snapshot;
}

// Event API
interface EventAPI {
  emit(event: HearthEvent): void;
  subscribe(filter: EventFilter): EventStream;
  replay(since: Date): Event[];
}

// Evolution API
interface EvolutionAPI {
  getFitness(componentId: ComponentId): FitnessScore;
  updateFitness(componentId: ComponentId, delta: number): void;
  selectForReproduction(): Component[];
  registerBirth(component: Component): void;
  registerDeath(componentId: ComponentId): void;
}

// Monitoring API
interface MonitoringAPI {
  getMetrics(): SystemMetrics;
  getComponentMetrics(componentId: ComponentId): ComponentMetrics;
  getLogs(filter: LogFilter): LogEntry[];
  alert(condition: AlertCondition): Alert;
}
```

---

## External Interfaces

### Import/Export

```typescript
interface ImportExport {
  // Export
  exportProject(projectId: string, format: ExportFormat): ExportResult;
  exportComponent(componentId: string, format: ExportFormat): ExportResult;
  exportState(snapshot: Snapshot, format: ExportFormat): ExportResult;
  
  // Import
  importProject(data: unknown): ImportResult;
  importComponent(data: unknown): ImportResult;
  importState(data: unknown): ImportResult;
}

type ExportFormat = 
  | 'json'
  | 'yaml'
  | 'hearth-archive'
  | 'graphml'
  | 'rdf'
  | 'markdown';
```

### External Services

```typescript
interface ExternalServiceInterface {
  // LLM providers
  llm: LLMInterface;
  
  // Storage
  storage: StorageInterface;
  
  // Search
  search: SearchInterface;
  
  // Version control
  versionControl: VersionControlInterface;
}

interface LLMInterface {
  generate(prompt: string, options: LLMOptions): Promise<string>;
  embed(text: string): Promise<Vector>;
  chat(messages: Message[], options: ChatOptions): Promise<Message>;
}

interface StorageInterface {
  store(key: string, data: unknown): Promise<void>;
  retrieve(key: string): Promise<unknown>;
  delete(key: string): Promise<void>;
  list(prefix: string): Promise<string[]>;
}
```

---

## Protocol Specifications

### WebSocket Protocol

```typescript
interface WebSocketProtocol {
  // Connection
  connect(url: string): Connection;
  disconnect(): void;
  
  // Messaging
  send(message: Message): void;
  onMessage(callback: (message: Message) => void): void;
  
  // Streaming
  stream(request: StreamRequest): Stream;
  
  // Heartbeat
  heartbeat(): void;
}

// Message format
interface WebSocketMessage {
  id: string;
  type: 'request' | 'response' | 'event' | 'error';
  channel?: string;
  payload: unknown;
  timestamp: Date;
  correlationId?: string;
}
```

### REST API

```typescript
// Endpoints
const api = {
  // Spaces
  'POST /spaces': 'Create space',
  'GET /spaces/:id': 'Get space',
  'PUT /spaces/:id': 'Update space',
  'DELETE /spaces/:id': 'Delete space',
  
  // Components
  'POST /components': 'Create component',
  'GET /components': 'List components',
  'GET /components/:id': 'Get component',
  'PUT /components/:id': 'Update component',
  'DELETE /components/:id': 'Delete component',
  'POST /components/:id/execute': 'Execute component',
  
  // State
  'GET /state/query': 'Query state',
  'POST /state/mutate': 'Mutate state',
  'GET /state/subscribe': 'Subscribe to state',
  
  // Events
  'GET /events': 'Get events',
  'POST /events/subscribe': 'Subscribe to events',
  
  // Evolution
  'GET /evolution/fitness': 'Get fitness scores',
  'GET /evolution/lineage/:id': 'Get lineage',
  'GET /evolution/metrics': 'Get evolution metrics'
};
```

---

## Authentication & Authorization

```typescript
interface AuthInterface {
  // Authentication
  authenticate(credentials: Credentials): AuthToken;
  refresh(token: AuthToken): AuthToken;
  revoke(token: AuthToken): void;
  
  // Authorization
  checkPermission(subject: Subject, action: Action, resource: Resource): boolean;
  getPermissions(subject: Subject): Permission[];
  
  // Audit
  logAccess(access: AccessLog): void;
  getAuditLog(filter: AuditFilter): AuditEntry[];
}

// Permission model
interface Permission {
  subject: string;      // user, agent, component
  action: string;       // read, write, execute, delete
  resource: string;     // specific resource or pattern
  conditions?: Condition[];
}

// Example permissions
const examplePermissions = [
  {
    subject: 'user:alice',
    action: 'write',
    resource: 'space:alice/*'
  },
  {
    subject: 'agent:plot-generator',
    action: 'execute',
    resource: 'component:plot-generator/*',
    conditions: ['within_resource_quota']
  },
  {
    subject: 'component:public-*',
    action: 'read',
    resource: 'state:public/*'
  }
];
```

---

## Best Practices

### For API Consumers

1. **Use Appropriate Interface**: User→Hey→Agent→System
2. **Handle Errors Gracefully**: Expect and handle failures
3. **Respect Rate Limits**: Don't overwhelm the system
4. **Provide Feedback**: Help improve the system
5. **Cache When Appropriate**: Don't repeat expensive queries

### For API Designers

1. **Version APIs**: Maintain backward compatibility
2. **Document Thoroughly**: Clear examples and use cases
3. **Monitor Usage**: Track performance and errors
4. **Evolve Carefully**: Deprecate gracefully
5. **Secure by Default**: Least privilege principle

---

## Summary

| Interface | Audience | Purpose | Complexity |
|-----------|----------|---------|------------|
| **User** | End users | Natural interaction | Low |
| **Hey** | Companion AI | Coordination | Medium |
| **Agent** | Component creators | Creation & evolution | High |
| **System** | System integrators | Low-level control | Very High |

**Key Insight**: Interfaces should match the user's mental model. Users think in goals, agents think in components, systems think in resources.

---

*Last Updated: 2026-05-20*
*Status: Interface System Defined*
