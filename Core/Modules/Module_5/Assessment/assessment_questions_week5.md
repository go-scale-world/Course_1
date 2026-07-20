# Week 5 Assessment — Connect to Tools, Then Connect to Each Other
### Running Project: Enterprise Problem Consolidation (EPC) Agent

---

## Section A — Knowledge Check (10 MCQs)

**A1.** What is the core capability MCP gives an agent that hand-written, tool-specific integration code does not?
A) Faster response times
B) A standard interface so any external system can be connected without rewriting the agent's internal logic
C) Automatic ticket clustering
D) The ability to generate images

**A2.** How does "Advanced RAG" in Week 5 differ conceptually from the RAG built in Week 2?
A) It uses a completely different retrieval concept
B) The concept is unchanged; only the granularity changed — retrieving structured sections instead of whole documents
C) It no longer uses embeddings
D) It only works with Gmail

**A3.** What does Context Engineering add on top of basic retrieval?
A) A way to skip retrieval entirely
B) It combines semantic similarity, keyword matching, and relevance scoring to construct the most useful context, not just the most similar one
C) A way to increase the model's temperature automatically
D) A way to store tickets permanently

**A4.** In the two-way MCP interface, what is the difference between a "read" and a "write" operation?
A) Reads are faster than writes
B) A read retrieves knowledge from a system; a write performs a real action, like creating a document or drafting a notification
C) Writes only work with Drive, reads only work with Gmail
D) There is no meaningful difference

**A5.** Why is a custom, hand-written Gmail integration function (like in Week 2) different in spirit from an MCP-based Gmail connection?
A) They are functionally identical, just different code style
B) The custom function only works for Gmail specifically; MCP lets the same interface extend to Drive, Docs, Chat, or any future tool without new agent logic
C) MCP cannot perform actions, only custom functions can
D) Custom functions are always faster than MCP

**A6.** What problem does the A2A Protocol solve that MCP does not?
A) A2A connects an agent to enterprise tools like Drive and Gmail
B) A2A connects one agent to another independently built agent, rather than connecting an agent to a tool
C) A2A replaces the need for an orchestrator
D) A2A only works within a single agent

**A7.** What is the purpose of the Agent Discovery & Registry?
A) It stores historical incident data
B) It lets agents register what they can do so other agents can find and use them dynamically, without hard-coded addresses
C) It validates ticket schemas
D) It replaces the need for guardrails

**A8.** What does an "Agent Skill" actually represent?
A) A specific line of code inside one agent
B) A reusable, versioned capability that describes what an agent can do, independent of which specific agent provides it
C) A type of Firestore document
D) A guardrail rule

**A9.** Why should a calling agent request a skill "by name" rather than by a specific agent or version?
A) Names are shorter and use less bandwidth
B) So the registry can resolve to whichever version is current, letting the underlying agent be upgraded without breaking the caller
C) Because agent names are not allowed in code
D) Because skills cannot be versioned

**A10.** What is the clearest distinction between MCP and A2A?
A) They are the same protocol used for different industries
B) MCP connects an agent to tools and enterprise systems; A2A connects an agent to other agents
C) MCP is for reading only, A2A is for writing only
D) A2A replaces MCP entirely in Week 5

---

## Section B — Applied Skill Check (5 questions)

**B1.** Below is a simplified context-retrieval function. One line is missing — without it, the agent receives raw retrieved sections instead of the most genuinely useful ones. What line belongs there?
```python
def get_context(agent, query, mcp_server):
    raw_sections = mcp_server.retrieve_sections(query)
    ______________________
    return ranked[:3]
```

**B2.** An MCP call to Drive returns these three sections for a query about "Android photo upload crash":
```
[Runbook §2: Known affected versions] score=0.88
[SLA §1: Response time commitments] score=0.41
[Postmortem §3: Root cause] score=0.79
```
Which sections should be included in the top-ranked context, and which should most likely be excluded? Why?

**B3.** Complete the missing branch so this function can both retrieve knowledge and perform an action through the same MCP interface:
```python
def mcp_call(agent, operation, mcp_server, **kwargs):
    if operation == "read":
        return mcp_server.retrieve_sections(**kwargs)
    ______________________
```

**B4.** An agent needs the "Root Cause Analysis" skill but doesn't know which specific agent currently provides it, or which version is live. Complete the correct function call, and explain why calling a specific agent directly would be the wrong approach:
```python
discover_agent_for_skill(______________________)
```

**B5.** A newly connected MCP server returns this response to a retrieval request:
```json
{"content": "Runbook text here..."}
```
The agent's code expects a response shaped like `{"sections": [...], "scores": [...]}`. What will happen, and what's the actual root cause?

---

## Section C — Holistic Scope Check (5 questions)

**C1.** A healthcare team reuses this architecture. A diagnostic agent needs a lab report from the hospital's records system, and also needs to consult a newly built Specialist Agent before finalizing its recommendation. Trace the full sequence, including which protocol handles each connection and why.

**C2.** A Compliance Agent gets upgraded from v1 to v2 while an existing Escalation Agent is actively calling it in production. Trace what should happen, referencing skill versioning specifically.

**C3.** Drive's MCP server goes down for ten minutes while the Escalation Agent is mid-report, needing a runbook excerpt. Trace what should happen, referencing MCP-specific failure handling.

**C4.** A new Compliance Agent registers itself in the registry, but its skill was never actually tested before registering. The first real caller receives a broken response. Trace what went wrong, and how the design should have prevented it.

**C5.** A new team joining the project wants to hard-code their new fraud-detection capability directly into the Escalation Agent's code. Explain why they should instead build it as a registered, discoverable Agent Skill.
