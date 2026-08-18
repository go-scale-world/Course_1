# Week 3 Slide Deck — Two S-Tier Lectures
### Running Project: Enterprise Problem Consolidation (EPC) Agent
### Design system: Signal Orange #F2661D (active) · Slate Blue #3E6FA8 (structural) · Pale Blue Tint #E8EFF7 · Deep Navy #1A2B4C (text/lines) · backgrounds white or Pale Blue Tint only

---
---

# S-TIER 1 — Agent Memory, State & Enterprise Safety
### Industries sampled: Healthcare, Finance, Legal, Cybersecurity

---

### Slide 1 — HOOK
**Text:** "Agent Memory & State is teaching a computer to remember what it was doing."
Sub-line: "If a related ticket arrives ten minutes later, does it know?"
**Visual:** White background. Definition in huge Deep Navy display type, centered, two lines. Sub-line small Slate Blue italic beneath.

---

### Slide 2 — PROBLEM PAIR
**Text (Card 1):** "Every event starts from zero, even mid-investigation." → "A detective who wakes up every morning with complete amnesia about yesterday's clues — this is exactly what we cannot do."
**Text (Card 2):** "Nothing checks if an action is actually safe." → "A brand-new intern handed building keys and email access on day one, with nobody checking their work — this is exactly what we cannot do."
**Visual:** Two Pale Blue Tint cards, Deep Navy frame. Card 1 icon: a head silhouette with a circular reset arrow inside it. Card 2 icon: a key with a small unchecked checkbox beside it. Analogy line ends in Signal Orange "...we cannot do."

---

### Slide 3 — COMPARISON TABLE
**Text:**
| Without Memory & Safety | With Memory & Safety |
|---|---|
| Every event starts from zero, even mid-investigation | Agent picks up exactly where it left off |
| No record of what the agent has already decided | Every decision is stored and checkable |
| Nothing checks if an action is actually safe | Every step passes through a validation checkpoint |
| A cleverly worded ticket can manipulate the agent | Malicious instructions are detected and blocked |
**Visual:** Two-column table, thin Deep Navy row rules only. Left column Slate Blue, right header Signal Orange, body Deep Navy. White background.

---

### Slide 4 — RUNNING EXAMPLE INTRO
**Text:** "Continuing an investigation, across several related tickets, over time."
**Visual:** Centered white background. One large Slate Blue icon of a folder with a small clock badge on it. "Enterprise Problem Consolidation (EPC) Agent" in Deep Navy beneath, the caption in Signal Orange italic underneath that.

---

### Slide 5 — CONCEPT DIAGRAM (Rep. 1) — *fixed shape, reused in Slide 11 and 17*
**Text:** READ STATE → REASON → VALIDATE → ACT → WRITE STATE
**Visual:** Five Slate Blue rounded rectangles in a horizontal row on Pale Blue Tint background, Deep Navy arrows between. No orange — pure structure, industry-independent shape.

---

### Slide 6 — EXECUTION FLOW (Rep. 2)
**Text (8 stages, two rows of four):**
Row 1: 1. Ticket Arrives · 2. Safety Check on Input · 3. Retrieve Existing Cluster · 4. Read Previous State
Row 2: 5. Reason / Continue Investigation · 6. Safety Check on Decision · 7. Update Live State + Archive · 8. Wait for Next Ticket
**Tags:** "Prompt Injection Defence" above box 2 · "Dual-Layer Memory: Live State" above box 4 · "Guardrails Wrap Output Too" above box 6 · "Historical Archive" above box 7
**Visual:** Eight Slate Blue boxes in two stacked rows of four, Deep Navy arrows left to right within each row and one Deep Navy connector dropping from the end of row 1 down to the start of row 2. Signal Orange pill tags above boxes 2, 4, 6, and 7.

---

