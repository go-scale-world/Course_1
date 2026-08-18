# Week 4 — Complete Step-by-Step Implementation Guide
### Enterprise Problem Consolidation (EPC) Platform

---

## How to Use This Guide

This picks up exactly where Week 3 left off. You should already have: Firestore collections `tickets` and `clusters` (5 clusters, `review_status` field), a BigQuery table `historical_incidents` (15 rows), working guardrails, and a live Cloud Run admin panel. We will not repeat that setup here.

If you haven't finished Week 3, stop and complete it first.

---

## Recap: What We're Building This Week

**Product Goal:** Add business judgment (how serious is this problem?), auto-draft a human-reviewable report, and connect all four agents into one automatic, event-triggered pipeline.

**Four things we're building:**
1. **Impact Scoring Agent** — scores each cluster 0–100 with a severity label, and double-checks its own score.
2. **Escalation Preparation Agent** — writes a short markdown report for any cluster serious enough to escalate.
3. **Orchestration** — one Cloud Run service exposing all four agent steps as web endpoints, called in sequence by a Cloud Workflows definition.
4. **Automatic triggering** — a Pub/Sub topic that, when a new ticket is published to it, automatically kicks off the whole pipeline.

**By the end of this week:** publishing a single new ticket will automatically flow through extraction → clustering → scoring → escalation prep, with zero manual steps in between.

---

## Step 4.1 — Build the Impact Scoring Agent

**Goal:** Write an agent that reads a cluster's tickets, produces an impact score (0–100) and severity label, and then double-checks its own answer before finalizing it.

**Exact steps:**
1. Open VS Code. Right-click the `agents` folder → **New File**.
2. Name it exactly: `impact_scoring_agent.py`
3. Paste the following:

```python
import os
import json
from dotenv import load_dotenv
import vertexai
from vertexai.generative_models import GenerativeModel
from google.cloud import firestore

load_dotenv()

PROJECT_ID = os.getenv("GCP_PROJECT_ID")
LOCATION = "us-central1"
MODEL_NAME = "gemini-2.0-flash-001"

SIGNIFICANCE_THRESHOLD = 60  # score at or above this is escalation-worthy

SCORING_PROMPT_TEMPLATE = """
You are a business impact analyst at an enterprise software company.

A cluster of {ticket_count} customer support tickets has been grouped together because
they describe the same underlying problem:

Cluster summary: {summary}
Individual tickets:
{ticket_list}

Score the business impact of this problem on a scale of 0-100, and classify its severity.

Respond with ONLY a valid JSON object in this exact format:
{{
  "impact_score": <integer 0-100>,
  "severity": "Low" | "Medium" | "High" | "Critical",
  "rationale": "one or two sentences explaining the score"
}}
"""

REFLECTION_PROMPT_TEMPLATE = """
You previously scored a cluster of support tickets as follows:

Impact score: {impact_score}
Severity: {severity}
Rationale: {rationale}

Ticket count in this cluster: {ticket_count}

Review this score against this rubric:
- Low (0-30): isolated, minor inconvenience, few tickets
- Medium (31-60): moderate inconvenience, noticeable pattern
- High (61-85): significant business impact, many affected users, urgent
- Critical (86-100): severe, widespread, or revenue/trust threatening

Is the severity label consistent with the numeric score under this rubric?
Respond with ONLY a valid JSON object:
{{
  "consistent": true | false,
  "corrected_severity": "Low" | "Medium" | "High" | "Critical"
}}
"""

vertexai.init(project=PROJECT_ID, location=LOCATION)
model = GenerativeModel(MODEL_NAME)
db = firestore.Client(project=PROJECT_ID)


def score_cluster(cluster: dict, ticket_texts: list[str]) -> dict:
    ticket_list = "\n".join(f"- {t}" for t in ticket_texts)
    prompt = SCORING_PROMPT_TEMPLATE.format(
        ticket_count=cluster["ticket_count"],
        summary=cluster["summary"],
        ticket_list=ticket_list,
    )

    response = model.generate_content(
        prompt,
        generation_config={"response_mime_type": "application/json"},
    )
    result = json.loads(response.text)

    # Self-reflection pass: check the score against a rubric before finalizing
    reflection_prompt = REFLECTION_PROMPT_TEMPLATE.format(
        impact_score=result["impact_score"],
        severity=result["severity"],
        rationale=result["rationale"],
        ticket_count=cluster["ticket_count"],
    )
    reflection_response = model.generate_content(
        reflection_prompt,
        generation_config={"response_mime_type": "application/json"},
    )
    reflection_result = json.loads(reflection_response.text)

    if not reflection_result["consistent"]:
        result["severity"] = reflection_result["corrected_severity"]
        result["rationale"] += " (Severity adjusted after self-review for rubric consistency.)"

    return result


def score_cluster_by_id(cluster_id: str) -> dict:
    cluster_doc = db.collection("clusters").document(cluster_id).get()
    cluster = cluster_doc.to_dict()

    ticket_texts = []
    for ticket_id in cluster["ticket_ids"]:
        ticket_doc = db.collection("tickets").document(ticket_id).get()
        ticket_texts.append(ticket_doc.to_dict()["raw_text"])

    result = score_cluster(cluster, ticket_texts)

    db.collection("clusters").document(cluster_id).update({
        "impact_score": result["impact_score"],
        "severity": result["severity"],
        "scoring_rationale": result["rationale"],
    })

    return result


if __name__ == "__main__":
    all_clusters = db.collection("clusters").stream()
    for doc in all_clusters:
        cluster_id = doc.id
        result = score_cluster_by_id(cluster_id)
        print(f"{cluster_id}: score={result['impact_score']} severity={result['severity']}")
        print(f"  rationale: {result['rationale']}\n")
```

