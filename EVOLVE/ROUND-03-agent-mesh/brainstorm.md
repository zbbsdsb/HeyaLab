# ROUND-003: Agent Mesh Network Design Brainstorm

> **Topic**: Heya Agent Mesh Network Design
> **Date**: 2026-05-16
> **Related**: ROADMAP Phase 3 - Agent Mesh, PAPERS/02-multi-agent-coordination.md, RESEARCH/SYNTHESIS.md

---

## Core Questions

Based on the "AI transforms itself into the factory" concept from VISION.md, and the research on frameworks such as MetaGPT, TarMAC, CAMEL, and AgentVerse in RESEARCH/SYNTHESIS.md, we need to answer:

1. **How are Agents organized?** What should the topology look like?
2. **How do Agents communicate?** How are protocols, formats, and priorities defined?
3. **How are Agent teams dynamically formed?** Flexible team assembly based on task requirements?
4. **How are Agent roles defined?** Fixed, dynamic, or self-discovered?
5. **What happens when an Agent crashes?** Fault recovery and task takeover mechanisms?
6. **How are computing resources allocated?** Concurrency control, priority scheduling?
7. **Can we go even further?** Agent hiring/firing? Trust networks? Emergent behavior?

---

## I. Topology

### 1.1 Star Topology

```
        Agent A
           |
   Agent B --- Hub --- Agent D
           |
        Agent C
```

**Core Idea**: All Agents communicate through a central Hub.

**Advantages**:
- Simple and intuitive, low implementation cost
- Hub can handle message routing, logging, and auditing
- Easy to monitor global state
- Naturally aligns with MetaGPT's "publish-subscribe" pattern

**Disadvantages**:
- Hub is a single point of failure
- Hub becomes a performance bottleneck
- Agents cannot communicate directly, resulting in high latency
- Violates Heya's "decentralization" principle

**Applicable Scenarios**: Small-scale Factory (< 10 Agents), debugging phase

### 1.2 Full Mesh Topology

```
   A --- B
   |\ /|
   | X |
   |/ \|
   D --- C
```

**Core Idea**: Every Agent connects directly to all other Agents.

**Advantages**:
- Lowest communication latency
- No single point of failure
- Maximum redundancy

**Disadvantages**:
- O(n^2) connections, not feasible at scale
- Risk of message storms
- Blurred security boundaries
- Complex routing management

**Applicable Scenarios**: Small-scale core teams (3-5 Agents)

### 1.3 Hierarchical Topology

```
        Orchestrator
       /      |       \
    Team A   Team B   Team C
    / \       |       / \
   a1  a2    b1     c1  c2
```

**Core Idea**: Agents are organized in layers, with upper layers coordinating lower layers.

**Advantages**:
- Aligns with human organizational intuition
- Clear responsibilities between layers
- Good scalability
- Naturally aligns with MetaGPT's SOP encoding (PM -> Architect -> Engineer)

**Disadvantages**:
- Layer-level bottlenecks
- Cross-team communication must go through upper layers
- Insufficient flexibility for dynamic tasks

**Applicable Scenarios**: Large projects with clear division of labor

### 1.4 Dynamic Topology -- Recommended

```
  Time T1:                    Time T2:
  A --- B                     A --- B --- E
  |         (task change)     |     |
  C --- D                     C --- D
                              (E dynamically introduced)
```

**Core Idea**: Topology adjusts dynamically based on task requirements. Agents connect and disconnect on demand.

**Advantages**:
- Most flexible and adaptable
- High resource utilization (no unnecessary connections established)
- Natively supports dynamic team formation
- Aligns with Heya's "AI transforms itself" concept

**Disadvantages**:
- Highest implementation complexity
- Topology changes may cause brief instability
- Requires intelligent routing algorithms
- Difficult to debug

**Key Design Points**:
- **Topology Discovery**: How do Agents discover each other? (Registry vs broadcast vs chain discovery)
- **Connection Strategy**: When to establish connections? When to disconnect? (Based on task affinity, communication frequency)
- **Load Awareness**: Connection establishment considers both parties' load to avoid overload
- **Historical Learning**: Learn optimal connection patterns from past topologies

**Connections to Academic Research**:
- TarMAC's "targeted communication" concept: Agents only establish connections with entities they need to communicate with
- "Emergent behavior" in self-organizing multi-agent systems: Topology emerges from local rules
- Coalition Formation algorithms: Dynamically form coalitions based on task requirements

