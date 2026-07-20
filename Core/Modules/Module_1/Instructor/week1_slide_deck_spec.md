# Week 1 Slide Deck — Building Autonomous AI Agents
### Running Project: Enterprise Problem Consolidation (EPC) Agent · Industries sampled: Healthcare, Finance, Legal, Cybersecurity
### Design system: Scale v1.0 — Signal Orange #F2661D (active) · Slate Blue #3E6FA8 (structural) · Pale Blue Tint #E8EFF7 · Deep Navy #1A2B4C (text/lines) · backgrounds white or Pale Blue Tint only

---

### Slide 1 — HOOK
**Text:** "Building Autonomous AI Agents is teaching a computer to do a job by itself."
Sub-line: "What's the difference between answering and deciding?"
**Visual:** Full white background. Definition set in huge Deep Navy display type, centered, three lines max. Sub-line in small Slate Blue italic beneath, no icon, no color accent — nothing to distract from the type.

---

### Slide 2 — PROBLEM PAIR
**Text (Card 1):** "No memory carries over between tickets." → "A consultant who forgets every conversation the second it ends — this is exactly what we cannot do."
**Text (Card 2):** "No one watches until a human asks." → "A guard who only checks the one door you point to — this is exactly what we cannot do."
**Visual:** Two equal Pale Blue Tint cards side by side on white background, Deep Navy frame lines. Each card: a Slate Blue line icon at top (Card 1: speech bubble with a fading/erase mark; Card 2: a shield with a static "off" bell), problem label in Deep Navy bold, analogy line below in Deep Navy regular with the closing phrase "...we cannot do" set in Signal Orange to mark the emotional beat of each card.

---

### Slide 3 — COMPARISON TABLE
**Text:**
| Without Autonomous Agents | With Autonomous Agents |
|---|---|
| Human triggers every check | Agent watches on its own |
| No memory between requests | Agent remembers what it saw |
| Related issues stay scattered | Agent connects the full picture |
| Person must spot the pattern | Agent spots it and acts |

**Visual:** Two-column table, no outer border, thin Deep Navy horizontal rules between rows only. Left column header and text in Slate Blue (the "without" state, structural/static). Right column header in Signal Orange (the "with" state — the active capability), text in Deep Navy for readability. White background.

---

### Slide 4 — RUNNING EXAMPLE INTRO
**Text:** "Enterprise Problem Consolidation (EPC) Agent"
**Visual:** Centered on white background: one large Slate Blue line-icon of an inbox/ticket stack with a single ticket highlighted in Signal Orange sliding into it. Project name in Deep Navy beneath, no other text, no bullets.

---

### Slide 5 — CONCEPT DIAGRAM (Rep. 1) — *the fixed shape, reused in Slides 11 and 17*
**Text (labels only):** PERCEIVE → UNDERSTAND → DECIDE → ACT, with REMEMBER beneath feeding back into PERCEIVE
**Visual:** Four equal Slate Blue rounded rectangles in a horizontal row on Pale Blue Tint background, connected by Deep Navy arrows left to right. A fifth Slate Blue box labeled REMEMBER sits below the row, connected by a Deep Navy return arrow curving from ACT back up into PERCEIVE. No orange yet — this slide is pure structure, establishing the shape that returns unchanged later.

---

### Slide 6 — EXECUTION FLOW (Rep. 2)
**Text (5 stage boxes):** 1. Ticket Arrives 2. Agent Interprets Ticket 3. Agent Produces Clean Output 4. Agent Checks What It Knows 5. Agent Decides and Acts
**Tags above stages 1–4:** Enterprise AI · Prompt Engineering · Structured Outputs · Memory & State
**Visual:** Five horizontal Slate Blue boxes on white background connected by Deep Navy arrows. Small pill-shaped Signal Orange tags float above boxes 1–4 (one tag per box, matching order). Box 5 has no tag — its Deep Navy border is slightly thicker, marking it as the payoff stage.

---

### Slide 7 — CODE (Rep. 3)
**Text (code panel):**
```python
def run_agent_cycle(new_ticket_text: str, memory_store):
    raw = perceive(new_ticket_text)              # PERCEIVE
    interpreted = understand(raw)                 # UNDERSTAND — Prompt Engineering
    structured_ticket = structure(interpreted)     # STRUCTURE — Structured Outputs
    related = remember(structured_ticket, memory_store)  # REMEMBER — Memory & State
    decide_and_act(structured_ticket, related)      # DECIDE & ACT
```
**Visual:** Single dark Deep Navy code panel centered on Pale Blue Tint background, monospace type in white/Pale Blue Tint, inline comments in Signal Orange to draw the eye straight to which line maps to which lecture concept. No other elements on slide.

---

### Slide 8 — CONTEXT-SWAP STRIP
**Text (4 mini-flows, same 5 stages, captions only change):**
Healthcare: Patient Message → Interpret Symptoms → Clinical Fields → History Check → Escalate/Monitor
Finance: Transaction → Interpret Pattern → Risk Fields → Account Check → Freeze/Allow
Legal: Filing Arrives → Interpret Category → Case Fields → Precedent Check → Route/Review
Cybersecurity: Log Entry → Interpret Threat → Incident Fields → Attack-History Check → Lock/Alert
**Visual:** Four identical miniature versions of Slide 6's execution flow stacked in a vertical strip, each row on alternating white/Pale Blue Tint background. Boxes and arrows stay Slate Blue/Deep Navy exactly as before — only the caption text changes per row. One small Signal Orange industry-name tag sits at the left edge of each row.

---