4. Save the file.

**Run it:**
```bash
cd agents
python impact_scoring_agent.py
```

**Expected output (approximate — your exact scores will vary):**
```
C001: score=78 severity=High
  rationale: Affects 4 tickets across Android users, a widespread and recurring crash impacting core functionality.

C002: score=65 severity=High
  rationale: Duplicate billing charges directly affect customer trust and revenue accuracy.

C003: score=45 severity=Medium
  rationale: Login issues affect a moderate number of users but have workarounds via other browsers.

C004: score=30 severity=Low
  rationale: Password reset delays are inconvenient but self-resolving and affect few tickets.

C005: score=40 severity=Medium
  rationale: Performance issues are regional and moderate in scope.
```

**How to verify success:**
1. Go to the Firestore console, open the `clusters` collection.
2. Open `C001` — confirm it now has `impact_score`, `severity`, and `scoring_rationale` fields in addition to what was there before.
3. Confirm severity values only ever appear as one of exactly `Low`, `Medium`, `High`, `Critical`.

**Common beginner mistake:** Running this before Week 3's `migrate_clusters_to_firestore.py`, so the `clusters` collection is empty. If nothing prints, go back and confirm you can see 5 clusters in the Firestore console first.

---

## Step 4.2 — Build the Escalation Preparation Agent

**Goal:** Write an agent that drafts a short, human-readable escalation report for any cluster whose impact score crosses the significance threshold.

**Why we skip Vector Search here:** Redeploying Week 2's Vector Search endpoint just for this step would restart hourly billing (Week 2, Step 2.15). Since we only have 15 historical incidents, we simply pass all of them directly into the prompt as context — Gemini easily reasons over this small amount of text without needing a dedicated search index.

**Exact steps:**
1. Right-click the `agents` folder → **New File**.
2. Name it exactly: `escalation_preparation_agent.py`
3. Paste the following:

