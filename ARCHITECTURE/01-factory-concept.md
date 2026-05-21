# Heya: The AI Factory

> **Describe what you want to create. Heya designs the factory. Together, you build anything.**

---

## 1. The Factory Metaphor

Heya is not a chatbot. It is not a code assistant. It is not an agent framework.

**Heya is an AI Factory — an AI that organizes itself into a factory to create products, with humans guiding the critical decisions.**

### The Core Insight

Traditional tools give you a fixed AI that answers questions. Heya gives you an AI that **transforms itself** based on what you want to build.

```
Traditional AI Tool:
  User → Fixed AI → Text Response

Heya Factory:
  User → "I want to build X" → AI designs a factory → Factory creates X
                                  (with human guidance)
```

The AI **is** the factory. It reorganizes its capabilities — reasoning, memory, tools, verification — into the optimal structure for your specific goal. And at every critical juncture, it asks you to decide.

### The Three-Layer Model

```
┌─────────────────────────────────────────────────────────┐
│                     PRODUCTS                             │
│    Software • Designs • Content • Strategies             │
│    Knowledge • Physical specs • Anything                 │
│         (What the factory creates)                        │
└─────────────────────────────────────────────────────────┘
                           ↑ created by
┌─────────────────────────────────────────────────────────┐
│                      FACTORY                             │
│    AI organized into:                                    │
│    Reasoning chains • Capability assembly                │
│    Self-verification • Long-term memory                  │
│    Tool orchestration • Human checkpoints                │
│    (The AI itself, structured for a purpose)              │
└─────────────────────────────────────────────────────────┘
                           ↑ powered by
┌─────────────────────────────────────────────────────────┐
│                   INFRASTRUCTURE                          │
│    Models • Storage • Compute • Network                   │
│    (Replaceable underlying resources)                      │
└─────────────────────────────────────────────────────────┘
```

### What This Means

| Traditional AI Tools | Heya Factory |
|---------------------|--------------|
| AI answers questions | AI transforms itself to build what you need |
| You use the tool | You guide the AI; the AI does the work |
| Output is text | Output is verified artifacts of any type |
| Memory is session-based | Memory is project-level and persistent |
| Capabilities are fixed | Capabilities are assembled per task |
| One-size-fits-all | Factory is purpose-built for your goal |
| Fully automated | AI acts, human decides at key moments |

---

## 2. Heya's Unique Capabilities

### 2.1 AI That Designs Itself

When you tell Heya what you want to build, the AI doesn't just start working. It **designs its own operating structure**:

```
User: "I want to build a game engine from scratch."

Heya: "Let me design a factory for this. Based on your goal, I'll need:
       - Architecture reasoning capability
       - Rust code generation capability
       - Graphics domain knowledge
       - Performance testing capability
       - Your approval at architecture decisions

       Here's my proposed factory design:
       [Factory Design]

       Does this look right? I can adjust before we start."
```

The factory design is **derived from intent**, not manually configured. The user confirms or adjusts, then production begins.

### 2.2 Universal Product Scope

Heya factories can create anything, not just code:

| Category | Examples |
|----------|---------|
| **Software** | Games, SaaS apps, CLI tools, libraries |
| **Design** | UI/UX mockups, brand identities, architectural plans |
| **Content** | Courses, video scripts, music, novels, marketing copy |
| **Strategy** | Business plans, go-to-market strategies, investment theses |
| **Knowledge** | Research reports, technical documentation, patent drafts |
| **Physical** | Hardware specs, 3D print models, supply chain designs |

The factory adapts its capabilities to the product type. A "music factory" assembles differently from a "game engine factory."

### 2.3 Human-AI Hybrid Execution

Heya doesn't try to do everything autonomously. It follows a clear division of labor:

```
┌─────────────────────────────────────────────────────────┐
│                    AI DOES                                │
│  • Analyze requirements                                  │
│  • Research domain knowledge                             │
│  • Generate drafts and implementations                   │
│  • Run automated verification                            │
│  • Remember everything                                   │
│  • Propose next steps                                    │
└─────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────┐
│                   HUMAN DECIDES                           │
│  • Approve factory design                                │
│  • Make architecture choices                             │
│  • Review critical outputs                                │
│  • Resolve ambiguous trade-offs                          │
│  • Define quality standards                              │
│  • Set direction and priorities                          │
└─────────────────────────────────────────────────────────┘
```

**Key principle**: AI proposes, human disposes. The AI never silently makes a high-stakes decision.

### 2.4 Project-Level Memory

Heya remembers everything across sessions:

- **Decisions**: "We chose ECS over OOP three weeks ago because..."
- **Failures**: "The Vulkan backend crashed on AMD cards, we rolled back"
- **Patterns**: "This user prefers concise summaries with tables"
- **Context**: "This is a game engine project, not a web app"
- **Human preferences**: "This user always wants to see alternatives before deciding"

The factory gets smarter the more you use it — not because the model changes, but because its accumulated memory guides every action.

### 2.5 Verified Artifacts

When a Heya factory produces something, it's not just text:

```
┌─────────────────────────────────────────┐
│           ArtifactReceipt                │
├─────────────────────────────────────────┤
│ artifact:    Game Engine v0.3 Module     │
│ type:        rust_module                 │
│ created_by:  heyafactory (claude + human)│
│ based_on:    [decision-47, mem-182]      │
│ verification:                             │
│   - compile:     PASS                    │
│   - unit_tests:  PASS (23/23)            │
│   - benchmark:   PASS (p95 < 50ms)       │
│   - human_review: APPROVED by user       │
│ signature:   verified                    │
└─────────────────────────────────────────┘
```

