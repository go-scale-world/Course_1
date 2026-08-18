# Week 04 — Human Review, Multi-Agent Architecture & Parallel Processing

---

## 1. Human-in-the-Loop Oversight (Dashboard Layer)
**Definition:** Extending human review into a real, usable interface — a browser dashboard — so approvals/rejections and system health are visible and actionable in real time, not buried in logs.

**Types:**
- **Live/auto-refreshing views** — dashboard updates as new data arrives.
- **Action-embedded review** — approve/reject happens inline, not in a separate tool.

**Examples:**
- New clusters appear automatically in an auto-refreshing table with no page reload needed.
- A reviewer clicks a row to see full ticket detail, then approves or rejects right there.
- If credentials are revoked mid-session, the dashboard shows a clear error instead of silently approving anything.

---

## 2. Multi-Agent Systems and Multi-Agent Orchestration
**Definition:** Splitting one large task across several specialized agents that each do one job well, coordinated together rather than one giant agent trying to do everything.

**Types:**
- **Sequential multi-agent** — agents run one after another in a fixed order.
- **Coordinated/dynamic multi-agent** — order can change based on conditions (e.g., severity).

**Examples:**
- The pipeline splits into four agents: Extract, Cluster, Score, Escalate — each with one job.
- If the clustering agent is stopped mid-run, the orchestrator retries or fails cleanly instead of losing the ticket.
- Each agent's step is captured in a detailed execution log for traceability.

---

## 3. Workflow Orchestration and Orchestrator-Worker Pattern
**Definition:** A central "orchestrator" component sequences multiple "worker" agents, passing data between them, retrying failures, and branching logic based on results.

**Types:**
- **Linear orchestration** — fixed sequence of workers.
- **Conditional/branching orchestration** — path changes based on data (e.g., severity level).

**Examples:**
- Cloud Workflows sequences Extract → Cluster → Score → Escalate, passing data at each step.
- A failed clustering step is automatically retried by the orchestrator rather than crashing the whole pipeline.
- A high-severity result branches straight to the Escalate agent instead of following the default path.

---

## 4. Task Decomposition
**Definition:** Breaking one large, complex problem into smaller, well-scoped sub-tasks that are easier to build, test, and assign to individual agents.

**Types:**
- **Functional decomposition** — split by job type (extract vs. cluster vs. score).
- **Sequential decomposition** — split by stage in a pipeline.

**Examples:**
- One enterprise ticket-triage problem is split into four discrete agent responsibilities.
- Each agent can be tested and improved independently without touching the others.
- If the scoring logic needs to change, only the Score agent needs updating.

---

## 5. Handoff Pattern
**Definition:** The formal transfer of data (and responsibility) from one agent to the next in a pipeline, with validation at each transition point.

**Types:**
- **Validated handoff** — data checked before being accepted by the next agent.
- **Direct handoff** — data passed with no intermediate check (riskier, avoided here).

**Examples:**
- Cluster output is validated before being handed to the Score agent.
- If a handoff fails validation, the ticket doesn't silently disappear — it's flagged.
- Each stage transition is logged so a failure can be traced to the exact handoff point.

---

## 6. Parallelization Pattern
**Definition:** Running multiple independent tasks (or ticket streams) at the same time rather than one after another, to handle scale without a growing backlog.

**Types:**
- **Data parallelism** — same process run on many items simultaneously (e.g., many tickets at once).
- **Task parallelism** — different steps run concurrently when they don't depend on each other.

**Examples:**
- Tickets are ingested in parallel rather than one at a time.
- Agent 3 can query historical data for multiple groups simultaneously without blocking.
- The system processes tickets at scale without building up a backlog.

---

## 7. Routing Pattern
**Definition:** Automatically directing a task down a different path based on some property of the data — like severity — rather than treating every input the same way.

**Types:**
- **Rule-based routing** — fixed thresholds decide the path.
- **Model-driven routing** — the LLM itself decides which path fits.

**Examples:**
- A group growing at 5x its baseline is automatically classified Critical and routed straight to escalation.
- Low-priority tickets route to a queue; Critical ones route to Agent 4 immediately.
- Priority levels (Low/Medium/High/Critical) each trigger a different downstream path.

---

## 8. Reflection Design Pattern
**Definition:** An agent reviews its own output against a checklist or quality bar and revises it *before* handing it off, rather than sending its first draft straight through.

**Types:**
- **Self-critique** — agent checks its own work against criteria.
- **Iterative refinement** — agent revises repeatedly until quality criteria are met.

**Examples:**
- Agent 4 checks its escalation draft against a quality checklist and fixes gaps before finalizing it.
- A deliberately incomplete draft is caught and corrected during self-review before reaching the human queue.
- Escalation packages show measurably fewer missing-field errors after this self-review step.

---

## 9. Plan-and-Solve Pattern
**Definition:** An agent first produces a short plan of sub-steps before executing them, instead of always running one fixed sequence regardless of the input.

**Types:**
- **Static planning** — one fixed plan used every time (what this pattern moves away from).
- **Dynamic planning** — plan generated fresh based on the specific input.

**Examples:**
- Agent 1 produces a short plan (which fields to check, which tools to consult) before extracting anything.
- An unusually-worded ticket produces a different sub-step plan than a standard one.
- The agent still reaches a correct classification even when its plan deviates from the "normal" path.

---

## 10. Planning Algorithms
**Definition:** The underlying logic that lets an agent generate a plan dynamically per-input, rather than following one static, hardcoded orchestration sequence.

**Types:**
- **Static sequencing** — one fixed order for every input (the old approach).
- **Adaptive planning** — plan generation adapts steps to each item's specific ambiguity.

**Examples:**
- A ticket with ambiguous phrasing causes Agent 1 to generate extra clarification sub-steps.
- A straightforward ticket produces a short, direct plan with fewer sub-steps.
- The adaptive plan still reaches the same required outcome (correct classification) via a different route.
