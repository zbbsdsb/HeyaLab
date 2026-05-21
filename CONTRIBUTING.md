# Contributing to Heya Research

> This repository is for **research and design**, not code implementation. Contributions are ideas, decisions, and documentation.

---

## How to Contribute

### 1. Propose an Idea

Have an idea for Heya's architecture, a new capability, or a design improvement?

1. Check [DECISIONS/](./DECISIONS/) to see if it's been discussed before
2. Check [ARCHITECTURE/](./ARCHITECTURE/) to understand the current design
3. Open an issue with the `proposal` label
4. Describe: What, Why, and How it would work

### 2. Challenge a Decision

See a decision you disagree with?

1. Read the full ADR in [DECISIONS/](./DECISIONS/)
2. Open an issue referencing the ADR number (e.g., "Challenge ADR-003")
3. Explain: What's wrong, what's the alternative, and why it's better

### 3. Submit a Decision Record

Made a decision through discussion? Document it.

1. Use the ADR template below
2. Name the file `NNN-short-title.md` (next available number)
3. Submit as a PR

### 4. Improve Documentation

Found unclear, outdated, or inconsistent documentation?

1. Fix it and submit a PR
2. Explain what changed and why in the PR description

---

## ADR Template

```markdown
# ADR-NNN: Short Title

**Date**: YYYY-MM-DD
**Status**: Proposed | Accepted | Deprecated | Superseded by ADR-XXX

## Context
What is the issue that we're seeing that is motivating this decision or change?

## Decision
What is the change that we're proposing and/or doing?

## Rationale
Why did we make this decision? What alternatives did we consider?

## Consequences
What becomes easier or more difficult to do because of this change?
```

---

## Repository Structure

```
HeyaLab/
├── VISION.md          # What Heya is and why it exists
├── ROADMAP.md         # Research phases and milestones
├── ARCHITECTURE/      # Technical architecture documents (English)
├── RESEARCH/          # Historical research documents (Chinese)
├── DECISIONS/         # Architecture Decision Records
├── docs/              # Additional documentation
└── CONTRIBUTING.md    # This file
```

---

## Guidelines

- **Language**: Architecture documents and ADRs are in English. Research documents are in Chinese.
- **Tone**: Direct and precise. Avoid hedging ("maybe", "perhaps", "we could consider").
- **Evidence**: Back up claims with reasoning, data, or references.
- **Consistency**: Read existing documents before writing new ones. Use established terminology.
- **No placeholders**: Every document must be complete. No "TODO" sections.

---

## Communication

- **Issues**: For proposals, questions, and challenges
- **Pull Requests**: For documentation changes and decision records
- **Discussions**: For open-ended exploration and brainstorming
