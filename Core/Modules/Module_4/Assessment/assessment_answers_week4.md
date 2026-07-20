# Week 4 Assessment — Answer Key
### Divide the Problem, Then Make the Division Intelligent · Running Project: EPC Agent

---

## Section A — Knowledge Check

**A1. Answer: B** — A) speed isn't the stated benefit; C) an orchestrator is still needed to coordinate the specialists; D) unrelated to database choice.

**A2. Answer: B** — A) the orchestrator coordinates, the specialist agents do the actual work; C) archive storage is a memory-layer concern, not orchestration; D) guardrails are a separate, still-necessary layer.

**A3. Answer: B** — A) network speed isn't what's being validated; C) login status is unrelated to handoffs; D) a specific field isn't the general check, correctness of the whole output is.

**A4. Answer: B** — A) the scope genuinely changed, not just the name; C) approval is still required for high-stakes decisions; D) it supervises all four agents, not just one.

**A5. Answer: B** — A) more agents alone doesn't guarantee accuracy; C) the same underlying model can power each agent; D) validation still applies to every specialist's output.

**A6. Answer: B** — A) that's the Handoff Pattern's job; C) storage is a memory-layer concern; D) tool choice is a Week 2 tool-calling concern.

**A7. Answer: B** — A) accuracy isn't what parallelization changes; C) the orchestrator is still needed per ticket; D) validation is the Handoff Pattern's job, not parallelization's.

**A8. Answer: B** — A) validation still happens, just with an added self-check; C) that describes the old, pre-reflection behavior; D) the agent does this itself, not a human.

**A9. Answer: B** — A) all four agents are still used, just applied adaptively; C) it applies based on ambiguity/complexity, not just severity; D) the orchestrator is still needed to run the resulting plan.

**A10. Answer: B** — A) they solve genuinely different problems; C) routing applies to any industry's tickets; D) routing and orchestration both run as part of normal execution, not conditionally on failure.

**Difficulty mix:** A1, A2, A3, A6, A7, A8 = straightforward recall. A4, A5, A9, A10 = require distinguishing closely related concepts (Week 3's approval panel vs. Week 4's dashboard, why specialization improves judgment, fixed pipeline vs. dynamic planning, routing vs. orchestration).

---

## Section B — Applied Skill Check

**B1. Expected answer:**
```python
validate_handoff(extraction)
```
Without this call, the Cluster Agent could receive a malformed or incomplete extraction and silently produce nonsensical groupings — the handoff must be validated before the next specialist starts working with it.

**B2. Expected answer:**
A `"Critical"` severity cluster should route straight to **immediate escalation**, skipping the normal step-by-step wait. A `"Low"` severity cluster should follow the **normal pipeline** (Extract → Cluster → Score → Escalate) as usual — routing changes the path taken, not the underlying system.

**B3. Expected answer:**
```python
asyncio.gather(*(route(t) for t in tickets))
```
This runs the routing (and downstream processing) for every ticket in the batch concurrently, rather than processing them one at a time in sequence.

**B4. Expected answer:**
```python
critique_agent.review(draft)
```
This is what actually generates the critique of the current draft — without it, `reflect()` has nothing to base an improvement decision on.

**B5. Expected answer:**
```python
dashboard.report_status(agent.name, result)
```
Without this line, each agent still runs and hands off correctly, but the dashboard has no way to know which agent just executed, what it produced, or whether it's healthy — supervision requires each stage to actively report its status, not just complete silently.

---

## Section C — Holistic Scope Check

**C1. Model answer:**
- Each of the 300 tickets should first pass through Routing — critical, urgent order issues (like payment failures) route straight to immediate escalation, while routine issues follow the normal four-step pipeline.
- Independent tickets, with no relationship to one another, should be processed in parallel rather than queued one at a time, using the Parallelization Pattern, so throughput doesn't collapse under the sudden volume.
- The Orchestrator still coordinates the four-step sequence for each ticket individually — parallelization and orchestration aren't in conflict, the same coordinated sequence just runs for many tickets simultaneously.
- Handoffs between Extract → Cluster → Score → Escalate still get validated for every single ticket, even under high parallel load — speed should never come at the cost of validation.
- The Human Supervision Dashboard should show elevated throughput and agent health during this surge, giving a human visibility into whether the system is actually keeping up, without needing to manually review all 300 tickets individually.

**C2. Model answer:**
- The Cluster Agent's output is missing `cluster_id` — this is exactly the kind of malformed handoff the Handoff Pattern exists to catch.
- `validate_handoff()` should reject this output before the Score Agent ever receives it, rather than letting it silently propagate downstream.
- The correct recovery is to flag this as a failed handoff and retry or re-run the Cluster Agent's step, rather than having the Score Agent attempt to work with incomplete data.
- If this isn't caught here, it becomes a cascading failure — the Score Agent produces a meaningless result, and the Escalation Agent drafts a report based on garbage, with the root cause only traced much later.
- This scenario is exactly why validation happens at every single handoff, not just once at the very end of the pipeline.

**C3. Model answer:**
- This is the Endless Reflection Loop failure mode — the agent keeps critiquing and revising, never satisfied, with no stopping condition in place.
- The fix is a hard cap on the number of reflection cycles, a `max_attempts` limit, after which the agent sends the best version it has produced so far, rather than continuing indefinitely.
- Without this cap, a single problematic ticket can silently consume resources and delay everything queued behind it in the pipeline.
- The Human Supervision Dashboard should be able to surface this — a ticket stuck in one stage for ten minutes is a visible anomaly, not something that should go unnoticed.
- This illustrates that reflection needs boundaries just like any other agent capability — self-improvement with no stopping condition is a liability, not a feature.

**C4. Model answer:**
- Instead of forcing the filing through the fixed Extract → Cluster → Score → Escalate sequence exactly as written, the agent should first understand the filing and generate a plan specific to it, following the Plan-and-Solve pattern.
- If the filing is genuinely ambiguous, the generated plan might include an early step to request clarification or gather more context, rather than guessing and proceeding blindly through the standard steps.
- The plan still executes step by step, evaluating after each one — if something doesn't check out, the agent can re-plan rather than committing blindly to its original plan.
- This doesn't replace the four specialized agents — it changes how and in what order their capabilities get applied to this specific, unusually complex case.
- The key distinction from the standard flow: complexity and ambiguity are recognized upfront, and the sequence adapts to them, rather than every filing being forced through an identical process regardless of how well it actually fits.

**C5. Model answer:**
- The orchestrator's job is to coordinate the actual execution of the four specialized agents for a single ticket, deciding what runs next, moment to moment.
- The dashboard's job is entirely different — it gives a human ongoing visibility into the health and status of the whole system, across many tickets, over time.
- Without the orchestrator, the four agents would have no coordinated way to hand work to each other at all — nothing would actually execute correctly.
- Without the dashboard, the orchestrator could be silently failing, backing up, or making decisions with nobody aware of it, until something visibly breaks downstream.
- Both need to exist because one is about making the system work correctly in the moment, and the other is about making that correctness visible and supervisable over time — they solve genuinely different problems, at different scopes.
