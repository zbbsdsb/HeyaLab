# Factory Design Guide

> This guide walks you through designing your own Heya Factory, from concept to blueprint.

---

## 1. Design Philosophy

### 1.1 Hybrid Approach

Heya supports two modes of factory design:

| Mode | Best For | Example |
|------|----------|---------|
| **Conversational** | Beginners, exploration, quick starts | "Help me design a factory for building mobile apps" |
| **Declarative** | Experts, version control, reproducibility | Edit YAML/JSON blueprint directly |
| **Hybrid** | Most real-world use | Start with conversation, refine with config |

**Recommended workflow:**

```
1. Conversational Design
   "I want to build X"
   ↓
   AI asks clarifying questions
   ↓
   AI generates draft blueprint

2. Declarative Refinement
   Review generated blueprint
   ↓
   Edit YAML/JSON for precision
   ↓
   Validate and activate

3. Evolutionary Improvement
   Use the factory
   ↓
   Learn from experience
   ↓
   Update blueprint
```

### 1.2 Start Simple, Add Complexity

Don't try to design the perfect factory on day one. Start with:

- **One pipeline** that does one thing well
- **Minimal tools** (just what you need)
- **Basic verification** (compile + test)
- **Default routing** (let Heya decide)

Then evolve based on actual usage.

---

## 2. The Design Process

### Step 1: Define Your Domain

**Questions to answer:**

1. What kind of products will this factory create?
   - Software applications?
   - Game engines?
   - Data pipelines?
   - Content/media?

2. What's the complexity level?
   - Single files/modules?
   - Multi-component systems?
   - Full applications?

3. What skills are needed?
   - Programming languages?
   - Domain knowledge?
   - External tools?

**Example:**

```
Domain: Game Engine Development
Products: Game engine modules (rendering, physics, audio, etc.)
Complexity: Multi-component systems with dependencies
Skills: Rust, graphics programming, performance optimization
```

### Step 2: Identify Pipelines

A pipeline is a sequence of steps that transforms inputs into outputs.

**Common pipeline patterns:**

| Pattern | Steps | Use Case |
|---------|-------|----------|
| **Create** | Analyze → Design → Implement → Test | Creating new components |
| **Modify** | Understand → Plan → Edit → Test | Modifying existing code |
| **Analyze** | Parse → Analyze → Report | Understanding codebases |
| **Debug** | Reproduce → Diagnose → Fix → Verify | Fixing bugs |
| **Review** | Read → Evaluate → Suggest | Code review |

**For each pipeline, define:**

```yaml
pipeline:
  id: "create-module"
  name: "Create Module"
  trigger:
    type: manual
  steps:
    - id: analyze
      action: "Analyze requirements and existing architecture"
      executor:
        type: model
        capability: architecture
      
    - id: design
      action: "Design module structure and interfaces"
      dependsOn: [analyze]
      executor:
        type: model
        capability: design
      timeout: 10m
      
    - id: implement
      action: "Generate implementation code"
      dependsOn: [design]
      executor:
        type: model
        capability: codegen
      
    - id: test
      action: "Run tests"
      dependsOn: [implement]
      executor:
        type: tool
        tool: test-runner
      
    - id: verify
      action: "Verify quality gates"
      dependsOn: [test]
      executor:
        type: subroutine
        capability: verify
```

### Step 3: Select Tools

Tools are external capabilities the factory can invoke.

**Tool categories:**

| Category | Examples | Risk Level |
|----------|----------|------------|
| **Build** | Compilers, bundlers | Medium |
| **Test** | Test runners, linters | Low |
| **Format** | Formatters, pretty-printers | Low |
| **Analyze** | Static analyzers, profilers | Low |
| **Deploy** | Docker, Kubernetes | High |
| **Network** | HTTP clients, API callers | High |

**For each tool, define:**

```yaml
tool:
  id: "rust-compiler"
  type: "build"
  version: "1.75.0"
  config:
    command: "cargo build --release"
    workingDir: "${projectRoot}"
  permissions:
    read: ["src/**", "Cargo.toml", "Cargo.lock"]
    write: ["target/**"]
    network: false
    approval: none
```

### Step 4: Define Verification

Verification ensures outputs meet quality standards.

**Verification levels:**

| Level | Checks | Cost | Coverage |
|-------|--------|------|----------|
| 0 - Syntax | Parse/compile | Low | Basic |
| 1 - Unit | Unit tests | Medium | Functions |
| 2 - Integration | Integration tests | Medium | Components |
| 3 - System | E2E tests, benchmarks | High | System |
| 4 - Regression | Golden samples, diffs | High | Behavior |
| 5 - Human | Code review, approval | Highest | Quality |

**Verification configuration:**

