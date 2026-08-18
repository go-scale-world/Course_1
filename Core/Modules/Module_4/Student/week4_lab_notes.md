# Week 4 — Lab Notes
### Enterprise Problem Consolidation (EPC) Platform

**What we built this week:** A true multi-agent pipeline — scoring and escalation agents, wired together with an orchestrator — plus event-driven automation so the whole thing runs itself the moment a new ticket arrives.

---

**Step 4.1 — Build the Impact Scoring Agent**
We wrote an agent that reads a cluster's tickets, produces an impact score (0–100) with a severity label, and double-checks its own answer before finalizing it.

**Step 4.2 — Build the Escalation Preparation Agent**
We wrote an agent that drafts a short, human-readable escalation report for any cluster whose impact score crosses our significance threshold.

**Step 4.3 — Install Flask**
We installed Flask, the lightweight web framework we'll use to expose each agent as its own HTTP endpoint.

**Step 4.4 — Build the Reusable Ticket Clustering Function**
We adapted Week 2's clustering logic into a reusable function that can assign one new ticket to an existing cluster, or start a new one, since the pipeline now handles tickets one at a time.

**Step 4.5 — Build the Pipeline HTTP Service**
We wrapped all four agents — extraction, clustering, scoring, and escalation prep — as four HTTP endpoints on one small web service.

**Step 4.6 — Deploy the Pipeline Service to Cloud Run**
We deployed this new pipeline service to a live URL, reusing the same Dockerfile pattern from Week 3.

**Step 4.7 — Enable Cloud Workflows**
We turned on Cloud Workflows, the service that will sequence our four agents together in the correct order.

**Step 4.8 — Grant Workflows Permission**
We gave our service account the permission Cloud Workflows needs to call our pipeline's endpoints.

**Step 4.9 — Write the Workflow Definition**
We defined, in a YAML file, the exact sequence of HTTP calls that make up our four-agent pipeline.

**Step 4.10 — Deploy the Workflow**
We deployed that workflow definition to Cloud Workflows, making it a live, callable orchestration.

**Step 4.11 — Execute the Workflow Manually**
We manually triggered one full pipeline run with a brand-new ticket and confirmed all four steps executed correctly, end to end.

**Step 4.12 — Enable Pub/Sub**
We turned on Pub/Sub, the messaging service that will let new tickets automatically trigger our pipeline.

**Step 4.13 — Create a Topic and Subscription**
We created a Pub/Sub topic (where tickets get published) and a subscription (which listens for them).

**Step 4.14 — Grant Pub/Sub Permissions**
We gave our service account the permissions it needs to publish and receive messages through Pub/Sub.

**Step 4.15 — Write the Publisher Script**
We installed the Pub/Sub client and wrote a script that publishes a new ticket as a message to our topic.

**Step 4.16 — Write the Listener Script**
We wrote a script that continuously watches our subscription and automatically triggers the Cloud Workflow the moment a new ticket message arrives.

**Step 4.17 — Full End-to-End Test**
We proved the entire pipeline runs automatically — from publishing a ticket, to the listener picking it up, to all four agent steps executing — with zero manual steps in between.

**Step 4.18 — Push Work to GitHub**
We committed and pushed all our Week 4 code to GitHub, keeping our version history current.

---

## ✅ Week 4 Outcome
By the end of the week, our pipeline ran as a fully event-driven, multi-agent system: publishing a ticket alone was enough to trigger extraction, clustering, scoring, and escalation prep automatically.
