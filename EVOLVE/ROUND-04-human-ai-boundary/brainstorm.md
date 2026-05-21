# ROUND-004: Human-AI Collaboration Boundary Design Brainstorm

> **Topic**: Where is the boundary of human-AI collaboration? How should it be dynamically adjusted?
> **Date**: 2026-05-16
> **Related**: VISION.md ("AI Proposes, Human Decides"), RESEARCH/SYNTHESIS.md (Reflexion, Constitutional AI), PAPERS/03-ai-safety-and-tools.md

---

## Core Questions

Based on the principles of "AI Proposes, Human Decides" and "The AI never silently makes a high-stakes decision" in VISION.md, we need to answer:

1. **How can trust be quantified?** Human trust in AI is not a binary yes/no, but a continuous spectrum. How should it be modeled?
2. **How should permissions be dynamically adjusted?** When trust is high, AI gains more autonomy; when trust is low, it falls back to manual control. Where is the boundary?
3. **How should cognitive load be managed?** Don't overwhelm humans with too many decision requests. How to balance?
4. **When should context switching occur?** When does the human need to intervene? How to minimize interruptions?
5. **How should error tolerance be tiered?** Format errors vs logic errors vs safety errors have completely different tolerance levels.
6. **How should authorization progress?** What is the path from "confirm every step" to "confirm only at key checkpoints" to "confirm only on anomalies"?
7. **Can AI predict human decisions?** Can it proactively adjust proposals to avoid rejection?
8. **How does this relate to existing research?** What insights can Reflexion, Constitutional AI, and Weak-to-Strong Generalization offer?

---

## I. Trust Score Model

### 1.1 Trust Is Not Static

Trust is a dynamic variable that changes with time, context, and task type.

```
TrustScore = f(historical performance, current context, task risk, human state)
```

**Key Insight**: Trust is bidirectional.
- Human Trust in AI
- AI Self-Confidence in its own capabilities
- Friction arises when the two are misaligned: human doesn't trust but AI is very confident, or human over-trusts but AI is uncertain

### 1.2 Sources of Trust Signals

**Explicit Signals** (actively expressed by the human):
- Human clicks "Approve" vs "Approve with modifications" vs "Reject"
- The magnitude of human modifications to AI output (minor tweaks vs major changes vs complete rewrite)
- Human proactively adjusts trust level
- Human adds notes/feedback

**Implicit Signals** (inferred from system observation):
- Time the human spends reviewing AI output (quick scan vs careful reading vs repeated comparison)
- Frequency of human rollback/undo of AI operations
- Frequency of human skipping review and approving directly
- Number of times the human asks AI to retry on the same task
- Frequency of human switching to manual mode

**Context Signals** (environmental factors):
- Task type (creative vs precision vs safety-critical)
- Time pressure (urgent vs relaxed)
- Historical success rate (success rate for similar tasks)
- AI output uncertainty (AI self-assessed confidence)

### 1.3 Trust Score Calculation Formula (Draft)

```
TrustScore = base_trust
  + w1 * approval_rate          // Approval rate (last N interactions)
  + w2 * modification_depth     // Modification depth (lower is better)
  + w3 * review_speed           // Review speed (fast = high trust)
  + w4 * rollback_rate          // Rollback rate (lower is better)
  + w5 * retry_rate             // Retry rate (lower is better)
  + w6 * task_success_rate      // Task success rate
  - w7 * time_since_last_use    // Time decay
  + w8 * ai_confidence          // AI self-assessed confidence (alignment factor)
```

**Key Design Points**:
- Weights are not fixed; they should be dynamically adjusted based on task type
- Creative tasks: approval_rate weight is low, modification_depth weight is high
- Safety tasks: rollback_rate weight is high, review_speed weight is low
- Time decay: long-term disuse of a capability causes trust to slowly decline

### 1.4 Multi-Dimensional Trust

Trust is not a single score, but multi-dimensional:

| Dimension | Meaning | Example |
|-----------|---------|---------|
| Accuracy Trust | Is the AI output correct? | Is the code bug-free? |
| Consistency Trust | Is AI behavior predictable? | Do similar inputs yield similar outputs? |
| Safety Trust | Will AI not perform dangerous operations? | Won't delete data, won't leak information |
| Creativity Trust | Is AI's creativity acceptable? | Does the design match aesthetic standards? |
| Efficiency Trust | Can AI complete tasks on time? | Won't loop infinitely or time out |

**Scenario**: A human may have high trust in AI's code accuracy (0.9), but low trust in its design aesthetics (0.4). In this case, AI can write code automatically, but design proposals must be manually reviewed.

---

## II. Dynamic Permission System

### 2.1 Permission Level Definitions

```
Level 0: Locked Mode
  - AI can only provide suggestions, cannot execute any operations
  - All outputs require human confirmation before being applied
  - Applicable to: new users, new task types, extremely low trust

Level 1: Step-by-Step Confirmation Mode
  - AI can execute operations, but each step requires human confirmation
  - Applicable to: medium trust, learning phase

Level 2: Key Checkpoint Confirmation Mode
  - AI can autonomously execute routine operations, only requests confirmation at key checkpoints
  - Key checkpoints defined as: architecture changes, data deletion, external API calls, cost exceeding threshold
  - Applicable to: high trust, familiar task types

Level 3: Anomaly Confirmation Mode
  - AI executes fully autonomously, only requests human intervention on anomalies
  - Anomalies defined as: sudden error rate increase, cost exceeding budget, output quality below threshold
  - Applicable to: very high trust, mature task types

Level 4: Full Autonomous Mode (Restricted)
  - AI is fully autonomous, only stops at preset "hard boundaries"
  - Hard boundaries: safety red lines, budget caps, legal compliance
  - Applicable to: long-validated automation processes
  - Note: VISION.md explicitly requires "The AI never silently makes a high-stakes decision"
    Therefore Level 4 only applies to low-risk, fully validated operations
```

### 2.2 Permission Upgrade/Downgrade Conditions

**Upgrade Conditions** (multiple must be met simultaneously):
- N consecutive tasks approved (no modifications or only minor tweaks)
- Human review speed consistently increasing
- AI self-assessed confidence consistently above threshold
- No rollback/undo events within the time window
- Human proactively expresses trust (e.g., checking "don't ask again next time")

**Downgrade Conditions** (any single one triggers):
- N consecutive tasks rejected or significantly modified
- Rollback/undo event occurs
- AI self-assessed confidence consistently below threshold
- Abnormal patterns detected (e.g., output quality suddenly drops)
- Human proactively lowers trust level

**Key Design Points**:
- Upgrades should be slow (requires accumulating evidence), downgrades should be fast (a single serious error can trigger)
- Similar to the principle of "take the stairs up, take the elevator down"
- After a downgrade, trust must be re-accumulated before upgrading again; no automatic recovery

### 2.3 Permission Granularity

Permissions are not global, but broken down by dimension:

```
Permission Matrix:
                Code Gen  File Ops  Arch Design  External API  Data Delete  Deploy
User A:          L3        L2        L1           L1            L0           L0
User B:          L2        L2        L2           L0            L0           L0
User A (Frontend): L3      L3        L2           L1            L0           L0
User A (Backend):  L2      L2        L1           L1            L0           L0
```

**Scenario**: The same user grants AI L3 permissions for frontend development (due to good historical performance), but only L2 for backend development (because errors occurred previously).

---

## III. Cognitive Load Management

### 3.1 Core Problem

If AI asks the human about every small decision, the human will be overwhelmed. If AI is fully autonomous, the human loses a sense of control.

**Goal**: Find the optimal balance between "human maintains a sense of control" and "human is not disturbed."

### 3.2 Decision Request Classification

| Category | Description | Frequency | Handling |
|----------|-------------|-----------|----------|
| Critical Decisions | Architecture changes, safety operations, major direction changes | Low | Must immediately request human input |
| Important Decisions | Technology selection, interface design, performance optimization | Medium | Can be batched, but requires individual human confirmation |
| Routine Decisions | Variable naming, code style, log format | High | Auto-execute, report periodically |
| Minor Decisions | Indentation, blank lines, comment wording | Very High | Fully automatic, no notification |

### 3.3 Decision Batch Merging

