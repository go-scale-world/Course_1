# Week 5 Slide Deck — Two S-Tier Lectures
### Running Project: Enterprise Problem Consolidation (EPC) Agent
### Design system: Signal Orange #F2661D (active) · Slate Blue #3E6FA8 (structural) · Pale Blue Tint #E8EFF7 · Deep Navy #1A2B4C (text/lines) · backgrounds white or Pale Blue Tint only

---
---

# S-TIER 1 — Model Context Protocol (MCP) & Enterprise Context
### Industries sampled: Healthcare, Finance, Legal, Manufacturing

---

### Slide 1 — HOOK
**Text:** "Model Context Protocol is teaching every tool to speak one language."
Sub-line: "Does our agent learn every system's language separately, forever?"
**Visual:** White background. Definition in huge Deep Navy display type, centered, two lines. Sub-line small Slate Blue italic beneath.

---

### Slide 2 — PROBLEM PAIR
**Text (Card 1):** "Every tool needs its own custom integration code." → "Every appliance in your house needing its own uniquely shaped wall socket — this is exactly what we cannot do."
**Text (Card 2):** "Adding a new system means rewriting agent logic." → "A translator who learned Drive's language, then must go back to school from zero for every new system — this is exactly what we cannot do."
**Visual:** Two Pale Blue Tint cards, Deep Navy frame. Card 1 icon: a wall outlet with three mismatched plug shapes around it. Card 2 icon: a speech bubble with a small graduation cap. Analogy line ends in Signal Orange "...we cannot do."

---

### Slide 3 — COMPARISON TABLE
**Text:**
| Without MCP | With MCP |
|---|---|
| Every tool needs its own custom integration code | Every tool speaks through the same standard interface |
| Adding a new system means rewriting agent logic | Adding a new system means adding a new MCP server |
| Integration knowledge is trapped in one agent's code | Integration knowledge is reusable across every agent |
| A system swap breaks the agent | A system swap is invisible to the agent |
**Visual:** Two-column table, thin Deep Navy row rules only. Left column Slate Blue, right header Signal Orange, body Deep Navy. White background.

---

### Slide 4 — RUNNING EXAMPLE INTRO
**Text:** "Connecting to Drive, Docs, Gmail, and Chat — through one interface."
**Visual:** Centered white background. One large Slate Blue icon of a single plug with four thin lines fanning out to four small generic app-window icons. "Enterprise Problem Consolidation (EPC) Agent" in Deep Navy beneath, caption in Signal Orange italic underneath.

---

### Slide 5 — CONCEPT DIAGRAM (Rep. 1) — *fixed shape, reused in Slide 11 and 17*
**Text:** AGENT → STANDARD INTERFACE → EXTERNAL SYSTEM → STANDARD RESPONSE
**Visual:** Four Slate Blue rounded rectangles in a horizontal row on Pale Blue Tint background, Deep Navy arrows. No orange — pure structure, industry-independent shape.

---

### Slide 6 — EXECUTION FLOW (Rep. 2)
**Text (6 stages):** 1. Agent Needs External Information or Action · 2. Every Tool Must Look Identical to the Agent · 3. Request Goes Through the MCP Server · 4. Retrieval Returns Structured Sections, Not Whole Documents · 5. Best Context Is Assembled, Not Just Most Similar · 6. Standard Response Returns to the Agent
**Tags above stages 2, 4, 5:** Tool Use & Integration (Revisited) · Advanced RAG · Context Engineering
**Visual:** Six Slate Blue boxes in a row, Deep Navy arrows, Signal Orange pill tags above boxes 2, 4, and 5.

---

### Slide 7 — CODE (Rep. 3)
**Text (code panel):**
```python
def get_context(agent, query, mcp_server):
    raw_sections = mcp_server.retrieve_sections(query)      # structured, not whole docs
    ranked = rank_by_relevance(raw_sections, query)          # semantic + keyword + score
    return ranked[:3]                                        # top three, not everything
```
**Visual:** Dark Deep Navy code panel on Pale Blue Tint background, white monospace code, Signal Orange inline comments on all three lines.

