# Proposal P007: Dynamic Topology Agent Mesh

> **Status**: Draft
> **Proposed by**: AI Assistant
> **Date**: 2026-05-16
> **Related**: ROUND-003 brainstorm, PAPERS/02-multi-agent-coordination.md

---

## Summary

This proposal presents a dynamic topology Agent Mesh architecture where connections between Agents are dynamically established and disconnected based on task requirements, communication frequency, and load status. Compared to static Star or fixed hierarchical structures, dynamic topology maximizes resource utilization and supports flexible dynamic team formation while maintaining low-latency communication.

The core design includes four subsystems: Topology Discovery (how Agents find each other), Dynamic Connection Management (when to establish/disconnect connections), Load-Aware Routing (how messages select the optimal path), and Topology Snapshot & Recovery (topology reconstruction after failures).

---

## Design

### Overall Architecture

```
┌──────────────────────────────────────────────────────────┐
│                    Mesh Control Plane                     │
│  ┌─────────────┐ ┌──────────────┐ ┌───────────────────┐  │
│  │  Discovery   │ │  Connection  │ │  Load-Aware       │  │
│  │  Service     │ │  Manager     │ │  Router           │  │
│  └──────┬──────┘ └──────┬───────┘ └────────┬──────────┘  │
│         │               │                   │             │
│  ┌──────┴───────────────┴───────────────────┴──────────┐  │
│  │              Topology State Store                     │  │
│  │   (connection table, capability index, trust scores,  │  │
│  │    historical topologies)                             │  │
│  └─────────────────────────────────────────────────────┘  │
└──────────────────────────────────────────────────────────┘
                           |
┌──────────────────────────┴───────────────────────────────┐
│                    Data Plane                             │
│   Agent A ←──→ Agent B ←──→ Agent C                      │
│       ↑            ↑                                      │
│       └──── Agent D ────┘                                 │
│   (direct peer-to-peer communication, bypassing Control   │  │
│    Plane)                                                 │
└──────────────────────────────────────────────────────────┘
```

### 1. Topology Discovery (Discovery Service)

Agents must register their information when joining the Mesh and be able to discover other Agents.

**Registration Information**:

```typescript
interface AgentProfile {
  agentId: string;
  capabilities: Capability[];
  status: AgentStatus;
  load: LoadMetrics;
  endpoint: string;           // communication endpoint
  metadata: {
    version: string;
    factoryId: string;        // Associated Factory
    teamId?: string;          // currently assigned team
    maxConcurrentTasks: number;
  };
}

interface Capability {
  name: string;               // e.g., "architecture", "code_review"
  version: string;
  confidence: number;         // 0-1, capability confidence level
  lastUsed: number;           // timestamp
  successRate: number;        // historical success rate
}

type AgentStatus = "online" | "busy" | "degraded" | "offline";

interface LoadMetrics {
  cpuUsage: number;           // 0-1
  memoryUsage: number;        // 0-1
  queueLength: number;
  activeTasks: number;
  availableSlots: number;
}
```

**Discovery Mechanism**:

```typescript
interface DiscoveryService {
  // Agent registration
  register(profile: AgentProfile): Promise<RegistrationResult>;

  // Agent deregistration
  deregister(agentId: string): Promise<void>;

  // Update status (heartbeat)
  heartbeat(agentId: string, update: Partial<AgentProfile>): Promise<void>;

  // Search Agents by capability
  discoverByCapability(
    required: string[],
    options?: {
      minConfidence?: number;
      maxLoad?: number;
      excludeAgents?: string[];
      preferLowLatency?: boolean;
    }
  ): Promise<AgentProfile[]>;

  // Get topology snapshot
  getTopologySnapshot(): Promise<TopologySnapshot>;

  // Subscribe to topology changes
  onTopologyChange(
    callback: (event: TopologyChangeEvent) => void
  ): UnsubscribeFn;
}

interface RegistrationResult {
  agentId: string;
  assignedCluster: string;
  peers: AgentProfile[];      // initial neighbor list
}

type TopologyChangeEvent =
  | { type: "agent_joined"; agent: AgentProfile }
  | { type: "agent_left"; agentId: string }
  | { type: "capability_changed"; agentId: string; capabilities: Capability[] }
  | { type: "status_changed"; agentId: string; oldStatus: AgentStatus; newStatus: AgentStatus }
  | { type: "connection_established"; source: string; target: string }
  | { type: "connection_dropped"; source: string; target: string };
```

