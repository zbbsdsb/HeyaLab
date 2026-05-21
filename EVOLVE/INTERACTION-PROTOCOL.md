# Heya R&D Interaction Protocol v2.0

> Small steps, fast iteration + immediate verification + ask before acting

---

## Core Principles

| Principle | Meaning | Anti-Pattern |
|-----------|---------|-------------|
| **Ask before acting** | Do not produce code or documentation before the direction is clear | Producing large outputs when the user says "continue" |
| **One at a time** | Advance only one specific Proposal per round | Pursuing 5 topics simultaneously |
| **Make assumptions explicit** | Provide rationale and counter-examples when presenting assumptions | Presenting conclusions directly |
| **Immediate verification** | Verify every output immediately | Accumulating everything for final acceptance |

---

## Standard Workflow

```
┌─────────────────────────────────────────┐
│  Phase 1: Focus                         │
│  User states goal -> AI asks key        │
│  clarifying questions -> User selects   │
│  one specific direction                 │
└─────────────────────────────────────────┘
                    ↓
┌─────────────────────────────────────────┐
│  Phase 2: Hypothesis                    │
│  AI presents specific hypothesis +      │
│  rationale + counter-examples ->        │
│  User confirms/corrects                 │
└─────────────────────────────────────────┘
                    ↓
┌─────────────────────────────────────────┐
│  Phase 3: Execution                     │
│  AI executes one specific task          │
│  (producing verifiable output) ->       │
│  User verifies the output               │
└─────────────────────────────────────────┘
                    ↓
┌─────────────────────────────────────────┐
│  Phase 4: Iteration                     │
│  Revise based on feedback -> Enter      │
│  next round                             │
└─────────────────────────────────────────┘
```

---

## Interaction Templates

### AI Clarifying Question Template

```
"You mentioned [goal]. I have identified [N] possible sub-directions:

1. [Direction A] - [One-line description]
2. [Direction B] - [One-line description]
3. [Direction C] - [One-line description]

Which would you like to explore first? Why?"
```

### AI Hypothesis Template

```
"My hypothesis: [specific hypothesis]

Rationale: [why this hypothesis was made]
Counter-example: [under what circumstances the hypothesis would not hold]

Do you agree with this hypothesis? Anything to add or correct?"
```

### AI Execution Template

```
"Understood, I will now execute: [specific task]

Expected output: [deliverable and format]
Verification criteria: [how to determine success]

Starting execution..."
```

---

## Relationship with EVOLVE

The 15 proposals in EVOLVE serve as a **material library**, not a to-do list.

Each interaction:
1. Select **one** Proposal from EVOLVE to explore in depth
2. Refine it step by step following this protocol
3. Once refined to an actionable state, convert it into a DECISION or code

---

*Effective date: 2026-05-16*