```yaml
verification:
  defaultLevel: 2
  
  checks:
    - type: compile
      required: true
      config:
        command: "cargo check"
        
    - type: unit-test
      required: true
      config:
        command: "cargo test"
        timeout: 5m
        
    - type: lint
      required: false
      config:
        command: "cargo clippy"
        failOn: error
        
    - type: format
      required: false
      config:
        command: "cargo fmt --check"
        
    - type: benchmark
      required: false
      config:
        command: "cargo bench"
        thresholds:
          p50_ms: 10
          p95_ms: 50
          
  gates:
    - name: "no-test-failures"
      condition: "unit-test.failed == 0"
      onFail: block
      
    - name: "coverage-threshold"
      condition: "coverage.percent >= 70"
      onFail: warn
```

### Step 5: Configure Memory

Memory determines what the factory remembers and for how long.

**Memory tiers:**

```yaml
memory:
  # Current task context
  working:
    ttl: 1h
    maxSize: 10MB
    storage: redis
    
  # Events and decisions
  episodic:
    retention: 90d
    consolidationPolicy: summarize-after-30d
    storage: postgresql
    
  # Knowledge and patterns
  semantic:
    embeddingModel: text-embedding-3-small
    indexConfig:
      dimensions: 1536
      metric: cosine
    storage: qdrant
    
  # Produced artifacts
  artifact:
    storage: s3
    signing: true
    retention: permanent
```

### Step 6: Set Up Routing

Routing determines which model/tool handles each task.

**Routing strategies:**

| Strategy | When to Use |
|----------|-------------|
| `cheapest` | High-volume, low-value tasks |
| `fastest` | Real-time, interactive tasks |
| `best_quality` | Critical decisions, architecture |
| `balanced` | Default for most tasks |
| `custom` | Domain-specific logic |

**Routing configuration:**

```yaml
routing:
  strategy: balanced
  
  providers:
    - name: openai
      models:
        - id: gpt-4-turbo
          capabilities: [reasoning, architecture, design]
          costTier: high
        - id: gpt-4o-mini
          capabilities: [simple-tasks, formatting]
          costTier: low
      priority: 1
      
    - name: anthropic
      models:
        - id: claude-sonnet-4
          capabilities: [codegen, analysis, review]
          costTier: medium
        - id: claude-opus-4
          capabilities: [complex-reasoning, architecture]
          costTier: high
      priority: 2
      
    - name: local
      models:
        - id: llama-3-70b
          capabilities: [simple-tasks, formatting]
          costTier: free
      priority: 3
      
  fallback:
    enabled: true
    order: [openai, anthropic, local]
    
  rules:
    - match:
        capability: architecture
      prefer: [gpt-4-turbo, claude-opus-4]
      
    - match:
        capability: codegen
        language: rust
      prefer: [claude-sonnet-4]
      
    - match:
        costConstraint: low
      prefer: [gpt-4o-mini, llama-3-70b]
```

### Step 7: Define Policies

Policies constrain factory behavior.

**Policy types:**

```yaml
policies:
  # Budget constraints
  - id: budget-limit
    type: budget
    spec:
      maxCostPerTask: 5.00
      maxCostPerDay: 50.00
      alertThreshold: 0.8
    enforcement: strict
    
  # Quality requirements
  - id: quality-floor
    type: quality
    spec:
      minTestCoverage: 70
      maxComplexity: 20
      requiredChecks: [compile, unit-test]
    enforcement: strict
    
  # Security rules
  - id: security
    type: security
    spec:
      noSecretsInPrompts: true
      sandboxUntrustedCode: true
      allowedDomains: ["github.com", "crates.io"]
    enforcement: strict
    
  # Human approval requirements
  - id: human-review
    type: behavior
    spec:
      requireApproval:
        - architecture
        - security-sensitive
        - breaking-changes
      autoApprove:
        - formatting
        - documentation
    enforcement: strict
    
  # Pacing for interactive use
  - id: pacing
    type: behavior
    spec:
      maxResponseLength: 500
      responseLatencyTarget: 2s
      allowInterruption: true
    enforcement: advisory
```

---

## 3. Complete Example: Game Engine Factory

Let's walk through designing a complete factory for game engine development.

### 3.1 Domain Definition

```
Domain: Game Engine Development
Goal: Create a factory that can develop game engine modules from scratch
Target: Rust-based, cross-platform (Windows, Linux), ECS architecture
```

### 3.2 Complete Blueprint

