# Week 4 Slide Deck — Two S-Tier Lectures
### Running Project: Enterprise Problem Consolidation (EPC) Agent
### Design system: Signal Orange #F2661D (active) · Slate Blue #3E6FA8 (structural) · Pale Blue Tint #E8EFF7 · Deep Navy #1A2B4C (text/lines) · backgrounds white or Pale Blue Tint only

---
---

# S-TIER 1 — Multi-Agent Systems & Enterprise Orchestration
### Industries sampled: Healthcare, Finance, Legal, Manufacturing

---

### Slide 1 — HOOK
**Text:** "Multi-Agent Systems is teaching several computers to share one job."
Sub-line: "Should one mind be excellent at extraction and business judgment, both?"
**Visual:** White background. Definition in huge Deep Navy display type, centered, two lines. Sub-line small Slate Blue italic beneath.

---

### Slide 2 — PROBLEM PAIR
**Text (Card 1):** "One agent handling reception through billing, alone." → "A solo doctor expected to personally handle triage, surgery, and billing for every patient — this is exactly what we cannot do."
**Text (Card 2):** "Every job wound together inside one tangled process." → "A single chef who takes the order, cooks, plates, and serves every table alone — this is exactly what we cannot do."
**Visual:** Two Pale Blue Tint cards, Deep Navy frame. Card 1 icon: a single figure wearing a stethoscope and a hard hat at once. Card 2 icon: a chef's hat with four small utensils crowded around it. Analogy line ends in Signal Orange "...we cannot do."

---

### Slide 3 — COMPARISON TABLE
**Text:**
| Without Multi-Agent Systems | With Multi-Agent Systems |
|---|---|
| One agent must be expert at every step | Each agent specializes in one responsibility |
| A change to one job risks breaking every other job | Each agent can be improved independently |
| Every ticket bottlenecks through one tangled process | Specialized agents coordinate efficiently |
| Failure anywhere breaks the whole reasoning chain | A failure is isolated and handled at that handoff |
**Visual:** Two-column table, thin Deep Navy row rules only. Left column Slate Blue, right header Signal Orange, body Deep Navy. White background.

---

### Slide 4 — RUNNING EXAMPLE INTRO
**Text:** "One agent becomes four specialists: Extract, Cluster, Score, Escalate."
**Visual:** Centered white background. One large Slate Blue icon of a single circle splitting into four smaller connected circles. "Enterprise Problem Consolidation (EPC) Agent" in Deep Navy beneath, caption in Signal Orange italic underneath.

---

### Slide 5 — CONCEPT DIAGRAM (Rep. 1) — *fixed shape, reused in Slide 11 and 17*
**Text:** RECEIVE WORK → SPECIALIST AGENT → HANDOFF → NEXT SPECIALIST AGENT → ... → COMPLETE
**Visual:** A horizontal chain of Slate Blue rounded rectangles on Pale Blue Tint background, alternating with small Deep Navy diamond "handoff" markers between each pair of boxes, ending in a Slate Blue "Complete" box. No orange — pure structure.

---

### Slide 6 — EXECUTION FLOW (Rep. 2)
**Text (6 stages):** 1. Ticket Arrives at One Giant Agent (the old way) · 2. Recognize the Overload · 3. Four Specialists Emerge: Extract → Cluster → Score → Escalate · 4. Something Decides Which Agent Runs Next · 5. Each Transition Must Be Safe · 6. Workflow Completes
**Tags above stages 2, 4, 5:** Task Decomposition · Workflow Orchestration · Handoff Pattern
**Visual:** Six Slate Blue boxes in a row (box 3 slightly wider, containing four small sub-labels: Extract/Cluster/Score/Escalate), Deep Navy arrows, Signal Orange pill tags above boxes 2, 4, and 5.

---

### Slide 7 — CODE (Rep. 3)
**Text (code panel):**
```python
def orchestrate(ticket):
    extraction = extract_agent.run(ticket)
    validate_handoff(extraction)                 # safe transfer, not just messaging
    cluster = cluster_agent.run(extraction)
    validate_handoff(cluster)
    score = score_agent.run(cluster)
    validate_handoff(score)
    return escalate_agent.run(score)
```
**Visual:** Dark Deep Navy code panel on Pale Blue Tint background, white monospace code. Each `validate_handoff(...)` line highlighted with a Signal Orange inline comment — visually repeating three times to emphasize the pattern.

