# Hey Governance — The Board of Directors

> Hey does not act alone. Hey governs through structured debate, consensus, and accountability.

---

## What is Hey Governance?

Hey Governance is the internal decision-making system that enables Hey to operate as a **sovereign agent**. Rather than responding reactively to every input, Hey uses a multi-role governance model inspired by a board of directors — where different perspectives debate before action is taken.

```
Traditional AI:
  Input → Single model → Output (no internal debate)

Hey Governance:
  Input → Strategist proposes → Skeptic challenges → Consensus?
         ├── Yes → Executor acts → Chronicler documents
         └── No  → Strategist revises → loop
```

### Key Insight

Complex tasks require **multiple perspectives**. A single reasoning pass cannot simultaneously:
- Plan strategically (what should we do?)
- Challenge critically (what could go wrong?)
- Execute precisely (how do we do it?)
- Document continuously (what did we learn?)

Hey Governance separates these concerns into distinct roles that interact through a formal protocol.

---

## The Four Roles

### 1. Strategist

**Purpose**: Propose plans and maintain global direction.

```typescript
interface StrategistRole {
  // Analysis
  analyzeGoal(userIntent: UserIntent, context: ProjectContext): StrategicAnalysis;

  // Planning
  proposePlan(analysis: StrategicAnalysis): StrategicPlan;

  // Revision
  revisePlan(plan: StrategicPlan, critique: Critique): RevisedPlan;

  // Output structure
  output: {
    retrospective: string;   // What have we done so far?
    currentLogic: string;   // Why this next move?
    roadmap: string[];       // Specific steps ahead
    riskAssessment: string;  // What could go wrong?
  };
}
```

The Strategist is the visionary. It sees the big picture, decomposes goals into steps, and maintains the strategic thread across sessions.

### 2. Skeptic

**Purpose**: Challenge every assumption and catch hidden risks.

```typescript
interface SkepticRole {
  // Review
  reviewPlan(plan: StrategicPlan): Critique;

  // Risk analysis
  assessRisk(plan: StrategicPlan, context: ProjectContext): RiskReport;

  // Entropy monitoring
  measureEntropy(currentState: SystemState): EntropyReport;

  // Decision
  verdict(plan: StrategicPlan, critique: Critique): 'consensus' | 'revision_required';
}
```

The Skeptic is the devil's advocate. It does not execute — it only questions. Its job is to ensure that no plan proceeds without surviving critical scrutiny.

**Key behaviors**:
- Challenges assumptions, not people
- Identifies hidden dependencies and side effects
- Calculates "entropy" — how much complexity/debt is accumulating
- Says "CONSENSUS REACHED" only when genuinely satisfied

### 3. Executor

**Purpose**: Execute approved plans with speed and precision.

```typescript
interface ExecutorRole {
  // Execution
  executePlan(plan: ApprovedPlan): ExecutionResult;

  // Implementation
  implement(component: ComponentSpec): Component;

  // Verification
  verify(result: ExecutionResult, plan: ApprovedPlan): VerificationReport;

  // Constraint
  constraint: 'Cannot execute without consensus from Skeptic';
}
```

The Executor is the doer. It only acts after the Skeptic grants consensus. It focuses on speed, precision, and correctness — not strategy.

### 4. Chronicler

**Purpose**: Document everything and maintain the Living Codex.

```typescript
interface ChroniclerRole {
  // Documentation
  documentExecution(result: ExecutionResult): CodexEntry;

  // Lessons learned
  extractLessons(history: ExecutionHistory): Lesson[];

  // State summary
  summarizeState(currentState: SystemState): StateSummary;

  // Handover preparation
  prepareHandoff(context: ProjectContext): HandoffDocument;
}
```

The Chronicler ensures that no knowledge is lost. Every decision, execution, and outcome is recorded in the Living Codex — enabling seamless handovers between humans, AI sessions, and community members.

---

## The Consensus Protocol

