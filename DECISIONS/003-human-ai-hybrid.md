# ADR-003: Human-AI Hybrid Execution

**Date**: 2026-05-15
**Status**: Accepted

## Context

We need to define the division of labor between AI and humans in the factory execution model.

## Options Considered

1. **Fully autonomous**: AI designs, executes, and delivers without human intervention
2. **Human-in-the-loop**: AI executes, human reviews every step
3. **Hybrid**: AI proposes and executes, human decides at critical junctures
4. **Human-driven**: Human makes all decisions, AI only generates

## Decision

**Hybrid model**: AI proposes and executes, humans decide at critical junctures.

## Rationale

- Fully autonomous is risky — AI can make bad high-stakes decisions without the user noticing
- Human-in-the-loop on every step is too slow — defeats the purpose of automation
- Human-driven wastes AI potential — the AI should do the heavy lifting
- Hybrid strikes the right balance: AI handles 80% of the work, humans handle the 20% that matters most

## Division of Labor

**AI does:**
- Analyze requirements and intent
- Research domain knowledge
- Generate drafts and implementations
- Run automated verification
- Remember everything
- Propose next steps and alternatives

**Humans decide:**
- Approve factory design
- Make architecture choices
- Review critical outputs
- Resolve ambiguous trade-offs
- Define quality standards
- Set direction and priorities

## Consequences

- The system must define clear "checkpoint" types where human input is required
- The AI must present sufficient context at each checkpoint for informed decision-making
- The AI must never silently skip a checkpoint
- Checkpoint configuration is part of the factory design