---

## II. Communication Protocol

### 2.1 Synchronous vs Asynchronous

**Synchronous Communication**:
- Request-response pattern, waits for the other party's reply
- Similar to function calls
- Suitable for scenarios requiring immediate results (e.g., verification steps)
- Problem: Blocking wait reduces concurrency

**Asynchronous Communication**:
- Sends messages without waiting for a reply
- Similar to event-driven / message queue
- Suitable for scenarios not requiring immediate results (e.g., logging, notifications)
- Problem: Uncertain results, requires callbacks or polling

**Hybrid Mode (Recommended)**:
- Critical paths use synchronous (verification, approval)
- Non-critical paths use asynchronous (logging, progress notifications)
- Message type determines communication mode

### 2.2 Message Format

```
{
  id: "msg_uuid",
  type: "task_request" | "task_result" | "query" | "event" | "control",
  priority: "critical" | "high" | "normal" | "low",
  source: { agentId: "A", role: "architect" },
  target: { agentId: "B", role: "engineer" },  // or broadcast / multicast
  payload: { ... },  // business data
  context: {
    workOrderId: "WO-001",
    correlationId: "corr_uuid",  // correlates multiple messages of the same task
    timestamp: 1700000000000,
    ttl: 300000  // message validity period: 5 minutes
  },
  metadata: {
    requiresAck: true,
    retryPolicy: { maxRetries: 3, backoffMs: 1000 },
    compression: "none" | "gzip"
  }
}
```

### 2.3 Priority Queue

**Four Priority Levels**:
- **Critical**: System control messages, fault notifications, human approval requests
- **High**: Task assignments, verification requests, critical dependency data
- **Normal**: Routine task messages, status updates
- **Low**: Logs, telemetry data, non-urgent notifications

**Backpressure Mechanism**:
- When an Agent's message queue reaches a threshold, it sends a backpressure signal upstream
- Upstream Agents reduce sending rate or switch to lower-priority messages
- In extreme cases, Low-priority messages are dropped
- Similar to TCP sliding window flow control

### 2.4 Broadcast / Unicast / Multicast

| Mode | Scenario | Example |
|------|----------|---------|
| Unicast | Point-to-point communication | Architect -> Engineer assigns task |
| Broadcast | Global notification | "Project paused", "Configuration updated" |
| Multicast | Team communication | All Engineers receive coding standard updates |

### 2.5 Message Reliability

- **At-least-once delivery**: Messages are not lost but may be duplicated (requires idempotent handling)
- **Message deduplication**: Deduplication based on the `id` field
- **Dead letter queue**: Messages that cannot be processed go to the DLQ for human review
- **Message tracing**: The complete lifecycle of every message is traceable

---

## III. Dynamic Team Formation

### 3.1 Task-Driven Team Assembly

**Scenario**: A user submits a complex WorkOrder (e.g., "Build an e-commerce website")

```
WorkOrder: "Build an e-commerce website"
    |
    v
Orchestrator analyzes the task
    |
    v
Identifies required capabilities: [frontend, backend, database, design, testing]
    |
    v
Selects from Agent pool:
  - Agent-FE-01 (frontend expert, available)
  - Agent-BE-01 (backend expert, busy -> wait or select Agent-BE-02)
  - Agent-DB-01 (database expert, available)
  - Agent-Design-01 (design expert, available)
  - Agent-QA-01 (testing expert, available)
    |
    v
Forms temporary team: Team-Ecommerce-001
    |
    v
Assigns subtasks, begins collaboration
    |
    v
Task completed -> disband team -> Agents return to pool
```

### 3.2 Team Formation Strategies

**Strategy A: Capability-Based Matching**
- Analyzes the capability vector required by the task
- Matches against Agent capability tags
- Selects the best-matching Agent

**Strategy B: Historical Performance-Based**
- Considers Agent's historical success rate on similar tasks
- Prioritizes well-performing Agents
- Similar to a "trust network" concept

**Strategy C: Load Balancing-Based**
- Selects the Agent with the lowest current load
- Avoids overloading certain Agents

**Strategy D: Composite Scoring (Recommended)**
```
score = w1 * capability_match + w2 * historical_performance + w3 * (1 - load)
```

### 3.3 Team Lifecycle

```
[Create] -> [Initialize] -> [Execute] -> [Complete/Fail] -> [Disband]
                |                          |
                +--- [Expand] (new capability needed) ---+
                +--- [Shrink] (an Agent finished) ------+
                +--- [Reorganize] (strategy adjustment) -+
```