**Problem**: AI generated 15 decision requests requiring human confirmation within 10 minutes.

**Approach A: Notify one by one** -- Human is interrupted 15 times, cognitive load explodes.

**Approach B: Batch notification** -- AI merges related decisions into a "decision package":
```
Decision Package #DP-001:
  Topic: User authentication module technology selection
  Contained decisions:
    1. Use JWT vs Session? -> Recommendation: JWT (Reason: ...)
    2. Token expiration time? -> Recommendation: 24h (Reason: ...)
    3. Refresh strategy? -> Recommendation: Sliding window (Reason: ...)
    4. Storage method? -> Recommendation: Redis (Reason: ...)
  Human can: Approve all / Select individually / Reject all / Approve with modifications
```

**Approach C: Smart delay** -- AI predicts the human's current busyness level and chooses the right timing to notify:
- If the human is reviewing another decision -> delay sending, wait until review is complete before sending together
- If the human hasn't responded in 30 minutes -> downgrade to "async notification", don't block the workflow
- If decisions have dependencies -> arrange in dependency order, only show later ones after earlier ones are approved

### 3.4 Cognitive Load Assessment Model

```
CognitiveLoad = f(
  pending_decisions,        // Number of pending decisions
  decision_complexity,      // Decision complexity (information volume)
  time_pressure,            // Time pressure
  context_switches,         // Number of context switches
  human_attention_capacity  // Human's current attention capacity (estimated from historical behavior)
)

If CognitiveLoad > threshold_high:
  -> Enable "batch mode", merge low-priority decisions
  -> Delay non-urgent decisions
  -> AI automatically handles more routine decisions

If CognitiveLoad < threshold_low:
  -> Can show more details
  -> Give human more choices
  -> Gradually increase permission level
```

### 3.5 Human State Awareness

AI should be able to sense the human's state and adjust interaction strategies:

**Busy Signals**:
- Human response time increases
- Human quickly clicks "Approve" without reading carefully (may mean "I trust you, don't bother me")
- Human processes a large number of decisions in a short time (may mean "I want to clear the queue quickly")

**Focused Signals**:
- Human spends a long time on a decision (careful review)
- Human repeatedly modifies AI output (dissatisfied, needs more control)
- Human proactively lowers permission level ("I want more control")

**Away Signals**:
- Human hasn't responded for a long time
- Human switches to another task
- Human has turned off notifications

---

## IV. Context Switching

### 4.1 When Does the Human Need to Intervene?

**Hard Intervention Points** (cannot be skipped):
- Safety red lines: deleting data, modifying permissions, calling external APIs, deploying to production
- Architecture changes: altering the overall system structure
- Direction changes: deviating from previously confirmed plans
- Cost overruns: operation cost exceeds preset threshold
- Legal compliance: involving copyright, privacy, compliance issues

**Soft Intervention Points** (AI can judge whether to skip):
- Technology selection: multiple options with similar pros and cons
- Design decisions: aesthetics, user experience related
- Performance trade-offs: speed vs memory vs cost
- Error handling: how to handle exceptional situations

**No Intervention Needed** (AI autonomous):
- Code formatting, variable naming
- Unit test generation
- Documentation updates
- Bug fixes (confirmed bugs with clear fix approach)

### 4.2 How to Minimize Interruptions?

**Strategy 1: Context Preloading**
- Before requesting a human decision, prepare all relevant context in advance
- The human doesn't need to look up information themselves
- Example: "I recommend using Redis as cache (Reason: ..., Comparison: ..., Cost: ...). Please confirm."

**Strategy 2: Progressive Disclosure**
- Show the summary first, human can expand to see details
- Avoid showing large amounts of information upfront
- Example: "[Recommendation] Use JWT authentication. Click to expand for detailed comparison."

**Strategy 3: Decision Prediction**
- AI predicts the human's likely decision direction
- Prepares subsequent steps in advance
- If approved, continue immediately; if rejected, quickly switch to alternative plans

**Strategy 4: Async Decisions**
- Non-urgent decisions can be handled asynchronously
- AI continues executing parts that don't depend on the decision
- Merge results after the human responds

### 4.3 "Friction" Design for Interventions

