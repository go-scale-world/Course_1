# Week 5 Assessment — Answer Key
### Connect to Tools, Then Connect to Each Other · Running Project: EPC Agent

---

## Section A — Knowledge Check

**A1. Answer: B** — A) speed isn't the stated benefit; C) unrelated to clustering; D) MCP connects to enterprise systems, not image generation.

**A2. Answer: B** — A) the underlying concept is explicitly unchanged; C) embeddings are still used, just at finer granularity; D) it applies across any connected MCP server, not just Gmail.

**A3. Answer: B** — A) retrieval still happens, it's refined, not skipped; C) temperature is unrelated to context construction; D) storage is a memory-layer concern, not context engineering.

**A4. Answer: B** — A) speed isn't the distinguishing factor described; C) both operations can apply to any connected system; D) the distinction is central to this cycle's lecture.

**A5. Answer: B** — A) they differ specifically in reusability and scope; C) MCP performs both reads and writes; D) speed isn't the stated difference.

**A6. Answer: B** — A) that's MCP's job, not A2A's; C) an orchestrator still coordinates execution within a pipeline; D) A2A specifically enables cross-agent communication.

**A7. Answer: B** — A) historical data lives in the memory layer, not the registry; C) schema validation is a Week 1 concern; D) guardrails are a separate, still-necessary layer.

**A8. Answer: B** — A) a skill is agent-independent, not tied to specific code; C) it's a registry concept, not a Firestore document type; D) guardrails are unrelated to skill definitions.

**A9. Answer: B** — A) bandwidth isn't the reason given; C) agent names are valid identifiers, that's not the constraint; D) skills are explicitly designed to be versioned.

**A10. Answer: B** — A) they are distinct protocols solving different problems; C) both protocols can involve reading and writing depending on the operation; D) A2A complements MCP, it doesn't replace it.

**Difficulty mix:** A1, A3, A4, A6, A7, A8 = straightforward recall. A2, A5, A9, A10 = require distinguishing closely related concepts (Advanced RAG vs. Week 2's RAG, custom integration vs. MCP, skill-name lookup vs. direct agent/version reference, MCP vs. A2A).

---

## Section B — Applied Skill Check

**B1. Expected answer:**
```python
ranked = rank_by_relevance(raw_sections, query)
```
Without this step, the agent would receive whatever sections were retrieved, unranked — Context Engineering's entire value is combining multiple relevance signals to surface the most genuinely useful pieces before they're returned.

**B2. Expected answer:**
The **Runbook §2** (0.88) and **Postmortem §3** (0.79) sections should be included — both are directly relevant to a photo upload crash. The **SLA §1** section (0.41) should most likely be excluded — it concerns response time commitments, not the technical issue itself, and its relevance score is far lower than the other two.

**B3. Expected answer:**
```python
return mcp_server.write(**kwargs)
```
This completes the two-way interface — when the operation isn't a read, the same `mcp_server` object handles the write (e.g., creating a document or drafting a notification) through the identical MCP connection.

**B4. Expected answer:**
```python
discover_agent_for_skill("root_cause_analysis", version="latest")
```
Calling a specific agent directly would hard-code a dependency that breaks the moment that agent is upgraded, replaced, or renamed. Asking for the skill by name lets the registry resolve whichever agent currently provides it, automatically.

**B5. Expected answer:**
The agent's code will fail, or behave unpredictably, trying to read `sections` and `scores` fields that simply don't exist in this response. This is a **Protocol Mismatch** — the root cause is that the newly connected MCP server doesn't correctly implement the expected interface. It should have been validated against the protocol specification before ever being connected.

---

## Section C — Holistic Scope Check

**C1. Model answer:**
- The diagnostic agent needs two different kinds of connections here — MCP should be used to retrieve the lab report from the hospital's records system, since that's a tool/knowledge connection, not an agent.
- The lab report retrieval should use structured, section-level retrieval (Advanced RAG) rather than pulling the entire record, with Context Engineering ranking the most relevant sections for this specific patient's situation.
- To consult the Specialist Agent, the diagnostic agent should use **A2A**, not MCP, since this is agent-to-agent communication, not agent-to-tool.
- Before calling the Specialist Agent, the diagnostic agent should discover it via the registry, asking for the relevant skill (e.g., "rare_disease_consultation") rather than hard-coding a specific agent's address.
- The final recommendation should combine both — the retrieved lab context and the specialist's input — with each connection using the protocol actually designed for that kind of relationship.

**C2. Model answer:**
- The Escalation Agent should be calling the Compliance skill by name (e.g., `"compliance_check"`), not by a specific agent instance or hard-coded version — this is the entire point of the discovery-plus-versioning design.
- When the Compliance Agent is upgraded to v2, the registry updates to point to the new version as current.
- The Escalation Agent's next call, resolved through `discover_agent_for_skill("compliance_check", version="latest")`, automatically routes to v2, with no code change required on the Escalation Agent's side.
- Had the Escalation Agent instead hard-coded a connection to the specific v1 agent, this upgrade would have either broken it outright or silently left it using an outdated version.
- This is exactly the scenario the lecture's versioning layer exists to handle — safe, invisible upgrades for any caller that follows the "ask by skill name" convention.

**C3. Model answer:**
- The runbook retrieval call to Drive's MCP server should fail with a timeout or connection error — an MCP Server Down failure.
- The correct response is to retry with backoff first, rather than immediately giving up or blocking the entire escalation report.
- If retries continue to fail within a reasonable window, the agent should fall back to whatever cached context is available, rather than stalling the report indefinitely.
- The escalation report should still be generated and sent, possibly noting that runbook context couldn't be retrieved this time, rather than the whole pipeline halting because one MCP server was temporarily unavailable.
- This illustrates why MCP failures need graceful degradation, not just retries — a temporarily unavailable tool shouldn't be allowed to block the rest of an otherwise working system.

**C4. Model answer:**
- This is a Silent Capability Loss failure — a skill was registered before it was actually verified to work correctly.
- The first genuine caller invokes it in good faith, expecting a working capability, and instead receives a broken response.
- The root design flaw is that registration happened without a required health check or working test call being passed first.
- The fix is to require a skill to pass a basic health check before it's allowed to register as available — registration should mean "verified to work," not merely "declared to exist."
- This connects to the broader lesson of the week: discoverability without verification just moves the trust problem somewhere less visible, rather than actually solving it.

**C5. Model answer:**
- A hard-coded connection means the Escalation Agent's code has to be directly modified any time the fraud-detection capability changes, moves, or gets upgraded — creating exactly the brittle dependency this week's lecture is designed to avoid.
- Registering it as a discoverable Agent Skill means any current or future agent, not just the Escalation Agent, can find and use this capability without needing to know it exists in advance.
- If the fraud-detection logic gets upgraded to a new version later, callers who ask for the skill by name automatically get the current version, with no code changes needed on their end.
- This also means the new team's own agent could later be replaced or refactored entirely, and as long as it still registers the same skill name, nothing calling it needs to change.
- In short: hard-coding creates a one-off, fragile dependency; registering a skill creates a reusable, upgradeable capability the entire ecosystem can benefit from.
