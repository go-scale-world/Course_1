# Week 02 — GCP/GitHub Basics, Embeddings & Tool Calling
### (Extensive edition — includes a deep dive into the current GitHub stack, the current GCP stack, and the Gemini Enterprise Agent Platform, as of July 2026)

---

## 1. GCP Platform Fundamentals
**Definition:** The foundational Google Cloud services — Identity and Access Management (IAM), project structure, and core compute/storage services — that every other GCP workload is built on top of.

**Types (core building blocks):**
- **IAM** — who (or what agent) can do what, on which resource, scoped by role.
- **Projects** — the billing/permission container everything lives inside.
- **Core services** — Compute Engine, Cloud Storage, Cloud Run, Pub/Sub, Firestore/BigQuery.

**Examples:**
- Creating a service account with only "Storage Object Viewer" so an agent can read files but never delete them.
- Structuring one GCP project per environment (dev/staging/prod) with separate IAM policies.
- A pipeline job authenticates using a workload identity instead of a hardcoded key.

### 🔎 Deep dive — the current GCP stack (2026)
Google Cloud rebranded its entire AI/agent stack in April 2026 at Cloud Next. **Vertex AI has been renamed and absorbed into the Gemini Enterprise Agent Platform**, alongside the former "Agentspace" product. This is now the single front door for building, scaling, governing, and optimizing agents on GCP. Key things that changed and matter for this course:

- **The old Vertex AI console is now the Agent Platform console**, reachable at `console.cloud.google.com/agent-platform`. Existing Vertex AI resources (Vector Search, endpoints, Model Garden) did not need migration — they were simply re-branded under the new umbrella.
- **The underlying API is still `aiplatform.googleapis.com`** — the name changed twice (AI Platform → Vertex AI → Agent Platform) but the API endpoint stayed the same for backward compatibility.
- **Agent Development Kit (ADK)** reached a stable v1.0 across Python, Go, Java, and TypeScript — it's Google's open-source, model-agnostic framework for coding agents (you are not locked into Gemini; Claude and other models are first-class citizens in Model Garden now).
- **Agent Engine** is the managed runtime — you write your agent locally with ADK, then run a single `adk deploy` command to push it to a scalable, production runtime with built-in observability and evaluation.
- **Memory Bank** (part of Agent Engine) now has generally-available "memory profiles" — structured, schema-based memory so an agent doesn't need an expensive search operation just to recall a fact about a user or session.
- **MCP servers are natively supported** inside GCP (e.g., pre-built BigQuery and Google Maps MCP servers), managed through a Cloud API Registry, so tools can be exposed to any agent without custom glue code.
- **The Agent2Agent (A2A) protocol** moved from preview to production, letting agents built on different frameworks/vendors discover and hand off work to each other.
- Core services this course actually touches (IAM, Cloud Run, Pub/Sub, Firestore, Cloud Storage) are unchanged in behavior — they're just increasingly wired directly into the Agent Platform as first-class integrations rather than being "separate" services you bolt on yourself.

---

## 2. GitHub-Based Version Control Workflow
**Definition:** The standard collaboration workflow using Git repositories, branches, and pull requests (PRs) to propose, review, and merge code changes safely.

**Types:**
- **Feature branching** — one branch per feature/fix, merged via PR.
- **Trunk-based development** — short-lived branches merged frequently into `main`.
- **Forking workflow** — used for open-source contributions from outside collaborators.

**Examples:**
- A student creates `feature/ticket-classifier`, commits changes, opens a PR, and a teammate reviews it before merging to `main`.
- A broken build is caught because a CI check runs automatically on every PR before merge is allowed.
- Branch protection rules require at least one approval before merging into `main`.

### 🔎 Deep dive — the current GitHub/Copilot stack (2026)
GitHub's own workflow tooling hasn't changed much, but the **AI layer wrapped around it has moved fast** and is directly relevant since this course wires GitHub into an agentic pipeline:

- **Agent HQ**, announced at GitHub Universe, is now a control plane that orchestrates coding agents from multiple vendors (Anthropic, OpenAI, Google, and others) across GitHub, VS Code, the CLI, and mobile — all under one Copilot subscription. Copilot is now positioned as an *orchestration layer*, not just an autocomplete tool.
- **Copilot coding agent** is a fully autonomous background worker: assign it a GitHub Issue, and it spins up a sandboxed environment, branches the repo, writes code, runs tests, and opens a pull request for review — all using GitHub Actions runners in the background.
- **Agent mode** (multi-step autonomous editing inside the IDE) went GA in VS Code and JetBrains as of March 2026 — previously VS Code-only.
- **Copilot code review** is now "agentic": it gathers full project context before suggesting changes and can hand its findings straight to the coding agent, which opens a fix PR automatically.
- **Model picker**: you can choose which underlying model (GPT-series, Claude, Gemini) powers a given Copilot session — GitHub rotates and adds models over time rather than being tied to one vendor.
- **Billing shifted from request-based to usage/credit-based** on June 1, 2026 — premium features like agent mode and model selection consume credits by token usage rather than a flat per-request count.
- **MCP support** is built into Copilot's agent mode, so agents can call external tools/data sources through the same protocol used elsewhere in this course.
- **GitHub Actions** got a re-architected core in 2026 for better reliability, workflow flexibility, and CI/CD observability — relevant since Copilot's coding agent literally runs on Actions runners.