---

### Slide 8 — CONTEXT-SWAP STRIP
**Text (4 mini-flows):**
Healthcare: Intake → Validate → Triage → Validate → Diagnosis → Validate → Billing
Finance: Intake → Validate → Risk Check → Validate → Approval → Validate → Reporting
Legal: Intake → Validate → Review → Validate → Risk Flag → Validate → Routing
Manufacturing: Intake → Validate → Inspect → Validate → Assemble → Validate → Ship
**Visual:** Four rows stacked, alternating white/Pale Blue Tint backgrounds. Compact 7-chip Slate Blue mini-flows with small Deep Navy diamond handoff markers, Signal Orange industry tag at the left of each row.

---

### Slide 9 — DEFINITION v2
**Text:** ~~Multi-Agent Systems is teaching several computers to share one job.~~ Multi-Agent Systems is teaching several specialized computers to **each own one responsibility, hand work safely to the next one, and let something coordinate the whole sequence.**
**Visual:** Same layout as Slide 1. v1 shown small, faded, struck through. New clause in Signal Orange within a Deep Navy sentence.

---

### Slide 10 — SCOPE-INCREASE GRID
**Text (4 tiles):** Decompose the Ticket Correctly · Run Specialists in Order · Validate Every Handoff · Allow Human Supervision
**Visual:** Four Pale Blue Tint square tiles in a row, white background, Slate Blue icons (split-arrow / numbered-sequence / checkmark-diamond / eye). Fourth tile outlined in Signal Orange — the new capability the next slides unpack.

---

### Slide 11 — CONCEPT DIAGRAM v2 (Cycle 2) — *reuses Slide 5's shape exactly*
**Text:** A "Human Supervision Dashboard" band (Approval Queue · Agent Health · Throughput · Status) sits above the unchanged chain: TICKET → EXTRACT → HANDOFF → CLUSTER → HANDOFF → SCORE → HANDOFF → ESCALATE → COMPLETE
**Visual:** Slide 5's chain untouched along the bottom. A new wide Signal Orange-bordered band spans the top of the slide, containing four small labels, connected to the chain below by a single thin Deep Navy arrow labeled "observes / intervenes" pointing down into the middle of the chain.

---

### Slide 12 — EXECUTION FLOW v2 (Cycle 2)
**Text (6 stages, 1 and 4 unchanged, 2/3/5 new):** 1. Ticket Enters the Pipeline · 2. Each Agent Reports Its Status as It Runs · 3. Dashboard Tracks: Approval Queue, Agent Health, Throughput, Status · 4. Handoffs Proceed as Before, Validated Each Time · 5. Human Can Intervene at Any Stage · 6. Workflow Completes
**Visual:** Six Slate Blue boxes in a row, Deep Navy arrows. Boxes 2, 3, and 5 have a small Signal Orange dot in the corner marking them as new. Box 3 is drawn taller, containing four tiny labeled sub-rows.

---

### Slide 13 — CODE v2 (Cycle 2)
**Text (code panel):**
```python
def orchestrate(ticket):
    for agent in [extract_agent, cluster_agent, score_agent, escalate_agent]:
        result = agent.run(ticket)
        dashboard.report_status(agent.name, result)   # supervise the whole workflow
        validate_handoff(result)
        ticket = result
    return ticket
```
**Visual:** Same dark panel style as Slide 7. Only the `dashboard.report_status(...)` line sits inside a soft rounded Signal-Orange-tinted highlight block — the single new line that makes the whole loop observable.

---

### Slide 14 — CONTEXT-SWAP STRIP v2
**Text (4 rows — what "Agent Health" / "Workflow Status" means):**
Healthcare: Is the triage agent responding normally? → How many patients are mid-workflow right now?
Finance: Is the risk-check agent up to date? → How many transactions are pending review?
Legal: Is the review agent keeping pace? → How many filings are still in progress?
Manufacturing: Is the inspection agent running normally? → How many units are mid-assembly right now?
**Visual:** Four rows, alternating white/Pale Blue Tint backgrounds, Signal Orange industry tag at left. Each row: a Slate Blue "Agent Health" box, Deep Navy arrow, a Signal Orange-outlined "Workflow Status" box.