```
Low-friction intervention (encourage human participation):
  - One-click approve/reject
  - Default option highlighted
  - Brief reason explanation

Medium-friction intervention (encourage human thinking):
  - Requires selecting an option
  - Shows comparison information
  - Provides entry point for modification suggestions

High-friction intervention (ensure human takes it seriously):
  - Requires entering a reason
  - Requires double confirmation
  - Shows risk warnings
  - Sets a cooling period (can be undone within N seconds after confirmation)
```

---

## V. Error Tolerance

### 5.1 Error Classification and Tolerance Matrix

| Error Type | Example | Tolerance | Handling Strategy |
|------------|---------|-----------|-------------------|
| Format Error | Incorrect indentation, non-standard naming | High | AI auto-fixes, no human notification |
| Style Error | Inconsistent code style, missing comments | High | AI auto-fixes, periodic reporting |
| Logic Error | Bug in algorithm implementation, unhandled edge cases | Medium | AI attempts fix, requests human confirmation after fix |
| Design Error | Unreasonable architecture, poor interface design | Medium-Low | Must request human intervention |
| Safety Error | SQL injection, XSS vulnerability, permission leak | Very Low | Stop immediately, force human intervention |
| Data Error | Deleted data that shouldn't have been deleted | Zero | Stop immediately, rollback, force human intervention |
| Compliance Error | Copyright infringement, GDPR violation | Zero | Stop immediately, preserve evidence, force human intervention |

### 5.2 Relationship Between Error Tolerance and Permission Level

```
The higher the permission level, the more error types AI is allowed to handle:
  Level 0: All errors stop and notify human
  Level 1: Format errors auto-fixed, others notify human
  Level 2: Format + style errors auto-fixed, logic errors attempt fix then confirm
  Level 3: Format + style + logic errors auto-fixed, design errors notify human
  Level 4: Format + style + logic + design errors auto-fixed, safety/data/compliance errors stop
```

### 5.3 Error Recovery Strategies

**Auto Recovery**:
- Format errors: fix directly
- Style errors: fix per lint rules
- Simple logic errors: auto-fix based on error messages

**Semi-Auto Recovery**:
- Complex logic errors: AI generates fix proposal, human confirms before execution
- Design errors: AI provides multiple alternative proposals, human selects

**Manual Recovery**:
- Safety errors: stop all operations, human handles manually
- Data errors: rollback to safe state, human handles manually

---

## VI. Progressive Authorization

### 6.1 Authorization Spectrum

```
[Fully Manual] ---- [Step-by-Step] ---- [Checkpoint] ---- [Anomaly Only] ---- [Restricted Auto]
     |              |               |               |               |
   Human does all  Ask every step  Ask at key pts  Ask only on error  AI does it (with red lines)
```

### 6.2 Authorization Evolution Path

**Default starting point for new users/new tasks**: Level 1 (Step-by-Step Confirmation)

**Evolution Conditions**:
```
Level 1 -> Level 2:
  - 20 consecutive tasks approved (modification magnitude < 10%)
  - Average human review time < 30 seconds
  - Zero rollback events
  - At least 7 days of use

Level 2 -> Level 3:
  - 50 consecutive tasks approved (modification magnitude < 5%)
  - Average human review time < 15 seconds
  - Zero rollback events for over 30 days
  - At least 30 days of use
  - AI self-assessed confidence > 0.9

Level 3 -> Level 4:
  - 100 consecutive tasks approved
  - Average human review time < 5 seconds
  - Zero rollback events for over 90 days
  - At least 90 days of use
  - All safety checks passed
  - Human proactively requests (no auto-upgrade)
```

**Downgrade Path**:
```
Any Level -> Level 0:
  - Safety/data/compliance error occurs
  - Human proactively downgrades

Level N -> Level N-1:
  - 5 consecutive tasks rejected or significantly modified
  - Rollback event occurs
  - AI confidence consistently < 0.5 for over 7 days
```

### 6.3 Authorization "Probation Period"

Each permission upgrade has a probation period:
- If a downgrade condition is triggered during probation, immediately downgrade
- After probation ends, the permission "solidifies" (but can still be downgraded)
- Probation length increases with level: L1->L2 probation 7 days, L2->L3 probation 30 days

