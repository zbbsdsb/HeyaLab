# Proposal P001: Hybrid Trigger Evolution System

> **Status**: Draft
> **Proposer**: AI Assistant
> **Date**: 2026-05-15

---

## Summary

Adopt a hybrid trigger mechanism combining performance monitoring, feedback analysis, and periodic review to ensure Factory evolves when needed while avoiding over-evolution.

---

## Design

### Trigger Levels

```
┌─────────────────────────────────────────┐
│  Level 1: Urgent Trigger (immediate)    │
│  - Success rate drops below threshold   │
│  - N consecutive failures               │
│  - Human explicitly requests a fix      │
├─────────────────────────────────────────┤
│  Level 2: Pattern Trigger (within 24h)  │
│  - Repetitive error pattern detected    │
│  - Correction rate consistently above   │
│    baseline                             │
│  - Multiple similar negative feedback   │
│    received                             │
├─────────────────────────────────────────┤
│  Level 3: Scheduled Trigger             │
│  (weekly/monthly)                       │
│  - Auto-review after N WorkOrders       │
│  - Periodic performance review          │
│  - New knowledge/tools available        │
└─────────────────────────────────────────┘
```

### Trigger Condition Definitions

**Urgent Trigger (Level 1)**
- `success_rate < 0.7` (configurable)
- `consecutive_failures >= 3`
- Human input: `/fix-factory` or similar command

**Pattern Trigger (Level 2)**
- `correction_rate > baseline * 1.5` sustained for 24h
- Same error type occurring >= 5 times
- Negative feedback cluster analysis shows a pattern

**Scheduled Trigger (Level 3)**
- Every 50 WorkOrders
- Automatic check every Monday at midnight
- When new relevant papers/tools are detected

---

## Advantages

1. **Responsiveness**: Urgent issues addressed immediately
2. **Preventive**: Pattern detection prevents problems from escalating
3. **Continuity**: Periodic optimization maintains competitiveness

## Risks

1. **Over-triggering** -> Set cooldown periods (max 1 trigger per Factory per 24h)
2. **False triggers** -> Level 1 and Level 2 require human confirmation

---

## Pending Decisions

- [ ] Specific values for each threshold
- [ ] Duration of cooldown periods
- [ ] Whether Level 3 should be fully automatic (no human confirmation required)