```python
import os
import json
from datetime import datetime, timezone
from dotenv import load_dotenv
import vertexai
from vertexai.generative_models import GenerativeModel
from google.cloud import firestore, bigquery

load_dotenv()

PROJECT_ID = os.getenv("GCP_PROJECT_ID")
LOCATION = "us-central1"
MODEL_NAME = "gemini-2.0-flash-001"
SIGNIFICANCE_THRESHOLD = 60

REPORT_PROMPT_TEMPLATE = """
You are preparing a business escalation report for a human reviewer.

Problem cluster summary: {summary}
Number of affected tickets: {ticket_count}
Impact score: {impact_score}/100
Severity: {severity}
Scoring rationale: {rationale}

Relevant historical incidents (for context only, may or may not be related):
{historical_context}

Write a short escalation report in Markdown, under 200 words, with these sections:
## Problem
## Business Impact
## Recommended Action

Be factual and concise. If a historical incident is genuinely relevant, reference it briefly.
"""

vertexai.init(project=PROJECT_ID, location=LOCATION)
model = GenerativeModel(MODEL_NAME)
db = firestore.Client(project=PROJECT_ID)
bq_client = bigquery.Client(project=PROJECT_ID)


def get_all_historical_incidents() -> str:
    query = "SELECT summary, outcome FROM `epc_archive.historical_incidents`"
    rows = bq_client.query(query).result()
    lines = [f"- {row.summary} (Outcome: {row.outcome})" for row in rows]
    return "\n".join(lines)


def prepare_escalation(cluster_id: str) -> dict | None:
    cluster_doc = db.collection("clusters").document(cluster_id).get()
    cluster = cluster_doc.to_dict()

    if cluster.get("impact_score", 0) < SIGNIFICANCE_THRESHOLD:
        print(f"{cluster_id}: below threshold ({cluster.get('impact_score', 0)}), skipping escalation.")
        return None

    historical_context = get_all_historical_incidents()

    prompt = REPORT_PROMPT_TEMPLATE.format(
        summary=cluster["summary"],
        ticket_count=cluster["ticket_count"],
        impact_score=cluster["impact_score"],
        severity=cluster["severity"],
        rationale=cluster.get("scoring_rationale", ""),
        historical_context=historical_context,
    )

    response = model.generate_content(prompt)
    report_markdown = response.text.strip()

    escalation_doc = {
        "cluster_id": cluster_id,
        "report_markdown": report_markdown,
        "severity": cluster["severity"],
        "impact_score": cluster["impact_score"],
        "created_at": datetime.now(timezone.utc).isoformat(),
        "review_status": "pending",
    }

    db.collection("escalations").document(cluster_id).set(escalation_doc)
    print(f"{cluster_id}: escalation report created.")
    return escalation_doc


if __name__ == "__main__":
    all_clusters = db.collection("clusters").stream()
    for doc in all_clusters:
        prepare_escalation(doc.id)
```

4. Save the file.

**Run it:**
```bash
python escalation_preparation_agent.py
```

**Expected output:**
```
C001: escalation report created.
C002: escalation report created.
C003: below threshold (45), skipping escalation.
C004: below threshold (30), skipping escalation.
C005: below threshold (40), skipping escalation.
```

(Exact numbers depend on Step 4.1's scoring output — expect **2 or 3** clusters to cross the threshold, matching the checklist's expectation.)

**How to verify success:**
1. Go to the Firestore console. A new collection called `escalations` should now exist.
2. Open one of the escalation documents (e.g., `C001`).
3. Confirm `report_markdown` contains three sections: `## Problem`, `## Business Impact`, `## Recommended Action`.
4. Roughly count the words in `report_markdown` — it should be well under 200 words.

**Common beginner mistake:** Seeing zero escalations created. This usually means every cluster scored below 60 in Step 4.1 — this can happen since Gemini's scoring has some randomness. If this happens, that's not a bug — but for testing purposes, you can temporarily lower `SIGNIFICANCE_THRESHOLD` to `40` in both this file and Step 4.1, rerun both scripts, and set it back to `60` afterward.

---

## Step 4.3 — Install Flask

**Goal:** Install the lightweight web framework we'll use to expose each agent as an HTTP endpoint.

**Exact steps:**
1. In VS Code's terminal, navigate back to the project root: `cd ..`
2. Confirm `(venv)` is active.
3. Run:
```bash
pip install flask gunicorn
```
4. Update your dependency list:
```bash
pip freeze > requirements.txt
```

**How to verify success:** `pip list` shows `Flask` and `gunicorn`.

---

## Step 4.4 — Build the Ticket Clustering Function (Reusable Version)

**Goal:** Adapt Week 2's clustering logic into a reusable function that can assign one *new* ticket to an existing Firestore cluster, or create a new one — since the pipeline processes tickets one at a time now, not as a single big batch.

**Exact steps:**
1. Right-click the `agents` folder → **New File**.
2. Name it exactly: `cluster_assignment.py`
3. Paste the following:

```python
import os
import numpy as np
from dotenv import load_dotenv
import vertexai
from vertexai.language_models import TextEmbeddingModel
from google.cloud import firestore

load_dotenv()

PROJECT_ID = os.getenv("GCP_PROJECT_ID")
LOCATION = "us-central1"
EMBEDDING_MODEL_NAME = "text-embedding-004"
SIMILARITY_THRESHOLD = 0.75

vertexai.init(project=PROJECT_ID, location=LOCATION)
embedding_model = TextEmbeddingModel.from_pretrained(EMBEDDING_MODEL_NAME)
db = firestore.Client(project=PROJECT_ID)


def cosine_similarity(vec_a, vec_b) -> float:
    a = np.array(vec_a)
    b = np.array(vec_b)
    return float(np.dot(a, b) / (np.linalg.norm(a) * np.linalg.norm(b)))


def assign_ticket_to_cluster(ticket_id: str, raw_text: str) -> str:
    new_embedding = embedding_model.get_embeddings([raw_text])[0].values

    existing_clusters = list(db.collection("clusters").stream())

    best_cluster_id = None
    best_score = 0.0

    for cluster_doc in existing_clusters:
        cluster = cluster_doc.to_dict()
        representative_ticket_id = cluster["ticket_ids"][0]
        rep_ticket = db.collection("tickets").document(representative_ticket_id).get().to_dict()
        rep_embedding = embedding_model.get_embeddings([rep_ticket["raw_text"]])[0].values

        similarity = cosine_similarity(new_embedding, rep_embedding)
        if similarity >= SIMILARITY_THRESHOLD and similarity > best_score:
            best_cluster_id = cluster_doc.id
            best_score = similarity

    if best_cluster_id:
        cluster_ref = db.collection("clusters").document(best_cluster_id)
        cluster = cluster_ref.get().to_dict()
        updated_ticket_ids = cluster["ticket_ids"] + [ticket_id]
        cluster_ref.update({
            "ticket_ids": updated_ticket_ids,
            "ticket_count": len(updated_ticket_ids),
        })
        return best_cluster_id

    existing_ids = [doc.id for doc in existing_clusters]
    next_number = len(existing_ids) + 1
    new_cluster_id = f"C{next_number:03d}"

    db.collection("clusters").document(new_cluster_id).set({
        "cluster_id": new_cluster_id,
        "ticket_count": 1,
        "ticket_ids": [ticket_id],
        "summary": raw_text[:100],
        "review_status": "pending",
    })
    return new_cluster_id
```