### 2. Dynamic Connection Management (Connection Manager)

Controls the establishment and disconnection of connections between Agents.

**Connection Decision Engine**:

```typescript
interface ConnectionManager {
  // Evaluate whether a connection should be established
  evaluateConnection(source: string, target: string): ConnectionDecision;

  // Establish a connection
  connect(source: string, target: string, reason: ConnectionReason): Promise<Connection>;

  // Disconnect
  disconnect(source: string, target: string, reason: DisconnectReason): Promise<void>;

  // Get all connections for an Agent
  getConnections(agentId: string): Connection[];

  // Get connection statistics
  getConnectionStats(agentId: string): ConnectionStats;
}

interface ConnectionDecision {
  shouldConnect: boolean;
  score: number;              // connection necessity score 0-1
  reason: string;
  estimatedBenefit: number;   // estimated benefit
  estimatedCost: number;      // estimated cost (resource consumption)
}

type ConnectionReason =
  | { type: "task_dependency"; taskId: string }
  | { type: "team_assignment"; teamId: string }
  | { type: "high_communication_frequency"; messagesPerMinute: number }
  | { type: "capability_request"; capability: string }
  | { type: "trust_based"; trustScore: number };

type DisconnectReason =
  | { type: "task_completed"; taskId: string }
  | { type: "idle_timeout"; idleDurationMs: number }
  | { type: "low_communication_frequency"; messagesPerMinute: number }
  | { type: "load_shedding"; overloadedAgent: string }
  | { type: "trust_degraded"; trustScore: number };

interface Connection {
  id: string;
  source: string;
  target: string;
  reason: ConnectionReason;
  establishedAt: number;
  stats: {
    messagesSent: number;
    messagesReceived: number;
    avgLatencyMs: number;
    lastActivityAt: number;
  };
  priority: "critical" | "high" | "normal" | "low";
}

interface ConnectionStats {
  totalConnections: number;
  activeConnections: number;
  avgLatencyMs: number;
  messagesPerMinute: number;
  topConnections: Connection[];  // sorted by communication frequency
}
```

**Connection Strategy Pseudocode**:

```typescript
class ConnectionPolicyEngine {
  // Evaluate whether a connection should be established between two Agents
  evaluateConnection(source: AgentProfile, target: AgentProfile): ConnectionDecision {
    const factors: number[] = [];

    // Factor 1: Task dependency (weight 0.4)
    const taskDependency = this.calculateTaskDependency(source, target);
    factors.push(taskDependency * 0.4);

    // Factor 2: Capability complementarity (weight 0.2)
    const capabilityComplement = this.calculateCapabilityComplement(source, target);
    factors.push(capabilityComplement * 0.2);

    // Factor 3: Communication frequency trend (weight 0.2)
    const commFrequency = this.getRecentCommFrequency(source.agentId, target.agentId);
    factors.push(Math.min(commFrequency / 10, 1) * 0.2);

    // Factor 4: Trust score (weight 0.1)
    const trust = this.getTrustScore(source.agentId, target.agentId);
    factors.push(trust * 0.1);

    // Factor 5: Load feasibility (weight 0.1)
    const loadFeasibility = this.calculateLoadFeasibility(source, target);
    factors.push(loadFeasibility * 0.1);

    const score = factors.reduce((a, b) => a + b, 0);

    return {
      shouldConnect: score > 0.5 && loadFeasibility > 0.3,
      score,
      reason: this.generateReason(factors),
      estimatedBenefit: score,
      estimatedCost: 1 - loadFeasibility,
    };
  }

  // Periodically prune idle connections
  pruneIdleConnections(): DisconnectRecommendation[] {
    const recommendations: DisconnectRecommendation[] = [];

    for (const conn of this.getAllConnections()) {
      const idleDuration = Date.now() - conn.stats.lastActivityAt;
      const idleThreshold = this.getIdleThreshold(conn.priority);

      if (idleDuration > idleThreshold) {
        recommendations.push({
          connectionId: conn.id,
          reason: { type: "idle_timeout", idleDurationMs: idleDuration },
          confidence: Math.min(idleDuration / (idleThreshold * 2), 1),
        });
      }
    }

    return recommendations;
  }

  private getIdleThreshold(priority: Connection["priority"]): number {
    switch (priority) {
      case "critical": return 30 * 60 * 1000;   // 30 minutes
      case "high":     return 15 * 60 * 1000;   // 15 minutes
      case "normal":   return 5 * 60 * 1000;    // 5 minutes
      case "low":      return 1 * 60 * 1000;    // 1 minute
    }
  }
}
```