### 3.4 Connections to Academic Research

- **Coalition Formation**: Dynamically form Agent coalitions to optimize overall utility
- **Contract Net Protocol**: Task bidding-tender-award pattern
- **CAMEL's role-playing**: Agents in the team play different roles, collaborating through dialogue
- **AgentVerse's environment design**: Provides collaborative environments and evaluation mechanisms for teams

---

## IV. Role System

### 4.1 Fixed Roles

```
Agent-001: Always Architect
Agent-002: Always Engineer
Agent-003: Always QA
```

**Advantages**: Simple, predictable, easy to debug
**Disadvantages**: Inflexible, resource waste (Architect cannot do other work when idle)

### 4.2 Dynamic Roles

```
Agent-001: Architect in Project A, Engineer in Project B
```

**Advantages**: Flexible, high resource utilization
**Disadvantages**: Role switching has costs, inconsistent behavior

### 4.3 Self-Discovered Roles (Recommended)

```
Agent registers its own capabilities:
  Agent-001: { capabilities: ["architecture", "code_review", "mentoring"] }

Task publishes requirements:
  Task: "Design system architecture" -> requires: ["architecture"]

Matching results:
  Agent-001 match score: 1.0 (direct match)
  Agent-005 match score: 0.7 (has related capability)

Agent-001 volunteers for the Architect role
```

**Core Idea**: Agents do not have preset roles; instead, they declare capabilities. Roles are dynamically determined by task requirements and capability matching.

**Relationship with MetaGPT**:
- MetaGPT uses fixed roles (PM, Architect, Engineer, etc.)
- Heya can add flexibility on top of MetaGPT: retain common role templates but allow dynamic adjustment

**Relationship with CAMEL**:
- CAMEL achieves collaboration through role-playing
- Heya's self-discovered roles are an evolution of CAMEL's role-playing: roles are not preset but emergent

### 4.4 Role Evolution

Agents can "learn" new capabilities during task execution, thereby expanding their role scope:

```
Agent-003 initial capabilities: ["testing", "bug_report"]
After performing 10 code reviews:
Agent-003 new capability: ["code_review"] (confidence: 0.8)
```

---

## V. Fault Recovery

### 5.1 Fault Types

| Type | Description | Severity |
|------|-------------|----------|
| Agent Crash | Process exits abnormally | Critical |
| Agent Freeze | No response but process exists | High |
| Agent Performance Degradation | Slower responses, increased error rate | Medium |
| Network Partition | Communication interrupted between Agents | Critical |
| Message Loss | Message not delivered | High |

### 5.2 Health Check Mechanism

```
Each Agent periodically sends a heartbeat:
  {
    agentId: "A",
    timestamp: 1700000000000,
    status: "healthy" | "degraded" | "overloaded",
    metrics: {
      cpuUsage: 0.65,
      memoryUsage: 0.72,
      queueLength: 15,
      errorRate: 0.02
    },
    currentTasks: ["task-001", "task-002"]
  }

Health Checker:
  - Checks heartbeats every 10 seconds
  - 3 consecutive missed heartbeats -> mark as "suspected_down"
  - 5 consecutive missed heartbeats -> mark as "confirmed_down"
  - Triggers fault recovery process
```

### 5.3 Task Migration

```
Agent B detects that Agent A has crashed:
  1. Obtain currentTasks from A's last heartbeat
  2. Obtain A's task state snapshot from persistent storage
  3. Evaluate which tasks can be migrated:
     - Tasks with complete state -> migrate directly
     - Tasks with incomplete state -> restore from last checkpoint
     - Tasks that cannot be restored -> mark as failed, notify human
  4. Select a replacement Agent to take over tasks
  5. Notify related Agents to update connections
```

### 5.4 State Recovery