```yaml
apiVersion: heya.io/v1
kind: FactoryBlueprint
metadata:
  name: game-engine-factory
  version: 1.0.0
  displayName: Game Engine Factory
  description: |
    Factory for developing game engine modules including rendering,
    physics, audio, and core systems. Uses Rust and ECS architecture.
  owner: engine-team
  tags:
    - game-development
    - rendering
    - rust
    - ecs

spec:
  # Pipelines
  pipelines:
    # Create a new module from scratch
    - id: create-module
      name: Create Module
      steps:
        - id: analyze
          action: |
            Analyze requirements and existing architecture.
            Identify dependencies and integration points.
          executor:
            type: model
            capability: architecture
            
        - id: design
          action: |
            Design module structure, interfaces, and ECS components.
            Document design decisions.
          dependsOn: [analyze]
          executor:
            type: model
            capability: design
          timeout: 15m
          
        - id: implement
          action: Generate implementation code
          dependsOn: [design]
          executor:
            type: model
            capability: codegen
          timeout: 30m
          
        - id: test
          action: Run tests
          dependsOn: [implement]
          executor:
            type: tool
            tool: cargo-test
          timeout: 10m
          
        - id: benchmark
          action: Run performance benchmarks
          dependsOn: [test]
          executor:
            type: tool
            tool: cargo-bench
          timeout: 15m
          
        - id: verify
          action: Verify all quality gates pass
          dependsOn: [benchmark]
          executor:
            type: subroutine
            capability: verify
            
      onFailure:
        action: ask
        message: "Pipeline failed. Would you like to retry, adjust, or abort?"
        
    # Modify existing module
    - id: modify-module
      name: Modify Module
      steps:
        - id: understand
          action: Understand current implementation and context
          executor:
            type: model
            capability: analysis
            
        - id: plan
          action: Plan modifications with minimal impact
          dependsOn: [understand]
          executor:
            type: model
            capability: design
            
        - id: edit
          action: Apply modifications
          dependsOn: [plan]
          executor:
            type: model
            capability: codegen
            
        - id: test
          action: Run tests
          dependsOn: [edit]
          executor:
            type: tool
            tool: cargo-test
            
        - id: verify
          action: Verify changes
          dependsOn: [test]
          executor:
            type: subroutine
            capability: verify

  # Tools
  tools:
    - id: cargo-build
      type: build
      version: "1.75"
      config:
        command: "cargo build --release"
      permissions:
        read: ["src/**", "Cargo.toml", "Cargo.lock"]
        write: ["target/**"]
        network: false
        approval: none
        
    - id: cargo-test
      type: test
      version: "1.75"
      config:
        command: "cargo test --all-features"
      permissions:
        read: ["src/**", "tests/**", "Cargo.toml"]
        write: ["target/**"]
        network: false
        approval: none
        
    - id: cargo-bench
      type: benchmark
      version: "1.75"
      config:
        command: "cargo bench"
      permissions:
        read: ["src/**", "benches/**", "Cargo.toml"]
        write: ["target/**"]
        network: false
        approval: none
        
    - id: cargo-fmt
      type: format
      version: "1.75"
      config:
        command: "cargo fmt"
      permissions:
        read: ["src/**"]
        write: ["src/**"]
        network: false
        approval: none
        
    - id: cargo-clippy
      type: lint
      version: "1.75"
      config:
        command: "cargo clippy -- -D warnings"
      permissions:
        read: ["src/**", "Cargo.toml"]
        write: []
        network: false
        approval: none

  # Verification
  verification:
    defaultLevel: 3
    
    checks:
      - type: compile
        required: true
        config:
          command: "cargo check --all-features"
          
      - type: unit-test
        required: true
        config:
          command: "cargo test --lib"
          timeout: 5m
          
      - type: integration-test
        required: true
        config:
          command: "cargo test --test '*'"
          timeout: 10m
          
      - type: lint
        required: true
        config:
          command: "cargo clippy -- -D warnings"
          
      - type: format
        required: false
        config:
          command: "cargo fmt --check"
          
      - type: benchmark
        required: false
        config:
          command: "cargo bench -- --save-baseline new"
          compareBaseline: last
          
    gates:
      - name: no-compile-errors
        condition: "compile.status == 'pass'"
        onFail: block
        
      - name: no-test-failures
        condition: "unit-test.failed + integration-test.failed == 0"
        onFail: block
        
      - name: no-clippy-warnings
        condition: "lint.warnings == 0"
        onFail: warn
        
      - name: benchmark-regression
        condition: "benchmark.regressed == 0"
        onFail: warn

  # Memory
  memory:
    working:
      ttl: 2h
      maxSize: 50MB
      storage: redis
      
    episodic:
      retention: 180d
      consolidationPolicy: summarize-after-60d
      storage: postgresql
      
    semantic:
      embeddingModel: text-embedding-3-small
      indexConfig:
        dimensions: 1536
        metric: cosine
      storage: qdrant
      
    artifact:
      storage: s3
      signing: true
      retention: permanent

  # Routing
  routing:
    strategy: balanced
    
    providers:
      - name: openai
        models:
          - id: gpt-4-turbo
            capabilities: [reasoning, architecture, design]
            costTier: high
          - id: gpt-4o
            capabilities: [codegen, analysis]
            costTier: medium
        priority: 1
        
      - name: anthropic
        models:
          - id: claude-sonnet-4
            capabilities: [codegen, review, rust]
            costTier: medium
          - id: claude-opus-4
            capabilities: [complex-reasoning, architecture]
            costTier: high
        priority: 2
        
    fallback:
      enabled: true
      order: [openai, anthropic]
      
    rules:
      - match:
          capability: architecture
        prefer: [gpt-4-turbo, claude-opus-4]
        
      - match:
          capability: codegen
          language: rust
        prefer: [claude-sonnet-4, gpt-4o]

  # Policies
  policies:
    - id: budget-limit
      type: budget
      spec:
        maxCostPerTask: 10.00
        maxCostPerDay: 100.00
      enforcement: strict
      
    - id: quality-requirements
      type: quality
      spec:
        minTestCoverage: 80
        requiredChecks: [compile, unit-test, lint]
      enforcement: strict
      
    - id: security
      type: security
      spec:
        noSecretsInPrompts: true
        sandboxUntrustedCode: true
      enforcement: strict
      
    - id: human-review
      type: behavior
      spec:
        requireApproval:
          - architecture
          - breaking-changes
          - public-api
      enforcement: strict
```

