# Week 06 — Observability, Evaluation & Capstone

---

## 1. AgentOps and Monitoring / Agent Observability and Tracing
**Definition:** The practice of continuously tracking an agent system's health — logging every agent and tool call's timing, status, and errors — so problems can be spotted and diagnosed rather than discovered by accident.

**Types:**
- **Metrics/monitoring** — dashboards tracking throughput, latency, error rates over time.
- **Tracing** — following a single request's full path through every agent/tool call it touched.

**Examples:**
- Every agent and tool call logs timing, status, and errors to a live dashboard.
- A malformed ticket is traced back to exactly which stage it failed at.
- Load-testing at 10,000 tickets/hour reveals the first bottleneck, which gets a documented fix.

---

## 2. Agent Runtime Management (Self-Maintaining Layer)
**Definition:** Extending runtime management so the system maintains itself — automatically refreshing its own data and isolating failures — instead of needing manual intervention to stay healthy.

**Types:**
- **Scheduled self-maintenance** — nightly jobs that refresh indexes/data automatically.
- **Failure isolation** — a dead-letter queue captures repeatedly-failing items so they don't block the pipeline.

**Examples:**
- A nightly pipeline re-indexes new Drive documents automatically.
- A ticket that fails three times lands safely in a dead-letter queue instead of stalling everything else.
- A malformed ticket reaches the dead-letter queue cleanly with no impact on live processing.

---

## 3. Deployed AI Agents and Building Autonomous AI Agents (Capstone Integration)
**Definition:** Bringing every piece built across the course together into one live, end-to-end running system — the full capstone — rather than isolated components tested separately.

**Types:**
- **Component-level testing** — each agent/piece tested alone (done in earlier weeks).
- **End-to-end integration** — the entire pipeline running together in one unbroken session.

**Examples:**
- All six weeks of build layers run live: ticket in, cluster forms, ops approves, alert fires, draft email appears — one session.
- The full system is supported by a documented architecture doc, cost audit, and red-team review.
- The complete workflow now moves a ticket from submission to approved escalation without manual intervention.

---

## 4. Autonomous Workflows (End-to-End)
**Definition:** The complete, unbroken automated flow from the moment a ticket is submitted to the moment an escalation is approved — with every step in between happening without a human manually kicking it off.

**Types:**
- **Partial automation** — some steps still need manual triggers (earlier in the course).
- **Full end-to-end automation** — every step, including monitoring and self-healing, runs unattended.

**Examples:**
- Tickets move from submission to approved escalation in a single, uninterrupted flow.
- No manual step is needed anywhere in the process once the system is deployed.
- Failure handling (dead-letter queue, retries) happens automatically within this same flow.

---

## 5. Business Process Automation
**Definition:** Using the full agentic pipeline to fully automate a real enterprise process end-to-end — in this case, ticket triage and escalation — replacing what would otherwise be manual, repetitive human work.

**Types:**
- **Partial process automation** — automates some steps, humans handle the rest.
- **Full process automation** — the entire business process runs without manual work, aside from approvals.

**Examples:**
- Enterprise ticket triage and escalation is fully automated from end to end.
- What used to require a support team manually reading and routing tickets now runs unattended.
- Human involvement is reduced to approval checkpoints rather than doing the triage itself.

---

## 6. Evaluation and Benchmarking Agents
**Definition:** Systematically testing an agent system under load and documenting where it breaks, so performance limits are known rather than discovered in production.

**Types:**
- **Load testing** — pushing volume to find breaking points.
- **Bottleneck analysis** — identifying and documenting the specific weak point found.

**Examples:**
- The system is load-tested at 10,000 tickets per hour.
- The first bottleneck found is identified and a specific fix is written up.
- Results are documented so future capacity planning has real data to work from.

---

## 7. Evaluator-Optimizer Pattern
**Definition:** A two-part pattern where one agent (the evaluator) scores another agent's output against a rubric, and a second step (the optimizer) automatically revises low-scoring output until it clears a quality threshold or hits a retry limit.

**Types:**
- **Single-pass evaluation** — score once, no revision (weaker approach).
- **Loop-based evaluator-optimizer** — score, revise, re-score, repeat until threshold or retry limit.

**Examples:**
- An evaluator agent scores each escalation package against a rubric before it reaches the human queue.
- A deliberately low-quality draft gets revised automatically until its score clears the threshold, within three attempts.
- If the retry limit is hit without success, the package is flagged for direct human review instead of looping forever.