---

### Slide 15 — DEFINITION v3
**Text:** ~~...hand work safely to the next one, and let something coordinate the whole sequence.~~ Multi-Agent Systems coordinates the whole sequence, and **lets a human supervise the health and status of the entire workflow** — not just approve one agent's output.
**Visual:** Same layout as Slides 1 and 9. v2 sentence faded/struck through above. New clause in Signal Orange appended.

---

### Slide 16 — BREAK-THE-SYSTEM GRID
**Text (6 tiles):** Bad Handoff Accepted · Wrong Next Agent · Silent Agent Failure · Cascading Failure · Dashboard Blind Spot · Duplicate Execution
**Sub-flow:** Detect → Validate → Retry
**Visual:** Six tiles, white background, Signal Orange border, Deep Navy icon per tile (broken-link, crossed-arrows, mute-icon, domino-chain, green-checkmark-with-hidden-x, two-overlapping-copies) + label. Beneath: compact 3-box Slate Blue flow (Detect → Validate → Retry), "Detect" in Signal Orange.

---

### Slide 17 — FINAL MODEL
**Text:** "Multi-Agent Systems divides one enterprise problem into specialized agents, each owning exactly one responsibility, coordinates their execution through an orchestrator, validates every handoff between them, and lets a human supervise the health and status of the entire workflow — not just one agent's output."
**Visual:** White background, Deep Navy paragraph on the left. On the right, Slide 5/11's chain shrunk into a round Signal Orange-bordered badge (four small dots for the specialists connected by thin Deep Navy handoff diamonds, a tiny orange band above representing the dashboard). Caption beneath: "Multi-agent systems are not multiple chatbots — each agent owns one responsibility."

---
---

# S-TIER 2 — Intelligent Agent Coordination & Design Patterns
### Industries sampled: Healthcare, Finance, Retail, Manufacturing

---

### Slide 1 — HOOK
**Text:** "Agent Design Patterns is teaching a system to choose its own strategy."
Sub-line: "Is a company-wide outage the same problem as a forgotten password?"
**Visual:** White background. Definition in huge Deep Navy display type, centered. Sub-line small Slate Blue italic beneath.

---

### Slide 2 — PROBLEM PAIR
**Text (Card 1):** "Every ticket waits in the exact same line." → "An ER making a paper cut and a heart attack wait in the same line — this is exactly what we cannot do."
**Text (Card 2):** "Tickets processed one at a time, even during a surge." → "A single-lane toll booth at rush hour with ten open lanes sitting empty — this is exactly what we cannot do."
**Visual:** Two Pale Blue Tint cards, Deep Navy frame. Card 1 icon: one waiting line with two very different figures in it. Card 2 icon: one occupied toll lane beside nine empty ones. Analogy line ends in Signal Orange "...we cannot do."

---

### Slide 3 — COMPARISON TABLE
**Text:**
| Without Adaptive Patterns | With Adaptive Patterns |
|---|---|
| Every ticket follows the identical fixed path | Tickets are routed by their actual severity |
| Tickets processed one at a time, in sequence | Independent tickets processed simultaneously |
| An agent's first draft is treated as final | An agent critiques and improves its own output |
| One rigid fixed sequence for everything | The plan adapts to how complex the ticket is |
**Visual:** Same table style as S-Tier 1 Slide 3. Left column Slate Blue, right header Signal Orange, body Deep Navy, thin row rules only.

---

### Slide 4 — RUNNING EXAMPLE INTRO
**Text:** "The fixed pipeline, learning to adapt."
**Visual:** Centered white background. One large Slate Blue icon of a single road forking into three paths of different widths. "Enterprise Problem Consolidation (EPC) Agent" in Deep Navy beneath, caption in Signal Orange italic underneath.

---

### Slide 5 — CONCEPT DIAGRAM (Rep. 1) — *fixed shape, reused in Slide 11 and 17*
**Text:** TASK → CHOOSE STRATEGY → EXECUTE (possibly in parallel) → SELF-CHECK → OUTPUT
**Visual:** Four Slate Blue rounded rectangles in a horizontal row on Pale Blue Tint background, Deep Navy arrows. "EXECUTE" box has a faint duplicated ghost-outline behind it hinting at parallelism — still entirely Slate Blue/Deep Navy, no orange yet.