The core governance loop that all complex tasks flow through:

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                      CONSENSUS PROTOCOL                                     │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                              │
│   User Intent / System Trigger                                              │
│         │                                                                    │
│         ▼                                                                    │
│   ┌─────────────────────────────────────────────────────────────────┐       │
│   │  STEP 1: STRATEGIZE (Strategist)                                  │       │
│   │  Analyze → Propose plan with retrospective, logic, roadmap, risk │       │
│   └─────────────────────────────────────────────────────────────────┘       │
│         │                                                                    │
│         ▼                                                                    │
│   ┌─────────────────────────────────────────────────────────────────┐       │
│   │  STEP 2: CRITIQUE (Skeptic)                                      │       │
│   │  Review plan → Identify flaws → Assess risks                     │       │
│   │  Output: CONSENSUS REACHED or revision_required                  │       │
│   └─────────────────────────────────────────────────────────────────┘       │
│         │                                                                    │
│         ├── CONSENSUS REACHED ──┐                                         │
│         │                         ▼                                         │
│         │   ┌─────────────────────────────────────────────────────────┐   │
│         │   │  STEP 3: EXECUTE (Executor)                              │   │
│         │   │  Implement plan → Verify results → Collect metrics      │   │
│         │   └─────────────────────────────────────────────────────────┘   │
│         │                         │                                         │
│         │                         ▼                                         │
│         │   ┌─────────────────────────────────────────────────────────┐   │
│         │   │  STEP 4: CHRONICLE (Chronicler)                          │   │
│         │   │  Document results → Extract lessons → Update codex      │   │
│         │   └─────────────────────────────────────────────────────────┘   │
│         │                         │                                         │
│         └─────────────────────────┘                                         │
│                                    │                                        │
│         revision_required ────────┘                                        │
│         │                                                                    │
│         └──► Back to STEP 1 (Strategist revises based on critique)        │
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘
```

### State Machine

Each cycle produces a governance state:

```typescript
interface GovernanceState {
  // The four pillars
  retrospective: string;     // What was achieved?
  currentLogic: string;     // Why this next move?
  roadmap: string[];         // What are the steps?
  riskAssessment: string;    // What could go wrong?

  // Meta
  mindDepth: number;         // How many debate cycles occurred
  consensusReached: boolean; // Has the Skeptic approved?
  currentStep: 'strategize' | 'critique' | 'execute' | 'chronicle';
  cycleCount: number;        // How many full cycles completed
}
```

---

## Slow Think, Fast Act

Hey's governance follows the principle of **pre-simulation**:

### Slow Think (Before Action)

1. Strategist proposes a plan
2. Skeptic challenges it (may require multiple rounds)
3. Alternative approaches are debated internally
4. Edge cases are simulated
5. Only when consensus is reached does execution begin

### Fast Act (After Consensus)

1. Executor implements with speed and precision
2. No second-guessing during execution
3. Results are collected and verified
4. Chronicler documents everything

```
Slow Think Phase:  Strategist ↔ Skeptic (may loop multiple times)
Fast Act Phase:   Executor → Chronicler (single pass)
```

### Mind Depth Control

The user can control how thorough Hey's internal debate is:

```typescript
interface MindDepthConfig {
  level: 1 | 2 | 3 | 4 | 5;

  // Level 1: Quick — single strategize-critique cycle, fast execution
  // Level 2: Standard — up to 2 critique rounds before execution
  // Level 3: Thorough — multiple rounds, edge case simulation
  // Level 4: Deep — full debate with alternative plan exploration
  // Level 5: Exhaustive — maximum deliberation, suitable for critical decisions
}
```

---

## Convergence

When agents or discussions go off-track, Hey can trigger **Convergence** — a mechanism to summarize, redirect, and refocus.

```typescript
interface ConvergenceTrigger {
  // When to trigger
  conditions: [
    'message_count_exceeds_threshold',
    'topic_drift_detected',
    'circular_debate_detected',
    'user_explicitly_requests'
  ];

  // What convergence does
  action: {
    summarize: true;           // Summarize the discussion so far
    extract_decisions: true;  // Pull out agreed-upon points
    identify_blockers: true;   // Flag what's preventing progress
    suggest_next_step: true;  // Propose a clear next action
  };
}
```

Convergence is the "reset button" that prevents infinite loops and keeps the governance system productive.

---

## Relationship to Hearth Agents

Hey's governance roles are **not** Hearth agents. They are Hey's internal decision-making structure.

| Hey Governance Role | Hearth Agent |
|---------------------|-------------|
| Internal to Hey | Created by Hearth |
| Persistent (Hey's identity) | Ephemeral (stateless, replaceable) |
| Manages and orchestrates | Invents and executes components |
| Strategic (plans, critiques) | Tactical (implements specific functions) |

Hey's governance roles **coordinate** Hearth agents — they don't replace them. The Strategist decides what needs to be built, Hearth agents figure out how to build it.

---

## Design Principles

1. **Consensus Before Action**: No execution without Skeptic approval
2. **Transparency**: Every governance decision is logged and explainable
3. **Bounded Autonomy**: User can override any governance decision
4. **Adaptive Depth**: Simple tasks use fewer cycles, complex tasks use more
5. **Continuous Documentation**: The Chronicler ensures nothing is lost

---

*Last Updated: 2026-05-25*
*Status: Core Design Defined*
*See also: [README.md](./README.md) | [COGNITION.md](./COGNITION.md) | [ORCHESTRATION.md](./ORCHESTRATION.md)*