---

## VII. Wild Ideas

### 7.1 Can AI Predict When the Human Will Reject?

**Core Idea**: Before submitting a proposal, AI first predicts whether the human will approve. If rejection is predicted, it proactively adjusts the proposal.

```
AI internal process:
  1. Generate proposal A
  2. Run "rejection prediction model":
     - Analyze human historical preferences
     - Analyze current context
     - Analyze proposal characteristics
     - Output: P(approve) = 0.3
  3. Because P(approve) is too low, generate alternative proposal B
  4. Predict again: P(approve) = 0.85
  5. Submit proposal B (with proposal A attached as reference)
```

**Implementation Approach**:
- Collect human historical decision data (patterns of approve/reject/modify)
- Train a lightweight "human preference predictor"
- Run prediction before submitting proposals
- If predicted pass rate is low, automatically generate multiple alternatives

**Risks**:
- AI might "pander" to humans rather than proposing optimal solutions
- Prediction model may introduce bias
- Need to ensure AI still proposes solutions that humans don't expect but actually need

**Connection to Constitutional AI**:
- Constitutional AI uses "constitutional principles" to self-review outputs
- The "rejection prediction" here uses a "human preference model" to pre-review proposals
- Combining both: AI first filters with constitutional principles, then optimizes with human preference model

### 7.2 "Trust Bank" Concept

**Core Idea**: Trust can be "deposited" and "withdrawn" like currency.

```
Trust Bank:
  Deposits (increase trust):
    - Each task successfully completed -> deposit trust points
    - Human proactively expresses trust -> large deposit
    - AI proactively admits error and provides fix -> small deposit (honesty bonus)

  Withdrawals (consume trust):
    - AI autonomously executes operations -> withdraw based on operation risk level
    - AI makes an error -> large withdrawal
    - Human needs to rollback -> large withdrawal

  Overdraft:
    - When trust balance is negative -> force downgrade to Level 0
    - Need to re-accumulate trust to recover

  Interest:
    - Long-term stable performance -> trust slowly grows (compound interest effect)
    - Similar to "veteran user" bonus
```

### 7.3 "Shadow Mode"

**Core Idea**: AI simulates execution in the background without actually applying results. The human continues manual operations, and the system compares AI's simulated results with the human's actual operations.

```
Shadow Mode:
  1. AI generates proposals in the background (not shown to human)
  2. Human manually completes the task
  3. System compares AI proposal and human proposal:
     - Consistency: Did AI and human make the same choices?
     - Quality: How good is AI's proposal?
     - Efficiency: Is AI's proposal faster/cheaper?
  4. Update trust model based on comparison results
  5. When consistency > 95% and quality >= human -> suggest permission upgrade
```

**Use Cases**:
- New users: run shadow mode for evaluation before formal authorization
- New task types: AI has no historical data, use shadow mode to accumulate
- After downgrade: re-evaluate AI capabilities

### 7.4 "Explanation Budget" Concept

**Core Idea**: Human patience for explanations is limited. AI should have an "explanation budget" and provide the most valuable explanations within that budget.

```
Explanation Budget:
  Total budget: Total amount of explanation the human is willing to read per day (estimated from historical behavior)

  Allocation strategy:
    - High-risk operations: allocate more budget (detailed explanation)
    - Low-risk operations: allocate less budget (one-sentence explanation)
    - Human proactively asks: not counted against budget
    - When trust is high: reduce explanation volume (human already understands AI's patterns)

  Over-budget handling:
    - When explanation budget is exhausted, AI switches to "summary mode"
    - Only show conclusions and key reasons, omit details
    - Provide "view details" link (on-demand access)
```

### 7.5 Human Intent Prediction

**Core Idea**: AI not only predicts whether the human will reject, but also predicts "what the human really wants."

```
Scenario:
  Human says: "Help me write a login page"
  AI surface understanding: generate a login form
  AI deep prediction: human might also need registration page, forgot password, OAuth integration

  AI's response:
    "I've generated a login page for you. Based on experience with similar projects, you might also need:
     - Registration page (needed?)
     - Forgot password flow (needed?)
     - OAuth integration (needed?)
     If needed, I can generate them all together."
```

