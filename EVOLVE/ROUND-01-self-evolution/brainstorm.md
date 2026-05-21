# ROUND-01: Self-Evolution Mechanism -- In-Depth Brainstorm

> **Topic**: How Heya Factory can self-improve and adapt
> **Date**: 2026-05-16
> **Round**: ROUND-01 (In-Depth Edition)
> **Related**: VISION.md / ROADMAP Phase 3 - Self-Evolution Framework / RESEARCH/SYNTHESIS.md

---

## Core Questions

According to VISION.md, Heya is an AI Factory -- AI organizes itself into a purposeful creative system, with humans guiding key decisions. VISION explicitly states: "The factory gets measurably better over time (success rate, cost efficiency, human correction rate)."

This means self-evolution is not optional but a core commitment of Heya. We need to answer:

1. **Under what conditions should Factory trigger self-evolution?**
2. **What specifically should evolve?** Pipeline, tools, validation rules, memory strategies...
3. **How can we guarantee that evolution does not destroy Factory's core identity?**
4. **What role do humans play in evolution?**
5. **Can Factory go beyond preset frameworks and invent entirely new work modes?**

---

## I. Evolution Trigger Conditions (When)

### 1.1 Performance Threshold Trigger

**Conventional approach**: Set fixed thresholds (e.g., success rate < 70% triggers evolution).

**Problem**: Fixed thresholds are static and cannot adapt to changes in Factory maturity. A newly created Factory with a 60% success rate may be normal, but a Factory that has been running for 6 months suddenly dropping to 60% is a serious problem.

**In-depth thinking**:
- Thresholds should be dynamically adjusted based on **historical baselines**, not fixed values
- Different types of Factories should have different performance models
- Focus should be on **trends** rather than absolute values -- 5 consecutive declines are more noteworthy than a single threshold breach
- Cost efficiency should also be factored into trigger conditions -- for the same results, is cost increasing?

### 1.2 Feedback Pattern Trigger

**Conventional approach**: Trigger after receiving N negative feedback entries.

**In-depth thinking**:
- **Semantic analysis** of feedback is more important than counting -- "the approach is wrong" and "minor formatting issue" carry completely different weights
- Should detect **feedback clustering patterns** -- if 10 feedback entries all say "steps are too slow," this is a clear optimization signal
- **Silence is also a signal** -- if humans stop providing feedback, it may mean they have given up, or the Factory is already good enough
- Should track **correction depth** -- did the human change a few words, or overturn the entire plan? The latter is a stronger evolution signal

### 1.3 Periodic Review Trigger

**Conventional approach**: Auto-check every N WorkOrders or on a weekly basis.

**In-depth thinking**:
- Periodic review should not merely "check metrics" but should be **structural self-reflection**
- Similar to MemRL's metacognition -- Factory should examine its own memory, strategies, and tool usage patterns
- Periodic review should produce **evolution proposals**, not just reports
- Can introduce the concept of "Evolution Day" -- periodically, Factory stops production and dedicates itself to self-improvement

### 1.4 Human Request Trigger

**Conventional approach**: Human inputs `/evolve` or a similar command.

**In-depth thinking**:
- Human requests should be divided into two categories: **directed evolution** ("optimize Pipeline speed") and **open-ended evolution** ("see what can be improved")
- Directed evolution should have higher priority and faster response
- Open-ended evolution requires more comprehensive review and can be merged with periodic review
- Humans can also **preset evolution preferences** -- "I value speed over perfection," which would influence evolution direction

### 1.5 Environmental Change Trigger (New)

**In-depth thinking**:
- When the underlying model is upgraded (e.g., GPT-4 -> GPT-5), Factory's prompts and strategies may need adjustment
- When new tools/services become available, Factory should evaluate whether they are worth integrating
- When the project domain changes (e.g., switching from web development to game development), Factory needs recalibration
- When best practices for similar Factories emerge, it should trigger "learning" rather than "reinventing"

---

## II. Evolution Content (What)

### 2.1 Pipeline Optimization

**Basic optimization**:
- Adjust step ordering
- Add/remove validation checkpoints
- Optimize information passing formats between steps

**In-depth optimization**:
- **Pipeline topology restructuring**: Evolve from a linear Pipeline to a conditional-branch DAG (Directed Acyclic Graph). For example, if a validation step detects a specific type of error, automatically route to a different repair path
- **Dynamic Pipeline**: Dynamically select Pipeline mode based on task complexity -- simple tasks take a fast track, complex tasks go through the full process
- **Pipeline componentization**: Each Pipeline step is a replaceable component; evolution can optimize individual steps without affecting others

