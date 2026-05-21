# Proposal P008: Standardized Communication Protocol

> **Status**: Draft
> **Proposed by**: AI Assistant
> **Date**: 2026-05-16
> **Related**: ROUND-003 brainstorm, P007 (Dynamic Topology), PAPERS/02-multi-agent-coordination.md (TarMAC)

---

## Summary

This proposal defines a standardized communication protocol for the Agent Mesh, covering message format, priority queues, backpressure mechanisms, broadcast/unicast/multicast modes, and complete message lifecycle management. The protocol design draws on TarMAC's targeted communication philosophy while meeting Heya's requirements for reliability, traceability, and human reviewability.

---

## Design

### Overall Architecture

```
┌──────────────────────────────────────────────────────────────┐
│                    Agent Communication Layer                  │
│                                                              │
│  ┌──────────┐  ┌──────────────┐  ┌────────────────────────┐  │
│  │ Message  │  │ Priority     │  │ Backpressure           │  │
│  │ Serializer│ │ Queue        │  │ Controller             │  │
│  └────┬─────┘  └──────┬───────┘  └───────────┬────────────┘  │
│       │               │                       │               │
│  ┌────┴───────────────┴───────────────────────┴────────────┐  │
│  │                  Message Dispatcher                      │  │
│  │  ┌─────────┐  ┌──────────┐  ┌──────────────┐           │  │
│  │  │ Unicast │  │ Multicast│  │ Broadcast    │           │  │
│  │  │ Channel │  │ Channel  │  │ Channel      │           │  │
│  │  └─────────┘  └──────────┘  └──────────────┘           │  │
│  └─────────────────────────────────────────────────────────┘  │
│                           │                                  │
│  ┌────────────────────────┴───────────────────────────────┐  │
│  │              Transport Layer                            │  │
│  │  (TCP/WebSocket/gRPC - pluggable transport)            │  │
│  └────────────────────────────────────────────────────────┘  │
└──────────────────────────────────────────────────────────────┘
```

### 1. Message Format Definition

All inter-Agent communication uses a unified message format:

