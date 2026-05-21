# ROUND-001 Summary: Self-Evolution Mechanism

> **Date**: 2026-05-15
> **Status**: Draft complete, pending decisions

---

## Deliverables

### Files Created

```
EVOLVE/ROUND-001-self-evolution/
├── brainstorm.md                    # Brainstorm record
├── proposals/
│   ├── P001-hybrid-trigger-system.md   # Hybrid Trigger System
│   ├── P002-sandbox-evolution.md       # Sandbox Validation Mechanism
│   └── P003-core-invariance.md         # Core Invariance Principle
└── summary.md                       # This file
```

---

## Proposal Overview

| Proposal | Topic | Key Concept | ROADMAP Alignment |
|----------|-------|-------------|-------------------|
| **P001** | Hybrid Trigger System | Three-level trigger (urgent/pattern/scheduled) | Phase 3: Self-Evolution Framework trigger conditions |
| **P002** | Sandbox Validation Mechanism | Sandbox testing + A/B deployment | Phase 3: Safety guarantee mechanisms |
| **P003** | Core Invariance Principle | Core Layer vs Extension Layer | Phase 3: Evolution boundary definition |

---

## Relationships Between Proposals

```
┌─────────────────────────────────────────────────────────┐
│                    Self-Evolution System                 │
├─────────────────────────────────────────────────────────┤
│  P001: When to evolve?                                  │
│  ├── Level 1: Urgent (success rate < 70%)               │
│  ├── Level 2: Pattern (repetitive error detection)      │
│  └── Level 3: Scheduled (every 50 WorkOrders)           │
├─────────────────────────────────────────────────────────┤
│  P003: What can evolve?                                 │
│  ├── Core Layer (immutable): product types, checkpoints, │
│  │   safety policies                                    │
│  └── Extension Layer (mutable): Pipeline, tools,        │
│      prompts                                            │
├─────────────────────────────────────────────────────────┤
│  P002: How to evolve safely?                            │
│  ├── Sandbox regression testing                         │
│  ├── A/B testing (shadow -> 5% -> 100%)                 │
│  └── Human approval + rollback capability               │
└─────────────────────────────────────────────────────────┘
```

---

## Pending Decisions

### Key Decision Points

1. **Trigger Thresholds**
   - Success rate threshold for urgent trigger (suggested: 0.7)
   - Correction rate multiplier for pattern trigger (suggested: 1.5x)
   - WorkOrder interval for scheduled trigger (suggested: 50)

2. **Core Layer Boundaries**
   - The specific inventory of the Core Layer needs precise definition
   - How to automatically detect whether evolution touches the Core Layer

3. **Human-in-the-Loop Model**
   - Should Level 3 (scheduled) be fully automatic?
   - Interface and workflow design for human approval

4. **Resources and Costs**
   - Resource limits for sandbox testing
   - Duration of A/B testing

---

## Next Steps

### Option A: Deep Design
Select one proposal for detailed design, producing a specification document:
- [ ] P001: Specific algorithms and configuration for the Trigger System
- [ ] P002: Architecture design for the Sandbox environment
- [ ] P003: Complete inventory of the Core Layer

### Option B: Start Round 2
Select a new brainstorm topic:
- [ ] Memory compression and retrieval optimization
- [ ] Multi-modal factories
- [ ] Distributed Agent Mesh

### Option C: Convert to DECISION
Convert current proposals into formal Architecture Decision Records (ADR):
- [ ] Create DECISIONS/007-self-evolution-framework.md

---

## Reflections

This round of brainstorming is based on academic research in RESEARCH/SYNTHESIS.md, particularly:
- **Self-Evolving Agents Survey** - Feedback loop framework
- **MetaGPT** - SOP encoding and intermediate validation
- **Reflexion** - Verbal RL and human feedback
- **MemRL** - Stability-plasticity decomposition

The three proposals complement each other, forming a complete self-evolution mechanism:
1. **P001** addresses "when to evolve"
2. **P003** addresses "what can evolve"
3. **P002** addresses "how to evolve safely"

---

*Awaiting user decision on next steps*