### Slide 7 — CODE (Rep. 3)
**Text (code panel):**
```python
def process_ticket(ticket, cluster_id):
    if not passes_input_safety_check(ticket.raw_text):     # blocks injected instructions
        return reject(ticket, reason="unsafe input")

    state = live_memory.read(cluster_id)                   # live state
    decision = reason(ticket, state)

    if not passes_decision_safety_check(decision):          # guardrail wraps output too
        return reject(decision, reason="unsafe decision")

    live_memory.write(cluster_id, decision)                 # live state
    archive.store(decision)                                 # historical archive
    return decision
```
**Visual:** Dark Deep Navy code panel on Pale Blue Tint background, white monospace code, Signal Orange inline comments on the two safety-check lines and the two write lines.

---

### Slide 8 — CONTEXT-SWAP STRIP
**Text (4 mini-flows):**
Healthcare: Message → Safety Check → Patient Case → Live State → Reason → Validate → Archive
Finance: Request → Safety Check → Account Case → Live State → Reason → Validate → Archive
Legal: Filing → Safety Check → Case File → Live State → Reason → Validate → Archive
Cybersecurity: Alert → Safety Check → Incident Case → Live State → Reason → Validate → Archive
**Visual:** Four rows stacked, alternating white/Pale Blue Tint backgrounds. Compact 7-chip Slate Blue mini-flows, Deep Navy arrows, Signal Orange industry tag at the left of each row.

---

### Slide 9 — DEFINITION v2
**Text:** ~~Agent Memory & State is teaching a computer to remember what it was doing.~~ Agent Memory & State is teaching a computer to **check that new input is safe, read what it already knows, reason with it, validate its own decision, and save its progress** — so it never starts from zero, and it's never trusted blindly.
**Visual:** Same layout as Slide 1. v1 shown small, faded, struck through. New clause in Signal Orange within a Deep Navy sentence.

---

### Slide 10 — SCOPE-INCREASE GRID
**Text (4 tiles):** Retrieve State Before Acting · Continue Investigation Across Tickets · Validate Every Decision · Maintain Complete Audit Trail
**Visual:** Four Pale Blue Tint square tiles in a row, white background, Slate Blue icons (archive-box / thread-of-dots / checkmark-shield / ledger). Fourth tile outlined in Signal Orange — the new capability the next slides unpack.

---

### Slide 11 — CONCEPT DIAGRAM v2 (Cycle 2) — *reuses Slide 5's shape exactly*
**Text:** READ STATE → REASON → VALIDATE → [severity check] → LOW-STAKES: ACT / HIGH-STAKES: HUMAN APPROVAL → ACT → WRITE STATE
**Visual:** Slide 5's five boxes completely untouched, with one new Signal Orange fork inserted right after VALIDATE — splitting into a Slate Blue "Act" path and a Signal Orange-outlined "Human Approval → Act" path, both converging back into the existing WRITE STATE box.

---