**Practical takeaway for this course:** GitHub is no longer "just" where code lives — it is becoming an agent execution surface in its own right (issue → autonomous agent → PR), which mirrors exactly the kind of pipeline you're building in this curriculum.

---

## 3. Retrieval Augmented Generation (RAG)
**Definition:** A technique where an LLM's response is "grounded" by first retrieving relevant external documents or data, then using that retrieved content as context for generation — instead of relying purely on what the model memorized during training.

**Types:**
- **Keyword-based retrieval** — classic search-index matching.
- **Semantic retrieval** — embedding-based similarity search.
- **Hybrid retrieval** — combines both semantic and keyword scoring (used later in Week 05).

**Examples:**
- A new support ticket is compared against past ticket embeddings to find which existing "cluster" of issues it belongs to.
- Four differently-worded tickets about "export failing" all retrieve and group under the same underlying issue.
- A login-related ticket does not match the export-failure cluster and correctly starts a new one.

---

## 4. Vector Databases
**Definition:** A database optimized for storing high-dimensional embedding vectors and performing fast nearest-neighbor similarity search, rather than exact-match queries.

**Types:**
- **Managed/cloud vector search** — e.g., Vertex AI Vector Search (fully managed, scales automatically).
- **Self-hosted vector stores** — run on your own infrastructure (out of scope here — GCP-managed only per this course's constraints).

**Examples:**
- Ticket descriptions are converted to embeddings and stored in a Vertex AI Vector Search index.
- A new ticket's embedding is compared against the index to find its nearest neighbors (similar past tickets).
- If no close match exists, the ticket seeds a brand-new cluster in the index.

---

## 5. Function Calling
**Definition:** A capability where an LLM doesn't just generate text — it can output a structured "call" to a specific function/tool with arguments, which your code then actually executes.

**Types:**
- **Single function call** — model picks one tool to invoke per turn.
- **Parallel/multiple function calls** — model can call several tools before responding.

**Examples:**
- The model decides on its own to call a "cluster history lookup" tool mid-extraction when it needs more context.
- Given a ticket, the model calls a "known-issue lookup" function instead of guessing whether the issue is already documented.
- A model returns `{"function": "lookup_cluster", "args": {"ticket_id": "1234"}}` instead of a plain-text guess.

---

## 6. Tool Use and Integration
**Definition:** The broader practice of giving an agent access to external tools (APIs, databases, messaging systems) it can call autonomously as part of completing a task.

**Types:**
- **Internal tools** — databases, internal lookups.
- **External/event tools** — messaging systems, triggers like Pub/Sub.

**Examples:**
- Pub/Sub triggers the entire extraction pipeline the moment a new ticket is submitted — no manual kickoff needed.
- The model calls a known-issue lookup tool autonomously while extracting fields from a ticket.
- An agent posts directly to a Chat channel as one of its available tools once a cluster is confirmed.

---

## 7. Reasoning-Planning-Action Loop and ReAct Pattern
**Definition:** ReAct ("Reason + Act") is a prompting/agent pattern where the model alternates between reasoning about what to do next and actually taking an action (like calling a tool), observing the result, and repeating until the task is done.

**Types:**
- **Single-step reasoning** — one thought, one action.
- **Iterative ReAct loop** — repeated think → act → observe cycles until a stopping condition is met.

**Examples:**
- The model reasons "I need more context before I can classify this ticket," calls the known-issue lookup tool, reads the result, then finalizes its classification.
- An agent checks cluster history, decides the ticket doesn't match anything existing, and creates a new cluster — all within one reasoning-action cycle.
- If the first tool call doesn't return useful info, the agent reasons again and tries a different tool before answering.

---

## 8. Autonomous Workflows
**Definition:** End-to-end pipelines that run without a human manually triggering each step — driven instead by events (like a new ticket arriving) that automatically kick off the full chain of processing.

**Types:**
- **Event-driven workflows** — triggered by a message/event (e.g., Pub/Sub).
- **Scheduled workflows** — triggered on a timer (e.g., nightly batch jobs, seen later in Week 06).

**Examples:**
- Publishing a new ticket to Pub/Sub automatically fires the entire extract → cluster → store pipeline with zero manual steps.
- Ten tickets published back-to-back are all processed and clustered correctly with none dropped.
- The model can invoke tools mid-pipeline whenever it needs more context, without a human stepping in.

---

## 📌 Bonus: Gemini Enterprise Agent Platform — what it actually is (2026)
Since this sits underneath everything GCP-related in the course, here's the plain-English version:

- **What it is:** Google's unified, enterprise-grade platform (announced April 22, 2026 at Cloud Next) for building, scaling, governing, and optimizing AI agents — effectively the successor and rebrand of Vertex AI plus Agentspace, combined.
- **Key components:** Agent Studio (low-code visual builder), ADK (code-first framework), Model Garden (200+ models — Gemini, Claude Opus/Sonnet/Haiku, Llama, Gemma), Agent Engine (managed runtime), Memory Bank (persistent structured memory), and governance features like per-agent IAM identities and cryptographic agent IDs.
- **Why it matters for this course:** it's the same governance and deployment layer this curriculum's Weeks 03–06 build toward manually (Firestore state, guardrails, Cloud Run deployment, observability) — this platform packages those patterns as managed, enterprise-ready features.
- **Multi-model, not Gemini-only:** despite the name, Claude and other third-party models are fully supported in Model Garden — it is not a walled garden restricted to Google's own models.