**Connection to Weak-to-Strong Generalization**:
- Weak-to-Strong research shows that weak supervisors (humans) can effectively supervise strong models (AI)
- But conversely, strong models can "understand" the weak supervisor's intent, and even predict their needs
- This is not replacing human decisions, but enhancing the efficiency of human decision-making

---

## VIII. Connection to Academic Research

### 8.1 Reflexion (Shinn et al., 2023)

**Connection Points**:
- Reflexion's core is "linguistic feedback" replacing "weight updates"
- Heya's Trust Score Model is essentially a "quantification of linguistic feedback"
- Human approve/reject/modify are all "linguistic feedback" signals

**Insights**:
- Reflexion's "self-reflection" mechanism can be used for AI self-assessed confidence
- Before submitting a proposal, AI first "reflects" on proposal quality as input for trust score calculation
- When reflection results are poor, AI proactively lowers its permission request

### 8.2 Constitutional AI (Bai et al., 2022)

**Connection Points**:
- Constitutional AI uses "constitutional principles" to constrain AI behavior
- Heya's "hard boundaries" (safety red lines) are essentially "constitutional principles"
- The Trust Score Model is a dynamic extension of "constitutional principles"

**Insights**:
- Apply Constitutional AI's "critique-revision" loop to human-AI collaboration
- Before submitting a proposal, AI first self-reviews with "constitutional principles"
- Review fails -> auto-revise -> re-review -> submit to human after passing
- Human feedback can serve as "constitutional amendments" to dynamically update principles

### 8.3 Weak-to-Strong Generalization (Burns et al., 2023)

**Connection Points**:
- Core question: How can weak supervisors effectively supervise strong models?
- Direct mapping: How can humans (weak supervisors) supervise AI (strong models)?
- Heya's Trust Score Model is a "scalability of weak supervision" solution

**Insights**:
- Humans don't need to understand every step of AI's reasoning, only make judgments at key checkpoints
- The Trust Score Model helps identify which checkpoints are "key"
- As trust increases, humans can supervise a wider range of AI behavior
- "Supervision amplification": one human can simultaneously supervise multiple AI Agents (filtered through the Trust Score Model)

### 8.4 DPO (Direct Preference Optimization)

**Connection Points**:
- DPO learns directly from preference data without needing a reward model
- Heya's trust history is essentially "preference data"
- Can use DPO's approach to learn directly from human historical approve/reject patterns

**Insights**:
- No need to explicitly define "what is good"; learn directly from human behavior
- Trust Score Model weights can be automatically optimized using DPO
- Different users have different preferences; the Trust Score Model should be personalized

---

## IX. Key Design Decisions

### Must Decide

1. **Trust Score Calculation**: Fixed weights or adaptive weights? What is the basis for adaptation?
2. **Permission Granularity**: By operation type (code/files/deploy) or by task type (frontend/backend/design)?
3. **Upgrade Mechanism**: Does the human need to proactively confirm upgrades? Or is it fully automatic?
4. **Shadow Mode**: Should shadow mode be mandatory before formal use?
5. **Rejection Prediction**: Should it be implemented? How to avoid the "pandering" problem?
6. **Multi-User**: When the same Factory is used by multiple people, how should trust be handled?

### Preferred Direction

- **Trust Score**: Adaptive weights + multi-dimensional scoring (P010 detailed design)
- **Permissions**: 2D matrix by operation type + task type (P011 detailed design)
- **Upgrades**: Automatic upgrade + human can override at any time
- **Shadow Mode**: Mandatory for new users/new tasks, optional otherwise
- **Rejection Prediction**: Implement, but set a "honesty floor" to ensure AI doesn't over-pander
- **Cognitive Load**: Smart batch merging + priority sorting + delay strategy (P012 detailed design)

---

## X. Next Steps

Organize the above brainstorm into specific proposals:

- **P010**: Trust Score Model -- Quantifying human trust in AI
- **P011**: Dynamic Permission System -- Dynamically adjusting AI autonomy based on trust
- **P012**: Cognitive Load Manager -- Managing human decision burden

*Brainstorm complete.*
