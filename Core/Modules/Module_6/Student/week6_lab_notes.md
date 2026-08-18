# Week 6 — Lab Notes
### Enterprise Problem Consolidation (EPC) Platform

**What we built this week:** Full observability into our system, a repeatable evaluation pipeline to measure quality, and the final production-ready capstone demonstration.

---

**Step 6.1 — Enable Cloud Trace**
We turned on Cloud Trace, the service that will let us follow a single request's full path through every part of our system.

**Step 6.2 — Verify Cloud Monitoring Is Enabled**
We confirmed Cloud Monitoring was active for our project, since this is what our dashboards and alerts will be built on.

**Step 6.3 — Grant New IAM Roles**
We gave our service account the additional permissions it needs to write traces and metrics.

**Step 6.4 — Install New Python Libraries**
We installed the additional libraries needed for this week's evaluation and monitoring code.

**Step 6.5 — Look Up Actual Cluster IDs**
We checked which cluster ID currently corresponds to which topic in our own Firestore data, since clustering results can vary slightly between students.

**Step 6.6 — Create the Golden Test Set**
We created 10 fixed test cases — a ticket paired with the cluster it should be assigned to — that we can re-run at any time to check clustering quality.

**Step 6.7 — Build the Read-Only Cluster Predictor**
We wrote a version of the clustering logic that only predicts a ticket's cluster without changing any real data, so evaluation runs never pollute our live database.

**Step 6.8 — Create the Evaluation Results Table and Run the Evaluation**
We ran our golden test set through the predictor and saved the results to a table, giving us a measurable accuracy score for our clustering.

**Step 6.9 — Wrap the Evaluation as a Vertex AI Pipeline**
We packaged this same evaluation logic as a proper, submittable Vertex AI Pipelines job — the standard way to run and track repeatable AI workflows on GCP.

**Step 6.10 — Add Distributed Tracing to the Pipeline Service**
We instrumented our pipeline service so every request it handles is automatically recorded as a trace in Cloud Trace.

**Step 6.11 — Redeploy the Pipeline Service With Tracing**
We redeployed our pipeline service with tracing enabled, so this instrumentation is active in the live version.

**Step 6.12 — Generate a Traced Execution and View It**
We triggered a real request and viewed its full trace in the console, confirming our tracing setup actually works.

**Step 6.13 — Push Custom Metrics to Cloud Monitoring**
We wrote a script that reads live counts from Firestore and reports them to Cloud Monitoring as custom metrics.

**Step 6.14 — Build the Monitoring Dashboard**
We built a dashboard in Cloud Monitoring to visualize our system's health and throughput at a glance.

**Step 6.15 — Apply Final Production Scaling Configuration**
We explicitly capped both Cloud Run services at a small, course-appropriate scale, keeping costs predictable instead of leaving defaults unconfigured.

**Step 6.16 — Final End-to-End Platform Demonstration**
We proved the complete platform works, start to finish, as one continuous flow — from ticket submission through to approved escalation.

**Step 6.17 — Push Final Code to GitHub**
We committed and pushed all our final Week 6 code to GitHub, completing our version history for the whole course.

---

## ✅ Week 6 Outcome
By the end of the week, our platform could monitor and trace itself, measure its own clustering accuracy through repeatable evaluation, and run the complete six-week pipeline live in one unbroken, production-scaled demonstration.