### Slide 9 — DEFINITION v2
**Text:** ~~Building Autonomous AI Agents is teaching a computer to do a job by itself.~~ Building Autonomous AI Agents is teaching a computer to **perceive continuously, understand reliably, remember what it has seen, and decide on its own what to do next** — without waiting to be asked.
**Visual:** Same centered layout as Slide 1. Original v1 text shown small, faded Slate Blue with strikethrough above the new line. New clause set in Signal Orange within the otherwise Deep Navy sentence, so the eye catches exactly what changed.

---

### Slide 10 — SCOPE-INCREASE GRID
**Text (5 tiles):** Understand · Reason · Remember · Structure Output · Recover
**Visual:** Five equal square Pale Blue Tint tiles in a single row on white background, each with one simple Slate Blue line icon (magnifying glass / thought bubble / floppy-disk-or-clock / brackets-JSON / refresh-arrow) and a one-word Deep Navy label beneath. The fifth tile ("Recover") has a thin Signal Orange outline instead of Deep Navy — it's the new, not-yet-explained capability the next slides will unpack.

---

### Slide 11 — CONCEPT DIAGRAM v2 (Cycle 2) — *reuses Slide 5's shape exactly*
**Text (added labels only):** VALIDATE inserted between DECIDE and ACT; RECOVER/RETRY branching beneath it
**Visual:** Identical four-box row and REMEMBER loop from Slide 5, completely unchanged in position and color. One new Signal Orange diamond labeled VALIDATE is inserted on the arrow between DECIDE and ACT, with a Signal Orange branch arrow dropping down to a new Slate Blue box labeled RECOVER/RETRY, which loops back up into DECIDE. This is the only new layer — nothing else on the diagram moves.

---

### Slide 12 — EXECUTION FLOW v2 (Cycle 2)
**Text (7 stages, stages 1–5 unchanged from Slide 6, stages 6–7 new):** 6. Agent Validates Its Own Decision · 7. Valid → Act / Invalid → Retry or Fall Back
**Visual:** Same five Slate Blue boxes from Slide 6, unchanged, now followed by two new boxes: a Signal Orange diamond (Validate) and a split Deep Navy arrow leading to two small end boxes — one Slate Blue ("Act"), one outlined in Signal Orange ("Retry/Fallback"). Same white background and arrow style as Slide 6.

---

### Slide 13 — CODE v2 (Cycle 2)
**Text (code panel):**
```python
def validate_decision(structured_ticket, decision) -> bool:
    if not schema_is_valid(structured_ticket):
        return False
    if decision == "escalate" and structured_ticket["urgency"] == "Low":
        return False                      # contradiction check
    return True

def run_agent_cycle(new_ticket_text, memory_store, attempt=0):
    ...                                    # same 5 lines as Cycle 1
    decision = decide(structured_ticket, related)
    if validate_decision(structured_ticket, decision):
        act(structured_ticket, decision)
    else:
        recover(structured_ticket, attempt)  # retry, then safe fallback
```
**Visual:** Same dark Deep Navy code panel style as Slide 7, Pale Blue Tint background. The `...` line is set in Slate Blue to visually mark "unchanged from before," while the new `if/else` block beneath it is bracketed by a thin Signal Orange line down the left margin, showing exactly what was added.

---

### Slide 14 — CONTEXT-SWAP STRIP v2
**Text (4 rows — what Validate catches / what Recover does):**
Healthcare: Escalation contradicts "Low" severity → Retry once, then route to a nurse
Finance: Missing required field → Retry extraction, then hold for manual review
Legal: Malformed deadline field → Retry parsing, then flag for paralegal review
Cybersecurity: "Ignore" contradicts 5 prior alerts → Retry with memory re-checked, then auto-escalate
**Visual:** Same four-row vertical strip layout as Slide 8, same industry order and Signal Orange tags. Each row now ends in a small two-step mini-flow (Retry → Fallback) rendered in the same Slate Blue/Signal Orange style as Slide 12's new stages.

---

### Slide 15 — DEFINITION v3
**Text:** ~~...decide on its own what to do next — without waiting to be asked.~~ Building Autonomous AI Agents is teaching a computer to perceive, understand, remember, and decide on its own, **check that decision against what it knows, and recover safely when something goes wrong.**
**Visual:** Same layout as Slides 1 and 9. Slide 9's full sentence now shown faded/struck-through above. New clause in Signal Orange appended at the end of the Deep Navy sentence.

---

### Slide 16 — BREAK-THE-SYSTEM GRID
**Text (6 tiles):** Hallucination · Malformed JSON · Prompt Failure · Missing Memory · Invalid State · Conflicting Info
**Sub-flow:** Detect → Retry → Recover
**Visual:** Six tiles in a 3×2 grid, red-orange tint background (a warmer, warning-shifted tone of Signal Orange, distinct from the tint backgrounds used elsewhere), each with a small Deep Navy warning-style icon and label. Beneath the grid, a compact three-box horizontal flow (Detect → Retry → Recover) in the same Slate Blue box / Deep Navy arrow style as the execution-flow slides, with "Detect" in Signal Orange to show it's the active trigger step.

---

### Slide 17 — FINAL MODEL
**Text:** "An autonomous AI agent perceives continuously, interprets through engineered prompts, structures its output, checks and updates memory, decides without being asked, validates its own decisions, and recovers safely — falling back to a human — when something goes wrong."
**Visual:** White background, text arranged in a tight paragraph block on the left in Deep Navy. On the right, Slide 5/11's full diagram is shrunk into a single round Signal Orange-bordered badge (all internal boxes reduced to dots connected by thin Deep Navy lines, VALIDATE/RECOVER still visible as a small loop) — a snapshot of the entire lecture's shape, not a bullet summary.