---

### Slide 6 — EXECUTION FLOW (Rep. 2)
**Text (5 stages):** 1. Ticket Arrives at the Fixed Pipeline (the old way) · 2. Should Every Ticket Follow the Same Path? · 3. What If Hundreds Arrive at Once? · 4. Agent Drafts an Escalation, Then Checks Itself · 5. Workflow Completes
**Tags above stages 2, 3, 4:** Routing · Parallelization · Reflection
**Visual:** Five Slate Blue boxes in a row, Deep Navy arrows, Signal Orange pill tags above boxes 2, 3, and 4 only.

---

### Slide 7 — CODE (Rep. 3)
**Text (code panel):**
```python
def route(ticket):
    return escalate_immediately(ticket) if ticket.severity == "Critical" else normal_pipeline(ticket)

async def process_batch(tickets):
    return await asyncio.gather(*(route(t) for t in tickets))   # parallel execution

def reflect(draft):
    critique = critique_agent.review(draft)
    return improve(draft, critique) if critique.has_issues else draft
```
**Visual:** Dark Deep Navy code panel on Pale Blue Tint background, three short functions stacked, white monospace, one Signal Orange inline comment on the `asyncio.gather(...)` line only.

---

### Slide 8 — CONTEXT-SWAP STRIP
**Text (4 mini-flows):**
Healthcare: Route by Urgency → Parallel Intake During a Mass Event → Reflect on a Draft Diagnosis
Finance: Route by Transaction Risk → Parallel Review During Volatility → Reflect on a Risk Report
Retail: Route by Order Value → Parallel Processing During a Flash Sale → Reflect on a Customer Reply
Manufacturing: Route by Defect Severity → Parallel Inspection During a Batch Run → Reflect on a Quality Report
**Visual:** Four rows stacked, alternating white/Pale Blue Tint backgrounds. Compact 3-box Slate Blue flows (Route → Parallel → Reflect), Deep Navy arrows, Signal Orange industry tag at the left of each row.

---

### Slide 9 — DEFINITION v2
**Text:** ~~Agent Design Patterns is teaching a system to choose its own strategy.~~ Agent Design Patterns is teaching a system to **route each problem down the right path, handle many problems at once, and check its own work before passing it along.**
**Visual:** Same layout as Slide 1. v1 shown small, faded, struck through. New clause in Signal Orange within a Deep Navy sentence.

---

### Slide 10 — SCOPE-INCREASE GRID
**Text (4 tiles):** Route by Severity · Process in Parallel · Reflect and Improve · Recognize Genuine Complexity
**Visual:** Four Pale Blue Tint square tiles in a row, white background, Slate Blue icons (fork / stacked-layers / refresh-loop / question-mark). Fourth tile outlined in Signal Orange — the not-yet-explained capability.

---

### Slide 11 — CONCEPT DIAGRAM v2 (Cycle 2) — *reuses Slide 6's flow shape, extends it with a new fork*
**Text:** TICKET → UNDERSTAND → GENERATE PLAN (varies per ticket) → EXECUTE STEP BY STEP → EVALUATE → FINISH, branching into PLAN A (simple) / PLAN B (complex) / PLAN C (ambiguous, needs clarification first)
**Visual:** A horizontal Slate Blue flow (Understand → Generate Plan → Execute → Evaluate → Finish), with one new Signal Orange three-way fork beneath "Generate Plan" leading to three small labeled boxes (Plan A / Plan B / Plan C). Everything else stays Slate Blue/Deep Navy — only the fork is new.

---

### Slide 12 — EXECUTION FLOW v2 (Cycle 2)
**Text (5 stages, 1 unchanged, 2–5 new):** 1. Ticket Arrives · 2. Agent Understands the Ticket First · 3. Agent Generates a Plan Specific to This Ticket · 4. Execute Step by Step, Evaluating as It Goes · 5. Different Tickets Receive Different Plans
**Tags above stages 2 and 5:** Plan-and-Solve · Planning Algorithms
**Visual:** Five Slate Blue boxes in a row, Deep Navy arrows, Signal Orange pill tags above box 2 and box 5. Box 4 has a small Signal Orange re-loop arrow back to box 3, indicating re-planning when evaluation fails.