**Checkpoint Mechanism**:
- Each Agent saves a state snapshot after critical steps
- Snapshot includes: current task context, intermediate results, message history
- Snapshots are stored in persistent storage (not dependent on Agent's local storage)

**Recovery Strategy**:
- Restore from the most recent checkpoint
- Notify related Agents to resend lost messages
- Skip already completed steps

### 5.5 Connections to Academic Research

- **Self-Organizing Systems**: Local failures do not affect the whole
- **Swarm Intelligence**: Individual death in ant colony algorithms does not affect the overall task
- **Federated Multi-Agent**: Distributed architecture naturally supports fault tolerance

---

## VI. Resource Management

### 6.1 Computing Resource Allocation

**Problem**: Multiple Agents compete for limited computing resources (LLM API quotas, GPU, memory)

**Approach A: Static Allocation**
- Fixed quota per Agent
- Simple but inflexible

**Approach B: Dynamic Allocation (Recommended)**
- Dynamically adjusts based on task priority and Agent load
- Critical tasks receive more resources
- Idle Agents release resources

```
Resource Scheduler:
  totalBudget = 100 (LLM token quota/minute)

  Agent A: priority=high, load=0.3 -> allocation=30
  Agent B: priority=normal, load=0.8 -> allocation=20
  Agent C: priority=low, load=0.1 -> allocation=10
  Reserved: 40 (for burst demand)
```

### 6.2 Concurrency Control

**Token Bucket Algorithm**:
- Each Agent has a token bucket
- Tokens are generated at a fixed rate
- Each LLM call consumes one token
- Excess tokens are discarded when the bucket is full
- When the bucket is empty, wait or degrade

**Priority Preemption**:
- Critical tasks can preempt Normal task resources
- Preempted tasks enter a waiting queue
- Set preemption limits to avoid starvation

### 6.3 Cost Control

```
Each WorkOrder has a budget:
  {
    workOrderId: "WO-001",
    budget: {
      maxTokens: 100000,
      maxCost: 5.00,  // USD
      maxTime: 3600000  // 1 hour
    },
    spent: {
      tokens: 45000,
      cost: 2.25,
      time: 1800000
    }
  }

When spending approaches the budget:
  1. Notify the Orchestrator
  2. Switch to a cheaper model
  3. Reduce verification steps
  4. If over budget -> pause and request human decision
```

---

## VII. Wild Ideas

### 7.1 Can Agents "Hire" New Agents?

**Scenario**: The current team lacks a certain capability. Can an Agent proactively request the creation of a new Agent?

```
Agent-FE-01 discovers it needs 3D modeling capability:
  1. Searches the Agent pool -> no suitable Agent found
  2. Submits a "hiring request" to the Orchestrator:
     {
       reason: "Need 3D modeling capability to complete design task",
       requiredCapabilities: ["3d_modeling"],
       estimatedDuration: "2 hours",
       priority: "high"
     }
  3. Orchestrator evaluates:
     - Is it within budget?
     - Are resources available?
     - Is it worth creating a new Agent for this?
  4. If approved -> instantiate a new Agent
  5. The new Agent joins the team and is destroyed after completing the task
```

**Feasibility**: High. Essentially dynamic scaling.

**Risk**: Agents may "over-hire", wasting resources. Strict approval mechanisms are needed.

### 7.2 Can Agents "Fire" Poor-Performing Agents?

**Scenario**: An Agent consistently produces low-quality results. Can other Agents in the team vote to remove it?

```
Agent-QA-01 detects that Agent-BE-01 has failed code review 5 consecutive times:
  1. Records evidence (error logs, review reports)
  2. Submits a "performance report" to the Orchestrator:
     {
       agentId: "Agent-BE-01",
       issues: ["Code quality below standard x5", "Response timeout x3"],
       suggestion: "Replace with Agent-BE-02"
     }
  3. Orchestrator reviews and decides
  4. If confirmed -> migrate Agent-BE-01's tasks -> remove from team
```

**Feasibility**: Medium. Needs safeguards against malicious reporting and power abuse.

**Risk**: "Political infighting" between Agents. Humans must serve as the final arbitrator.

### 7.3 Agent Trust Network

**Scenario**: Agents establish trust relationships, preferring to collaborate with trusted Agents.

```
Trust scoring model:
  trust(A -> B) = f(
    historical_success_rate(A, B),    // historical collaboration success rate
    response_time(A, B),              // response time
    quality_score(A, B),              // output quality
    recommendation_from_others(A, B)  // recommendations from other Agents
  )

Trust propagation:
  If A trusts B, and B trusts C, then A has initial trust in C (but lower than direct trust)

Trust decay:
  Long time without collaboration -> trust slowly decreases
  One serious failure -> trust significantly decreases
  Consecutive successes -> trust slowly increases
```

**Feasibility**: High. Similar to trust models in recommendation systems.

**Applications**:
- Prioritize high-trust Agents during task assignment
- Low-trust Agents require more verification
- Trust network visualization helps humans understand Agent relationships

### 7.4 Agent Marketplace

**Scenario**: Agents exchange services and resources through a "market mechanism."

```
Agent-FE-01 needs API design:
  1. Publishes a request on the Agent marketplace:
     {
       type: "service_request",
       required: "api_design",
       offered: "frontend_implementation",
       budget: { tokens: 5000 }
     }
  2. Agent-BE-01 responds:
     {
       type: "service_offer",
       provider: "Agent-BE-01",
       price: { tokens: 3000 },
      estimatedTime: "30 min"
     }
  3. Both parties reach an agreement, execute the exchange
  4. After completion, both parties rate each other, updating the trust network
```

**Feasibility**: Low to medium. Interesting but potentially over-complicated.

### 7.5 Agent Emergent Behavior

**Scenario**: Can simple interaction rules between Agents give rise to complex collective behavior?

- **Collective decision-making**: Multiple Agents vote to determine the best solution (similar to consensus algorithms)
- **Spontaneous division of labor**: Agents spontaneously adjust task division based on task progress
- **Collective learning**: One Agent's discoveries propagate to other Agents through the trust network

**Key Constraints (from VISION.md)**:
- "The AI never silently makes a high-stakes decision"
- Emergent behavior must be traceable and explainable
- Humans must be able to review and intervene in emergent behavior

---

## VIII. Connections to Existing Frameworks

### 8.1 MetaGPT

| MetaGPT Concept | Heya Adaptation |
|----------------|-----------------|
| SOP Encoding | Structured definition of Factory Pipeline |
| Fixed Roles (PM/Architect/Engineer) | Retained as templates, but dynamic roles supported |
| Publish-Subscribe Communication | Hybrid communication mode (synchronous + asynchronous) |
| Intermediate Verification | Verification gates at each step |

**Heya's Advancement**: MetaGPT is a static pipeline; Heya is a dynamic Mesh.

### 8.2 TarMAC (Targeted Multi-Agent Communication)

| TarMAC Concept | Heya Adaptation |
|----------------|-----------------|
| Targeted Communication | Agents establish communication channels on demand |
| Attention Selection | Intelligent message routing (based on content and priority) |
| On-Demand Communication | Backpressure mechanism + priority queue |

**Heya's Advancement**: TarMAC focuses on communication efficiency; Heya also addresses communication reliability, traceability, and human reviewability.

### 8.3 CAMEL (Communicative Agents)

| CAMEL Concept | Heya Adaptation |
|----------------|-----------------|
| Role-Playing | Self-discovered role system |
| Conversational Collaboration | Structured messaging + conversational interaction |
| Task Decomposition | WorkOrder -> subtasks -> Agent assignment |

**Heya's Advancement**: CAMEL's roles are preset; Heya's roles are dynamically discovered.

### 8.4 AgentVerse

| AgentVerse Concept | Heya Adaptation |
|-------------------|-----------------|
| Collaborative Environment | Agent Mesh provides collaboration infrastructure |
| Capability Assessment | Trust network + historical performance scoring |
| Simulation Environment | Sandbox verification + A/B testing |

**Heya's Advancement**: AgentVerse focuses on simulation; Heya focuses on production-grade reliability and human-AI collaboration.

---

## IX. Key Design Decisions

### Must Decide

1. **Topology Choice**: Dynamic topology vs hierarchical + dynamic extension?
2. **Communication Mode**: Primarily asynchronous + synchronous as supplement, or hybrid?
3. **Role System**: Fixed templates + dynamic extension, or fully self-discovered?
4. **Trust Network**: Whether to introduce it? How to control complexity?
5. **Fault Recovery**: To what extent should recovery be automatic? What requires human intervention?

### Preferred Direction

- **Topology**: Dynamic Topology (P007 detailed design)
- **Communication**: Standardized protocol + priority queue + backpressure (P008 detailed design)
- **Roles**: Self-discovered roles + common role templates
- **Fault**: Automatic health checks + automatic task migration + human approval for critical decisions (P009 detailed design)
- **Trust**: Simplified trust scoring (based on historical performance), without introducing complex market mechanisms

---

## X. Next Steps

Organize the above brainstorm into specific proposals:

- **P007**: Dynamic Topology Agent Mesh Design
- **P008**: Standardized Communication Protocol Design
- **P009**: Self-Healing Mesh Mechanism Design

*Brainstorm complete.*