Every artifact is:
- **Traceable**: Know exactly how it was created and why
- **Verified**: Automated checks + human approval at key gates
- **Reproducible**: Can recreate from the same inputs and memory

---

## 3. Comparison with Existing Tools

| Capability | ChatGPT/Claude | Cursor/Copilot | Dify/FastGPT | Devin | **Heya** |
|------------|---------------|----------------|--------------|-------|----------|
| Conversational interface | ✅ | ✅ | ✅ | ✅ | ✅ |
| Code generation | ✅ | ✅ | ⚠️ | ✅ | ✅ |
| Non-code products | ⚠️ | ❌ | ⚠️ | ❌ | ✅ |
| Project-level memory | ❌ | ❌ | ❌ | ⚠️ | ✅ |
| AI self-organization | ❌ | ❌ | ❌ | ❌ | ✅ |
| Human-in-the-loop by design | ❌ | ✅ | ❌ | ⚠️ | ✅ |
| Verified artifacts | ❌ | ❌ | ❌ | ⚠️ | ✅ |
| Intent-driven factory design | ❌ | ❌ | ❌ | ❌ | ✅ |
| Self-evolution | ❌ | ❌ | ❌ | ❌ | ✅ |
| Cross-domain reuse | ❌ | ❌ | ⚠️ | ❌ | ✅ |

### Why Existing Tools Can't Do This

**ChatGPT/Claude**: Session-based, no project memory, output is text, fully autonomous (no structured human checkpoints).

**Cursor/Copilot**: File-level context, no project orchestration, code-only, no verification pipeline.

**Dify/FastGPT**: Workflow configuration requires manual setup, no AI-driven factory design, no self-evolution.

**Devin**: Autonomous agent, minimal human oversight, code-only, no factory abstraction.

**Heya's unique position**: The only system where AI **organizes itself** into a purpose-built factory, creates **any type of product**, and maintains **structured human-AI collaboration** throughout.

---

## 4. Core Design Principles

### Principle 1: AI Proposes, Human Decides

> "The AI does the heavy lifting. Humans make the calls that matter."

Every Heya interaction follows this pattern:

```
1. UNDERSTAND: What does the user want to create?
2. DESIGN:   AI proposes a factory structure
3. APPROVE:  Human confirms or adjusts the design
4. EXECUTE:  AI produces (with human check-ins at key points)
5. VERIFY:   Automated checks + human review
6. REMEMBER: Store everything for future use
```

### Principle 2: AI Is the Factory

> "The factory isn't a container for AI. The AI transforms itself into the factory."

This means:
- Factory components (reasoning, memory, tools, verification) are **AI capabilities**, not external services
- The AI **assembles** different capability combinations for different tasks
- The same AI can be a "game engine factory" or a "marketing strategy factory" depending on what you ask

### Principle 3: Intent-Driven Design

> "Describe what you want. Don't configure how to get there."

Users never touch configuration files. They describe their intent in natural language, and the AI derives the factory structure:

```
Intent → AI derives capabilities → AI proposes factory → Human approves → Production begins
```

Configuration (YAML/JSON) exists as an **internal representation** for reproducibility and version control, but users interact through conversation.

### Principle 4: Replaceable Infrastructure

> "The factory is independent of its power source."

All infrastructure (models, storage, compute) is accessed through stable interfaces. Your factory design outlives any specific model or service.

### Principle 5: Observable Everything

> "If it happened, there's a trace."

Every action emits structured telemetry — what was done, why, by whom (AI or human), and what was produced.

---

## 5. Factory Lifecycle

### Phase 1: Design (Human + AI)

```
User describes intent
    → AI asks clarifying questions
    → AI proposes factory design
    → Human reviews and approves
    → Factory is activated
```

### Phase 2: Produce (AI executes, Human guides)

```
WorkOrder submitted
    → AI executes with assembled capabilities
    → Human checkpoints at critical junctures
    → Automated verification runs
    → Human approves final output
    → ArtifactReceipt issued
```

### Phase 3: Evolve (AI learns, Human steers)

```
Experience accumulates
    → AI identifies patterns and improvements
    → AI proposes factory adjustments
    → Human approves changes
    → Factory becomes more effective
```

### Phase 4: Share (Optional)

```
Factory + Memory templates
    → Packaged as portable factory
    → Others can instantiate and adapt
```

---

## 6. What Heya is NOT

| Heya is NOT | Because |
|-------------|---------|
| A chatbot | Chat is the interface, not the product |
| A fully autonomous agent | Humans make critical decisions |
| A code-only tool | Factories create any type of product |
| A configuration system | Users describe intent, not YAML |
| A replacement for human judgment | AI proposes, human decides |
| A single AI model | Multiple models collaborate through the factory |

---

## 7. The Heya Promise

**For Users:**
> "Describe what you want to create. I'll design a factory for it, do the heavy lifting, and check in with you when it matters."

**For Developers:**
> "Build on stable interfaces. Your factory designs will work with today's models and tomorrow's. Your artifacts will be traceable and reproducible."

**For Organizations:**
> "Accumulate organizational intelligence. Every decision, failure, and success makes your factories stronger. Share proven factories across teams."

---

## Next Steps

- **[Core Architecture](./02-core-architecture.md)**: How the AI organizes itself into a factory
- **[Core Contracts](./03-core-contracts.md)**: Formal specifications of factory design, work orders, and artifacts
- **[Factory Design Guide](./04-factory-design-guide.md)**: How to create factories through conversation
