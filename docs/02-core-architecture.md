# Core Architecture

> How the AI organizes itself into a factory, and how humans participate.

---

## 1. System Overview

Heya's architecture is built around a central principle: **the AI is the factory**. The system provides the infrastructure for an AI to transform itself into a purpose-built creation system, with structured human participation at critical points.

```
┌─────────────────────────────────────────────────────────────────────────┐
│                          HEYA PLATFORM                                   │
│                                                                          │
│  ┌────────────────────────────────────────────────────────────────────┐ │
│  │                    FACTORY DESIGNER                                │ │
│  │  (AI + Human collaboration)                                        │ │
│  │                                                                     │ │
│  │  User Intent → Clarification → Capability Derivation               │ │
│  │                → Factory Proposal → Human Approval                 │ │
│  └────────────────────────────────────────────────────────────────────┘ │
│                              │                                           │
│                              ▼                                           │
│  ┌────────────────────────────────────────────────────────────────────┐ │
│  │                    FACTORY RUNTIME                                  │ │
│  │  (AI-organized capabilities)                                       │ │
│  │                                                                     │ │
│  │  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌──────────┐          │ │
│  │  │ Reasoning│  │  Memory  │  │  Verify  │  │  Tools   │          │ │
│  │  │  Engine  │  │  System  │  │  System  │  │  System  │          │ │
│  │  └──────────┘  └──────────┘  └──────────┘  └──────────┘          │ │
│  │         │             │             │             │                │ │
│  │         └─────────────┴─────────────┴─────────────┘                │ │
│  │                            │                                        │ │
│  │  ┌──────────────────────────────────────────────────┐              │ │
│  │  │              HUMAN CHECKPOINT LAYER                │              │ │
│  │  │  Approval gates • Review requests • Decisions      │              │ │
│  │  └──────────────────────────────────────────────────┘              │ │
│  │                            │                                        │ │
│  │  ┌──────────┐  ┌──────────┐                                       │ │
│  │  │ Metacog  │  │  Model   │                                       │ │
│  │  │  Layer   │  │  Router  │                                       │ │
│  │  └──────────┘  └──────────┘                                       │ │
│  └────────────────────────────────────────────────────────────────────┘ │
│                              │                                           │
│  ┌────────────────────────────────────────────────────────────────────┐ │
│  │                      CONTRACT LAYER                                 │ │
│  │   FactoryBlueprint • WorkOrder • ArtifactReceipt • FactoryMemory   │ │
│  └────────────────────────────────────────────────────────────────────┘ │
│                              │                                           │
│  ┌────────────────────────────────────────────────────────────────────┐ │
│  │                    INFRASTRUCTURE SPI                               │ │
│  │   Model SPI • Storage SPI • Compute SPI • Network SPI              │ │
│  └────────────────────────────────────────────────────────────────────┘ │
└─────────────────────────────────────────────────────────────────────────┘
```

---

## 2. Factory Designer

**Purpose**: Transform user intent into a factory design through AI-human collaboration.

This is where the AI **designs itself**. When a user describes what they want to create, the Factory Designer:

### 2.1 Intent Understanding

```
User: "I want to build a game engine from scratch."

AI analyzes:
├── Domain: Game development
├── Product type: Software (complex system)
├── Key capabilities needed: Graphics, physics, audio, ECS architecture
├── Likely tools: Rust compiler, test frameworks, profilers
├── Verification needs: Compile, test, benchmark, performance regression
├── Human decision points: Architecture choices, API design, platform targets
└── Estimated complexity: High
```

### 2.2 Clarification

The AI asks targeted questions to resolve ambiguities:

```
AI: "I have a few questions before I design the factory:
     1. What rendering backends? (Vulkan, D3D12, Metal, or all?)
     2. What platforms? (Windows, Linux, console?)
     3. Do you need an editor, or just the runtime?
     4. What's your experience level with engine development?"
```

### 2.3 Capability Derivation

Based on the intent and answers, the AI derives the required capabilities:

```
Derived Factory Structure:
├── Reasoning: Architecture design, system decomposition, API design
├── Generation: Rust code, shader code, build scripts, tests
├── Verification: Compile, unit test, benchmark, regression
├── Memory: Architecture decisions, failure patterns, performance baselines
├── Tools: cargo (build/test), vulkan-sdk, benchmarking tools
├── Human Checkpoints:
│   ├── Architecture approval (before implementation)
│   ├── API design review (before public interfaces)
│   └── Final acceptance (before artifact release)
└── Model Routing:
    ├── Architecture tasks → GPT-5 / Claude Opus (deep reasoning)
    ├── Code generation → Claude Sonnet (strong code capability)
    └── Testing/verification → Local models (cost efficiency)
```

