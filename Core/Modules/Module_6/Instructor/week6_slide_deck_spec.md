# Week 6 Slide Deck (Capstone) — Production Operations, Evaluation & Enterprise Readiness
### Running Project: Enterprise Problem Consolidation (EPC) Agent — the entire platform · Industries sampled: Healthcare, Finance, Legal, Manufacturing
### Design system: Signal Orange #F2661D (active) · Slate Blue #3E6FA8 (structural) · Pale Blue Tint #E8EFF7 · Deep Navy #1A2B4C (text/lines) · backgrounds white or Pale Blue Tint only

**One journey, not two:** this week has a single S-Tier lecture — the deck below is one continuous 17-slide sequence, not a split.

---

### Slide 1 — HOOK
**Text:** "Enterprise Readiness is proving a system can run itself."
Sub-line: "Not 'can it run.' Can we prove it runs — when nobody's watching?"
**Visual:** White background. Definition in huge Deep Navy display type, centered, two lines. Sub-line small Slate Blue italic beneath. No icon, no accent color.

---

### Slide 2 — PROBLEM PAIR
**Text (Card 1):** "We've only ever tested this by watching it ourselves." → "Engineers who drove one test car across a new bridge, then opened it to the public — this is exactly what we cannot do."
**Text (Card 2):** "Nobody can tell if it's quietly struggling." → "A new hire who aces one interview, then gets left alone with zero check-ins — this is exactly what we cannot do."
**Visual:** Two Pale Blue Tint cards, Deep Navy frame lines. Card 1 icon: a bridge with a single car crossing it. Card 2 icon: a person silhouette alone in an empty room. Analogy line ends in Signal Orange "...we cannot do."

---

### Slide 3 — COMPARISON TABLE
**Text:**
| Without Production Readiness | With Production Readiness |
|---|---|
| Only tested manually, a few tickets at a time | Runs continuously, unattended, at real volume |
| No visibility after deployment | Every step is observable and traceable |
| "It works" is a feeling | "It works" is a measured number |
| A stuck step goes unnoticed | Failures are isolated, logged, and recovered automatically |
**Visual:** Two-column table, thin Deep Navy row rules only, no outer border. Left column Slate Blue, right column header Signal Orange, body text Deep Navy. White background.

---

### Slide 4 — RUNNING EXAMPLE INTRO
**Text:** "Are you actually ready?"
**Visual:** Centered white background. One large Slate Blue icon of the full six-week platform represented as a simple stacked-layers icon (six thin horizontal bars stacked). "Enterprise Problem Consolidation (EPC) Platform" in Deep Navy beneath, the question in Signal Orange italic underneath that.

---

### Slide 5 — CONCEPT DIAGRAM (Rep. 1) — *fixed shape, reused in Slide 11 and 17*
**Text:** STEP EXECUTES → EMIT TELEMETRY → LOGGED / TRACED → VISIBLE TO HUMANS
**Visual:** Four Slate Blue rounded rectangles in a horizontal row on Pale Blue Tint background, Deep Navy arrows between. No orange — pure structure, the invariant shape of any system explaining its own behavior.

---

### Slide 6 — EXECUTION FLOW (Rep. 2)
**Text (main flow, 10 compact stage chips in one row, smaller type):** Ticket Submitted → Agent Starts → Retrieve Context → Reason → Call Tools → Coordinate Agents → Human Approval → Escalation Generated → Notification → Ready for Next
**Band beneath (parallel layer):** "Emit Telemetry — which agent, which tool, how long, where it failed" tagged **Observability & Tracing**
**Band beneath that (continuous layer):** "Nightly Refresh · Scheduled Maintenance · Dead-Letter Queue · Auto Recovery" tagged **Runtime Management**
**Visual:** Ten small Slate Blue chips in one connected row across the top (this is literally every capability built across the whole course, compressed). Below it, a thin horizontal Pale Blue Tint band spanning the full width with a Signal Orange tag "Observability & Tracing" — drawn as a parallel layer, not a new step, with light dotted Deep Navy connector lines up into each chip rather than a sequential arrow. Below that, a second band in a slightly darker tint with a Signal Orange tag "Runtime Management," containing four small labels side by side.

