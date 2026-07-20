# Week 5 — Lab Notes
### Enterprise Problem Consolidation (EPC) Platform

**What we built this week:** Real tool integrations (Drive, Gmail, Chat) exposed through a standardized MCP server, plus an upgraded hybrid RAG pipeline that gives our escalation agent much more precise historical context.

---

**Step 5.1 — Enable the Google Drive API**
We turned on the Google Drive API, so our code can be authorized to read documents from Drive.

**Step 5.2 — Enable the Gmail API**
We turned on the Gmail API, so our code can be authorized to create draft emails.

**Step 5.3 — Configure the OAuth Consent Screen**
We told Google what our app is called and which permissions it will request, since Drive and Gmail require every app to declare itself before anyone can grant it access.

**Step 5.4 — Create an OAuth Client ID**
We created the OAuth credentials our code needs to request permission to access Drive and Gmail on our behalf.

**Step 5.5 — Install OAuth and Google API Libraries**
We installed the Python libraries needed to handle Google's authorization flow and call the Drive/Gmail APIs.

**Step 5.6 — Create 3 Reference Documents in Drive**
We created a small, fixed set of enterprise reference documents that our Escalation Preparation Agent will later pull context from.

**Step 5.7 — Run the One-Time Authorization Script**
We ran a script that grants our code read-only access to Drive and draft-only access to Gmail — a one-time step whose approval is saved locally for reuse.

**Step 5.8 — Build and Test the Drive Tool**
We built and tested a tool that lets our agent search and read documents from Google Drive.

**Step 5.9 — Build and Test the Gmail Draft Tool**
We built and tested a tool that lets our agent create Gmail drafts (never send emails directly) as part of an escalation.

**Step 5.10 — Set Up a Google Chat Space and Webhook**
We created a Chat Space and generated a webhook URL our code can post alert messages to, with no OAuth required for this part.

**Step 5.11 — Build and Test the Chat Alert Tool**
We built and tested a tool that posts an alert message directly into our Chat Space.

**Step 5.12 — Install the MCP SDK and Build the MCP Server**
We exposed our three tools (Drive, Gmail, Chat) as a real MCP server — the standardized way AI systems discover and call external tools.

**Step 5.13 — Test the MCP Server With a Python Client**
We wrote a small client that connects to our MCP server the same way a real MCP-compatible AI assistant would, listing and calling its tools.

**Step 5.14 — Build Hybrid RAG With Re-Ranking**
We upgraded Week 2's simple semantic search into a hybrid pipeline — combining semantic and keyword search, then re-ranking with Gemini for genuinely relevant results, without needing to redeploy the costly Vector Search endpoint.

**Step 5.15 — Upgrade the Escalation Preparation Agent**
We wired the Drive, Gmail, and Chat tools plus the new hybrid RAG retrieval into the Escalation Preparation Agent, replacing last week's shortcut of passing in all 15 incidents at once.

**Step 5.16 — Run the Upgraded Agent End-to-End**
We ran the fully upgraded agent from start to finish and confirmed it correctly pulled context, drafted communications, and posted alerts.

**Step 5.17 — Push Work to GitHub**
We committed and pushed all our Week 5 code to GitHub, keeping our version history current.

---

## ✅ Week 5 Outcome
By the end of the week, our escalation agent could enrich every cluster with real historical context, automatically draft documentation and communications, and call any of its tools through a standardized MCP interface.