---

### Slide 8 — CONTEXT-SWAP STRIP
**Text (4 mini-flows):**
Healthcare: Query → MCP → Lab System → Structured Sections → Ranked Context
Finance: Query → MCP → Reporting System → Structured Sections → Ranked Context
Legal: Query → MCP → Document System → Structured Sections → Ranked Context
Manufacturing: Query → MCP → Maintenance Log System → Structured Sections → Ranked Context
**Visual:** Four rows stacked, alternating white/Pale Blue Tint backgrounds. Compact 5-chip Slate Blue mini-flows, Deep Navy arrows, Signal Orange industry tag at the left of each row.

---

### Slide 9 — DEFINITION v2
**Text:** ~~Model Context Protocol is teaching every tool to speak one language.~~ Model Context Protocol is teaching every external tool to speak through one standard interface, **returning structured, ranked context instead of whole documents, so the agent's own reasoning never has to change.**
**Visual:** Same layout as Slide 1. v1 shown small, faded, struck through. New clause in Signal Orange within a Deep Navy sentence.

---

### Slide 10 — SCOPE-INCREASE GRID
**Text (4 tiles):** Connect to Drive, Docs, Gmail, Chat · Retrieve Only Relevant Sections · Rank What's Most Useful · No Change to Internal Logic
**Visual:** Four Pale Blue Tint square tiles in a row, white background, Slate Blue icons (four-app-grid / funnel / bar-ranking / gear-unchanged). Fourth tile outlined in Signal Orange — the not-yet-explained capability the next slides unpack.

---

### Slide 11 — CONCEPT DIAGRAM v2 (Cycle 2) — *reuses Slide 5's shape exactly*
**Text:** AGENT → STANDARD INTERFACE → EXTERNAL SYSTEM → (READ / WRITE) → STANDARD RESPONSE
**Visual:** Slide 5's four boxes untouched, with one new Signal Orange fork inserted after "External System" — splitting into a Slate Blue "Read" path and a Signal Orange-outlined "Write" path, both converging back into the existing "Standard Response" box.

---

### Slide 12 — EXECUTION FLOW v2 (Cycle 2)
**Text (4 stages, 1–2 unchanged, 3 new):** 1. Agent Needs External Information or Action · 2. For Retrieval: Same Read Path as Before · 3. For Actions: Same MCP Interface, Different Operation · 4. Standard Response Returns Either Way
**Visual:** Four Slate Blue boxes in a row. Box 3 has a Signal Orange dot marking it as new, with a small two-line caption beneath listing examples (create a Docs summary / draft a Gmail notification / post a Chat alert).

---

### Slide 13 — CODE v2 (Cycle 2)
**Text (code panel):**
```python
def mcp_call(agent, operation, mcp_server, **kwargs):
    if operation == "read":
        return mcp_server.retrieve_sections(**kwargs)
    return mcp_server.write(**kwargs)   # e.g. create_doc, draft_email, post_message
```
**Visual:** Same dark panel style as Slide 7. The `return mcp_server.write(...)` line sits inside a soft rounded Signal-Orange-tinted highlight — the one new branch added to an otherwise unchanged function.

---

### Slide 14 — CONTEXT-SWAP STRIP v2
**Text (4 rows — read example / write example):**
Healthcare: Retrieve a similar past case → Create a discharge summary
Finance: Retrieve a prior risk report → Draft a compliance notification
Legal: Retrieve a related past clause → Create a case summary document
Manufacturing: Retrieve a past maintenance log → Draft a technician alert
**Visual:** Four rows, alternating white/Pale Blue Tint backgrounds, Signal Orange industry tag at left. Each row: a Slate Blue "Read" box, Deep Navy arrow, a Signal Orange-outlined "Write" box.

---

### Slide 15 — DEFINITION v3
**Text:** ~~...returning structured, ranked context instead of whole documents...~~ Model Context Protocol returns structured, ranked context, and **performs real actions like creating a document or drafting a notification through that same interface** — whether it's retrieving or acting.
**Visual:** Same layout as Slides 1 and 9. v2 sentence faded/struck through above. New clause in Signal Orange appended.