---

## 4. Common Patterns

### 4.1 Simple Code Factory

For basic code generation tasks:

```yaml
apiVersion: heya.io/v1
kind: FactoryBlueprint
metadata:
  name: simple-code-factory
  version: 1.0.0
spec:
  pipelines:
    - id: generate
      steps:
        - id: generate
          action: Generate code from specification
          executor: { type: model, capability: codegen }
        - id: verify
          action: Verify output
          executor: { type: subroutine, capability: verify }
          
  tools: []
  
  verification:
    defaultLevel: 0
    checks:
      - type: syntax
        required: true
        
  memory:
    working: { ttl: 1h }
    
  routing:
    strategy: balanced
    
  policies: []
```

### 4.2 Analysis Factory

For codebase analysis and documentation:

```yaml
apiVersion: heya.io/v1
kind: FactoryBlueprint
metadata:
  name: analysis-factory
  version: 1.0.0
spec:
  pipelines:
    - id: analyze
      steps:
        - id: parse
          action: Parse and index codebase
          executor: { type: tool, tool: code-indexer }
        - id: analyze
          action: Analyze structure and patterns
          executor: { type: model, capability: analysis }
        - id: report
          action: Generate report
          executor: { type: model, capability: documentation }
          
  tools:
    - id: code-indexer
      type: analysis
      config: { languages: [typescript, python, rust] }
      
  verification:
    defaultLevel: 0
    
  memory:
    semantic:
      embeddingModel: text-embedding-3-small
      
  routing:
    strategy: balanced
```

---

## 5. Anti-Patterns to Avoid

### ❌ Over-Engineering

Don't start with complex pipelines, multiple tools, and strict policies.

```yaml
# ❌ Bad: Too complex for first version
pipelines:
  - id: everything
    steps: [analyze, design, implement, test, benchmark, document, deploy, monitor]
    
# ✅ Good: Start simple
pipelines:
  - id: create
    steps: [implement, test]
```

### ❌ Premature Optimization

Don't optimize routing before you know your usage patterns.

```yaml
# ❌ Bad: Over-specified routing
routing:
  rules:
    - match: { capability: codegen, language: rust, time: morning }
      prefer: [claude-sonnet]
    - match: { capability: codegen, language: rust, time: afternoon }
      prefer: [gpt-4]
      
# ✅ Good: Let the system learn
routing:
  strategy: balanced
```

### ❌ Ignoring Memory

Memory is what makes factories improve over time.

```yaml
# ❌ Bad: No memory configuration
memory: {}

# ✅ Good: Configure memory tiers
memory:
  working: { ttl: 1h }
  episodic: { retention: 90d }
  semantic: { embeddingModel: text-embedding-3-small }
```

### ❌ No Verification

Without verification, you can't trust the output.

```yaml
# ❌ Bad: No verification
verification: {}

# ✅ Good: At least basic verification
verification:
  defaultLevel: 1
  checks:
    - type: compile
      required: true
    - type: test
      required: true
```

---

## Next Steps

- **[API Reference](./05-api-reference.md)**: REST APIs for managing factories
- **[Getting Started](./06-getting-started.md)**: Quick start tutorial