### Slide 12 — EXECUTION FLOW v2 (Cycle 2)
**Text (6 stages, 1 collapsed, 2–5 new):** 1. Read/Reason/Validate (as before) · 2. Check Decision Severity · 3. Low/Medium → Act Directly · High/Critical → Pause for Approval · 4. Human Approves or Rejects · 5. Approved → Act / Rejected → Log and Stop · 6. Write State
**Visual:** A single collapsed Slate Blue chip for stage 1 (representing Slide 6's whole flow), then a Signal Orange diamond ("Severity?"), branching into a Slate Blue "Act" box and a Signal Orange-outlined "Human Approval" box (with a small person icon), both merging into a final Slate Blue "Write State" box.

---

### Slide 13 — CODE v2 (Cycle 2)
**Text (code panel):**
```python
def maybe_require_approval(decision):
    if decision.severity in ("High", "Critical"):
        return await_human_approval(decision)   # pause, don't act alone
    return decision                              # low-stakes, proceed automatically
```
**Visual:** Same dark panel style as Slide 7. The entire `if` block is inside a soft rounded highlight in lighter navy tone, since this whole three-line function is itself the new addition — the highlight marks the function as a unit, not a specific line within it.

---

### Slide 14 — CONTEXT-SWAP STRIP v2
**Text (4 rows — high-stakes decision / who approves):**
Healthcare: Recommending a medication change → A physician
Finance: A large or unusual transfer → A manager
Legal: Terminating a contract → A partner
Cybersecurity: Locking down a compromised account → A security lead
**Visual:** Four rows, alternating white/Pale Blue Tint backgrounds, Signal Orange industry tag at left. Each row: a Slate Blue "Decision" box, Deep Navy arrow, a Signal Orange-outlined "Approver" box with a small person icon.

---

### Slide 15 — DEFINITION v3
**Text:** ~~...validate its own decision, and save its progress...~~ Agent Memory & State is teaching a computer to check input, read what it knows, reason, validate, **pause for human approval when the stakes are high**, and save its progress — never blindly trusted with the decisions that matter most.
**Visual:** Same layout as Slides 1 and 9. v2 sentence faded/struck through above. New clause in Signal Orange appended.

---

### Slide 16 — BREAK-THE-SYSTEM GRID
**Text (6 tiles):** Lost State · Stale State Read · Prompt Injection Slips Through · Guardrail False Positive · Approval Bottleneck · Audit Trail Gap
**Sub-flow:** Detect → Escalate → Resolve
**Visual:** Six tiles, white background, Signal Orange border, Deep Navy icon per tile (empty-folder, clock-with-conflict-arrows, masked-text-bubble, blocked-checkmark, hourglass-with-person, gap-in-a-list) + label. Beneath: compact 3-box Slate Blue flow (Detect → Escalate → Resolve), "Detect" in Signal Orange.

---

### Slide 17 — FINAL MODEL
**Text:** "Agent Memory & State checks whether new input is safe, reads what the agent already knows, reasons using that knowledge, validates its own conclusion, pauses for human approval when the stakes are high, and permanently records what happened — so it never starts from zero, and it's never trusted blindly."
**Visual:** White background, Deep Navy paragraph on the left. On the right, Slide 5/11's diagram shrunk into a round Signal Orange-bordered badge (dots for Read/Reason/Validate/Act/Write connected by thin Deep Navy lines, small orange fork marking the human-approval addition). Caption beneath: "Memory is not just remembering — it's what makes every decision checkable."

---
---

# S-TIER 2 — Deploying & Operating Enterprise AI Agents
### Industries sampled: Healthcare, Finance, Retail, Manufacturing

---

### Slide 1 — HOOK
**Text:** "Deploying an AI Agent is making it available for everyone to use."
Sub-line: "If your laptop is off, does the agent exist for anyone?"
**Visual:** White background. Definition in huge Deep Navy display type, centered. Sub-line small Slate Blue italic beneath.

---

### Slide 2 — PROBLEM PAIR
**Text (Card 1):** "It only runs on one person's machine." → "A brilliant chef who only ever cooks in his own kitchen, for himself, and never opens a restaurant — this is exactly what we cannot do."
**Text (Card 2):** "It has no plan for handling more than a handful of tickets." → "A coffee stand built for one customer at a time, suddenly facing a line of ten thousand — this is exactly what we cannot do."
**Visual:** Two Pale Blue Tint cards, Deep Navy frame. Card 1 icon: a chef's hat inside a single small house outline. Card 2 icon: one counter window with a long line of dots behind it. Analogy line ends in Signal Orange "...we cannot do."

---

### Slide 3 — COMPARISON TABLE
**Text:**
| Without Deployment & Scaling | With Deployment & Scaling |
|---|---|
| Only runs on one person's machine | Reachable by the whole company, anytime |
| Manually started and stopped | Runs continuously, 24/7 |
| Falls over under heavy traffic | Scales automatically to meet demand |
| Updates require rebuilding everything | New versions roll out with no interruption |
**Visual:** Same table style as S-Tier 1 Slide 3. Left column Slate Blue, right header Signal Orange, body Deep Navy, thin row rules only.

---

### Slide 4 — RUNNING EXAMPLE INTRO
**Text:** "Becoming a real, always-on service."
**Visual:** Centered white background. One large Slate Blue icon of a single server rack glowing steadily. "Enterprise Problem Consolidation (EPC) Agent" in Deep Navy beneath, caption in Signal Orange italic underneath.

---

### Slide 5 — CONCEPT DIAGRAM (Rep. 1) — *fixed shape, reused in Slide 11 and 17*
**Text:** REQUEST ARRIVES → RUNNING SERVICE HANDLES IT → RESPONSE RETURNED
**Visual:** Three Slate Blue rounded rectangles in a horizontal row on Pale Blue Tint background, Deep Navy arrows. Simplest diagram of the week — deliberately minimal, since this is the invariant shape of any hosted service.

---

### Slide 6 — EXECUTION FLOW (Rep. 2)
**Text (5 stages):** 1. Support Ticket Arrives · 2. Agent Code Must Run the Same Way Everywhere · 3. Something Must Keep It Running Continuously · 4. Agent Executes (Everything From S-Tier 1) · 5. Response Returned
**Tags above stages 2 and 3:** Containerization · Cloud Run
**Visual:** Five Slate Blue boxes in a row, Deep Navy arrows, Signal Orange pill tags above box 2 and box 3. Box 4 shown slightly wider than the others with a small stacked-layers icon inside it, hinting that everything from the first lecture lives inside this one stage.

---

### Slide 7 — CODE (Rep. 3)
**Text (two-part code panel):**
```dockerfile
FROM python:3.11
COPY . /app
RUN pip install -r requirements.txt
CMD ["python", "agent_service.py"]
```
```bash
gcloud run deploy epc-agent --source .
```
**Visual:** Dark Deep Navy panel split into two stacked sections with a thin Deep Navy divider line — top section labeled "Dockerfile" in small Slate Blue caption text, bottom section labeled "Deploy Command." White monospace code in both, no comments needed since the narration explains each line.

---

### Slide 8 — CONTEXT-SWAP STRIP
**Text (4 mini-flows):**
Healthcare: Message → Container → Cloud Run → Agent Executes → Response
Finance: Transaction → Container → Cloud Run → Agent Executes → Response
Retail: Order Issue → Container → Cloud Run → Agent Executes → Response
Manufacturing: Sensor Alert → Container → Cloud Run → Agent Executes → Response
**Visual:** Four rows stacked, alternating white/Pale Blue Tint backgrounds. Compact 5-chip Slate Blue mini-flows, Deep Navy arrows, Signal Orange industry tag at the left of each row.

---

### Slide 9 — DEFINITION v2
**Text:** ~~Deploying an AI Agent is making it available for everyone to use.~~ Deploying an AI Agent is teaching it to **run the same way everywhere, stay available continuously, and respond to anyone who needs it.**
**Visual:** Same layout as Slide 1. v1 shown small, faded, struck through. New clause in Signal Orange within a Deep Navy sentence.

---

### Slide 10 — SCOPE-INCREASE GRID
**Text (4 tiles):** Handle One Request · Handle Many at Once · Handle a Sudden Spike · Avoid Wasting Resources
**Visual:** Four Pale Blue Tint square tiles in a row, white background, Slate Blue icons (single-dot / grid-of-dots / spike-graph / battery-saving). Fourth tile outlined in Signal Orange, hinting at the cost-conscious scaling logic coming next.

---

### Slide 11 — CONCEPT DIAGRAM v2 (Cycle 2) — *reuses Slide 5's shape exactly*
**Text:** REQUEST ARRIVES → RUNNING SERVICE HANDLES IT → RESPONSE RETURNED, with a branch down to AUTO-SCALE UP OR DOWN, and a separate parallel track: NEW CODE PUSHED → BUILT → DEPLOYED → OLD VERSION REPLACED, NO DOWNTIME
**Visual:** Slide 5's three boxes untouched at the top. A Signal Orange branch arrow drops from the middle box down to a small Slate Blue "Auto-Scale" box. Below that, a completely separate horizontal row of four small Slate Blue boxes (the deployment track) runs in parallel, visually detached from the main row to show it happens continuously in the background, not as a step in the request path.

---

### Slide 12 — EXECUTION FLOW v2 (Cycle 2)
**Text (two parallel tracks):**
Scaling: 1. Traffic Increases → More Containers Start → Requests Distributed · 2. Traffic Drops → Unused Containers Removed
Deployment: 1. Developer Pushes to GitHub · 2. Tests Run · 3. Container Built · 4. Deployed to Cloud Run · 5. Old Version Replaced, Users Never Notice
**Visual:** Two horizontal rows, clearly separated with a labeled divider. Top row ("Scaling") — two Slate Blue boxes with a Signal Orange up/down arrow icon between them. Bottom row ("Deployment") — five smaller Slate Blue boxes in sequence, Deep Navy arrows, last box bordered in Signal Orange to mark "no downtime" as the payoff.

---

### Slide 13 — CODE v2 (Cycle 2)
**Text (code panel):**
```bash
gcloud run deploy epc-agent --source . --min-instances=0 --max-instances=10
```
**Visual:** Dark Deep Navy panel, one line of code, large monospace type since there's so little text. The `--min-instances=0` and `--max-instances=10` flags are individually highlighted with small Signal Orange underlines, each with a tiny caption above ("cost control" / "growth ceiling").

---

### Slide 14 — CONTEXT-SWAP STRIP v2
**Text (4 rows — scaling trigger / redeployment scenario):**
Retail: Traffic spikes during a major sale → A pricing bug fix ships mid-sale, no downtime
Healthcare: Message volume spikes during flu season → A new symptom-check rule ships, no downtime
Finance: Transaction volume spikes during volatility → A fraud rule update ships, no downtime
Manufacturing: Sensor readings spike during a plant startup → A calibration fix ships, no downtime
**Visual:** Four rows, alternating white/Pale Blue Tint backgrounds, Signal Orange industry tag at left. Each row: a Slate Blue "Scaling Trigger" box, Deep Navy arrow, a Signal Orange-outlined "Redeployment" box.

---

### Slide 15 — DEFINITION v3
**Text:** ~~...run the same way everywhere, stay available continuously, and respond to anyone who needs it.~~ Deploying an AI Agent is teaching it to run everywhere, stay available, **scale automatically to match real demand, and receive updates safely without ever interrupting the people relying on it.**
**Visual:** Same layout as Slides 1 and 9. v2 sentence faded/struck through above. New clause in Signal Orange appended.

---

### Slide 16 — BREAK-THE-SYSTEM GRID
**Text (6 tiles):** Cold Start Delay · Runaway Scaling Cost · Bad Deploy Reaches Live Users · State Lost During Scaling · Container Works Locally, Fails in Prod · No Rollback Ready
**Sub-flow:** Detect → Rollback → Fix
**Visual:** Six tiles, white background, Signal Orange border, Deep Navy icon per tile (clock-with-hourglass, dollar-sign-spike, broken-shield, disappearing-folder, laptop-vs-server-mismatch, missing-undo-arrow) + label. Beneath: compact 3-box Slate Blue flow (Detect → Rollback → Fix), "Detect" in Signal Orange.

---

### Slide 17 — FINAL MODEL
**Text:** "Deploying and Operating an Enterprise AI Agent packages it so it runs identically everywhere, hosts it somewhere continuously available, scales it automatically to match real demand, and updates it safely without ever interrupting the people relying on it."
**Visual:** White background, Deep Navy paragraph on the left. On the right, Slide 5/11's diagram shrunk into a round Signal Orange-bordered badge (dots for Request/Service/Response connected by thin Deep Navy lines, small orange scale-arrow and deploy-arrow marking the two additions). Caption beneath in Slate Blue italic: "This is why live state lives in Firestore, not inside the container — a container can vanish the moment traffic drops. Memory cannot."

---

## Note on source content

This spec was built directly from your uploaded `lecture_notes.md`, which is genuinely Week 3 content — both S-Tier headers match (Agent Memory/Safety, then Deployment/Operations), and the closing lines of S-Tier 2 explicitly tie back to S-Tier 1's Firestore design decision, confirming they belong together. No mismatch this time.

Industries needed light adaptation for consistency: S-Tier 1 mixes a Healthcare/Finance/HR/Cybersecurity table with a Healthcare/Finance/Legal/Manufacturing table in the source — I standardized on Healthcare, Finance, Legal, Cybersecurity throughout and adapted the two odd-ones-out (HR → Legal, Manufacturing → Cybersecurity) using the same formula the source already uses elsewhere. S-Tier 2's two tables overlapped more naturally, so I kept Healthcare, Finance, Retail, Manufacturing as given.

Still waiting on Weeks 4 and 5 — upload each with a distinct filename whenever you're ready.
