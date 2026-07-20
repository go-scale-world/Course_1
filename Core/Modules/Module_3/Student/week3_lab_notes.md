# Week 3 — Lab Notes
### Enterprise Problem Consolidation (EPC) Platform

**What we built this week:** Real persistent storage for our data, safety guardrails around the AI, a human review dashboard, and our first live cloud deployment.

---

**Step 3.1 — Enable Firestore and Create a Database**
We turned on Firestore and created our actual database instance, since Firestore requires an explicit, named database before it can be used.

**Step 3.2 — Grant Firestore Access**
We gave our service account permission to read and write data in Firestore.

**Step 3.3 — Enable BigQuery and Create a Dataset**
We turned on BigQuery and created a named dataset to hold our historical incident table for long-term, queryable storage.

**Step 3.4 — Grant BigQuery Access**
We gave our service account the two roles it needs: one to create tables and insert data, and one to actually run queries.

**Step 3.5 — Install New Python Libraries**
We installed the additional libraries this week's code needs to talk to Firestore and BigQuery.

**Step 3.6 — Migrate Tickets Into Firestore**
We loaded our 20 sample tickets into a real Firestore collection, tagging each one with its cluster ID from Week 2.

**Step 3.7 — Generate Cluster Summaries and Migrate Clusters**
We used Gemini to write a one-sentence summary for each cluster, then saved each cluster as its own Firestore document, including a review-status field for later use.

**Step 3.8 — Create and Populate the BigQuery Historical Archive**
We loaded our 15 historical incidents into a real, queryable BigQuery table, giving us a proper long-term archive.

**Step 3.9 — Build the Input Classifier**
We built a guardrail that inspects every incoming ticket and flags anything that looks like a prompt-injection attempt or unsafe input, before it ever reaches the extraction agent.

**Step 3.10 — Build the Output Validator**
We built a second guardrail that double-checks an agent's output — both its structure (schema) and whether it plausibly makes sense.

**Step 3.11 — Build the Gradio Admin Panel**
We built a simple web page where a human reviewer can see each pending cluster and approve or reject it with a single click.

**Step 3.12 — Write the Dockerfile**
We wrote a Dockerfile — a recipe describing exactly how to package our admin panel into a container image that Cloud Run can run.

**Step 3.13 — Create a .dockerignore File**
We created a `.dockerignore` file to make sure our secret key file and local settings never get copied into the container image.

**Step 3.14 — Deploy to Cloud Run**
We built our container in the cloud and deployed it as a live, public web service, without needing Docker installed on our own computer.

**Step 3.15 — Push Work to GitHub**
We committed and pushed all our Week 3 code to GitHub, keeping our version history current.

---

## ✅ Week 3 Outcome
By the end of the week, our data persisted properly in real databases, malicious or broken input/output was caught automatically, and a human reviewer could approve clusters through a live, deployed web dashboard.
