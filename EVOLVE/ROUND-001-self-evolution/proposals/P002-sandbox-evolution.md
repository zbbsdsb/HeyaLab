# Proposal P002: Sandbox Evolution Validation Mechanism

> **Status**: Draft
> **Proposer**: AI Assistant
> **Date**: 2026-05-15

---

## Summary

All evolutions must pass validation in a sandbox environment before deployment, ensuring that evolution does not break Factory's core functionality.

---

## Design

### Sandbox Validation Workflow

```
┌─────────────┐    ┌─────────────┐    ┌─────────────┐
│  Evolution   │ -> │  Sandbox    │ -> │  Regression │
│  Proposal    │    │  Clone      │    │  Testing    │
│  (new config)│    │  (isolated) │    │  (historical│
│              │    │             │    │   data)     │
└─────────────┘    └─────────────┘    └──────┬──────┘
                                              │
┌─────────────┐    ┌─────────────┐    ┌──────▼──────┐
│  Deploy/    │ <- │  Human      │ <- │  A/B        │
│  Rollback   │    │  Approval   │    │  Testing    │
│             │    │             │    │  (low       │
│             │    │             │    │   traffic)  │
└─────────────┘    └─────────────┘    └─────────────┘
```

### Sandbox Testing Content

**1. Regression Testing**
- Use the most recent 100 successful WorkOrders as the test set
- The new Factory must achieve `success_rate >= 0.95` (relative to the original Factory)
- Execution time must not exceed 120% of the original Factory

**2. Boundary Testing**
- Extreme input handling
- Error recovery capability
- Performance under resource constraints

**3. Consistency Testing**
- Identical inputs produce identical outputs (determinism verification)
- Output format compliance with specifications

### A/B Testing Strategy

**Phase 1: Shadow Mode**
- New Factory runs in parallel but does not actually execute
- Compare output differences
- Proceed to Phase 2 after 24h with no anomalies

**Phase 2: Canary (Low Traffic)**
- 5% traffic routed to the new Factory
- Monitor key metrics
- Proceed to Phase 3 after 48h with no regression

**Phase 3: Full Deployment**
- 100% traffic switchover
- Maintain rollback capability for 7 days

---

## Advantages

1. **Safety**: Multi-layer validation prevents destructive evolutions
2. **Rollback capability**: Any issue can be immediately reversed
3. **Data-driven**: Deployment decisions based on actual performance

## Risks

1. **High testing cost** -> Use sampling instead of full historical data
2. **Deployment delay** -> Emergency fixes can skip A/B testing (requires human confirmation)

---

## Pending Decisions

- [ ] Resource limits for the sandbox environment
- [ ] Size and selection strategy for the test set
- [ ] Pass criteria for each phase