---

### Slide 16 — BREAK-THE-SYSTEM GRID
**Text (6 tiles):** MCP Server Down · Wrong Section Retrieved · Stale Context · Write Conflict · Silent Write Failure · Protocol Mismatch
**Sub-flow:** Detect → Retry → Validate
**Visual:** Six tiles, white background, Signal Orange border, Deep Navy icon per tile (plug-with-x, magnifying-glass-with-wrong-mark, clock-with-outdated-doc, two-overlapping-pens, missing-checkmark, mismatched-puzzle-piece) + label. Beneath: compact 3-box Slate Blue flow (Detect → Retry → Validate), "Detect" in Signal Orange.

---

### Slide 17 — FINAL MODEL
**Text:** "Model Context Protocol lets an agent connect to any enterprise system — Drive, Docs, Gmail, Chat, or something not yet invented — through one standard interface that retrieves structured, ranked context and performs real actions, without the agent's own reasoning ever needing to change."
**Visual:** White background, Deep Navy paragraph on the left. On the right, Slide 5/11's diagram shrunk into a round Signal Orange-bordered badge (dots for Agent/Interface/System/Response connected by thin Deep Navy lines, small orange fork marking the Read/Write addition). Caption beneath: "MCP is not another tool — it's the universal language that lets any agent use any enterprise system."

---
---

# S-TIER 2 — Agent-to-Agent Communication & Capability Discovery
### Industries sampled: Healthcare, Finance, Legal, Cybersecurity

---

### Slide 1 — HOOK
**Text:** "Agent-to-Agent Protocol is teaching agents to talk to each other directly."
Sub-line: "Can our agents find a specialist they've never been introduced to?"
**Visual:** White background. Definition in huge Deep Navy display type, centered. Sub-line small Slate Blue italic beneath.

---

### Slide 2 — PROBLEM PAIR
**Text (Card 1):** "Agents only know coworkers they were hard-wired to." → "Employees who can only talk to coworkers they were personally introduced to on day one — this is exactly what we cannot do."
**Text (Card 2):** "No way to find a specialist that didn't exist yesterday." → "A phone system with no directory, where you must already know the number to call anyone — this is exactly what we cannot do."
**Visual:** Two Pale Blue Tint cards, Deep Navy frame. Card 1 icon: two figures connected by a single fixed line, a third figure isolated nearby. Card 2 icon: a phone with a crossed-out directory book. Analogy line ends in Signal Orange "...we cannot do."

---

### Slide 3 — COMPARISON TABLE
**Text:**
| Without A2A | With A2A |
|---|---|
| Agents only know coworkers they were hard-wired to | Agents can discover any registered agent dynamically |
| A new agent requires manually rewriting existing code | A new agent registers itself and becomes discoverable |
| An upgraded agent silently breaks old connections | Agents can discover the current version automatically |
| Capabilities live locked inside one specific agent | Capabilities exist as reusable skills any agent can call |
**Visual:** Same table style as S-Tier 1 Slide 3. Left column Slate Blue, right header Signal Orange, body Deep Navy, thin row rules only.

---

### Slide 4 — RUNNING EXAMPLE INTRO
**Text:** "A new Compliance Agent joins — nobody wired it in."
**Visual:** Centered white background. One large Slate Blue icon of four connected dots (the existing four agents) with a fifth dot appearing nearby, connected by a dotted Signal Orange line instead of a solid one. "Enterprise Problem Consolidation (EPC) Agent" in Deep Navy beneath, caption in Signal Orange italic underneath.

---

### Slide 5 — CONCEPT DIAGRAM (Rep. 1) — *fixed shape, reused in Slide 11 and 17*
**Text:** AGENT 1 → A2A → AGENT 2 → A2A → AGENT 3
**Visual:** Three Slate Blue circles connected by two Deep Navy "A2A" labeled arrows on Pale Blue Tint background. No orange — pure structure, the invariant shape of agent-to-agent communication.

