# Proposal P003: Core Invariance Principle

> **Status**: Draft
> **Proposer**: AI Assistant
> **Date**: 2026-05-15

---

## Summary

Define Factory's core behavior layer (immutable) and extension layer (evolvable), ensuring Factory's essential identity remains stable.

---

## Design

### Core Layer - Immutable

```
┌─────────────────────────────────────────┐
│           CORE LAYER                    │
│  (Evolution must not touch; preserves   │
│   Factory identity)                     │
├─────────────────────────────────────────┤
│  • Product Type Definition              │
│  • Core Input/Output Contract           │
│  • Human Checkpoint Positions           │
│  • Safety Policies and Constraints      │
│  • Core Validation Rules (Level 0-1)    │
└─────────────────────────────────────────┘
```

**Why immutable?**
- Changing the product type = this is a different Factory
- Changing checkpoint positions = the human-in-the-loop model changes
- Changing safety policies = the risk exposure changes

### Extension Layer - Evolvable

```
┌─────────────────────────────────────────┐
│        EXTENSION LAYER                  │
│  (Can be continuously optimized via     │
│   evolution)                            │
├─────────────────────────────────────────┤
│  • Pipeline step implementations        │
│  • Tool selection and parameter tuning  │
│  • Prompts and role definitions         │
│  • Advanced validation rules (Level 2-4)│
│  • Memory retrieval strategies          │
│  • Error repair strategies              │
└─────────────────────────────────────────┘
```

**Why evolvable?**
- These are implementation details that do not affect Factory's essence
- Optimizing these can improve efficiency and quality
- On failure, rollback is possible under the protection of the Core Layer

### Evolution Boundary Check

Every evolution proposal must pass a boundary check:

```typescript
interface EvolutionBoundaryCheck {
  // Whether the evolution touches the Core Layer
  touchesCoreLayer: boolean;

  // If it touches the Core Layer, explicit human approval is required
  requiredHumanApproval: boolean;

  // Extensions that can be auto-evolved
  safeExtensions: string[];

  // Changes requiring approval
  sensitiveChanges: string[];
}
```

---

## Advantages

1. **Identity stability**: Factory will not "turn into something else"
2. **Safety baseline**: In the worst case, the Core Layer guarantees basic functionality
3. **Evolution freedom**: Bold experimentation is possible within constraints

## Risks

1. **Core Layer defined too broadly** -> Constrains evolution space
2. **Core Layer defined too narrowly** -> Loses protective effect

---

## Pending Decisions

- [ ] Specific inventory of the Core Layer
- [ ] How to detect whether evolution touches the Core Layer
- [ ] Workflow design for human approval
