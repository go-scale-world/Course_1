# Week 2 Slide Deck — Two S-Tier Lectures
### Running Project: Enterprise Problem Consolidation (EPC) Agent · Industries sampled: Healthcare, Finance, Legal, Cybersecurity
### Design system: Signal Orange #F2661D (active) · Slate Blue #3E6FA8 (structural) · Pale Blue Tint #E8EFF7 · Deep Navy #1A2B4C (text/lines) · backgrounds white or Pale Blue Tint only

---
---

# S-TIER 1 — Retrieval-Augmented Generation (RAG)

---

### Slide 1 — HOOK
**Text:** "Retrieval-Augmented Generation is looking up facts before answering a question."
Sub-line: "Did you answer from memory, or did you look something up first?"
**Visual:** White background. Definition in huge Deep Navy display type, centered, two lines. Sub-line small Slate Blue italic beneath. No icon, no accent color.

---

### Slide 2 — PROBLEM PAIR
**Text (Card 1):** "Agent only knows general patterns, not this company's history." → "A new employee expected to recognize a three-month-old issue with no access to past records — this is exactly what we cannot do."
**Text (Card 2):** "No way to search what it already has." → "A librarian who's read everything but has no card catalog for the shelf in front of her — this is exactly what we cannot do."
**Visual:** Two equal Pale Blue Tint cards, Deep Navy frame lines. Card 1 icon: a person silhouette with a small question mark. Card 2 icon: an open book with a magnifying glass and a slash through it. Problem label bold Deep Navy, analogy line below with "...we cannot do" in Signal Orange.

---

### Slide 3 — COMPARISON TABLE
**Text:**
| Without RAG | With RAG |
|---|---|
| Agent only knows general patterns | Agent looks up the company's own history |
| Every ticket treated in isolation | Related tickets recognized despite different wording |
| Can't fit all history into one prompt | Retrieves only the genuinely relevant pieces |
| Conclusions are generic | Conclusions are grounded in real incidents |
**Visual:** Two-column table, thin Deep Navy horizontal rules between rows only, no outer border. Left header/text Slate Blue. Right header Signal Orange, text Deep Navy. White background.

---

### Slide 4 — RUNNING EXAMPLE INTRO
**Text:** "Does this ticket describe an issue that already exists?"
**Visual:** Centered white background. One large Slate Blue line-icon of a magnifying glass over a document stack. "Enterprise Problem Consolidation (EPC) Agent" in Deep Navy beneath the icon, the question text in Signal Orange italic underneath that. No other text.

---

### Slide 5 — CONCEPT DIAGRAM (Rep. 1) — *fixed shape, reused in Slide 11 and 17*
**Text:** QUERY → SEARCH KNOWLEDGE → RETRIEVE RELEVANT PIECES → REASON WITH EVIDENCE
**Visual:** Four Slate Blue rounded rectangles in a horizontal row on Pale Blue Tint background, Deep Navy arrows between them. No orange — pure structure, industry-independent shape.

---

