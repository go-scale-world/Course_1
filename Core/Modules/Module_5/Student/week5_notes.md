# Week 05 — MCP, Advanced RAG & Agent-to-Agent Interoperability

---

## 1. Model Context Protocol (MCP)
**Definition:** An open standard that lets an AI agent connect to external tools and data sources (like Drive, Docs, Gmail, Chat) through a common interface, instead of writing custom integration code for every single tool.

**Types:**
- **First-party MCP servers** — built and hosted by the tool provider (e.g., a Drive MCP server).
- **Custom/self-hosted MCP servers** — wrap your own internal APIs so agents can call them the same standardized way.

**Examples:**
- Tool servers are deployed for Drive, Docs, Gmail, and Chat, and any agent can call them.
- When a new tool is added to the server, every agent can use it immediately with zero code changes.
- Agent 4 uses the MCP Docs server to write a summary automatically once a cluster is approved.

---

## 2. Tool Use and Integration (Advanced)
**Definition:** Extending an agent's tool use beyond simple lookups into real, multi-tool workflows — querying one system and writing to another as part of a single reasoning process.

**Types:**
- **Read tools** — querying data (e.g., searching Drive for related history).
- **Write/action tools** — creating output (e.g., drafting a Gmail message).

**Examples:**
- Agent 2 queries Drive on every new cluster to enrich it with historical context.
- Agent 4 writes a Docs summary, a Gmail draft, and a Chat post once approval happens.
- Every cluster is now enriched automatically instead of requiring a person to search manually.

---

## 3. Retrieval Augmented Generation (RAG) — Section-Level
**Definition:** A more refined version of RAG where documents are broken into meaningful sections (not just whole documents) and tagged, so retrieval returns exactly the relevant part, not a whole unrelated file.

**Types:**
- **Whole-document retrieval** — simpler, less precise (used earlier in the course).
- **Section-level retrieval** — indexed by parts like timeline, root cause, resolution, with metadata tags.

**Examples:**
- Incident documents are re-indexed by section (timeline, root cause, resolution) with severity and area tags.
- A vague, loosely related incident ranks below a specific, relevant one every time.
- Clusters receive highly relevant past incidents enriched with resolution ownership details.

---

## 4. Context Engineering
**Definition:** Deliberately designing what information (and how much of it) gets fed into the model's context window, combining retrieval methods and scoring to keep only the most relevant material.

**Types:**
- **Semantic-only context** — pure embedding similarity.
- **Hybrid context (semantic + keyword)** — combines both signals, then re-ranks with a scorer.

**Examples:**
- Retrieval combines semantic similarity and keyword matching, then a scorer keeps just the top three results.
- Irrelevant matches get filtered out before they ever reach the model's context.
- This keeps responses grounded without overwhelming the model with noise.

---

## 5. Agent-to-Agent (A2A) Protocol
**Definition:** A standardized protocol that lets independent agents communicate and hand off tasks directly to each other, instead of relying only on one central orchestrator's fixed sequencing.

**Types:**
- **Orchestrator-driven handoff** — central workflow tool sequences everything (used in Week 04).
- **Direct A2A handoff** — agents talk to each other directly using a shared protocol.

**Examples:**
- Agents hand off tasks to each other using the shared protocol rather than relying solely on Cloud Workflows.
- A fifth agent added mid-session can be addressed by existing agents with no hard-coded integration.
- This mirrors the real-world A2A protocol that reached production status across many organizations in 2026.

---

## 6. Agent Discovery and Registry
**Definition:** A shared, queryable registry where each agent publishes its own identity and capabilities, so other agents (or systems) can find and use it dynamically.

**Types:**
- **Static registration** — capabilities hardcoded and never updated.
- **Dynamic registry** — agents register themselves and can be discovered live.

**Examples:**
- Each agent registers itself and its capabilities in a shared registry.
- A newly added agent can be discovered dynamically with no code changes to existing agents.
- Other agents can query the registry to find "who can do X" at runtime.

---

## 7. Agent Skills (Modular Capabilities)
**Definition:** Packaging a specific capability as a versioned, reusable "Skill" that can be attached to any agent in the registry, instead of rewriting core logic every time a capability needs to move or scale.

**Types:**
- **Single-agent capability** — logic baked directly into one agent (less flexible).
- **Portable Skill** — versioned package attachable to any agent.

**Examples:**
- A Skill built for Agent 2 is attached to Agent 4 and works correctly on the first attempt.
- Skills can be moved onto a different agent and work immediately, with no rewrite.
- Versioning means an updated Skill can roll out without breaking agents already using the old version.