---

### Slide 7 — CODE (Rep. 3)
**Text (code panel):**
```python
def run_step(step_name, fn, *args):
    start = time.time()
    try:
        result = fn(*args)
        trace.log(step_name, duration=time.time() - start, status="success")
        return result
    except Exception as e:
        trace.log(step_name, duration=time.time() - start, status="failed", error=str(e))
        dead_letter_queue.add(step_name, args, e)   # isolate repeated failures
        raise
```
**Visual:** Dark Deep Navy code panel on Pale Blue Tint background, white monospace code, one Signal Orange inline comment on the `dead_letter_queue.add(...)` line. Nothing else on slide.

---

### Slide 8 — CONTEXT-SWAP STRIP
**Text (4 rows — same full flow, what gets traced/refreshed):**
Healthcare: Telemetry on every diagnostic step → Nightly case-file refresh
Finance: Telemetry on every fraud-check step → Nightly risk-model refresh
Legal: Telemetry on every review step → Nightly precedent refresh
Manufacturing: Telemetry on every inspection step → Nightly calibration refresh
**Visual:** Four rows stacked, alternating white/Pale Blue Tint backgrounds. Each row: Signal Orange industry tag at left, then two small Slate Blue boxes connected by a Deep Navy arrow (Telemetry box → Refresh box). Same visual language as Slide 6's bands, miniaturized.

---

### Slide 9 — DEFINITION v2
**Text:** ~~Enterprise Readiness is proving a system can run itself.~~ Enterprise Readiness is teaching a system to **run its entire workflow automatically, emit visibility into every step it takes, and stay healthy over days and weeks of continuous operation.**
**Visual:** Same layout as Slide 1. v1 shown small, faded Slate Blue, struck through. New clause in Signal Orange within an otherwise Deep Navy sentence.

---

### Slide 10 — SCOPE-INCREASE GRID
**Text (5 tiles):** Run Continuously, Unattended · Trace Every Step · Isolate & Recover Automatically · Refresh Knowledge on Schedule · Prove Real Performance
**Visual:** Five Pale Blue Tint square tiles in a row, white background. Slate Blue icons (infinity loop / eye-with-magnifier / shield-refresh / calendar-refresh / bar-chart). Fifth tile ("Prove Real Performance") outlined in Signal Orange, icon and label in Signal Orange — the new, not-yet-explained capability.

---

### Slide 11 — CONCEPT DIAGRAM v2 (Cycle 2) — *extends Slide 6's full flow, not Slide 5's abstract shape*
**Text:** [FULL PLATFORM FLOW] → ESCALATION DRAFT → EVALUATOR AGENT → QUALITY SCORE → (BELOW THRESHOLD / MEETS THRESHOLD) → OPTIMIZER REVISES → EVALUATE AGAIN / SEND
**Visual:** A single compressed Slate Blue chip labeled "Full Platform Flow" (representing all of Slide 6, collapsed) leading into "Escalation Draft," then a Signal Orange diamond ("Evaluator"), branching: below-threshold loops through a Slate Blue "Optimizer Revises" box back into the diamond (Signal Orange loop arrow), meets-threshold goes straight to a Slate Blue "Send" box. Everything before the diamond stays untouched/collapsed — only the new evaluator loop is drawn in detail.

---

### Slide 12 — EXECUTION FLOW v2 (Cycle 2)
**Text (4 stages):** 1. Full Platform Flow Runs as Before · 2. Prove the System Performs Well · 3. Improve Output Before Delivering It · 4. Approved Escalation Delivered
**Sub-labels under stage 2:** Throughput · Latency · Bottleneck Analysis · Success/Failure Rate · Scalability
**Tags above stages 2 and 3:** Evaluation & Benchmarking · Evaluator–Optimizer Pattern
**Visual:** Four Slate Blue boxes in a row, Deep Navy arrows. Signal Orange pill tags above box 2 and box 3. Box 2 has five tiny label chips listed beneath it in smaller type (the five things being measured). Box 4 has a thicker Deep Navy border marking it as the final payoff stage.