### 3. Load-Aware Router

Messages select the optimal path through the Mesh.

```typescript
interface MeshRouter {
  // Send a message (automatically selects route)
  route(message: MeshMessage): Promise<RoutingResult>;

  // Get routing information
  getRoute(source: string, target: string): Route;

  // Get routing statistics
  getRoutingStats(): RoutingStats;
}

interface MeshMessage {
  id: string;
  source: string;
  target: string;
  payload: unknown;
  priority: MessagePriority;
  constraints?: {
    maxLatencyMs?: number;
    requiresEncryption?: boolean;
    preferredPath?: string[];  // hinted path
  };
}

interface Route {
  source: string;
  target: string;
  path: string[];              // list of Agents traversed
  estimatedLatencyMs: number;
  hops: number;
  pathType: "direct" | "relay";
}

interface RoutingResult {
  success: boolean;
  route: Route;
  actualLatencyMs: number;
  retries: number;
}

interface RoutingStats {
  totalMessagesRouted: number;
  avgLatencyMs: number;
  directRouteRatio: number;    // direct connection ratio
  relayRouteRatio: number;     // relay ratio
  droppedMessages: number;
}
```

**Routing Strategy Pseudocode**:

```typescript
class LoadAwareRouter implements MeshRouter {
  async route(message: MeshMessage): Promise<RoutingResult> {
    // 1. Try direct route
    const directRoute = this.findDirectRoute(message.source, message.target);
    if (directRoute && this.isRouteFeasible(directRoute, message)) {
      return this.sendViaRoute(message, directRoute);
    }

    // 2. Find relay routes
    const relayRoutes = this.findRelayRoutes(message.source, message.target);
    const feasibleRoutes = relayRoutes
      .filter(r => this.isRouteFeasible(r, message))
      .sort((a, b) => this.scoreRoute(a, message) - this.scoreRoute(b, message));

    if (feasibleRoutes.length > 0) {
      return this.sendViaRoute(message, feasibleRoutes[0]);
    }

    // 3. Find target via Discovery Service
    const targetProfile = await this.discovery.discoverByCapability(
      [], // search by Agent ID
      { excludeAgents: [] }
    ).then(agents => agents.find(a => a.agentId === message.target));

    if (targetProfile) {
      // Establish temporary connection and send
      await this.connectionManager.connect(
        message.source,
        message.target,
        { type: "capability_request", capability: "direct_message" }
      );
      return this.route(message); // re-route
    }

    // 4. Routing failed
    return {
      success: false,
      route: { source: message.source, target: message.target, path: [], estimatedLatencyMs: Infinity, hops: 0, pathType: "direct" },
      actualLatencyMs: 0,
      retries: 0,
    };
  }

  // Route scoring: comprehensively consider latency, load, and reliability
  private scoreRoute(route: Route, message: MeshMessage): number {
    const latencyScore = 1 - Math.min(route.estimatedLatencyMs / 5000, 1);
    const hopPenalty = route.hops * 0.1;
    const loadScore = this.calculatePathLoadScore(route);
    const reliabilityScore = this.calculatePathReliability(route);

    return latencyScore * 0.4 + loadScore * 0.3 + reliabilityScore * 0.3 - hopPenalty;
  }

  private calculatePathLoadScore(route: Route): number {
    const agents = route.path;
    if (agents.length === 0) return 1;

    const avgLoad = agents.reduce((sum, agentId) => {
      const profile = this.getAgentProfile(agentId);
      return sum + (profile?.load.cpuUsage ?? 1);
    }, 0) / agents.length;

    return 1 - avgLoad; // lower load = higher score
  }
}
```

