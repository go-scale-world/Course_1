# Course Master Blueprint: Agentic AI on GCP

**Status:** DRAFT | Confirmed by Shrish: [pending / date]

<!-- ========================================== -->
<!-- ZONE 1: PRODUCT & CONTENT (Owned by Dev) -->
<!-- ========================================== -->

## 1. Product & Delivery Architecture

- **Delivery Mode:** [ Hybrid Labs]
- **Complexity Level:** [Beginner]
- **Estimated Time:** 13 weeks, 2 session each, 3 hrs for each session
- **Curriculum Structure:**

- *EPR Project Explainer:* Students will build an Enterprise Problem Consolidation Agent one layer at a time. The journey starts with understanding incoming Inputs and gradually expands to clustering, impact scoring, human approval workflows, enterprise integrations, monitoring, and cloud deployment. By Week 15, individual agentic AI capabilities come together as a complete end-to-end system that can detect emerging business problems within minutes of their first appearance.

| Wk | Skill Topic | What Will Happen This Week | What the Lab Covers | What It Adds to the EPR System |
|---|---|---|---|---|
| 1 | Introduction to Agentic AI and Models & Enterprise AI Landscape and Data Foundations | The student is introduced to what LLMs and agents are and how agentic behaviour differs from a normal API call. A raw, messy support ticket goes in and a clean structured result comes out. The student also looks at how agentic systems are already used in enterprise support and ops, and maps EPR against those patterns. Inputs stop disappearing after processing. | A Python script calls Gemini on each raw ticket and returns JSON with product area, urgency, region, and confidence. A batch pipeline pulls Inputs from cloud storage, validates against a schema, and writes to raw, results, and audit log tables. | EPR can take any ticket, short, vague, or angry, and consistently return a complete, correctly structured result. EPR also persists every ticket and result. Nothing disappears silently, and every failure can be traced to the exact ticket and stage. |
| 2 | Semantic Understanding through Embeddings & Autonomous Tool Use and Event-Driven Pipelines | The system starts recognising that two differently worded Inputs describe the same problem. The pipeline also stops needing a human to trigger it. It runs on its own as soon as a ticket arrives. | Ticket descriptions are embedded and stored in a vector index. New Inputs match to an existing cluster or start a new one. The model is given tools such as cluster history and known issue lookup that it calls on its own. Pub/Sub triggers the pipeline on every submission. | EPR groups related Inputs into shared clusters, each with an ID, description, count, and inventory. EPR is also fully event driven. No manual trigger is needed, and the model can pull extra context mid process when required. |
| 3 | Stateful Memory with Firestore & Guardrails and Enterprise Safety | Each problem group gets a status that updates on its own as new Inputs arrive. The system also learns to defend itself. Suspicious or manipulative Inputs are stopped before they can cause damage. | A Firestore document tracks ticket count, arrival rate, regions, and customer types per group. Every update is also archived. An input classifier blocks injected instructions. An output validator checks results. Every cluster waits in a human approval queue. | EPR maintains a live state per problem group. Counts and growth rates update automatically instead of needing a manual check. EPR also protects itself from malicious input, and every action is logged with a timestamp and actor. |
| 4 | Deployment and Human-in-the-Loop Interfaces | The pipeline becomes a live service, and a human can now watch and control it from a browser instead of a terminal. | The pipeline is wrapped in an API, containerised, and deployed to Cloud Run with automatic redeploy on every push. A Gradio dashboard shows clusters and supports approve or reject actions. | EPR is now a reachable live service with a working control panel. Approvals or rejections update the system immediately. |
| 5 | Multi-Agent Orchestration and Parallel Scoring | The single pipeline splits into a set of specialised agents that hand off work to each other, and can now handle many Inputs at once while prioritising the urgent ones. | The system splits into Extract, Cluster, Score, and Escalate agents, sequenced by Cloud Workflows. Inputs are ingested in parallel and scored as Low, Medium, High, or Critical based on historical growth patterns. | EPR runs as a coordinated multi-agent workflow with a full execution log, and processes Inputs at scale while flagging unusual spikes as Critical. |
| 6 | Enterprise Tool Integration via MCP & Model Fine-Tuning for Enterprise Context & Capstone and Production Readiness | EPR starts pulling context from company Drive on its own, and drafts documentation, emails, and chat messages once something is approved. The model is also adapted to the organisation's own language and terminology, and compared against the base model. The full system then runs end to end in one session, and gets proven under a defined load target. | Tool servers are deployed for Drive, Docs, Gmail, and Chat. Agent 2 queries Drive on every new cluster, and Agent 4 writes a Docs summary, Gmail draft, and Chat post on approval. Gemini is fine-tuned on 150 to 200 company Inputs and deployed as a separate endpoint, benchmarked field by field against the base model on 30 held-out Inputs. All components run live in the capstone session. Ten Inputs go in, a cluster forms, ops approves, a Chat alert fires, and a Gmail draft appears. Architecture doc, cost audit, and red team review are submitted. The system is load tested against a defined throughput target *(see Evidence line below — do not quote a specific Inputs/hour figure externally until that test has run and the result is recorded here)*. | EPR is enriched with historical context on every cluster, and produces documentation, communication drafts, and notifications automatically on approval. EPR also recognises internal product names, team references, and domain specific terms more accurately than before, and moves Inputs from submission to approved escalation in a single flow, supported by documented architecture, cost analysis, and a written up fix for the first bottleneck found under load. |

- **Evidence / Proof Source:** 

*   Curriculum architecture is derived from Google Cloud's own reference framework for agentic system design, as documented at [Choose an agentic AI architecture](https://docs.cloud.google.com/architecture/choose-agentic-ai-architecture-components), and cross-mapped against the Google Cloud Skills Boost GEAR pathway ([skills.google/paths/3273](https://www.skills.google/paths/3273)), ensuring component-level and sequencing alignment with an industry-recognised, vendor-endorsed technology stack.
*   The course is constructed using a backward design methodology, wherein terminal learning outcomes and capability milestones were determined first, with weekly modules, labs, and assessed challenges subsequently reverse-engineered to serve those outcomes.
*   Pedagogical sequencing follows a journey and storytelling framework, positioning the student as the protagonist advancing the EPR system through escalating states of competence, so that each week's build is experienced as narrative progression rather than an isolated technical exercise.

<!-- ========================================== -->
<!-- ZONE 2: MARKETING & ATTRIBUTION (Owned by Archita) -->
<!-- ========================================== -->

## 2. Commercial & GTM Strategy

- **Core Hook:** [The primary value proposition]
- **SEO Target Keywords:** [Keyword 1, Keyword 2]
- **Student Persona:** [e.g., Non-technical Managers, Data Analysts]
- **Evidence / Proof Source:** [The gap this course closes, in one sentence without the word "skills" (M2), and the data behind the hook and persona. No claim without a proof source (M6).]

<!-- ========================================== -->
<!-- ZONE 3: FINANCIALS (Owned by Aparna) -->
<!-- ========================================== -->

## 3. Financial Controls

- **Base Tier Price (INR):** [Amount]
- **Corporate Tier Price (INR):** [Amount]
- **Cloud Infrastructure Cost Cap per Student:** [Amount in USD]
- **Evidence / Proof Source:** [Cost-per-student calculation that sets the price floor (A8).]
- **Finance Sign-off:** [Aparna, date. Required before any price is announced (A4).]