---

### Slide 13 — CODE v2 (Cycle 2)
**Text (code panel):**
```python
def plan_and_solve(ticket):
    understanding = understand(ticket)
    plan = generate_plan(understanding)      # different per ticket, not fixed
    result = understanding
    for step in plan:
        result = execute_step(step, result)
        if not evaluate(result):
            plan = generate_plan(result)     # re-plan if something's off
    return result
```
**Visual:** Same dark panel style as Slide 7. Both `generate_plan(...)` calls are highlighted with the same lighter-navy rounded background block, visually connecting "plan once at the start" and "re-plan inside the loop" as the same idea repeated.

---

### Slide 14 — CONTEXT-SWAP STRIP v2
**Text (4 rows — Plan A / B / C):**
Healthcare: Standard Symptom Check / Multi-Condition Review / Ask a Clarifying Question First
Finance: Standard Transaction Check / Multi-Account Risk Review / Request More Transaction Detail First
Retail: Standard Order Check / Multi-Item Fraud Review / Request Additional Payment Verification First
Manufacturing: Standard Defect Check / Multi-Line Correlation Review / Request Additional Sensor Data First
**Visual:** Four rows, industry tag at left, then three small boxes per row (Plan A and Plan B Slate Blue, Plan C outlined in Signal Orange since it's the "ask first" branch), alternating white/Pale Blue Tint row backgrounds.

---

### Slide 15 — DEFINITION v3
**Text:** ~~...check its own work before passing it along.~~ Agent Design Patterns routes, parallelizes, and reflects, and **generates a plan specific to how complex or ambiguous each problem actually is** — rather than following one fixed sequence for everything.
**Visual:** Same layout as Slides 1 and 9. v2 sentence faded/struck through above. New clause in Signal Orange appended.

---

### Slide 16 — BREAK-THE-SYSTEM GRID
**Text (6 tiles):** Routing Misclassification · Parallel Race Condition · Endless Reflection Loop · Reflection Makes It Worse · Bad Plan, No Re-Planning · Plan Drifts From Intent
**Sub-flow:** Detect → Diagnose → Correct
**Visual:** Six tiles, white background, Signal Orange border, Deep Navy icon per tile (wrong-fork, two-overlapping-arrows, infinite-loop, thumbs-down, broken-map, drifting-arrow) + label. Beneath: compact 3-box Slate Blue flow (Detect → Diagnose → Correct), "Detect" in Signal Orange.

---

### Slide 17 — FINAL MODEL
**Text:** "Agent Design Patterns lets a multi-agent system route each ticket by severity, execute independent tickets in parallel, reflect on and improve its own drafts, and generate a plan specific to each ticket's actual complexity — rather than following one fixed sequence for everything."
**Visual:** White background, Deep Navy paragraph on the left. On the right, Slide 5/11's flow shrunk into a round Signal Orange-bordered badge (dots for Task/Strategy/Execute/Check connected by thin Deep Navy lines, small orange fork marking the Plan A/B/C addition). Caption beneath: "None of this replaces the orchestrator — these are strategies the same orchestrator now uses."

---
---

## Note on source content

This spec was built directly from your uploaded `lecture_notes.md`, which is genuinely Week 4 content — both S-Tier headers match (Multi-Agent Systems & Orchestration, then Intelligent Agent Coordination & Design Patterns), and S-Tier 2's own bridge line and closing lines correctly reference "our four agents" and the orchestrator built in S-Tier 1. No mismatch.

Industries needed light standardizing: S-Tier 1's two tables mostly agreed (Healthcare, Finance, Legal common to both) — I dropped Retail in favor of Manufacturing, which only appeared once, and adapted its row using the same intake→inspect→assemble→ship language the lecture's own Step 3 intuition already used. S-Tier 2's two tables barely overlapped (Healthcare/Finance/Retail/Manufacturing vs. Healthcare/Finance/Legal/Cybersecurity) — I standardized on the first set and adapted the Legal/Cybersecurity rows into Retail/Manufacturing equivalents using the same "standard check / multi-item review / request more detail first" formula.

Still waiting on Week 5 — upload whenever you're ready.