### 2.4 Factory Proposal

The AI presents the factory design for human approval:

```
AI: "Here's my proposed factory for your game engine project:

     📋 Factory: Game Engine Factory v1.0
     🎯 Goal: Build a cross-platform game engine in Rust

     Capabilities I'll assemble:
     • Architecture reasoning (using GPT-5)
     • Rust code generation (using Claude Sonnet)
     • Automated testing (compile + unit + benchmark)
     • Performance regression detection

     Where I'll need your input:
     1. Before I start: Approve the overall architecture
     2. During development: Review public API designs
     3. Before release: Final acceptance of each module

     Does this look right? Want me to adjust anything?"
```

---

## 3. Factory Runtime

Once the factory is designed and approved, the Factory Runtime executes it. The runtime is the AI organized into the factory structure.

### 3.1 Reasoning Engine

**Purpose**: The AI's core thinking capability, structured for the specific task.

The Reasoning Engine is not a separate component — it's the AI itself, operating with a specific reasoning strategy derived from the factory design.

**Reasoning Strategies:**

| Strategy | When Used | Description |
|----------|----------|-------------|
| `architect` | System design | Top-down decomposition, interface design, trade-off analysis |
| `implement` | Code/content creation | Bottom-up implementation, test-driven, iterative refinement |
| `analyze` | Understanding existing work | Pattern recognition, dependency mapping, gap identification |
| `debug` | Problem solving | Hypothesis generation, systematic elimination, root cause analysis |
| `review` | Quality assurance | Critical evaluation, comparison against standards, suggestion generation |

**Example — Architect Reasoning:**

```
Task: "Design the rendering pipeline"

AI reasoning chain:
1. What are the requirements? (from WorkOrder + Memory)
2. What are the constraints? (platform, performance, compatibility)
3. What are the options? (forward, deferred, forward+)
4. What are the trade-offs? (complexity vs flexibility vs performance)
5. What does memory say? (past decisions, failures, patterns)
6. What's my recommendation? (with rationale)
7. → PRESENT TO HUMAN FOR DECISION
```

### 3.2 Memory System

**Purpose**: Persistent, project-level knowledge that makes the factory smarter over time.

Memory is the AI's long-term knowledge — not a database it queries, but part of its thinking process.

**Memory Tiers:**

```
┌─────────────────────────────────────────────────────────────┐
│                    WORKING MEMORY                           │
│  Current task context, active conversation, recent outputs  │
│  Lifetime: Current session                                  │
│  Loaded into: AI context window                             │
└─────────────────────────────────────────────────────────────┘
                           │ consolidated after session
                           ▼
┌─────────────────────────────────────────────────────────────┐
│                    EPISODIC MEMORY                          │
│  Events: decisions made, failures encountered,              │
│  solutions found, human feedback received                   │
│  Lifetime: Project duration (configurable retention)        │
│  Retrieved by: Semantic search + time relevance             │
└─────────────────────────────────────────────────────────────┘
                           │ patterns extracted over time
                           ▼
┌─────────────────────────────────────────────────────────────┐
│                    SEMANTIC MEMORY                          │
│  Knowledge: domain facts, proven patterns,                  │
│  learned preferences, best practices                        │
│  Lifetime: Permanent (versioned)                             │
│  Retrieved by: Semantic similarity                          │
└─────────────────────────────────────────────────────────────┘
                           │
                           ▼
┌─────────────────────────────────────────────────────────────┐
│                    ARTIFACT MEMORY                          │
│  Produced outputs with full provenance and verification     │
│  Lifetime: Permanent                                         │
│  Retrieved by: Direct reference + dependency graph          │
└─────────────────────────────────────────────────────────────┘
```

**How Memory Influences AI Behavior:**

```
Without memory:
  AI: "I'll use OOP for the game objects."

With memory (recalling decision-47):
  AI: "Last time we discussed this, we chose ECS over OOP for better
       cache locality. Should we stick with that decision, or has
       something changed?"
```

### 3.3 Verification System

**Purpose**: Ensure outputs meet quality standards before human review.