### 4. Topology Snapshot & Recovery

```typescript
interface TopologySnapshot {
  timestamp: number;
  version: number;
  agents: AgentProfile[];
  connections: Connection[];
  teams: TeamSnapshot[];
  routingTable: Record<string, Route[]>;
}

interface TeamSnapshot {
  teamId: string;
  members: string[];
  taskIds: string[];
  leader: string;
  createdAt: number;
}

interface TopologyRecoveryService {
  // Save current topology snapshot
  saveSnapshot(): Promise<TopologySnapshot>;

  // Restore from snapshot
  restoreFromSnapshot(snapshot: TopologySnapshot): Promise<RecoveryResult>;

  // Incremental update
  applyDelta(delta: TopologyDelta): Promise<void>;
}

interface TopologyDelta {
  version: number;
  changes: TopologyChangeEvent[];
}

interface RecoveryResult {
  success: boolean;
  recoveredAgents: number;
  recoveredConnections: number;
  failedRecoveries: { agentId: string; reason: string }[];
  durationMs: number;
}
```

---

## Topology Change Flow Example

```
Time T0: Initial state
  Agents: [A, B, C]
  Connections: [A-B, A-C]

Time T1: New task arrives, needs D's capability
  1. Discovery finds D and registers it
  2. ConnectionManager evaluates connection necessity for A-D, B-D, C-D
  3. Establish critical connections: A-D (task dependency), B-D (capability complementarity)
  4. Do not establish C-D (score below threshold)

Time T2: Task completed
  1. ConnectionManager marks A-D, B-D for cleanup
  2. Wait for idle_timeout
  3. Disconnect
  4. If D has no tasks for a long time -> mark as idle -> can be reclaimed

Time T3: Agent B crashes
  1. Health check detects B is unreachable
  2. TopologyRecoveryService loads the latest snapshot
  3. Migrate B's tasks to other Agents
  4. Update topology: remove all of B's connections
  5. Notify related Agents
```

---

## Advantages

1. **Resource Efficiency**: Only necessary connections are established, avoiding O(n^2) full-mesh overhead
2. **Flexibility**: Topology changes dynamically with tasks, adapting to different scenarios
3. **Scalability**: Supports large-scale Agent networks through relay routing
4. **Fault Tolerance**: Topology snapshots + automatic recovery; single-point failures do not affect the whole
5. **Alignment with Heya's Philosophy**: "AI transforms itself" -- the Mesh structure itself is the result of AI self-organization

## Risks

1. **Topology Oscillation**: Frequent connection establishment/disconnection causing instability
   - Mitigation: Set minimum connection lifetime (e.g., 30 seconds) to avoid frequent switching
2. **Routing Latency**: Relay paths increase communication latency
   - Mitigation: Prefer direct connections, relay only as fallback; maintain persistent connections between hot-spot Agents
3. **Implementation Complexity**: Dynamic topology control plane logic is complex
   - Mitigation: Phased implementation; first support static topology + dynamic extension, then evolve to fully dynamic
4. **Debugging Difficulty**: Constantly changing topology makes issues hard to reproduce
   - Mitigation: Topology snapshots + change logs + visualization tools

---

## Open Decisions

- [ ] Should Discovery Service be centralized (registry) or decentralized (Gossip protocol)?
- [ ] What should the minimum connection lifetime be?
- [ ] What is the maximum hop count limit for relay routing?
- [ ] How frequently should topology snapshots be saved?
- [ ] Should cross-Factory Agent discovery and connection be supported?
- [ ] Should trust scores be factored into connection decisions? (Related to P009)
