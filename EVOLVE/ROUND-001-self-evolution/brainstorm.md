# ROUND-001: Self-Evolution Mechanism Brainstorm

> **Topic**: How Heya Factory can self-improve and adapt
> **Date**: 2026-05-15
> **Related**: ROADMAP Phase 3 - Self-Evolution Framework

---

## Core Questions

Based on the research foundation in RESEARCH/SYNTHESIS.md, we need to answer:

1. **Under what conditions should Factory trigger self-evolution?**
2. **What specifically should evolve?** (SOPs, tools, validation rules, memory strategies...)
3. **How can we ensure evolution does not break Factory's core functionality?**
4. **What role do humans play in the evolution process?**

---

## Brainstorm Record

### Trigger Conditions (When)

**Proposal A: Performance Threshold Trigger**
- When Factory's success rate drops below a configured threshold
- When the human correction rate remains consistently above normal levels
- When execution time/cost exceeds expectations

**Proposal B: Feedback Accumulation Trigger**
- After receiving a certain number of negative feedback entries
- When repetitive error patterns are detected
- When a human explicitly requests improvement

**Proposal C: Periodic Review Trigger**
- Automatic review after every N WorkOrders completed
- Scheduled (weekly/monthly) evolution checks
- When new data/knowledge becomes available

**Proposal D: Hybrid Trigger**
- Combining performance thresholds + feedback accumulation + periodic review
- Priority: human request > performance issues > feedback patterns > periodic review

---

### Evolution Content (What)

**Dimension 1: Pipeline Optimization**
- Adjust step ordering
- Add/remove validation checkpoints
- Optimize information passing between steps

**Dimension 2: Tool Usage Strategy**
- Learn new tool combinations
- Optimize tool invocation parameters
- Discover synergies between tools

**Dimension 3: Prompt Engineering**
- Improve role definitions
- Optimize context packaging
- Adjust output format requirements

**Dimension 4: Validation Rules**
- Add checkpoints based on error types
- Adjust validation strictness
- Optimize repair strategies

**Dimension 5: Memory Strategy**
- Improve retrieval query methods
- Optimize memory storage structure
- Adjust memory importance scoring

---

### Safety Guarantees (Safety)

**Mechanism 1: Sandbox Testing**
- Test the evolved Factory in an isolated environment
- Use historical WorkOrders for regression testing
- A/B testing: new and old versions running in parallel

**Mechanism 2: Progressive Deployment**
- Start with a small-scale trial (10% traffic)
- Monitor key metrics, deploy fully after confirming no regression
- Maintain rollback capability

**Mechanism 3: Core Invariance**
- Define Factory's core behaviors (immutable)
- Evolution can only occur in the Extension Layer
- Critical decisions still require human confirmation

---

### Human-in-the-Loop

**Scenario 1: Evolution Proposal Approval**
- AI proposes evolution plan -> human reviews -> approve/modify/reject
- Provide evolution rationale and impact predictions
- Visualize the changes

**Scenario 2: Real-time Feedback Learning**
- Human corrections automatically converted into learning signals
- No explicit approval required, but can be reviewed and reverted
- Similar to Reflexion's verbal RL

**Scenario 3: Evolution Direction Guidance**
- Humans set evolution priorities
- AI explores autonomously within constraints
- Periodically report evolution outcomes

---

## Questions to Clarify

1. **Evolution granularity**: Should evolution target individual Factories, or enable cross-Factory learning?
2. **Knowledge sharing**: How can one Factory's evolution benefit other Factories?
3. **Evolution pace**: Should we iterate rapidly or proceed cautiously?
4. **Failure handling**: If evolution causes performance degradation, how do we detect and recover?

---

## Next Steps

Organize the above proposals into specific design plans and create proposal documents.

*Brainstorm in progress...*