Verification is the AI's **self-check mechanism** — it catches issues before they reach the human.

**Verification Levels:**

```
Level 0: Self-Check
    │ AI reviews its own output for obvious issues
    │ (format, completeness, consistency)
    │
    ▼
Level 1: Automated Validation
    │ Run tools: compile, lint, format check, schema validation
    │
    ▼
Level 2: Testing
    │ Run unit tests, integration tests
    │ Compare against baselines
    │
    ▼
Level 3: Benchmarking
    │ Performance tests, regression detection
    │ Golden sample comparison
    │
    ▼
Level 4: Human Review
    │ Present to human with context and recommendation
    │ Human approves, requests changes, or rejects
```

**Key principle**: AI never presents work to the human that hasn't passed at least Level 1. Humans only see pre-validated work.

### 3.4 Tool System

**Purpose**: Extend the AI's capabilities beyond pure reasoning.

Tools are the AI's "hands" — they let the AI do things it can't do with reasoning alone.

**Tool Categories:**

| Category | Examples | Human Approval |
|----------|----------|----------------|
| **Read** | File reader, code parser, web fetch | Never needed |
| **Build** | Compilers, bundlers, formatters | Never needed |
| **Test** | Test runners, linters, benchmarks | Never needed |
| **Write** | File writer, database write | On first use |
| **Deploy** | Docker, cloud deploy, publish | Always |
| **External** | HTTP API calls, email, notifications | Always |

**Tool Risk Model:**

```
Risk assessment is automatic:
├── Read-only operations → Execute immediately
├── Write to project → Execute with logging
├── External network → Ask human first time, remember preference
└── Irreversible → Always ask human
```

### 3.5 Human Checkpoint Layer

**Purpose**: Ensure humans make the decisions that matter.

This is what makes Heya a **hybrid** system, not an autonomous agent.

**Checkpoint Types:**

| Checkpoint | Trigger | What Human Sees |
|------------|---------|-----------------|
| `design_approval` | Before starting a new major component | Factory design, capability plan |
| `architecture_decision` | When multiple valid approaches exist | Options with trade-offs, AI recommendation |
| `critical_output` | Before releasing a significant artifact | The artifact + verification results + AI assessment |
| `direction_change` | When the AI detects the approach isn't working | Problem description, proposed pivot, alternatives |
| `budget_alert` | When approaching resource limits | Current spending, projected total, options |

**Checkpoint Flow:**

```
AI reaches a checkpoint
    │
    ▼
AI prepares context:
├── What it wants to do
├── Why (reasoning chain)
├── What it already tried
├── What the options are
├── What it recommends
└── What it needs from the human
    │
    ▼
Present to human (conversation or UI)
    │
    ▼
Human responds:
├── Approve → AI continues
├── Modify → AI adjusts and re-presents
├── Reject → AI tries alternative
└── Defer → AI proceeds with default (if allowed by policy)
```

### 3.6 Metacognition Layer

**Purpose**: The AI's ability to monitor and improve its own operation.

The Metacognition Layer watches the factory run and detects when things aren't working:

```
Signals it monitors:
├── Success rate (are outputs passing verification?)
├── Human feedback patterns (lots of corrections?)
├── Cost efficiency (spending too much on retries?)
├── Time efficiency (taking too long on simple tasks?)
└── Memory utilization (missing relevant past experiences?)

When a problem is detected:
1. Diagnose the likely cause
2. Propose an adjustment to the human
3. If approved, modify factory behavior
4. Remember what worked and what didn't
```

### 3.7 Model Router

**Purpose**: Select the optimal AI model for each subtask.

Different tasks need different models. The Router ensures cost-quality balance.

**Routing Logic:**

```
Task arrives
    │
    ▼
What type of task? (reasoning, generation, analysis, review)
    │
    ▼
What's the quality requirement? (critical, standard, low)
    │
    ▼
What's the cost budget? (from factory policy)
    │
    ▼
Select model:
├── Critical reasoning → Best available (GPT-5, Claude Opus)
├── Code generation → Strong coder (Claude Sonnet, GPT-4o)
├── Simple tasks → Fast & cheap (GPT-4o-mini, local models)
└── Fallback → If primary unavailable, use next best
```

---

## 4. Data Flow

### 4.1 Factory Design Flow