---

### Slide 6 — EXECUTION FLOW (Rep. 2)
**Text (5 stages):** 1. A New Agent (Compliance Agent) Comes Online · 2. How Does Anyone Know It Exists? · 3. Other Agents Discover It Dynamically · 4. How Does It Say What It Can Do? · 5. Communication Begins Through A2A
**Tags above stages 2 and 4:** Agent Discovery & Registry · Agent Skills
**Visual:** Five Slate Blue boxes in a row, Deep Navy arrows, Signal Orange pill tags above box 2 and box 4.

---

### Slide 7 — CODE (Rep. 3)
**Text (code panel):**
```python
def register_agent(agent, skills):
    registry.add(agent.id, skills)               # e.g. ["compliance_check_v1"]

def discover_agent_for_skill(skill_name):
    return registry.find(skill_name)              # dynamic lookup, no hard-coded address

def call_via_a2a(skill_name, payload):
    agent = discover_agent_for_skill(skill_name)
    return agent.invoke(skill_name, payload)
```
**Visual:** Dark Deep Navy code panel on Pale Blue Tint background, three short functions stacked, white monospace, Signal Orange inline comments on the registry-add and registry-find lines.

---

### Slide 8 — CONTEXT-SWAP STRIP
**Text (4 mini-flows):**
Healthcare: New Specialist Agent Registers → Discovered by Diagnostic Agent → Invoked via A2A
Finance: New Compliance Agent Registers → Discovered by Fraud Agent → Invoked via A2A
Legal: New Risk-Review Agent Registers → Discovered by Intake Agent → Invoked via A2A
Cybersecurity: New Threat-Detection Agent Registers → Discovered by SOC Agent → Invoked via A2A
**Visual:** Four rows stacked, alternating white/Pale Blue Tint backgrounds. Compact 3-chip Slate Blue mini-flows, Deep Navy arrows, Signal Orange industry tag at the left of each row.

---

### Slide 9 — DEFINITION v2
**Text:** ~~Agent-to-Agent Protocol is teaching agents to talk to each other directly.~~ Agent-to-Agent Protocol is teaching agents to **register what they can do, discover each other dynamically, and communicate directly, without anyone hard-coding the connection in advance.**
**Visual:** Same layout as Slide 1. v1 shown small, faded, struck through. New clause in Signal Orange within a Deep Navy sentence.

---

### Slide 10 — SCOPE-INCREASE GRID
**Text (4 tiles):** Multiple Agents Registering · Multiple Skills Across Agents · Upgrade Without Breaking Callers · Keep Week 4's Pipeline Working
**Visual:** Four Pale Blue Tint square tiles in a row, white background, Slate Blue icons (grid-of-dots / layered-badges / shield-refresh / linked-chain). Fourth tile outlined in Signal Orange — the not-yet-explained capability.

---

### Slide 11 — CONCEPT DIAGRAM v2 (Cycle 2) — *reuses Slide 5's shape exactly*
**Text:** AGENT 1 → A2A → DISCOVER SKILL → [Registry resolves current version] → AGENT 2 (v2, current) — with Agent 2 v1 shown retired, old callers unaffected
**Visual:** Slide 5's three circles untouched. Between "Agent 1" and "Agent 2," a new Signal Orange rounded box ("Registry: resolves version") is inserted on the A2A arrow. A small greyed-out "v1" circle sits beside the active Slate Blue "v2" circle, connected by a Deep Navy "retired" label — showing the old version quietly stepping aside.

---

### Slide 12 — EXECUTION FLOW v2 (Cycle 2)
**Text (5 stages, 1 and 5 unchanged, 2–4 new):** 1. Agent Requests a Skill by Name · 2. Registry Resolves to the Current Version · 3. If the Provider Is Upgraded, the Registry Points to the New Version Automatically · 4. Old Callers Keep Working · 5. Communication Proceeds Through A2A as Before
**Visual:** Five Slate Blue boxes in a row, Deep Navy arrows. Boxes 2, 3, and 4 have a small Signal Orange dot marking them as new. Box 3 has a tiny upward-version-arrow icon inside it.