```typescript
// ============ Core Message Types ============

type MessagePriority = "critical" | "high" | "normal" | "low";

type MessageType =
  | "task_request"        // request task execution
  | "task_result"         // task execution result
  | "task_progress"       // task progress update
  | "query"               // query request
  | "query_response"      // query response
  | "event"               // event notification (asynchronous)
  | "control"             // control command (pause, resume, cancel)
  | "heartbeat"           // heartbeat
  | "discovery"           // discovery request/response
  | "error"               // error notification
  | "ack"                 // acknowledgment
  | "human_checkpoint"    // request human approval
  | "topology_change";    // topology change notification

interface MeshMessage {
  // === Message Identity ===
  id: string;                          // globally unique message ID (UUID v7, time-ordered)
  type: MessageType;
  priority: MessagePriority;

  // === Addressing ===
  source: AgentAddress;
  target: TargetAddress;              // unicast / multicast / broadcast

  // === Business Payload ===
  payload: MessagePayload;

  // === Context ===
  context: MessageContext;

  // === Reliability ===
  reliability: ReliabilityConfig;

  // === Metadata ===
  metadata: MessageMetadata;
}

// ============ Addressing ============

interface AgentAddress {
  agentId: string;
  role?: string;                      // current role (e.g., "architect")
  factoryId: string;
  endpoint?: string;                  // communication endpoint
}

type TargetAddress =
  | { mode: "unicast"; target: AgentAddress }
  | { mode: "multicast"; targets: AgentAddress[]; groupId?: string }
  | { mode: "broadcast"; factoryId: string };

// ============ Payload ============

type MessagePayload =
  | TaskRequestPayload
  | TaskResultPayload
  | TaskProgressPayload
  | QueryPayload
  | QueryResponsePayload
  | EventPayload
  | ControlPayload
  | HeartbeatPayload
  | DiscoveryPayload
  | ErrorPayload
  | AckPayload
  | HumanCheckpointPayload
  | TopologyChangePayload;

// --- Task Request ---
interface TaskRequestPayload {
  taskId: string;
  taskType: string;                   // e.g., "code_generation", "design_review"
  description: string;
  requirements: {
    capabilities: string[];
    inputs: Record<string, unknown>;
    expectedOutputs: string[];
    constraints?: Record<string, unknown>;
  };
  deadline?: number;                  // deadline timestamp
  budget?: ResourceBudget;
}

// --- Task Result ---
interface TaskResultPayload {
  taskId: string;
  status: "success" | "partial" | "failed";
  outputs: Record<string, unknown>;
  artifacts: ArtifactReference[];     // output artifact references
  metrics: {
    durationMs: number;
    tokensUsed: number;
    cost: number;
  };
  error?: {
    code: string;
    message: string;
    stack?: string;
    recoverable: boolean;
  };
}

// --- Task Progress ---
interface TaskProgressPayload {
  taskId: string;
  phase: string;                      // current phase
  progress: number;                   // 0-1
  message: string;
  estimatedRemainingMs?: number;
}

// --- Event Notification ---
interface EventPayload {
  eventType: string;                  // e.g., "task_started", "checkpoint_reached"
  data: Record<string, unknown>;
}

// --- Control Command ---
interface ControlPayload {
  command: "pause" | "resume" | "cancel" | "reroute" | "escalate";
  targetTaskId?: string;
  reason: string;
  parameters?: Record<string, unknown>;
}

// --- Human Approval ---
interface HumanCheckpointPayload {
  checkpointId: string;
  workOrderId: string;
  taskId: string;
  checkpointType: "approval" | "review" | "decision";
  subject: string;
  options?: string[];                 // options for human to choose from
  context: {
    whatWasDone: string;
    whyNeedsApproval: string;
    impact: string;
    recommendedAction?: string;
  };
  deadline?: number;
}

// ============ Context ============

interface MessageContext {
  workOrderId: string;                // Associated WorkOrder
  correlationId: string;              // correlates multiple messages in the same task chain
  causationId?: string;               // causal chain: source message ID that triggered this message
  parentTaskId?: string;              // parent task ID (subtask scenario)
  timestamp: number;                  // send time
  ttl: number;                        // message validity period (ms), 0 = never expires
  traceId: string;                    // distributed tracing ID
}

// ============ Reliability Config ============

interface ReliabilityConfig {
  deliveryGuarantee: "at_least_once" | "at_most_once" | "exactly_once";
  requiresAck: boolean;
  retryPolicy: RetryPolicy;
  idempotencyKey?: string;            // idempotency key for deduplication
}

interface RetryPolicy {
  maxRetries: number;
  backoffStrategy: "fixed" | "exponential" | "adaptive";
  initialDelayMs: number;
  maxDelayMs: number;
  jitter: boolean;                    // whether to add random jitter
}

// ============ Metadata ============

interface MessageMetadata {
  schemaVersion: string;              // message format version
  sizeBytes: number;
  compression: "none" | "gzip" | "brotli";
  encrypted: boolean;
  routing: {
    preferredPath?: string[];          // hinted routing path
    maxHops?: number;                 // maximum relay hops
  };
  custom?: Record<string, unknown>;   // extension fields
}
```

### 2. Priority Queue

Each Agent maintains a local priority message queue:

```typescript
interface PriorityMessageQueue {
  // Enqueue
  enqueue(message: MeshMessage): void;

  // Dequeue (returns the highest-priority message)
  dequeue(): MeshMessage | null;

  // Batch dequeue
  dequeueBatch(maxCount: number): MeshMessage[];

  // View queue status
  getStatus(): QueueStatus;

  // Shed low-priority messages (used during backpressure)
  shedLowPriority(count: number): number;

  // Peek by condition
  peek(predicate?: (msg: MeshMessage) => boolean): MeshMessage | null;

  // Register backpressure callback
  onBackpressure(callback: (event: BackpressureEvent) => void): UnsubscribeFn;
}

interface QueueStatus {
  totalMessages: number;
  byPriority: Record<MessagePriority, number>;
  oldestMessageAgeMs: number;
  processingRate: number;             // messages/second
  isUnderBackpressure: boolean;
}

interface BackpressureEvent {
  agentId: string;
  queueUtilization: number;           // 0-1
  threshold: number;
  action: "notify" | "shed" | "reject";
  shedCount?: number;
}
```

**Priority Queue Implementation Pseudocode**:

```typescript
class AgentPriorityQueue implements PriorityMessageQueue {
  private queues: Map<MessagePriority, MeshMessage[]> = new Map();
  private maxSize: number;
  private backpressureThreshold: number;  // e.g., 0.8

  constructor(config: { maxSize: number; backpressureThreshold: number }) {
    this.maxSize = config.maxSize;
    this.backpressureThreshold = config.backpressureThreshold;
    // Initialize queues by priority
    const priorities: MessagePriority[] = ["critical", "high", "normal", "low"];
    for (const p of priorities) {
      this.queues.set(p, []);
    }
  }

  enqueue(message: MeshMessage): void {
    const queue = this.queues.get(message.priority)!;
    const totalSize = this.getTotalSize();

    // Check if backpressure is needed
    if (totalSize / this.maxSize > this.backpressureThreshold) {
      this.triggerBackpressure(message);
    }

    // When queue is full, shed low-priority messages
    if (totalSize >= this.maxSize) {
      if (message.priority === "low" || message.priority === "normal") {
        this.shedLowPriority(1);
      } else {
        // Critical/High messages are not allowed to be dropped; block and wait
        this.waitForSpace().then(() => queue.push(message));
        return;
      }
    }

    // Insert in timestamp order
    const insertIdx = queue.findIndex(m => m.context.timestamp > message.context.timestamp);
    if (insertIdx === -1) {
      queue.push(message);
    } else {
      queue.splice(insertIdx, 0, message);
    }
  }

  dequeue(): MeshMessage | null {
    // Search in priority order
    const priorities: MessagePriority[] = ["critical", "high", "normal", "low"];
    for (const p of priorities) {
      const queue = this.queues.get(p)!;
      if (queue.length > 0) {
        return queue.shift()!;
      }
    }
    return null;
  }

  shedLowPriority(count: number): number {
    let shed = 0;
    // Shed low first, then normal
    for (const priority of ["low", "normal"] as MessagePriority[]) {
      const queue = this.queues.get(priority)!;
      while (queue.length > 0 && shed < count) {
        const removed = queue.shift()!;
        this.moveToDeadLetterQueue(removed);
        shed++;
      }
    }
    return shed;
  }

  private triggerBackpressure(incomingMessage: MeshMessage): void {
    const utilization = this.getTotalSize() / this.maxSize;
    const event: BackpressureEvent = {
      agentId: incomingMessage.source.agentId,
      queueUtilization: utilization,
      threshold: this.backpressureThreshold,
      action: utilization > 0.95 ? "reject" : "notify",
    };
    this.backpressureCallbacks.forEach(cb => cb(event));
  }
}
```

### 3. Backpressure Mechanism

The backpressure mechanism prevents downstream Agents from being overwhelmed by upstream messages.

```typescript
interface BackpressureController {
  // Get the current backpressure state of an Agent
  getBackpressureState(agentId: string): BackpressureState;

  // Upstream Agent checks whether it can send to the target
  canSend(sourceId: string, targetId: string, priority: MessagePriority): CanSendResult;

  // Register backpressure listener
  onBackpressureChange(agentId: string, callback: (state: BackpressureState) => void): UnsubscribeFn;
}

interface BackpressureState {
  agentId: string;
  level: "green" | "yellow" | "orange" | "red";
  queueUtilization: number;           // 0-1
  processingRate: number;             // messages/second
  incomingRate: number;               // messages/second
  estimatedRecoveryTimeMs?: number;   // estimated time to recover
}

interface CanSendResult {
  allowed: boolean;
  reason?: string;
  suggestedDelayMs?: number;          // suggested send delay
  alternativeTarget?: string;         // suggested alternative Agent
}

// Backpressure level definitions
const BACKPRESSURE_LEVELS = {
  green:  { threshold: 0.5,  action: "none" },
  yellow: { threshold: 0.7,  action: "notify_upstream" },
  orange: { threshold: 0.85, action: "throttle_normal_low" },
  red:    { threshold: 0.95, action: "reject_normal_low" },
} as const;
```

**Backpressure Propagation Pseudocode**:

```typescript
class BackpressureControllerImpl implements BackpressureController {
  canSend(sourceId: string, targetId: string, priority: MessagePriority): CanSendResult {
    const state = this.getBackpressureState(targetId);

    switch (state.level) {
      case "green":
        return { allowed: true };

      case "yellow":
        // Notify only, but allow sending
        return { allowed: true, reason: "target approaching capacity" };

      case "orange":
        // Throttle Normal and Low priority
        if (priority === "normal" || priority === "low") {
          return {
            allowed: false,
            reason: "target under backpressure",
            suggestedDelayMs: 5000,
          };
        }
        return { allowed: true };

      case "red":
        // Only allow Critical
        if (priority === "critical") {
          return { allowed: true, reason: "critical message allowed during red backpressure" };
        }
        return {
          allowed: false,
          reason: "target at capacity",
          suggestedDelayMs: 15000,
          alternativeTarget: this.findAlternativeAgent(targetId),
        };
    }
  }
}
```

### 4. Broadcast / Unicast / Multicast