### Slide 6 — EXECUTION FLOW (Rep. 2)
**Text (5 stages):** 1. Ticket Arrives · 2. Text Must Become Comparable · 3. Comparable Vectors Need a Home · 4. Search for Nearest Matches · 5. Retrieved Tickets Inform a Decision
**Tags above stages 2–5:** Embeddings · Vector Databases · Similarity Search · Ticket Clustering
**Visual:** Five horizontal Slate Blue boxes, white background, Deep Navy arrows. Signal Orange pill tags float above boxes 2–5 (stage 1 has no tag — it's the raw trigger event). Box 5 border slightly thicker to mark the payoff stage.

---

### Slide 7 — CODE (Rep. 3)
**Text (code panel):**
```python
def retrieve_similar_tickets(new_ticket_text, vector_index, top_k=3):
    query_vector = embed(new_ticket_text)             # text -> comparable numbers
    return vector_index.search(query_vector, top_k)   # the few genuinely relevant matches
```
**Visual:** Single dark Deep Navy code panel on Pale Blue Tint background, white monospace code, Signal Orange inline comments. Nothing else on slide.

---

### Slide 8 — CONTEXT-SWAP STRIP
**Text (4 mini-flows):**
Healthcare: Symptom → Embed → Case-File Index → Nearest Cases → Diagnosis Grouping
Finance: Transaction → Embed → Fraud-Pattern Index → Nearest Patterns → Fraud Grouping
Legal: Clause → Embed → Contract Index → Nearest Clauses → Risk Grouping
Cybersecurity: Log Entry → Embed → Threat Index → Nearest Incidents → Threat Grouping
**Visual:** Four identical miniature 5-box flows stacked vertically, alternating white/Pale Blue Tint row backgrounds. Slate Blue boxes, Deep Navy arrows, unchanged from Slide 6's style — only captions change. Signal Orange industry tag at the left of each row.

---

### Slide 9 — DEFINITION v2
**Text:** ~~Retrieval-Augmented Generation is looking up facts before answering a question.~~ Retrieval-Augmented Generation is teaching a computer to **turn text into comparable numbers, store them, search for the closest matches, and reason using only what it actually found** — not just what it already knew.
**Visual:** Same centered layout as Slide 1. v1 shown small, faded Slate Blue, struck through. New clause in Signal Orange within an otherwise Deep Navy sentence.

---

### Slide 10 — SCOPE-INCREASE GRID
**Text (4 tiles):** Understand the Ticket · Retrieve Related History · Decide: Existing or New · Update the Record
**Visual:** Four equal Pale Blue Tint square tiles in a row, white background. Slate Blue line icons (magnifying glass / archive box / fork-in-road / floppy disk). Fourth tile ("Update the Record") outlined in Signal Orange instead of Deep Navy — the new, not-yet-explained capability.

---

### Slide 11 — CONCEPT DIAGRAM v2 (Cycle 2) — *extends the execution-flow shape from Slide 6*
**Text:** QUERY → EMBED → SEARCH → RETRIEVE NEIGHBOURS → CLUSTER DECISION → (JOIN EXISTING CLUSTER / CREATE NEW CLUSTER) → UPDATE HISTORY
**Visual:** The five boxes from Slide 6 unchanged, now followed by one new Signal Orange diamond ("Cluster Decision") that branches into two Slate Blue boxes ("Join Existing" / "Create New"), both arrows converging into one final Slate Blue box ("Update History"). Only the new fork and merge are orange-accented — everything before it is untouched.

---

### Slide 12 — EXECUTION FLOW v2 (Cycle 2)
**Text (7 stages, 1–4 unchanged, 5–7 new):** 5. Compare Top Match Against Threshold · 6. Above → Join Existing / Below → Create New · 7. Update Cluster History
**Visual:** Same four boxes from Slide 6 (stages 1–4), followed by a Signal Orange diamond ("Threshold?"), splitting into two small end boxes (Slate Blue "Join Cluster," Signal Orange-outlined "Create New"), both merging into a final Slate Blue box ("Update History"). Boxes clearly separated vertically — no overlap between the two branch outcomes.

---

### Slide 13 — CODE v2 (Cycle 2)
**Text (code panel):**
```python
def assign_cluster(new_ticket_text, vector_index, threshold=0.75):
    neighbours = retrieve_similar_tickets(new_ticket_text, vector_index)
    if neighbours and neighbours[0].score >= threshold:
        return neighbours[0].cluster_id       # join existing cluster
    return create_new_cluster(new_ticket_text)  # describes something new
```
**Visual:** Same dark panel style as Slide 7. The `retrieve_similar_tickets(...)` call is set in Slate Blue to show it's reused unchanged; the new `if/return` logic beneath sits inside a soft rounded highlight block (not an edge stripe) in a slightly lighter navy tone, marking what's new.

---

### Slide 14 — CONTEXT-SWAP STRIP v2
**Text (4 rows — what "join" vs "new" means):**
Healthcare: Symptom matches a known case pattern → A genuinely new symptom pattern
Finance: Transaction matches a known fraud pattern → A genuinely new fraud pattern
Legal: Clause matches a known risk pattern → A genuinely new risk pattern
Cybersecurity: Alert matches a known attack pattern → A genuinely new attack pattern
**Visual:** Same four-row strip layout as Slide 8. Each row shows two small boxes connected by an arrow: Slate Blue "Join" box on the left, Signal Orange-outlined "New" box on the right, industry tag at far left.

---

### Slide 15 — DEFINITION v3
**Text:** ~~...reason using only what it actually found — not just what it already knew.~~ Retrieval-Augmented Generation is teaching a computer to search for the closest matches, **decide whether those matches are close enough to count as the same issue, and update its own memory of what it now knows** — reasoning always from evidence, never from memory alone.
**Visual:** Same layout as Slides 1 and 9. v2 sentence shown faded/struck through above. New clause in Signal Orange appended in Deep Navy sentence.

---

### Slide 16 — BREAK-THE-SYSTEM GRID
**Text (6 tiles):** Irrelevant Retrieval · Stale Index · Threshold Too Loose · Threshold Too Strict · Empty Retrieval · Duplicate Clusters
**Sub-flow:** Detect → Diagnose → Recalibrate
**Visual:** Six tiles, white background, Signal Orange border on each, small Deep Navy warning-style icon (search-with-X, clock, funnel-wide-open, funnel-too-narrow, empty-box, two-overlapping-circles) + label. Beneath: a compact 3-box Slate Blue flow (Detect → Diagnose → Recalibrate), "Detect" set in Signal Orange as the active trigger.

---

### Slide 17 — FINAL MODEL
**Text:** "Retrieval-Augmented Generation converts new information into comparable numbers, stores and searches to find genuinely relevant evidence, decides whether that evidence represents the same issue, and reasons only from what was actually found — keeping its memory continuously up to date."
**Visual:** White background, Deep Navy paragraph on the left. On the right, Slide 5/11's diagram shrunk into a round Signal Orange-bordered badge (dots for Query/Search/Retrieve/Reason connected by thin Deep Navy lines, small orange diamond marking the Cluster Decision addition). Caption beneath the badge: "RAG is not Vector Search — Vector Search is the retrieval engine that makes RAG possible."

---
---

# S-TIER 2 — Intelligent Agent Coordination & Design Patterns

---

### Slide 1 — HOOK
**Text:** "Agent Design Patterns is teaching a system to choose its own strategy."
Sub-line: "Is a company-wide outage really the same problem as a forgotten password?"
**Visual:** White background. Definition in huge Deep Navy display type, centered. Sub-line small Slate Blue italic beneath.

---

### Slide 2 — PROBLEM PAIR
**Text (Card 1):** "Every ticket waits in the same line, regardless of urgency." → "An ER that makes a paper cut and a heart attack wait in the exact same line — this is exactly what we cannot do."
**Text (Card 2):** "Tickets processed one at a time, even during a surge." → "A single-lane toll booth at rush hour with ten open lanes sitting empty — this is exactly what we cannot do."
**Visual:** Two Pale Blue Tint cards, Deep Navy frame. Card 1 icon: a single waiting line with one figure highlighted. Card 2 icon: a single toll lane with cars queued. Analogy line ends in Signal Orange "...we cannot do."

---

### Slide 3 — COMPARISON TABLE
**Text:**
| Without Adaptive Patterns | With Adaptive Patterns |
|---|---|
| Every ticket follows the identical path | Tickets are routed by actual severity |
| Tickets processed one at a time | Independent tickets processed simultaneously |
| First draft is treated as final | Agent critiques and improves its own output |
| One rigid fixed sequence for everything | The plan adapts to how complex the ticket is |
**Visual:** Same table style as S-Tier 1 Slide 3. Left column Slate Blue, right column header Signal Orange, body text Deep Navy, thin row rules only.

---

### Slide 4 — RUNNING EXAMPLE INTRO
**Text:** "The EPC Agent's fixed pipeline, learning to adapt."
**Visual:** Centered white background. One large Slate Blue line-icon of a single road forking into three paths. "Enterprise Problem Consolidation (EPC) Agent" in Deep Navy beneath. No other text.

---

### Slide 5 — CONCEPT DIAGRAM (Rep. 1) — *fixed shape, reused in Slide 11 and 17*
**Text:** TASK → CHOOSE STRATEGY → EXECUTE (possibly in parallel) → SELF-CHECK → OUTPUT
**Visual:** Four Slate Blue rounded rectangles in a horizontal row on Pale Blue Tint background, Deep Navy arrows. "EXECUTE" box has a small duplicated ghost-outline behind it to hint at parallelism, still entirely Slate Blue/Deep Navy — no orange yet.

---

### Slide 6 — EXECUTION FLOW (Rep. 2)
**Text (5 stages):** 1. Ticket Arrives at Fixed Pipeline · 2. Should Every Ticket Follow the Same Path? · 3. What if Hundreds Arrive at Once? · 4. Agent Drafts, Then Checks Itself · 5. Workflow Completes
**Tags above stages 2–4:** Routing · Parallelization · Reflection
**Visual:** Five horizontal Slate Blue boxes, white background, Deep Navy arrows. Signal Orange pill tags above boxes 2, 3, and 4 only (stages 1 and 5 are the old bookends, untagged).

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
**Visual:** Dark Deep Navy code panel on Pale Blue Tint background, three short functions stacked with a blank line between each, white monospace text, Signal Orange inline comment on the parallel-execution line only.

---

### Slide 8 — CONTEXT-SWAP STRIP
**Text (4 mini-flows):**
Healthcare: Route by Urgency → Parallel Intake During a Mass Event → Reflect on a Draft Diagnosis
Finance: Route by Transaction Risk → Parallel Review During Volatility → Reflect on a Risk Report
Legal: Route by Case Urgency → Parallel Review During a Filing Surge → Reflect on a Draft Brief
Cybersecurity: Route by Threat Severity → Parallel Triage During an Incident → Reflect on a Draft Alert
**Visual:** Four identical miniature 3-box flows (Route → Parallel → Reflect) stacked vertically, alternating white/Pale Blue Tint rows. Slate Blue boxes, Deep Navy arrows, Signal Orange industry tag at the left of each row.

---

### Slide 9 — DEFINITION v2
**Text:** ~~Agent Design Patterns is teaching a system to choose its own strategy.~~ Agent Design Patterns is teaching a system to **route each problem down the right path, handle many problems at once, and check its own work before passing it along.**
**Visual:** Same layout as Slide 1. v1 shown small, faded, struck through. New clause in Signal Orange within a Deep Navy sentence.

---

### Slide 10 — SCOPE-INCREASE GRID
**Text (4 tiles):** Route by Severity · Process in Parallel · Reflect and Improve · Recognize Genuine Complexity
**Visual:** Four Pale Blue Tint square tiles in a row, white background, Slate Blue icons (fork / stacked-layers / refresh-loop / question-mark). Fourth tile ("Recognize Genuine Complexity") outlined in Signal Orange — the not-yet-explained capability the next slides unpack.

---

### Slide 11 — CONCEPT DIAGRAM v2 (Cycle 2) — *reuses the execution-flow shape from Slide 6*
**Text:** TICKET → UNDERSTAND → GENERATE PLAN (varies per ticket) → EXECUTE STEP BY STEP → EVALUATE → FINISH, branching into PLAN A (simple) / PLAN B (complex) / PLAN C (ambiguous, needs clarification first)
**Visual:** A horizontal Slate Blue flow (Understand → Generate Plan → Execute → Evaluate → Finish), with one new Signal Orange three-way fork beneath "Generate Plan" leading to three small labeled boxes (Plan A / Plan B / Plan C). Everything else in the flow stays Slate Blue/Deep Navy — only the fork is new.

---

### Slide 12 — EXECUTION FLOW v2 (Cycle 2)
**Text (5 stages, 1 unchanged, 2–5 new):** 2. Agent Understands the Ticket First · 3. Agent Generates a Plan Specific to This Ticket · 4. Execute Step by Step, Evaluating as It Goes · 5. Different Tickets Receive Different Plans
**Tags above stages 2 and 5:** Plan-and-Solve · Planning Algorithms
**Visual:** Five Slate Blue boxes in a row, Deep Navy arrows, Signal Orange pill tags above box 2 ("Plan-and-Solve") and box 5 ("Planning Algorithms"). Box 4 has a small re-loop arrow back to box 3, colored Signal Orange, indicating re-planning when evaluation fails.

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
**Visual:** Same dark panel style as Slide 7. The two `generate_plan(...)` calls are both highlighted with a soft rounded background block in a lighter navy tone — same highlight color used both times to visually connect "plan once at the start" and "re-plan inside the loop" as the same idea repeated.

---

### Slide 14 — CONTEXT-SWAP STRIP v2
**Text (4 rows — Plan A / B / C):**
Healthcare: Standard Symptom Check / Multi-Condition Review / Ask a Clarifying Question First
Finance: Standard Transaction Check / Multi-Account Risk Review / Request More Transaction Detail First
Legal: Standard Clause Review / Multi-Clause Risk Analysis / Request the Missing Document Section First
Cybersecurity: Standard Log Check / Multi-System Correlation / Request Additional Log Context First
**Visual:** Four rows, industry tag at left, then three small boxes side by side per row (Plan A Slate Blue, Plan B Slate Blue, Plan C outlined in Signal Orange since it's the "ask first" branch), alternating white/Pale Blue Tint row backgrounds.

---

### Slide 15 — DEFINITION v3
**Text:** ~~...check its own work before passing it along.~~ Agent Design Patterns is teaching a system to route, parallelize, and reflect, and **generate a plan specific to how complex or ambiguous each problem actually is** — rather than following one fixed sequence for everything.
**Visual:** Same layout as Slides 1 and 9. v2 sentence faded/struck through above. New clause in Signal Orange appended to the Deep Navy sentence.

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

## ⚠️ Note on source content

Your uploaded `lecture_notes.md` is titled "Week 2" and its S-Tier 1 (RAG) matches Week 2 content correctly. However, its **S-Tier 2 section is "Intelligent Agent Coordination & Design Patterns"** — its own bridge line references "our four agents" and the Extract → Cluster → Score → Escalate pipeline, which is Week 4 material, not Week 2's original Tool Calling lecture. I built this spec faithfully from what's actually in the file you uploaded, since that's what you asked for — but if this was an accidental mix-up (wrong file, or content pasted into the wrong slot), let me know and I'll rebuild S-Tier 2 using the actual Tool Calling & Agent Decision Making content instead.