---

### Slide 13 — CODE v2 (Cycle 2)
**Text (code panel):**
```python
def evaluator_optimizer_loop(draft, threshold=80, max_attempts=3):
    for attempt in range(max_attempts):
        score = evaluator_agent.score(draft)
        if score >= threshold:
            return draft                        # good enough, send it
        draft = optimizer_agent.revise(draft, score)
    return draft                                 # best effort after max attempts
```
**Visual:** Same dark panel style as Slide 7. The `if score >= threshold: return draft` block sits inside a soft rounded highlight in a lighter navy tone to mark the "good enough, stop early" exit path — not an edge stripe, a background block.

---

### Slide 14 — CONTEXT-SWAP STRIP v2
**Text (4 rows — what's benchmarked / what the evaluator checks):**
Healthcare: Diagnoses processed per hour under real patient load → Clinical accuracy and completeness before sending
Finance: Transactions reviewed per minute during peak volatility → Regulatory compliance and clarity before sending
Legal: Filings reviewed per day under a real caseload → Legal accuracy and proper citation before sending
Manufacturing: Inspections completed per shift under full production → Safety compliance and completeness before sending
**Visual:** Four rows, alternating white/Pale Blue Tint backgrounds, Signal Orange industry tag at left. Each row: a Slate Blue "Benchmark" box on the left, Deep Navy arrow, a Signal Orange-outlined "Evaluator Checks" box on the right.

---

### Slide 15 — DEFINITION v3
**Text:** ~~...stay healthy over days and weeks of continuous operation.~~ Enterprise Readiness is teaching a system to run its workflow, stay healthy over time, **prove its real-world performance through measured benchmarks, and improve its own output through an evaluator and optimizer working together** — before a human ever needs to step in.
**Visual:** Same layout as Slides 1 and 9. v2 sentence faded/struck through above. New clause in Signal Orange appended to the Deep Navy sentence.

---

### Slide 16 — BREAK-THE-SYSTEM GRID
**Text (6 tiles):** Traced But Unnoticed · Silent Dead-Letter Buildup · Benchmark Measured Unrealistically · Evaluator Blind Spot · Optimizer Never Converges · Auto-Recovery Masks a Real Problem
**Sub-flow:** Detect → Alert → Review
**Visual:** Six tiles, white background, Signal Orange border, Deep Navy icon per tile (declining-chart-with-no-bell, overflowing-tray, chart-with-gap, eye-with-question-mark, infinite-circular-arrows, bandage-over-warning-sign) + label. Beneath: compact 3-box Slate Blue flow (Detect → Alert → Review), "Detect" in Signal Orange — this week's failures are mostly about nobody watching, so the fix is human review, not just retry.

---

### Slide 17 — FINAL MODEL
**Text:** "Enterprise Readiness lets an enterprise AI platform run its entire automated workflow continuously, emit visibility through tracing and monitoring, maintain its own health through scheduled maintenance and failure isolation, prove its real-world performance through measured benchmarks, and improve its own output through an evaluator and optimizer working together — all with minimal human intervention."
**Visual:** White background, Deep Navy paragraph on the left. On the right, Slide 5/11's diagram shrunk into a round Signal Orange-bordered badge (dots for Execute/Telemetry/Trace/Visible connected by thin Deep Navy lines, small orange diamond marking the Evaluator addition). Beneath the badge, a one-line caption in Slate Blue italic: "Week 1: 'teaching a computer to do a job by itself.' Week 6: proving it can. One continuous idea."

---

## Note on source content

This spec was built directly from your uploaded `lecture_notes.md`, which is genuinely Week 6's capstone content (single S-Tier, matches its own title and closing lines) — no mismatch this time. Industries used throughout (Healthcare, Finance, Legal, Manufacturing) come straight from the file's own two context-progression tables, which were already consistent with each other, so no substitutions were needed.

Still waiting on real Weeks 3, 4, and 5 — upload each with a distinct filename whenever you're ready and I'll build those specs too.