```typescript
interface MessageDispatcher {
  // Unicast: send to a specific Agent
  unicast(message: MeshMessage): Promise<DeliveryReceipt[]>;

  // Multicast: send to a group of Agents
  multicast(message: MeshMessage): Promise<DeliveryReceipt[]>;

  // Broadcast: send to all Agents in the Factory
  broadcast(message: MeshMessage): Promise<DeliveryReceipt[]>;

  // Subscribe to a multicast group
  joinGroup(agentId: string, groupId: string): Promise<void>;

  // Leave a multicast group
  leaveGroup(agentId: string, groupId: string): Promise<void>;

  // Get multicast group members
  getGroupMembers(groupId: string): AgentAddress[];
}

interface DeliveryReceipt {
  messageId: string;
  targetAgentId: string;
  status: "delivered" | "pending" | "failed" | "rejected";
  timestamp: number;
  error?: string;
  latencyMs?: number;
}
```

**Multicast Group Management Pseudocode**:

```typescript
class MulticastGroupManager {
  private groups: Map<string, Set<string>> = new Map();

  // Predefined groups (based on roles)
  private readonly ROLE_GROUPS: Record<string, string[]> = {
    "role:engineer": [],     // dynamically populated
    "role:architect": [],
    "role:qa": [],
    "role:designer": [],
    "team:*": [],            // wildcard: all teams
  };

  joinGroup(agentId: string, groupId: string): void {
    if (!this.groups.has(groupId)) {
      this.groups.set(groupId, new Set());
    }
    this.groups.get(groupId)!.add(agentId);
  }

  leaveGroup(agentId: string, groupId: string): void {
    this.groups.get(groupId)?.delete(agentId);
  }

  getGroupMembers(groupId: string): string[] {
    // Support wildcard matching
    if (groupId.endsWith("*")) {
      const prefix = groupId.slice(0, -1);
      const members: string[] = [];
      for (const [gid, agents] of this.groups) {
        if (gid.startsWith(prefix)) {
          members.push(...agents);
        }
      }
      return [...new Set(members)];
    }
    return [...(this.groups.get(groupId) ?? [])];
  }

  // Automatically update groups when Agent roles change
  onRoleChange(agentId: string, oldRole?: string, newRole?: string): void {
    if (oldRole) {
      this.leaveGroup(agentId, `role:${oldRole}`);
    }
    if (newRole) {
      this.joinGroup(agentId, `role:${newRole}`);
    }
  }
}
```

### 5. Message Lifecycle Management

```typescript
interface MessageLifecycleManager {
  // Send a message
  send(message: MeshMessage): Promise<SendResult>;

  // Receive message acknowledgment
  acknowledge(messageId: string, targetAgentId: string): void;

  // Get message status
  getMessageStatus(messageId: string): MessageStatus;

  // Retry a failed message
  retry(messageId: string): Promise<SendResult>;

  // Cancel a message
  cancel(messageId: string): void;

  // Purge expired messages
  purgeExpired(): number;

  // Get dead letter queue
  getDeadLetterQueue(limit?: number): DeadLetterEntry[];
}

interface SendResult {
  messageId: string;
  status: "sent" | "queued" | "failed";
  deliveryReceipts: DeliveryReceipt[];
  timestamp: number;
}

interface MessageStatus {
  messageId: string;
  state: "pending" | "sent" | "delivered" | "acknowledged" | "failed" | "expired" | "cancelled";
  createdAt: number;
  sentAt?: number;
  deliveredAt?: number;
  acknowledgedAt?: number;
  failedAt?: number;
  retryCount: number;
  lastError?: string;
  deliveryReceipts: DeliveryReceipt[];
}

interface DeadLetterEntry {
  message: MeshMessage;
  failureReason: string;
  failedAt: number;
  retryCount: number;
  originalTarget: AgentAddress;
}
```

**Message Send Flow Pseudocode**:

```typescript
class MessageLifecycleManagerImpl implements MessageLifecycleManager {
  async send(message: MeshMessage): Promise<SendResult> {
    // 1. Validate message format
    this.validateMessage(message);

    // 2. Check TTL
    if (message.context.ttl > 0) {
      const age = Date.now() - message.context.timestamp;
      if (age > message.context.ttl) {
        return { messageId: message.id, status: "failed", deliveryReceipts: [], timestamp: Date.now() };
      }
    }

    // 3. Check backpressure
    if (message.target.mode === "unicast") {
      const bpResult = this.backpressureController.canSend(
        message.source.agentId,
        message.target.target.agentId,
        message.priority
      );
      if (!bpResult.allowed) {
        // Based on configuration: queue and wait or fail immediately
        if (message.priority === "critical") {
          await this.waitForBackpressureRelief(message.target.target.agentId);
        } else {
          return { messageId: message.id, status: "failed", deliveryReceipts: [], timestamp: Date.now() };
        }
      }
    }

    // 4. Serialize + compress
    const serialized = this.serializer.serialize(message);

    // 5. Route and send
    let receipts: DeliveryReceipt[];
    switch (message.target.mode) {
      case "unicast":
        receipts = await this.dispatcher.unicast(message);
        break;
      case "multicast":
        receipts = await this.dispatcher.multicast(message);
        break;
      case "broadcast":
        receipts = await this.dispatcher.broadcast(message);
        break;
    }

    // 6. Record message status
    this.recordMessageStatus(message, receipts);

    // 7. Handle messages requiring ACK
    if (message.reliability.requiresAck) {
      this.startAckTimer(message, receipts);
    }

    return {
      messageId: message.id,
      status: "sent",
      deliveryReceipts: receipts,
      timestamp: Date.now(),
    };
  }

  // ACK timeout handling
  private startAckTimer(message: MeshMessage, receipts: DeliveryReceipt[]): void {
    const pendingReceipts = receipts.filter(r => r.status === "pending");
    const timeout = message.reliability.retryPolicy.initialDelayMs;

    setTimeout(() => {
      const unacknowledged = pendingReceipts.filter(r =>
        this.getMessageStatus(message.id).state !== "acknowledged"
      );

      if (unacknowledged.length > 0) {
        const retryCount = this.getRetryCount(message.id);
        if (retryCount < message.reliability.retryPolicy.maxRetries) {
          this.retry(message.id);
        } else {
          this.moveToDeadLetterQueue(message, "max retries exceeded");
        }
      }
    }, timeout);
  }
}
```

---

## Message Flow Examples

### Example 1: Task Assignment (Unicast + Synchronous Wait)

```
Architect -> Engineer: task_request (priority: high, requiresAck: true)
  |
  v
Engineer receives message, joins queue
  |
  v
Engineer dequeues and processes, sends ack
  |
  v
Architect receives ack, marks as delivered
  |
  v
Engineer completes, sends task_result (priority: normal)
  |
  v
Architect receives result, verifies, marks as acknowledged
```

### Example 2: System Notification (Broadcast + Asynchronous)

```
Orchestrator -> All Agents: control (command: "pause", priority: critical)
  |
  v
All Agents receive the message
  |
  v
Each Agent independently sends ack
  |
  v
Orchestrator collects all acks, confirms all are paused
```

### Example 3: Backpressure Scenario

```
Agent-A sends a normal message to Agent-B
  |
  v
Agent-B queue utilization at 90% (orange level)
  |
  v
BackpressureController returns: allowed=false, suggestedDelayMs=5000
  |
  v
Agent-A waits 5 seconds and retries
  |
  v
Agent-B queue drops to 60% (green level)
  |
  v
Agent-A sends successfully
```

---

## Advantages

1. **Standardization**: Unified message format; all Agents use the same protocol, reducing integration costs
2. **Reliability**: At-least-once delivery + ACK + retry + dead letter queue ensures messages are not lost
3. **Controllability**: Four-level priority + backpressure mechanism prevents system overload
4. **Flexibility**: Unicast/multicast/broadcast modes cover all communication scenarios
5. **Observability**: Complete lifecycle of every message is traceable, supporting debugging and auditing
6. **Alignment with TarMAC**: Targeted communication + attention selection = on-demand communication + priority routing

## Risks

1. **Protocol Complexity**: A complete message protocol is complex to implement and may introduce bugs
   - Mitigation: Phased implementation; first support core message types, then gradually expand
2. **Performance Overhead**: Message serialization, compression, and ACK mechanisms add latency
   - Mitigation: Use the full protocol for Critical/High messages; simplify processing for Low messages
3. **Backpressure Propagation Delay**: Backpressure signals take time to propagate, potentially causing brief overload
   - Mitigation: Predictive backpressure (forecast queue growth trends based on historical data)
4. **Dead Letter Queue Accumulation**: If many messages enter the DLQ, human intervention may be needed
   - Mitigation: DLQ alerts + automatic failure analysis + human approval

---

## Open Decisions

- [ ] Message serialization format: JSON vs Protocol Buffers vs MessagePack?
- [ ] Message transport layer: WebSocket vs gRPC vs custom?
- [ ] ACK timeout values: default timeouts for different priorities?
- [ ] Dead letter queue handling strategy: auto-retry vs human approval vs discard?
- [ ] Message size limit: maximum bytes per message?
- [ ] Should message encryption be supported (end-to-end encryption vs transport-layer encryption)?
- [ ] Multicast group lifecycle management: auto-destroy idle groups?