```
┌──────────────┐
│ User Intent  │
│ "Build X"    │
└──────────────┘
       │
       ▼
┌──────────────┐
│ AI           │
│ Understands  │
│ & Clarifies  │
└──────────────┘
       │
       ▼
┌──────────────┐
│ AI Derives   │
│ Capabilities │
└──────────────┘
       │
       ▼
┌──────────────┐
│ AI Proposes  │
│ Factory      │──────→ Human Reviews
│ Design       │←────── Human Approves/Adjusts
└──────────────┘
       │
       ▼
┌──────────────┐
│ Factory      │
│ Activated    │
└──────────────┘
```

### 4.2 WorkOrder Execution Flow

```
┌──────────────┐
│ WorkOrder    │
│ Submitted    │
└──────────────┘
       │
       ▼
┌──────────────┐
│ AI Loads     │
│ Relevant     │
│ Memory       │
└──────────────┘
       │
       ▼
┌──────────────┐
│ AI Executes  │◀─────────────────────────────┐
│ Task         │                               │
└──────────────┘                               │
       │                                       │
       ▼                                       │
┌──────────────┐                               │
│ AI Self-     │                               │
│ Verify       │                               │
└──────────────┘                               │
       │                                       │
       ▼                                       │
┌──────────────┐     ┌──────────────┐         │
│ Human        │────→│ Human        │         │
│ Checkpoint?  │     │ Reviews      │         │
└──────────────┘     └──────────────┘         │
       │ No                │                    │
       ▼                  ▼                    │
┌──────────────┐   ┌──────────────┐           │
│ Continue     │   │ Approved?   │──No────────┘
│              │   └──────────────┘   AI adjusts
└──────────────┘       │ Yes
                       ▼
              ┌──────────────┐
              │ Create       │
              │ Artifact     │
              │ Receipt      │
              └──────────────┘
                       │
                       ▼
              ┌──────────────┐
              │ Update       │
              │ Memory       │
              └──────────────┘
```

---

## 5. Infrastructure Abstraction

All infrastructure is accessed through Service Provider Interfaces (SPIs).

### 5.1 Model SPI

```
interface ModelProvider {
  id: string
  capabilities: ModelCapability[]

  invoke(request: ModelRequest): Promise<ModelResponse>
  estimateCost(request: ModelRequest): Cost
  isAvailable(): boolean
}
```

**Supported Providers:**
- OpenAI (GPT-4, GPT-5)
- Anthropic (Claude 3.5, Claude 4)
- Google (Gemini)
- Open Source (via Ollama, vLLM)
- Chinese Providers (Qwen, DeepSeek, GLM)

### 5.2 Storage SPI

```
interface StorageProvider {
  get(key: string): Promise<Buffer>
  set(key: string, value: Buffer, options?: SetOptions): Promise<void>
  query(query: StorageQuery): Promise<StorageResult[]>
  vectorSearch(vector: number[], options?: VectorSearchOptions): Promise<VectorSearchResult[]>
}
```

**Supported Backends:**
- PostgreSQL (with pgvector)
- Redis
- Qdrant
- S3-compatible object storage

### 5.3 Compute SPI

```
interface ComputeProvider {
  execute(spec: ExecutionSpec): Promise<ExecutionResult>
  getStatus(executionId: string): Promise<ExecutionStatus>
  cancel(executionId: string): Promise<void>
}
```

**Supported Environments:**
- Docker containers
- WASI runtimes
- Cloud functions

---

## 6. Scalability Considerations

### 6.1 Model Independence

The factory design is independent of any specific model. If a model becomes unavailable or a better one emerges, the Router switches automatically.

### 6.2 Memory Scalability

- **Working memory**: Bounded by context window, managed by summarization
- **Episodic memory**: Indexed for fast retrieval, consolidated over time
- **Semantic memory**: Vector-indexed for semantic search
- **Artifact memory**: Content-addressed (hash-based) for deduplication

### 6.3 Performance Targets

| Metric | Target | Notes |
|--------|--------|-------|
| Factory design time | < 30s | From intent to proposal |
| Checkpoint response time | < 5s | Present context to human |
| Memory query latency | < 50ms | For recent entries |
| Verification latency | Varies | Depends on test complexity |

---

## Next Steps

- **[Core Contracts](./03-core-contracts.md)**: Formal specifications of factory design, work orders, and artifacts
- **[Factory Design Guide](./04-factory-design-guide.md)**: How to create factories through conversation