---

### Slide 13 — CODE v2 (Cycle 2)
**Text (code panel):**
```python
def discover_agent_for_skill(skill_name, version="latest"):
    return registry.find(skill_name, version)   # resolves to current version automatically
```
**Visual:** Same dark panel style as Slide 7. The `version="latest"` parameter is individually highlighted with a small Signal Orange underline and a tiny caption above it reading "the one new addition."

---

### Slide 14 — CONTEXT-SWAP STRIP v2
**Text (4 rows — skill upgraded / who was unaffected):**
Healthcare: Root Cause Analysis v1 → v2 → Every agent that called it by name
Finance: Risk Scoring v1 → v2 → Every agent that called it by name
Legal: Clause Review v1 → v2 → Every agent that called it by name
Cybersecurity: Threat Correlation v1 → v2 → Every agent that called it by name
**Visual:** Four rows, alternating white/Pale Blue Tint backgrounds, Signal Orange industry tag at left. Each row: a Slate Blue "v1 → v2" box, Deep Navy arrow, a Signal Orange-outlined "Unaffected Callers" box.

---

### Slide 15 — DEFINITION v3
**Text:** ~~...discover each other dynamically, and communicate directly...~~ Agent-to-Agent Protocol lets agents register, discover, and communicate, and **safely receive upgrades to the skills they depend on — without breaking anyone already relying on it.**
**Visual:** Same layout as Slides 1 and 9. v2 sentence faded/struck through above. New clause in Signal Orange appended.

---

### Slide 16 — BREAK-THE-SYSTEM GRID
**Text (6 tiles):** Stale Registry Entry · Duplicate Registration · Skill Name Collision · Version Mismatch · Circular Discovery · Silent Capability Loss
**Sub-flow:** Detect → Verify → Correct
**Visual:** Six tiles, white background, Signal Orange border, Deep Navy icon per tile (ghost-entry, two-overlapping-badges, colliding-labels, mismatched-shapes, circular-arrow-loop, broken-first-call) + label. Beneath: compact 3-box Slate Blue flow (Detect → Verify → Correct), "Detect" in Signal Orange.

---

### Slide 17 — FINAL MODEL
**Text:** "Agent-to-Agent Protocol lets independently built agents register what they can do, discover each other dynamically without hard-coded connections, communicate directly through a shared protocol, and safely evolve their capabilities through versioned skills — so the ecosystem can grow without anyone rewriting what already works."
**Visual:** White background, Deep Navy paragraph on the left. On the right, Slide 5/11's diagram shrunk into a round Signal Orange-bordered badge (three dots connected by thin Deep Navy "A2A" lines, small orange registry-box marking the versioning addition). Caption beneath: "MCP connects an agent to tools. A2A connects an agent to other agents. Together — nothing needs hard-coded wiring anymore."

---
---

## Note on source content

This spec was built directly from your uploaded `lecture_notes.md`, which is genuinely Week 5 content — both S-Tier headers match (MCP & Enterprise Context, then Agent-to-Agent Communication & Capability Discovery), and S-Tier 2's bridge line correctly distinguishes A2A from MCP, with the closing lines tying both together as "tool interoperability vs. agent interoperability." No mismatch.

Industries needed light standardizing: S-Tier 1's two tables both agreed on Healthcare, Finance, Legal — I dropped Retail (appeared once) in favor of Manufacturing (appeared in the second table), adapting Retail's "order system" row into a maintenance-log equivalent. S-Tier 2's two tables also agreed on Healthcare, Finance, Legal — I standardized on Cybersecurity as the fourth (matching the second table) and adapted the first table's Manufacturing row into a threat-detection equivalent using the same "registers → discovered by → invoked" formula.

That's all five weeks now — 1 through 5, plus the capstone Week 6 you gave me first. Let me know if you want any of them revisited, or if you're ready to move these specs into actual decks.
