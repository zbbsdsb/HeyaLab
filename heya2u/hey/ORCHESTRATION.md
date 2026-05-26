# Hey Orchestration — Coordinating Agents and Hearth

> Hey is the conductor. Agents are the musicians. Hearth is the concert hall.

---

## What is Hey Orchestration?

Hey Orchestration is the system by which Hey coordinates Hearth agents, manages Hearth spaces, and ensures that the creation ecosystem operates effectively. It is the **operational arm** of Hey's sovereignty — where governance decisions become concrete actions.

```
Without Orchestration:
  User → Hearth → Agents invent components → ??? (no coordination)

With Hey Orchestration:
  User → Hey analyzes → Hey dispatches agents → Agents invent →
  Hey monitors → Hey synthesizes → Hey presents results
```

### Key Insight

Hearth's agents are powerful but stateless. Without coordination, they produce noise. Hey Orchestration provides the **direction, sequencing, and synthesis** that turns agent output into coherent creation.

---

## Orchestration Modes

### 1. Sequential Mode

For straightforward tasks where steps must happen in order.

```typescript
interface SequentialOrchestration {
  steps: OrchestrationStep[];
  currentStep: number;
  status: 'pending' | 'running' | 'paused' | 'complete';

  execute(): Promise<OrchestrationResult>;
}

interface OrchestrationStep {
  id: string;
  description: string;
  agent?: AgentId;           // Optional: specific agent for this step
  input: StepInput;
  expectedOutput: StepOutput;
  verification: VerificationCheck;
}
```