### 2.2 Tool Strategy Optimization

**Basic optimization**:
- Learn new tool combinations
- Optimize tool invocation parameters

**In-depth optimization**:
- **Tool discovery**: Can Factory proactively search for and evaluate new tools? (Similar to LATM's tool creation approach)
- **Tool synthesis**: Combine multiple tools into "super tools" -- a high-level abstraction that internally calls multiple low-level tools
- **Tool retirement**: Detect tools that are no longer used and suggest removal, reducing Cognitive Load and maintenance costs
- **Tool substitution**: When a tool frequently errors, automatically find alternatives

### 2.3 Prompt Engineering Optimization

**Basic optimization**:
- Improve role definitions
- Optimize context packaging

**In-depth optimization**:
- **Dynamic few-shot selection**: Based on current task characteristics, retrieve the most similar past successful cases from memory as few-shot examples
- **Prompt A/B testing**: Maintain multiple prompt versions for the same step and automatically select the best-performing one
- **Prompt compression**: As model capabilities improve, verbose prompts may become unnecessary; evolution should detect and simplify them
- **Cross-Factory prompt transfer**: Can prompt patterns validated in one Factory be transferred to other Factories?

### 2.4 Validation Rule Optimization

**Basic optimization**:
- Add checkpoints based on error types
- Adjust validation strictness

**In-depth optimization**:
- **Validation rule generation**: Can Factory automatically generate new validation rules based on historical error data?
- **Validation cost optimization**: Not all outputs require equally strict validation -- low-risk outputs can take a fast validation track
- **Validation feedback loop**: Validation rules themselves need to evolve -- if a rule has never been triggered, it may be redundant
- **Human calibration**: Deviations between validation rules and human standards should be continuously tracked and corrected

### 2.5 Memory Strategy Optimization

**Basic optimization**:
- Improve retrieval query methods
- Optimize memory storage structure

**In-depth optimization**:
- **Memory forgetting**: Not all memories are worth keeping permanently. Similar to human forgetting curves, low-importance, long-unaccessed memories should be archived or deleted
- **Memory compression**: Merge multiple similar memories into a single high-level "rule of thumb"
- **Memory index optimization**: As memory volume grows, retrieval efficiency may decline. Evolution should optimize indexing strategies
- **Cross-Factory memory sharing**: Can one Factory's "lessons learned" help another Factory avoid the same mistakes? (Requires privacy and security considerations)

---

## III. Safety Guarantees (Safety)

### 3.1 Sandbox Testing

**Basic approach**: Test the evolved Factory in an isolated environment.

**In-depth thinking**:
- The sandbox should not merely be an "isolated environment" but a **reproducible experimental environment** -- identical inputs should produce comparable outputs
- The sandbox should support **snapshots and replay** -- any test can be precisely reproduced
- The sandbox should have **resource limits** -- preventing runaway evolution from consuming excessive compute resources
- Sandbox testing should include **adversarial testing** -- deliberately providing difficult inputs to test Factory's robustness

### 3.2 Progressive Deployment

**Basic approach**: Start with a small-scale trial, then deploy fully after confirming no issues.

**In-depth thinking**:
- Deployment should not be a simple "percentage switch" but **confidence-based progressive deployment**
- **Automatic rollback conditions** should be defined -- no human intervention needed; when metrics fall below thresholds, automatically roll back
- **Evolution history** should be preserved -- each evolution is a version that can be returned to at any time
- During deployment, **human satisfaction signals** should be continuously collected, not just automated metrics

### 3.3 Core Invariance

**Basic approach**: Define immutable core behaviors.

**In-depth thinking**:
- "Core invariance" should not be static -- as Factory matures, certain core constraints may be relaxable
- Core invariance should be divided into **hard constraints** (absolutely unviolable) and **soft constraints** (require additional approval to modify)
- Hard constraints should be kept to a minimum -- only items truly critical to Factory identity and safety
- There should be a **core constraint review mechanism** -- periodically evaluate whether existing constraints are still reasonable

### 3.4 Evolution Budget (New Concept)

- Each evolution operation has a "cost" (compute resources, testing time, deployment risk)
- Factory should have an **evolution budget** -- autonomously evolve within budget; exceeding budget requires human approval
- Budget can be dynamically adjusted based on Factory "health" -- well-performing Factories receive more autonomous evolution budget
- Evolution budget should also consider **human attention budget** -- do not submit too many evolution proposals for human approval at once

---

## IV. Human-in-the-Loop

### 4.1 Evolution Proposal Approval

**Basic approach**: AI proposes a plan, human approves.

**In-depth thinking**:
- Approval should not be a binary "approve/reject" -- should support **conditional approval** ("approved, but only within this scope")
- Humans should be able to see **impact predictions** -- what capabilities will this change affect? What risks might it introduce?
- Should support **batch approval** -- merge multiple small, low-risk evolutions into a single approval request
- The approval interface should be **intuitive** -- use diff views to show changes, rather than making humans read large blocks of text

### 4.2 Real-time Feedback Learning

**Basic approach**: Human corrections automatically converted into learning signals.

**In-depth thinking**:
- Feedback should distinguish between **immediate corrections** ("change this here") and **directional feedback** ("the overall approach is wrong")
- Immediate corrections can be learned automatically; directional feedback should trigger deeper evolutionary analysis
- Should track **feedback adoption rate** -- if Factory frequently ignores human feedback, this is a serious problem
- Humans should be able to **revoke** a learning event -- "that correction doesn't count, I was wrong at the time"

### 4.3 Evolution Direction Guidance

**Basic approach**: Humans set priorities, AI explores within constraints.

**In-depth thinking**:
- Guidance should not be static -- priorities change as the project progresses
- Can introduce **Evolution OKRs** -- humans set goals ("raise success rate to 90%"), Factory autonomously plans how to achieve them
- Factory should **proactively report** evolution progress -- not wait for humans to ask, but periodically push evolution reports
- There should be an **Evolution Roadmap** -- a medium-to-long-term evolution plan co-maintained by Factory and humans

---

## V. Wild Ideas

### 5.1 Can Factory invent new Pipeline patterns?

**Idea**: Not just optimize existing Pipelines, but invent entirely new work modes from scratch.

**Feasibility analysis**:
- The Self-Evolving Agents Survey mentions that the most advanced self-evolving agents can modify their own architecture
- In Heya's context, this means Factory could invent entirely new SOPs, not merely adjust existing ones
- **Implementation path**: Factory analyzes a large number of successful/failed cases, discovers patterns not covered by the existing Pipeline, then proposes entirely new Pipeline designs
- **Risk**: Novel Pipelines have no historical data support; validation cost is extremely high
- **Conclusion**: Feasible, but must undergo rigorous sandbox validation and human approval

### 5.2 Can Factory learn from other Factories' experiences?

**Idea**: Cross-Factory knowledge transfer.

**Feasibility analysis**:
- MemRL's core idea is reinforcement learning on memory -- if memory can be shared across Factories, learning can cross Factory boundaries
- **Implementation path**: Establish a "shared experience pool" where each Factory can contribute and retrieve experiences
- **Challenge**: Domain differences between Factories may make experiences inapplicable -- requires semantic matching, not simple copying
- **Privacy concern**: Factory A's users may not want Factory B to know their project details -- requires experience desensitization
- **Conclusion**: High value but high complexity; recommended as a long-term goal

### 5.3 Can Factory perform "hypothetical evolution"?

**Idea**: Without actually executing evolution, simulate "what would happen if we made this change" in a sandbox.

**Feasibility analysis**:
- Similar to "what-if analysis" in software engineering
- **Implementation path**: Create multiple variants of Factory in the sandbox, test each variant's performance against historical data
- **Value**: Can evaluate multiple evolution directions simultaneously and select the optimal one
- **Cost**: Significant compute resource consumption, but far lower than the cost of an actual deployment failure
- **Conclusion**: Should be part of the standard evolution workflow

### 5.4 Can Factory "dream"?

**Idea**: During idle time, Factory randomly explores new strategy combinations, similar to animal play behavior.

**Feasibility analysis**:
- The "exploration vs exploitation" tradeoff in reinforcement learning -- "dreaming" is exploration
- **Implementation path**: Periodically (e.g., at night) randomly generate and test Pipeline variants in the sandbox
- **Value**: May discover optimization directions humans have not considered
- **Risk**: Wastes compute resources; may produce meaningless variants
- **Conclusion**: Interesting but low priority; can be attempted after Factory matures

### 5.5 Evolution of Evolution (Meta-Evolution)

**Idea**: The evolution mechanism itself can also evolve.

**Feasibility analysis**:
- The ultimate vision of the Self-Evolving Agents Survey -- agents not only evolve their behavior but also evolve their own evolution rules
- **Implementation path**: Make evolution trigger conditions, validation rules, deployment strategies, etc., also objects of evolution
- **Risk**: Infinite recursion -- who evolves the "evolution of evolution"? Need to set meta-evolution boundaries
- **Conclusion**: This is the ultimate goal, but requires extreme caution. Recommended only after Factory has been running stably for 6 months

---

## VI. Connections to Academic Research

### 6.1 MemRL (Zhang et al., 2026)

**Connection points**:
- MemRL's core is "runtime reinforcement learning on memory" -- this is precisely the theoretical foundation of Heya's self-evolution
- **Stability-plasticity decomposition**: Heya's Core/Extension separation directly corresponds to this concept
  - Core Layer = stable component (not easily changed)
  - Extension Layer = plastic component (continuous learning)
- **Runtime RL**: Heya does not update model weights but "learns" by modifying SOPs, prompts, and validation rules -- this is exactly MemRL's approach

### 6.2 Reflexion (Shinn et al., 2023)

**Connection points**:
- Reflexion's "linguistic feedback" mechanism -- human feedback stored as natural language, AI reflects on this feedback in subsequent attempts
- Heya's evolution proposals should include a **reflection process** -- not just "what was changed" but also "why it was changed" and "expected effects"
- Reflexion's "self-reflection" -- Factory should periodically examine its own behavioral patterns, not merely rely on external feedback

### 6.3 Self-Evolving Agents Survey (Fang et al., 2025)

**Connection points**:
- Four-component feedback loop: Inputs -> Agent System -> Environment -> Optimisers
  - **Inputs**: User intent + WorkOrder
  - **Agent System**: Factory (AI-organized capabilities)
  - **Environment**: Tools, storage, external systems
  - **Optimisers**: Metacognition Layer + human feedback
- Three evolution levels mentioned in the survey:
  1. **Parameter-level evolution**: Updating model weights (Heya does not do this)
  2. **Prompt-level evolution**: Modifying prompts and strategies (Heya's primary evolution method)
  3. **Architecture-level evolution**: Modifying agent structure (Heya's ultimate goal)

### 6.4 MetaGPT (Hong et al., 2023)

**Connection points**:
- SOP encoding -- Heya's Pipeline is the encoded form of SOPs
- Intermediate validation -- each step has a validation gate; evolution can modify these gates
- Structured communication -- steps communicate using structured documents; evolution can optimize document formats

### 6.5 CoALA (Sumers et al., 2023)

**Connection points**:
- Three types of memory in cognitive architectures -- Heya's memory system is directly based on this
- Memory evolution is an important part of self-evolution -- retrieval strategies, storage formats, and forgetting rules can all evolve

---

## VII. Key Insights and Principles

### Insight 1: Evolution is layered

Not all evolutions are equally important. Should distinguish:
- **Micro-evolution**: Parameter tuning, prompt fine-tuning (high frequency, low risk, automatable)
- **Meso-evolution**: Pipeline step addition/removal, tool replacement (medium frequency, medium risk, requires approval)
- **Macro-evolution**: Pipeline topology restructuring, core strategy changes (low frequency, high risk, requires deep approval)

### Insight 2: Evolution needs "evolution metadata"

Each evolution should record:
- Trigger reason (why evolve?)
- Change content (what was changed?)
- Validation results (how did it perform?)
- Human feedback (what did humans think?)
- Rollback history (was it rolled back? why?)

This metadata itself is a valuable learning resource.

### Insight 3: The goal of evolution is to "reduce the need for evolution"

The best evolution makes Factory good enough that frequent evolution is no longer needed. If Factory needs to constantly evolve, it indicates a problem with the foundational design.

### Insight 4: Human-AI trust is a prerequisite for evolution

If humans do not trust Factory's evolution proposals, the entire system degrades to manual operation. Building trust requires:
- Transparent evolution rationale
- Verifiable evolution effects
- Reversible evolution operations
- Continuous evolution quality tracking

---

## VIII. Questions to Explore

1. What is the minimum viable unit of evolution? How small can a single evolution be?
2. How to quantify "evolution success"? Besides success rate, what other metrics exist?
3. When multiple evolution directions conflict, how should tradeoffs be made?
4. Should evolution have "directionality"? Or should it be entirely data-driven?
5. How to prevent "evolution drift" -- Factory gradually deviating from original intent?

---

*Brainstorm complete. Next step: convert key ideas into specific proposals.*