4. Save the file. (No test block here — we'll test it through the pipeline service in the next steps.)

**How to verify success:** No red underlines. This file has no runnable `if __name__` section — that's expected, since it's a helper module.

---

## Step 4.5 — Build the Pipeline HTTP Service

**Goal:** Wrap all four agents (extraction, clustering, scoring, escalation prep) as four HTTP endpoints on one small web service.

**Exact steps:**
1. Right-click the `deployment` folder → **New File**.
2. Name it exactly: `pipeline_service.py`
3. Paste the following:

```python
import os
import sys
from flask import Flask, request, jsonify
from dotenv import load_dotenv
from google.cloud import firestore

sys.path.append("../agents")
from meaning_extraction_agent import extract_ticket_meaning
from cluster_assignment import assign_ticket_to_cluster
from impact_scoring_agent import score_cluster_by_id
from escalation_preparation_agent import prepare_escalation

load_dotenv()

PROJECT_ID = os.getenv("GCP_PROJECT_ID")
db = firestore.Client(project=PROJECT_ID)

app = Flask(__name__)


@app.route("/extract", methods=["POST"])
def extract_endpoint():
    data = request.get_json()
    ticket_id = data["ticket_id"]
    raw_text = data["raw_text"]

    extraction = extract_ticket_meaning(raw_text)

    db.collection("tickets").document(ticket_id).set({
        "ticket_id": ticket_id,
        "raw_text": raw_text,
        "source": data.get("source", "pipeline"),
        "timestamp": data.get("timestamp", ""),
        **extraction,
    })

    return jsonify({"ticket_id": ticket_id, "extraction": extraction})


@app.route("/cluster", methods=["POST"])
def cluster_endpoint():
    data = request.get_json()
    ticket_id = data["ticket_id"]

    ticket_doc = db.collection("tickets").document(ticket_id).get()
    raw_text = ticket_doc.to_dict()["raw_text"]

    cluster_id = assign_ticket_to_cluster(ticket_id, raw_text)

    db.collection("tickets").document(ticket_id).update({"cluster_id": cluster_id})

    return jsonify({"ticket_id": ticket_id, "cluster_id": cluster_id})


@app.route("/score", methods=["POST"])
def score_endpoint():
    data = request.get_json()
    cluster_id = data["cluster_id"]

    result = score_cluster_by_id(cluster_id)

    return jsonify({"cluster_id": cluster_id, "score_result": result})


@app.route("/prepare", methods=["POST"])
def prepare_endpoint():
    data = request.get_json()
    cluster_id = data["cluster_id"]

    escalation = prepare_escalation(cluster_id)

    return jsonify({
        "cluster_id": cluster_id,
        "escalated": escalation is not None,
    })


@app.route("/", methods=["GET"])
def health_check():
    return jsonify({"status": "EPC pipeline service is running"})


if __name__ == "__main__":
    port = int(os.environ.get("PORT", 8080))
    app.run(host="0.0.0.0", port=port)
```

4. Save the file.

**Run it locally:**
```bash
cd deployment
python pipeline_service.py
```

**Expected output:**
```
 * Running on http://0.0.0.0:8080
```

**How to verify success:** Open a **second** terminal (keep this one running), activate the venv there too, and test the health check:
```bash
curl http://localhost:8080/
```
Expected response:
```json
{"status": "EPC pipeline service is running"}
```

Stop the server in the first terminal with `Ctrl + C` once confirmed.

**Common beginner mistake:** Trying to run `curl` in the same terminal window that's running the Flask server — that terminal is busy serving requests and won't accept new commands until you stop it. Always use a second terminal window for testing.

---

## Step 4.6 — Deploy the Pipeline Service to Cloud Run

**Goal:** Put the pipeline service on a live URL, reusing the same Dockerfile pattern from Week 3, pointed at this new file.

**Exact steps:**
1. Open your existing `Dockerfile` at the project root (created in Week 3).
2. Find this line:
```dockerfile
CMD ["python", "guardrails/admin_panel.py"]
```
3. Replace it with:
```dockerfile
CMD ["python", "deployment/pipeline_service.py"]
```
4. Save the file.
5. In the terminal, navigate to the project root: `cd ..`
6. Run:
```bash
gcloud run deploy epc-pipeline-service --source . --region us-central1 --service-account epc-platform-agent@epc-platform-123456.iam.gserviceaccount.com --allow-unauthenticated --set-env-vars GCP_PROJECT_ID=epc-platform-123456
```
(Replace both Project ID placeholders with your real one.)
7. Wait 3–8 minutes for the build and deploy to finish.

**Expected output:**
```
Service [epc-pipeline-service] revision [...] has been deployed and is serving 100 percent of traffic.
Service URL: https://epc-pipeline-service-abcdefgh-uc.a.run.app
```

**How to verify success:** Copy the Service URL, and in your browser visit that exact URL (no trailing path). You should see:
```json
{"status": "EPC pipeline service is running"}
```

**Write this URL down** — you'll paste it into the Workflow definition next.

**Important note:** This deploys a **second, separate** Cloud Run service from Week 3's `epc-admin-panel` — they run independently, even though they were built from the same `Dockerfile` file at two different points in time. Changing the `Dockerfile`'s `CMD` again later and redeploying `epc-admin-panel` would not affect this service, and vice versa.

---

## Step 4.7 — Enable Cloud Workflows

**Exact steps:**
1. Go to `https://console.cloud.google.com`
2. Search `Workflows`, click the result named **Workflows**.
3. If prompted, click **Enable**.
4. Wait for it to finish.

**How to verify success:** You land on a page titled "Workflows" with a button **Create**, and no "Enable API" banner remains.

---

## Step 4.8 — Grant Workflows Permission to Your Service Account

**Exact steps:**
1. Go to **IAM**, edit `epc-platform-agent@...` (pencil icon ✏️).
2. Click **+ Add Another Role**, search and select: `Workflows Invoker`
3. Click **Save**.

**How to verify success:** Your service account now shows 8 roles total: Vertex AI User, Storage Object Admin, Cloud Datastore User, BigQuery Data Editor, BigQuery Job User, Workflows Invoker (plus the 2 you'll add in Step 4.14 shortly).

---

## Step 4.9 — Write the Workflow Definition

**Goal:** Define, in a YAML file, the exact sequence of HTTP calls that make up the 4-agent pipeline.

**Exact steps:**
1. Right-click the `deployment` folder → **New File**.
2. Name it exactly: `epc_pipeline_workflow.yaml`
3. Paste the following, **replacing `PASTE_YOUR_PIPELINE_SERVICE_URL_HERE` with your real Service URL from Step 4.6**:

```yaml
main:
  params: [input]
  steps:
    - init:
        assign:
          - service_url: "PASTE_YOUR_PIPELINE_SERVICE_URL_HERE"
          - ticket_id: ${input.ticket_id}
          - raw_text: ${input.raw_text}
          - source: ${input.source}
          - timestamp: ${input.timestamp}

    - extract_step:
        call: http.post
        args:
          url: ${service_url + "/extract"}
          body:
            ticket_id: ${ticket_id}
            raw_text: ${raw_text}
            source: ${source}
            timestamp: ${timestamp}
        result: extract_result

    - cluster_step:
        call: http.post
        args:
          url: ${service_url + "/cluster"}
          body:
            ticket_id: ${ticket_id}
        result: cluster_result

    - assign_cluster_id:
        assign:
          - cluster_id: ${cluster_result.body.cluster_id}

    - score_step:
        call: http.post
        args:
          url: ${service_url + "/score"}
          body:
            cluster_id: ${cluster_id}
        result: score_result

    - prepare_step:
        call: http.post
        args:
          url: ${service_url + "/prepare"}
          body:
            cluster_id: ${cluster_id}
        result: prepare_result

    - return_result:
        return:
          ticket_id: ${ticket_id}
          cluster_id: ${cluster_id}
          escalated: ${prepare_result.body.escalated}
```

4. Save the file.

**How to verify success:** No red underlines. Indentation must use spaces only (never tabs) — YAML is strict about this.

**Common beginner mistake:** Leaving the placeholder URL unchanged. If you forget this, every workflow execution will fail immediately with a connection error.

---

## Step 4.10 — Deploy the Workflow

**Exact steps:**
1. In the terminal, from the project root, run:
```bash
gcloud workflows deploy epc-pipeline-workflow --source=deployment/epc_pipeline_workflow.yaml --location=us-central1 --service-account=epc-platform-agent@epc-platform-123456.iam.gserviceaccount.com
```
(Replace the Project ID placeholder.)
2. Wait about 15–30 seconds.

**Expected output:**
```
Deploying workflow [epc-pipeline-workflow] to location [us-central1]...done.
```

**How to verify success:** Go to the console, search `Workflows`, click it — `epc-pipeline-workflow` should appear in the list.

---

## Step 4.11 — Execute the Workflow Manually (End-to-End Test, One Ticket)

**Goal:** Manually trigger one full pipeline run with a brand-new ticket, and confirm all four steps execute correctly.

**Exact steps:**
1. In the terminal, run:
```bash
gcloud workflows execute epc-pipeline-workflow --location=us-central1 --data="{\"ticket_id\": \"T021\", \"raw_text\": \"My Android app crashes every time I try to upload a picture\", \"source\": \"manual_test\", \"timestamp\": \"2026-01-10T09:00:00Z\"}"
```
(On Mac/Linux, you may need to adjust the quote escaping slightly — if you get a parsing error, try wrapping the whole `--data` value in single quotes instead, with double quotes inside.)
2. The terminal will print an execution name and a note that it's running.

**Check the result:**
```bash
gcloud workflows executions describe EXECUTION_ID --workflow=epc-pipeline-workflow --location=us-central1
```
(Replace `EXECUTION_ID` with the ID printed from the previous command — or simpler: just wait 15 seconds and check the console instead, as shown below.)

**Easier verification via console:**
1. Go to the console, search `Workflows`, click **epc-pipeline-workflow**.
2. Click the **Executions** tab.
3. Click the most recent execution.
4. Confirm the status shows a green checkmark (**Succeeded**), and the output at the bottom shows something like:
```json
{
  "ticket_id": "T021",
  "cluster_id": "C001",
  "escalated": true
}
```

**How to verify success in Firestore:**
1. Open the `tickets` collection — confirm a new document `T021` exists with extracted fields and a `cluster_id`.
2. Open the `clusters` collection — confirm ticket `T021` was added to the correct cluster's `ticket_ids` list (since this ticket is another Android photo-upload crash, it should join `C001`).

**Common beginner mistake:** A failed execution almost always means the `service_url` in your YAML file doesn't exactly match your real Cloud Run URL (missing `https://`, trailing slash, or a typo). Open the failed execution in the console — it shows exactly which step failed and the error message returned.

---

## Step 4.12 — Enable Pub/Sub

**Exact steps:**
1. Search `Pub/Sub`, click the result named **Pub/Sub**.
2. If prompted, click **Enable**.

**How to verify success:** You land on the Pub/Sub dashboard with tabs for "Topics" and "Subscriptions," no enable banner remaining.

---

## Step 4.13 — Create a Topic and Subscription

**Exact steps:**
1. On the Pub/Sub page, click **Topics** in the left sidebar.
2. Click **+ Create Topic**.
3. In **Topic ID**, type exactly: `new-ticket-topic`
4. Leave "Add a default subscription" **checked**.
5. Click **Create**.

**How to verify success:** You're taken to the topic's details page. Click **Subscriptions** in the left sidebar — you should see an auto-created subscription, something like `new-ticket-topic-sub`. Click on it, and rename it for clarity if you like, or just note its exact ID (write it down).

---

## Step 4.14 — Grant Pub/Sub Permissions

**Exact steps:**
1. Go to **IAM**, edit `epc-platform-agent@...`.
2. Add role: `Pub/Sub Publisher`
3. Add role: `Pub/Sub Subscriber`
4. Click **Save**.

**How to verify success:** Your service account now has 8 total roles including these two new ones.

---

## Step 4.15 — Install the Pub/Sub Client and Write the Publisher Script

**Exact steps:**
1. In the terminal, from the project root:
```bash
pip install google-cloud-pubsub
pip freeze > requirements.txt
```
2. Right-click the `deployment` folder → **New File**.
3. Name it exactly: `publish_ticket.py`
4. Paste the following:

```python
import os
import json
import sys
from dotenv import load_dotenv
from google.cloud import pubsub_v1

load_dotenv()

PROJECT_ID = os.getenv("GCP_PROJECT_ID")
TOPIC_ID = "new-ticket-topic"

publisher = pubsub_v1.PublisherClient()
topic_path = publisher.topic_path(PROJECT_ID, TOPIC_ID)


def publish_ticket(ticket_id: str, raw_text: str, source: str = "pubsub_test"):
    message = {
        "ticket_id": ticket_id,
        "raw_text": raw_text,
        "source": source,
        "timestamp": "2026-01-11T10:00:00Z",
    }
    data = json.dumps(message).encode("utf-8")
    future = publisher.publish(topic_path, data)
    message_id = future.result()
    print(f"Published ticket {ticket_id} (message ID: {message_id})")


if __name__ == "__main__":
    publish_ticket("T022", "Dashboard export to PDF is completely broken again for me")
    publish_ticket("T023", "I keep getting billed two times a month, please fix this")
```

5. Save the file.

**Run it:**
```bash
cd deployment
python publish_ticket.py
```

**Expected output:**
```
Published ticket T022 (message ID: 1234567890123456)
Published ticket T023 (message ID: 1234567890123457)
```

**How to verify success:** Go to the console → Pub/Sub → your topic → click the **Messages** tab (or check the subscription's "Messages" metric) — you should see 2 unacknowledged messages waiting.

---

## Step 4.16 — Write the Listener Script

**Goal:** Write a script that continuously watches the subscription and, for every new ticket message, automatically triggers the Cloud Workflow.

**Exact steps:**
1. Right-click the `deployment` folder → **New File**.
2. Name it exactly: `pubsub_listener.py`
3. Paste the following:

```python
import os
import json
from dotenv import load_dotenv
from google.cloud import pubsub_v1
from google.cloud.workflows.executions_v1 import ExecutionsClient
from google.cloud.workflows.executions_v1.types import Execution

load_dotenv()

PROJECT_ID = os.getenv("GCP_PROJECT_ID")
SUBSCRIPTION_ID = "new-ticket-topic-sub"  # confirm this matches your actual subscription ID from Step 4.13
LOCATION = "us-central1"
WORKFLOW_ID = "epc-pipeline-workflow"

subscriber = pubsub_v1.SubscriberClient()
subscription_path = subscriber.subscription_path(PROJECT_ID, SUBSCRIPTION_ID)

workflows_client = ExecutionsClient()
workflow_parent = f"projects/{PROJECT_ID}/locations/{LOCATION}/workflows/{WORKFLOW_ID}"


def trigger_workflow(ticket_data: dict):
    execution = Execution(argument=json.dumps(ticket_data))
    response = workflows_client.create_execution(parent=workflow_parent, execution=execution)
    print(f"Triggered workflow execution: {response.name}")


def callback(message):
    ticket_data = json.loads(message.data.decode("utf-8"))
    print(f"Received ticket: {ticket_data['ticket_id']}")
    trigger_workflow(ticket_data)
    message.ack()


if __name__ == "__main__":
    print(f"Listening for new tickets on {subscription_path}...")
    print("Press Ctrl+C to stop.\n")

    streaming_pull_future = subscriber.subscribe(subscription_path, callback=callback)

    try:
        streaming_pull_future.result()
    except KeyboardInterrupt:
        streaming_pull_future.cancel()
        print("\nListener stopped.")
```

4. **Before saving**, double-check `SUBSCRIPTION_ID` matches the exact subscription ID you noted in Step 4.13.
5. Save the file.

**How to verify success:** No red underlines. We'll run and test it in the next step.

---

## Step 4.17 — Full End-to-End Test

**Goal:** Prove the entire pipeline runs automatically — from publishing a ticket, to the listener picking it up, to the workflow executing all four agent steps, with zero manual intervention in between.

**Exact steps:**
1. Open a terminal, activate `(venv)`, navigate to the `deployment` folder.
2. Start the listener:
```bash
python pubsub_listener.py
```
3. You should see:
```
Listening for new tickets on projects/epc-platform-123456/subscriptions/new-ticket-topic-sub...
Press Ctrl+C to stop.
```
4. **Leave this terminal running.** Open a **second** terminal window, activate `(venv)` there too, navigate to `deployment`.
5. Run the publisher again (or edit `publish_ticket.py` with a new ticket first, to avoid processing the same IDs twice):
```bash
python publish_ticket.py
```
6. Watch the **first terminal** (the listener) — within a few seconds, you should see:
```
Received ticket: T022
Triggered workflow execution: projects/epc-platform-123456/locations/us-central1/workflows/epc-pipeline-workflow/executions/abc123...
Received ticket: T023
Triggered workflow execution: projects/epc-platform-123456/locations/us-central1/workflows/epc-pipeline-workflow/executions/def456...
```

**How to verify success:**
1. Go to the console → Workflows → `epc-pipeline-workflow` → **Executions** tab. You should see two new executions with "Succeeded" status, roughly matching the time you just published.
2. Go to Firestore → `tickets` collection — confirm `T022` and `T023` now exist with full extraction data and `cluster_id` fields.
3. Go to Firestore → `escalations` collection — if either ticket's cluster crossed the significance threshold, a new or updated escalation document should reflect it.
4. Press `Ctrl + C` in the listener terminal to stop it once you're done testing.

**Common beginner mistake:** Running the publisher script twice with the exact same hardcoded ticket IDs (`T022`, `T023`) — Firestore will simply overwrite the same documents rather than erroring, which can be confusing when checking results. For repeat testing, change the ticket IDs and text in `publish_ticket.py` each time.

**Troubleshooting:** If the listener never prints "Received ticket," confirm `SUBSCRIPTION_ID` in `pubsub_listener.py` exactly matches what's shown in the console under Pub/Sub → Subscriptions.

---

## Step 4.18 — Push Your Work to GitHub

**Exact steps:**
```bash
cd ..
git add .
git commit -m "Week 4: Impact Scoring, Escalation Prep, Cloud Workflows orchestration, and Pub/Sub triggering complete"
git push
```

**How to verify success:** Confirm `agents/impact_scoring_agent.py`, `agents/escalation_preparation_agent.py`, `agents/cluster_assignment.py`, and everything in `deployment/` appear on GitHub.

---

# WEEK 4 COMPLETION CHECKLIST

- [ ] `agents/impact_scoring_agent.py` runs successfully and writes `impact_score`, `severity`, and `scoring_rationale` to every cluster in Firestore.
- [ ] `agents/escalation_preparation_agent.py` runs successfully and creates escalation documents (2–3 of them) only for clusters scoring at or above 60.
- [ ] Each escalation report in Firestore contains `## Problem`, `## Business Impact`, and `## Recommended Action` sections, under 200 words.
- [ ] `epc-pipeline-service` is deployed to Cloud Run and its health check (`GET /`) responds successfully.
- [ ] `epc-pipeline-workflow` is deployed and visible under Workflows in the console.
- [ ] I manually executed the workflow once with a test ticket and confirmed a "Succeeded" execution with correct output.
- [ ] Pub/Sub topic `new-ticket-topic` and its subscription both exist.
- [ ] Service account has Pub/Sub Publisher, Pub/Sub Subscriber, and Workflows Invoker roles.
- [ ] Running `pubsub_listener.py` in one terminal and `publish_ticket.py` in another successfully triggers automatic, end-to-end workflow executions with no manual steps.
- [ ] New tickets published this way appear correctly in Firestore's `tickets` collection with extraction data and a `cluster_id`.
- [ ] I pushed all Week 4 code to GitHub.

Once every box is checked, Week 4 is complete. Let me know when you're ready for Week 5.