**Use when**: Tasks have clear dependencies (step B requires step A's output).

### 2. Swarm Mode

For complex tasks that benefit from parallel exploration.

```typescript
interface SwarmOrchestration {
  // Phase 1: Dispatch
  dispatch(task: ComplexTask, availableAgents: Agent[]): DispatchPlan;

  // Phase 2: Parallel execution
  executeParallel(plan: DispatchPlan): Promise<AgentResult[]>;

  // Phase 3: Synthesis
  synthesize(results: AgentResult[], originalTask: ComplexTask): SynthesizedResult;
}

interface DispatchPlan {
  assignments: Assignment[];
  strategy: 'divide_and_conquer' | 'explore_and_select' | 'debate_and_resolve';
}

interface Assignment {
  agentId: AgentId;
  task: string;
  context: string;
  priority: number;
}
```

**Use when**: Tasks can be decomposed into independent subtasks (e.g., "design the UI" + "implement the API" + "write tests").

### 3. Governance Mode

For critical decisions that require the full consensus protocol.

```typescript
interface GovernanceOrchestration {
  // Uses the full Board of Directors
  executeWithGovernance(intent: UserIntent): Promise<GovernanceResult>;

  // Internally:
  // 1. Strategist proposes plan
  // 2. Skeptic critiques
  // 3. (loop until consensus)
  // 4. Executor implements
  // 5. Chronicler documents
}
```

**Use when**: High-stakes decisions, architectural choices, or when the user explicitly requests thorough analysis.

---

## Hearth Coordination

### Space Activation

Hey activates and configures Hearth spaces on behalf of the user.

```typescript
interface HearthCoordination {
  // Create a new Hearth space
  activateSpace(config: SpaceConfig): HearthSpace;

  // Configure existing space
  configureSpace(spaceId: SpaceId, patch: SpacePatch): void;

  // Monitor active spaces
  monitorSpaces(): SpaceMonitor[];

  // Deactivate
  deactivateSpace(spaceId: SpaceId): void;
}

interface SpaceConfig {
  name: string;
  boundaries: Boundary[];
  resources: ResourceAllocation;
  initialContext: ProjectContext;
  governanceMode: 'sequential' | 'swarm' | 'governance';
  mindDepth: number;
}
```

### Agent Management

Hey creates, configures, and manages the agents within Hearth.

```typescript
interface AgentManagement {
  // Create agents for a task
  inventAgents(task: Task): AgentSpec[];

  // Configure existing agents
  configureAgent(agentId: AgentId, config: AgentConfig): void;

  // Monitor agent performance
  monitorAgents(): AgentPerformance[];

  // Prune underperforming agents
  pruneAgents(criteria: PruningCriteria): void;
}
```

### Progress Monitoring

Hey tracks the state of all active orchestration.

```typescript
interface ProgressMonitor {
  // Real-time status
  getStatus(): OrchestrationStatus;

  // Event stream
  subscribe(listener: ProgressListener): Subscription;

  // Checkpoints
  createCheckpoint(): Checkpoint;
  restoreCheckpoint(checkpointId: string): void;

  // Alerts
  alerts: ProgressAlert[];
}
```

---

## The Dispatch Protocol

The core algorithm Hey uses to decide how to orchestrate a task:

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                      DISPATCH PROTOCOL                                       │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                              │
│   User Intent Received                                                      │
│         │                                                                    │
│         ▼                                                                    │
│   ┌─────────────────────────────────────────────────────────────────┐       │
│   │  1. ANALYZE                                                       │    │
│   │  Parse intent → Classify complexity → Identify dependencies       │    │
│   └─────────────────────────────────────────────────────────────────┘       │
│         │                                                                    │
│         ▼                                                                    │
│   ┌─────────────────────────────────────────────────────────────────┐       │
│   │  2. SELECT MODE                                                  │    │
│   │  Simple task → Sequential mode                                    │    │
│   │  Complex task → Swarm mode                                        │    │
│   │  Critical decision → Governance mode                               │    │
│   │  User preference → Use specified mode                             │    │
│   └─────────────────────────────────────────────────────────────────┘       │
│         │                                                                    │
│         ▼                                                                    │
│   ┌─────────────────────────────────────────────────────────────────┐       │
│   │  3. PREPARE                                                      │    │
│   │  Retrieve context → Check cognition store → Load episodic memory │    │
│   │  → Assess ecological impact                                        │    │
│   └─────────────────────────────────────────────────────────────────┘       │
│         │                                                                    │
│         ▼                                                                    │
│   ┌─────────────────────────────────────────────────────────────────┐       │
│   │  4. EXECUTE                                                      │    │
│   │  Dispatch agents → Monitor progress → Handle errors              │    │
│   │  → Collect results                                                │    │
│   └─────────────────────────────────────────────────────────────────┘       │
│         │                                                                    │
│         ▼                                                                    │
│   ┌─────────────────────────────────────────────────────────────────┐       │
│   │  5. SYNTHESIZE                                                    │    │
│   │  Combine agent outputs → Verify quality → Generate summary       │    │
│   │  → Store to Mantle → Update Living Codex                          │    │
│   └─────────────────────────────────────────────────────────────────┘       │
│         │                                                                    │
│         ▼                                                                    │
│   Present to user via Veil                                                │
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## Convergence Trigger

When orchestration goes off-track, Hey can trigger Convergence.

### Detection Conditions

```typescript
interface ConvergenceDetection {
  triggers: {
    messageOverflow: {
      threshold: number;      // e.g., 50 messages without progress
      action: 'summarize_and_refocus';
    };
    topicDrift: {
      detection: 'semantic_similarity';
      threshold: number;      // e.g., similarity < 0.3 from original intent
      action: 'redirect_to_original_intent';
    };
    circularDebate: {
      detection: 'repeated_arguments';
      threshold: number;      // e.g., same argument 3 times
      action: 'force_decision';
    };
    userRequest: {
      detection: 'explicit_trigger';  // User says "converge" or "summarize"
      action: 'full_convergence';
    };
  };
}
```

### Convergence Actions

```typescript
interface ConvergenceAction {
  summarize: {
    keyPoints: string[];
    decisionsMade: string[];
    openQuestions: string[];
  };
  extractDecisions: {
    agreed: Decision[];
    contested: Decision[];    // Needs user input
    deferred: Decision[];      // Parked for later
  };
  suggestNextStep: {
    recommendation: string;
    rationale: string;
    requiresUserInput: boolean;
  };
}
```

---

## Error Handling

### Agent Failure

```typescript
interface AgentFailureHandling {
  // When an agent fails
  onAgentFailure(agentId: AgentId, error: Error): FailureResponse;

  // Strategies
  strategies: {
    retry: 'Re-invoke the same agent with error context';
    reassign: 'Assign the task to a different agent';
    simplify: 'Break the task into smaller pieces';
    escalate: 'Notify the user and ask for guidance';
    fallback: 'Use a simpler, more reliable approach';
  };
}
```

### Resource Pressure

```typescript
interface ResourcePressureHandling {
  // When Hearth resources are scarce
  onResourcePressure(pressure: ResourcePressure): PressureResponse;

  // Strategies
  strategies: {
    prioritize: 'Focus on highest-priority tasks only';
    throttle: 'Slow down non-essential agents';
    checkpoint: 'Save state and pause low-priority work';
    notify: 'Alert the user about resource constraints';
  };
}
```

---

## Relationship to Other Components

```
Hey Orchestration
  ├── Manages → Hearth Agents (dispatch, monitor, prune)
  ├── Activates → Hearth Spaces (create, configure, monitor)
  ├── Produces → Mantle Artifacts (store results with lineage)
  ├── Consumes → Ember Seeds (use seeds as context for dispatch)
  ├── Presents via → Veil (show progress, results, dashboards)
  └── Coordinates → Grove (multi-user orchestration)
```

---

## Design Principles

1. **Governance First**: Every orchestration decision flows through the governance protocol
2. **Adaptive Complexity**: Simple tasks get simple orchestration, complex tasks get full governance
3. **Graceful Degradation**: When agents fail, the system adapts rather than crashing
4. **Full Traceability**: Every dispatch, execution, and result is logged
5. **User Override**: The user can intervene at any point — pause, redirect, or cancel

---

*Last Updated: 2026-05-25*
*Status: Core Design Defined*
*See also: [README.md](./README.md) | [GOVERNANCE.md](./GOVERNANCE.md) | [COGNITION.md](./COGNITION.md)*
