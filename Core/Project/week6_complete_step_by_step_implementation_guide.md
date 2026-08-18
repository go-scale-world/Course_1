# Week 6 — Complete Step-by-Step Implementation Guide
### Enterprise Problem Consolidation (EPC) Platform — Final Week

---

## How to Use This Guide

This picks up exactly where Week 5 left off. You should have a full 4-agent pipeline, live on Cloud Run, triggered automatically via Pub/Sub, enriched with Drive/Gmail/Chat integrations. We will not repeat that setup here.

If you haven't finished Week 5, stop and complete it first.

---

## Recap: What We're Building This Week

**Product Goal:** Make the platform observable, measurable, and properly production-configured — the final layer that turns a working prototype into something a real business could trust and monitor.

**What we're building:**
1. **Evaluation harness** — 10 golden-set test cases that check whether clustering is actually working correctly, with results logged to BigQuery.
2. **Vertex AI Pipeline** — the evaluation wrapped as a real, submittable Vertex AI Pipelines job.
3. **Distributed tracing** — Cloud Trace instrumentation across the pipeline service, so you can see exactly how long each of the 4 agent steps took for any given ticket.
4. **A monitoring dashboard** — 3 live metrics: tickets processed, clusters formed, escalations created.
5. **Final production configuration** — sensible scaling limits on both Cloud Run services.

By the end of this week, the EPC Platform is complete.

---

## Step 6.1 — Enable Cloud Trace

**Exact steps:**
1. Go to `https://console.cloud.google.com`
2. Confirm the project selector shows `epc-platform`.
3. Search the top bar for: `Cloud Trace`
4. Click the result.
5. If prompted, click **Enable**. Wait for it to finish.

**How to verify success:** You land on the Trace **Trace list** page with no "Enable API" banner, currently showing "No traces found" (expected — we haven't generated any yet).

---

## Step 6.2 — Verify Cloud Monitoring Is Enabled

**Exact steps:**
1. Search the top bar for: `Monitoring`
2. Click the result named **Monitoring**.

**How to verify success:** Cloud Monitoring is enabled by default in every GCP project, so you should land directly on the Monitoring **Overview** dashboard with no enable step needed. If you do see an "Enable" button for some reason, click it and wait.

---

## Step 6.3 — Grant New IAM Roles

**Exact steps:**
1. Search `IAM`, click **IAM**.
2. Edit your service account `epc-platform-agent@...` (pencil icon ✏️).
3. Click **+ Add Another Role**, search and select: `Cloud Trace Agent`
4. Click **+ Add Another Role** again, search and select: `Monitoring Metric Writer`
5. Click **Save**.

**How to verify success:** Your service account now has 10 total roles, including these two new ones.

---

## Step 6.4 — Install New Python Libraries

**Exact steps:**
1. In VS Code's terminal, confirm `(venv)` is active and you're in the `epc-platform` root.
2. Run:
```bash
pip install google-cloud-monitoring opentelemetry-api opentelemetry-sdk opentelemetry-exporter-gcp-trace opentelemetry-instrumentation-flask kfp
```
3. This installs several packages — wait for it to finish completely (`kfp`, the Kubeflow Pipelines SDK, has many dependencies and may take a minute or two).
4. Update your dependency list:
```bash
pip freeze > requirements.txt
```

**How to verify success:** Run `pip list` and confirm `google-cloud-monitoring`, `opentelemetry-sdk`, `opentelemetry-exporter-gcp-trace`, `opentelemetry-instrumentation-flask`, and `kfp` all appear.

---

## Step 6.5 — Look Up Your Actual Cluster IDs

**Goal:** Before writing golden-set test cases, confirm which cluster ID currently corresponds to which topic in your own Firestore data — this can vary slightly between students since clustering has some natural variance.

**Exact steps:**
1. Go to the console, search `Firestore`, click it.
2. Open the `clusters` collection.
3. For each cluster document, note its `cluster_id` and read its `summary` field.
4. Write down a simple mapping, for example:
```
Photo upload issues       -> C001
Billing double charges    -> C002
Login blank screen        -> C003
Password reset delays     -> C004
Dashboard performance     -> C005
```

**How to verify success:** You have a short list mapping each of your real cluster IDs to a topic. You'll use this in the next step — if your IDs differ from the example above, that's completely normal; just use your own.

---

## Step 6.6 — Create the Golden Test Set

**Goal:** Create 10 fixed test cases — a ticket text paired with the cluster it *should* be assigned to — that we can re-run any time to check clustering quality.

**Exact steps:**
1. Right-click the `data` folder → **New File**.
2. Name it exactly: `golden_tickets.json`
3. Paste the following, **replacing each `expected_cluster` value with your own actual cluster IDs from Step 6.5**:

```json
[
  {
    "test_id": "G001",
    "input_ticket": "Photos won't upload, the app just crashes every time on Android",
    "expected_cluster": "C001"
  },
  {
    "test_id": "G002",
    "input_ticket": "My camera roll images fail to attach and the app force closes",
    "expected_cluster": "C001"
  },
  {
    "test_id": "G003",
    "input_ticket": "I was charged for my subscription two times in the same month",
    "expected_cluster": "C002"
  },
  {
    "test_id": "G004",
    "input_ticket": "Double billing again this cycle, please look into my account",
    "expected_cluster": "C002"
  },
  {
    "test_id": "G005",
    "input_ticket": "The sign-in page is completely blank when I open it in Chrome",
    "expected_cluster": "C003"
  },
  {
    "test_id": "G006",
    "input_ticket": "Login screen shows nothing at all, just a white page",
    "expected_cluster": "C003"
  },
  {
    "test_id": "G007",
    "input_ticket": "I requested a password reset email an hour ago and still nothing",
    "expected_cluster": "C004"
  },
  {
    "test_id": "G008",
    "input_ticket": "Forgot my password and the reset link email never showed up",
    "expected_cluster": "C004"
  },
  {
    "test_id": "G009",
    "input_ticket": "Dashboard takes forever to load, especially from our office overseas",
    "expected_cluster": "C005"
  },
  {
    "test_id": "G010",
    "input_ticket": "Loading the dashboard is painfully slow outside the US",
    "expected_cluster": "C005"
  }
]
```

4. Save the file.

**How to verify success:** The file contains exactly 10 objects, each with `test_id`, `input_ticket`, and `expected_cluster`, and each `expected_cluster` value matches a real cluster ID that currently exists in your Firestore `clusters` collection.

**Common beginner mistake:** Copying the example `expected_cluster` values (C001–C005) without checking they actually match your own Firestore data. If your clustering produced different groupings or different IDs, the evaluation will show false failures that aren't really bugs — just mismatched expectations.

---

## Step 6.7 — Build the Read-Only Cluster Predictor

**Goal:** Write a version of the clustering logic that only *predicts* which cluster a ticket belongs to, without changing any data — essential for testing, since we don't want evaluation runs to pollute real cluster data.

**Exact steps:**
1. Right-click the `agents` folder → **New File**.
2. Name it exactly: `predict_cluster.py`
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


def predict_cluster(raw_text: str) -> str:
    """Read-only: predicts which existing cluster a ticket would join. Never writes to Firestore."""
    new_embedding = embedding_model.get_embeddings([raw_text])[0].values

    clusters = list(db.collection("clusters").stream())
    best_cluster_id = None
    best_score = 0.0

    for cluster_doc in clusters:
        cluster = cluster_doc.to_dict()
        representative_id = cluster["ticket_ids"][0]
        rep_ticket = db.collection("tickets").document(representative_id).get().to_dict()
        rep_embedding = embedding_model.get_embeddings([rep_ticket["raw_text"]])[0].values

        similarity = cosine_similarity(new_embedding, rep_embedding)
        if similarity >= SIMILARITY_THRESHOLD and similarity > best_score:
            best_cluster_id = cluster_doc.id
            best_score = similarity

    return best_cluster_id or "NEW"


if __name__ == "__main__":
    test_text = "My photos won't upload and the app keeps crashing"
    result = predict_cluster(test_text)
    print(f"Predicted cluster: {result}")
```

4. Save the file.

**Run it:**
```bash
cd agents
python predict_cluster.py
```

**Expected output:**
```
Predicted cluster: C001
```

**How to verify success:** The predicted cluster matches whichever cluster your photo-upload tickets actually live in (per Step 6.5). Then re-check Firestore — confirm `predict_cluster.py` did **not** add any new tickets to any cluster (unlike `cluster_assignment.py` from Week 4, this version never writes anything).

---

## Step 6.8 — Create the Evaluation Results Table and Run the Evaluation

**Exact steps:**
1. Right-click the `agents` folder → **New File**.
2. Name it exactly: `evaluate_clustering.py`
3. Paste the following:

```python
import os
import json
from dotenv import load_dotenv
from google.cloud import bigquery
from predict_cluster import predict_cluster

load_dotenv()

PROJECT_ID = os.getenv("GCP_PROJECT_ID")
DATASET_ID = "epc_archive"
TABLE_ID = "evaluation_results"
GOLDEN_FILE = "../data/golden_tickets.json"

bq_client = bigquery.Client(project=PROJECT_ID)
table_ref = f"{PROJECT_ID}.{DATASET_ID}.{TABLE_ID}"

schema = [
    bigquery.SchemaField("test_id", "STRING", mode="REQUIRED"),
    bigquery.SchemaField("input_ticket", "STRING", mode="REQUIRED"),
    bigquery.SchemaField("expected_cluster", "STRING", mode="REQUIRED"),
    bigquery.SchemaField("pass_fail", "STRING", mode="REQUIRED"),
]


def create_table_if_needed():
    try:
        bq_client.get_table(table_ref)
        print(f"Table {table_ref} already exists.")
    except Exception:
        table = bigquery.Table(table_ref, schema=schema)
        bq_client.create_table(table)
        print(f"Created table {table_ref}")


def run_evaluation():
    with open(GOLDEN_FILE, "r") as f:
        golden_cases = json.load(f)

    results = []
    pass_count = 0

    for case in golden_cases:
        predicted = predict_cluster(case["input_ticket"])
        outcome = "PASS" if predicted == case["expected_cluster"] else "FAIL"
        if outcome == "PASS":
            pass_count += 1

        print(f"{case['test_id']}: expected={case['expected_cluster']} predicted={predicted} -> {outcome}")

        results.append({
            "test_id": case["test_id"],
            "input_ticket": case["input_ticket"],
            "expected_cluster": case["expected_cluster"],
            "pass_fail": outcome,
        })

    errors = bq_client.insert_rows_json(table_ref, results)
    if errors:
        print("Errors inserting results:", errors)
    else:
        print(f"\nInserted {len(results)} evaluation results into BigQuery.")

    accuracy = (pass_count / len(golden_cases)) * 100
    print(f"Overall accuracy: {pass_count}/{len(golden_cases)} ({accuracy:.0f}%)")


if __name__ == "__main__":
    create_table_if_needed()
    run_evaluation()
```

4. Save the file.

**Run it:**
```bash
python evaluate_clustering.py
```

**Expected output:**
```
G001: expected=C001 predicted=C001 -> PASS
G002: expected=C001 predicted=C001 -> PASS
G003: expected=C002 predicted=C002 -> PASS
...
G010: expected=C005 predicted=C005 -> PASS

Inserted 10 evaluation results into BigQuery.
Overall accuracy: 9/10 (90%)
```

**How to verify success:**
1. Go to BigQuery, expand `epc_archive` → `evaluation_results`, click **Preview** — confirm 10 rows exist with `test_id`, `input_ticket`, `expected_cluster`, `pass_fail`.
2. A result of 80–100% accuracy is a healthy outcome for this small test set. If you see a `FAIL`, that's not necessarily a problem with your code — it's useful, real signal. Open that specific test case's `input_ticket` and think about why the embedding model might have placed it differently than expected; this is exactly the kind of insight an evaluation harness exists to surface.

**Common beginner mistake:** Running this before double-checking your `expected_cluster` values in Step 6.6 actually match your real Firestore data — mismatched expectations will show up as false failures.

---

## Step 6.9 — Wrap the Evaluation as a Vertex AI Pipeline

**Goal:** Package the same evaluation logic as a real, submittable Vertex AI Pipelines job — the standardized way to run and track repeatable ML/AI workflows on GCP.

**Why the code looks duplicated:** Vertex AI Pipeline components run inside their own isolated container and cannot import your local project files. So the component below re-implements the prediction logic self-contained inside the function body — this is normal, expected practice for lightweight KFP components, not a mistake.

**Exact steps:**
1. Right-click the `agents` folder → **New File**.
2. Name it exactly: `evaluation_pipeline.py`
3. Paste the following:

```python
import os
import json
from dotenv import load_dotenv
from kfp import dsl, compiler
from google.cloud import aiplatform

load_dotenv()

PROJECT_ID = os.getenv("GCP_PROJECT_ID")
LOCATION = "us-central1"

# CHANGE THIS to your actual bucket name from Week 2
BUCKET_NAME = "epc-platform-123456-embeddings"
PIPELINE_ROOT = f"gs://{BUCKET_NAME}/pipeline_root"
SERVICE_ACCOUNT = f"epc-platform-agent@{PROJECT_ID}.iam.gserviceaccount.com"

GOLDEN_FILE = "../data/golden_tickets.json"


@dsl.component(
    base_image="python:3.11",
    packages_to_install=[
        "google-cloud-aiplatform",
        "google-cloud-firestore",
        "google-cloud-bigquery",
        "numpy",
    ],
)
def run_evaluation(project_id: str, golden_tickets_json: str) -> str:
    import json
    import numpy as np
    import vertexai
    from vertexai.language_models import TextEmbeddingModel
    from google.cloud import firestore, bigquery

    location = "us-central1"
    similarity_threshold = 0.75

    vertexai.init(project=project_id, location=location)
    embedding_model = TextEmbeddingModel.from_pretrained("text-embedding-004")
    db = firestore.Client(project=project_id)
    bq_client = bigquery.Client(project=project_id)

    def cosine_similarity(vec_a, vec_b):
        a = np.array(vec_a)
        b = np.array(vec_b)
        return float(np.dot(a, b) / (np.linalg.norm(a) * np.linalg.norm(b)))

    def predict_cluster(raw_text):
        new_embedding = embedding_model.get_embeddings([raw_text])[0].values
        clusters = list(db.collection("clusters").stream())
        best_cluster_id = None
        best_score = 0.0
        for cluster_doc in clusters:
            cluster = cluster_doc.to_dict()
            rep_id = cluster["ticket_ids"][0]
            rep_ticket = db.collection("tickets").document(rep_id).get().to_dict()
            rep_embedding = embedding_model.get_embeddings([rep_ticket["raw_text"]])[0].values
            similarity = cosine_similarity(new_embedding, rep_embedding)
            if similarity >= similarity_threshold and similarity > best_score:
                best_cluster_id = cluster_doc.id
                best_score = similarity
        return best_cluster_id or "NEW"

    golden_cases = json.loads(golden_tickets_json)
    table_ref = f"{project_id}.epc_archive.evaluation_results"
    results = []
    pass_count = 0

    for case in golden_cases:
        predicted = predict_cluster(case["input_ticket"])
        outcome = "PASS" if predicted == case["expected_cluster"] else "FAIL"
        if outcome == "PASS":
            pass_count += 1
        results.append({
            "test_id": case["test_id"],
            "input_ticket": case["input_ticket"],
            "expected_cluster": case["expected_cluster"],
            "pass_fail": outcome,
        })

    bq_client.insert_rows_json(table_ref, results)

    accuracy = (pass_count / len(golden_cases)) * 100
    return f"Evaluation complete: {pass_count}/{len(golden_cases)} passed ({accuracy:.0f}%)"


@dsl.pipeline(name="epc-evaluation-pipeline")
def evaluation_pipeline(project_id: str, golden_tickets_json: str):
    run_evaluation(project_id=project_id, golden_tickets_json=golden_tickets_json)


if __name__ == "__main__":
    with open(GOLDEN_FILE, "r") as f:
        golden_tickets_json = f.read()

    compiler.Compiler().compile(evaluation_pipeline, "compiled_evaluation_pipeline.yaml")
    print("Pipeline compiled to compiled_evaluation_pipeline.yaml")

    aiplatform.init(project=PROJECT_ID, location=LOCATION)

    job = aiplatform.PipelineJob(
        display_name="epc-evaluation-run",
        template_path="compiled_evaluation_pipeline.yaml",
        pipeline_root=PIPELINE_ROOT,
        parameter_values={
            "project_id": PROJECT_ID,
            "golden_tickets_json": golden_tickets_json,
        },
    )

    job.submit(service_account=SERVICE_ACCOUNT)
    print("\nPipeline submitted successfully.")
    print(f"View progress at: https://console.cloud.google.com/vertex-ai/pipelines/runs?project={PROJECT_ID}")
```

4. Replace `BUCKET_NAME` with your real bucket name from Week 2.
5. Save the file.

**Run it:**
```bash
python evaluation_pipeline.py
```

**Expected output:**
```
Pipeline compiled to compiled_evaluation_pipeline.yaml

Pipeline submitted successfully.
View progress at: https://console.cloud.google.com/vertex-ai/pipelines/runs?project=epc-platform-123456
```

**How to verify success:**
1. Open the printed link (or search `Vertex AI Pipelines` in the console).
2. Click on the run named `epc-evaluation-run` (or similar).
3. Watch the single node in the pipeline graph — it starts grey/pending, turns blue while running, and turns green once complete. This typically takes 3–6 minutes (Vertex AI needs to provision a container to run your component).
4. Once green, click the node and check its output — it should show the same `"Evaluation complete: X/10 passed (...)"` message.
5. Check BigQuery's `evaluation_results` table — it should now contain 20 rows total (10 from your direct Step 6.8 run, plus 10 more from this pipeline run).

**Common beginner mistake:** Forgetting to replace the placeholder `BUCKET_NAME`, which causes the pipeline submission to fail immediately with a "bucket not found" or permission error, since `pipeline_root` must point to a real, accessible bucket.

**Troubleshooting:** If the pipeline node turns red (failed), click it and check the **Logs** link shown in the details panel — this opens Cloud Logging filtered to that exact pipeline step, showing the Python error directly.

---

## Step 6.10 — Add Distributed Tracing to the Pipeline Service

**Goal:** Instrument `pipeline_service.py` so every HTTP request it handles is automatically recorded as a trace in Cloud Trace.

**Exact steps:**
1. Open `deployment/pipeline_service.py`.
2. Find these lines near the top:
```python
from flask import Flask, request, jsonify
from dotenv import load_dotenv
from google.cloud import firestore
```
3. Directly below them, add:
```python
from opentelemetry import trace
from opentelemetry.sdk.trace import TracerProvider
from opentelemetry.sdk.trace.export import BatchSpanProcessor
from opentelemetry.exporter.cloud_trace import CloudTraceSpanExporter
from opentelemetry.instrumentation.flask import FlaskInstrumentor
```
4. Find this line:
```python
app = Flask(__name__)
```
5. Replace it with:
```python
trace.set_tracer_provider(TracerProvider())
cloud_trace_exporter = CloudTraceSpanExporter(project_id=PROJECT_ID)
trace.get_tracer_provider().add_span_processor(BatchSpanProcessor(cloud_trace_exporter))

app = Flask(__name__)
FlaskInstrumentor().instrument_app(app)
```
6. Save the file.

**How to verify success:** No red underlines. Every incoming HTTP request to this service will now automatically generate a trace span with no further code changes needed for the 4 existing endpoints.

---

## Step 6.11 — Redeploy the Pipeline Service With Tracing

**Exact steps:**
1. Open your root `Dockerfile`.
2. Confirm the `CMD` line still reads:
```dockerfile
CMD ["python", "deployment/pipeline_service.py"]
```
(If you changed it back to the admin panel at some point, change it back to this.)
3. In the terminal, from the project root:
```bash
cd ..
gcloud run deploy epc-pipeline-service --source . --region us-central1 --service-account epc-platform-agent@epc-platform-123456.iam.gserviceaccount.com --allow-unauthenticated --set-env-vars GCP_PROJECT_ID=epc-platform-123456
```
(Replace the Project ID placeholders.)
4. Wait 3–8 minutes.

**How to verify success:** The deployment finishes with a "has been deployed" message and the same Service URL as Week 4 (redeploying updates the existing service rather than creating a new one).

---

## Step 6.12 — Generate a Traced Execution and View It

**Exact steps:**
1. Trigger one full pipeline run using the Cloud Workflow from Week 4:
```bash
gcloud workflows execute epc-pipeline-workflow --location=us-central1 --data="{\"ticket_id\": \"T024\", \"raw_text\": \"App crashes every time I attach a photo on my Samsung phone\", \"source\": \"trace_test\", \"timestamp\": \"2026-01-12T09:00:00Z\"}"
```
2. Wait about 30 seconds for the execution to complete.
3. Go to the console, search `Trace`, click **Trace list** (or **Trace Explorer**).
4. You should see new trace entries appear, each corresponding to one of the 4 HTTP calls (`/extract`, `/cluster`, `/score`, `/prepare`) made by the workflow.
5. Click on one trace — you'll see a waterfall/timeline view showing exactly how long that request took, broken down by internal spans (Flask request handling, any instrumented sub-calls).

**How to verify success:** You can see 4 separate trace entries corresponding to your test ticket's journey through the pipeline, each with a duration and a timestamp roughly matching when you ran the command.

**Common beginner mistake:** Looking for traces immediately, with zero delay. Cloud Trace's exporter batches and sends spans periodically (via `BatchSpanProcessor`), so there's typically a 10–30 second delay between a request happening and its trace becoming visible in the console.

---

## Step 6.13 — Push Custom Metrics to Cloud Monitoring

**Goal:** Write a script that reads current counts directly from Firestore and reports them to Cloud Monitoring as custom metrics.

**Exact steps:**
1. Right-click the `monitoring` folder → **New File**.
2. Name it exactly: `push_custom_metrics.py`
3. Paste the following:

```python
import os
import time
from dotenv import load_dotenv
from google.cloud import firestore, monitoring_v3

load_dotenv()

PROJECT_ID = os.getenv("GCP_PROJECT_ID")

db = firestore.Client(project=PROJECT_ID)
client = monitoring_v3.MetricServiceClient()
project_name = f"projects/{PROJECT_ID}"


def get_counts() -> tuple[int, int, int]:
    tickets_count = len(list(db.collection("tickets").stream()))
    clusters_count = len(list(db.collection("clusters").stream()))
    escalations_count = len(list(db.collection("escalations").stream()))
    return tickets_count, clusters_count, escalations_count


def write_metric(metric_name: str, value: int):
    series = monitoring_v3.TimeSeries()
    series.metric.type = f"custom.googleapis.com/epc_platform/{metric_name}"
    series.resource.type = "global"
    series.resource.labels["project_id"] = PROJECT_ID

    now = time.time()
    seconds = int(now)
    nanos = int((now - seconds) * 10**9)
    interval = monitoring_v3.TimeInterval({"end_time": {"seconds": seconds, "nanos": nanos}})
    point = monitoring_v3.Point({"interval": interval, "value": {"int64_value": value}})
    series.points = [point]

    client.create_time_series(name=project_name, time_series=[series])


if __name__ == "__main__":
    tickets, clusters, escalations = get_counts()

    write_metric("tickets_processed", tickets)
    write_metric("clusters_formed", clusters)
    write_metric("escalations_created", escalations)

    print(f"Pushed metrics -> tickets_processed={tickets}, clusters_formed={clusters}, escalations_created={escalations}")
```

4. Save the file.

**Run it:**
```bash
cd monitoring
python push_custom_metrics.py
```

**Expected output:**
```
Pushed metrics -> tickets_processed=24, clusters_formed=5, escalations_created=2
```

**How to verify success:**
1. Go to the console, search `Metrics Explorer`, click it.
2. Click **Select a metric**.
3. In the search box, type: `epc_platform`
4. You should see 3 custom metrics listed: `tickets_processed`, `clusters_formed`, `escalations_created`, all under the "Custom Metrics" / "Global" category.
5. Click `tickets_processed` — you should see a single data point plotted matching the number just printed.

**Common beginner mistake:** Looking for the metric immediately without waiting. Custom metrics can take 1–2 minutes to first appear in Metrics Explorer after the very first write — subsequent writes to the same metric show up faster.

---

## Step 6.14 — Build the Monitoring Dashboard

**Exact steps:**
1. Search `Dashboards`, click the result under Monitoring.
2. Click **+ Create Dashboard**.
3. In the title field at the top, type: `EPC Platform Overview`
4. Click **Add Widget** → **Line Chart** (or **Scorecard**, either works well for this).
5. In the widget's metric selector, type `epc_platform` in the search box, and select `tickets_processed`.
6. Give the widget a title: click the widget's settings/pencil icon and rename it to `Tickets Processed`.
7. Click **Add Widget** again, repeat for `clusters_formed`, titled `Clusters Formed`.
8. Click **Add Widget** again, repeat for `escalations_created`, titled `Escalations Created`.
9. Click **Save** at the top of the page.

**How to verify success:** Your dashboard shows 3 widgets, each displaying one metric. Since we've only pushed data points a few times, the charts will show sparse points rather than smooth lines — that's expected at this small scale. Re-running `push_custom_metrics.py` a few times (perhaps after processing a few more test tickets) will populate the charts further.

**Common beginner mistake:** Expecting a fully populated, real-time-looking dashboard immediately. This dashboard reflects however many times you've manually run `push_custom_metrics.py` — in a real production system, this script would run on a schedule (for example, via Cloud Scheduler every 5 minutes), which is a natural next step beyond this course.

---

## Step 6.15 — Apply Final Production Scaling Configuration

**Goal:** Explicitly cap both Cloud Run services at a small, course-appropriate scale, rather than leaving them at unconfigured defaults.

**Exact steps:**
1. In the terminal, from the project root, run:
```bash
gcloud run services update epc-admin-panel --region us-central1 --min-instances=0 --max-instances=2
```
2. Then run:
```bash
gcloud run services update epc-pipeline-service --region us-central1 --min-instances=0 --max-instances=2
```

**Expected output (for each command):**
```
Deploying...
...
Service [epc-admin-panel] revision [...] has been deployed and is serving 100 percent of traffic.
```

**How to verify success:**
1. Search `Cloud Run` in the console, click either service.
2. Click **Edit & Deploy New Revision** (or view the service's **Revisions** tab).
3. Under "Autoscaling," confirm **Minimum number of instances: 0** and **Maximum number of instances: 2**.

**Why these numbers:** `min-instances=0` means you pay nothing when no one is using the platform (it "cold starts" on the next request). `max-instances=2` caps costs by preventing runaway scaling — entirely appropriate for a course project handling a handful of test tickets, not real production traffic.

---

## Step 6.16 — Final End-to-End Platform Demonstration

**Goal:** Prove the complete platform works, start to finish, as one continuous flow.

**Exact steps:**
1. Open a terminal, start the Pub/Sub listener (from Week 4):
```bash
cd deployment
python pubsub_listener.py
```
2. In a **second** terminal, edit `publish_ticket.py` with one brand-new ticket ID and text, then run it:
```bash
python publish_ticket.py
```
3. Watch the listener terminal — confirm it receives the ticket and triggers the workflow.
4. Go to Firestore — confirm the new ticket appears in `tickets` with full extraction and a `cluster_id`.
5. If the ticket's cluster crosses the significance threshold, confirm a new document appears in `escalations`, with a `report_markdown`, `gmail_draft_id`, and `chat_message`.
6. Go to `https://mail.google.com` → Drafts — confirm the notification draft exists.
7. Go to your Google Chat space — confirm the alert posted.
8. Open your live `epc-admin-panel` Cloud Run URL — confirm the new cluster (if newly created) appears as "pending," and approve or reject it.
9. Run `push_custom_metrics.py` once more, and confirm the dashboard's numbers reflect this new activity.
10. Go to Cloud Trace — find the trace for this newest ticket's 4 pipeline steps.

**How to verify success:** Every single step above completes without manual intervention beyond publishing the ticket and reviewing it in the admin panel — this is the full, working Enterprise Problem Consolidation Platform, end to end.

---

## Step 6.17 — Push Final Code to GitHub

**Exact steps:**
```bash
cd ..
git add .
git commit -m "Week 6: Evaluation harness, Vertex AI Pipeline, tracing, monitoring dashboard, and final production config complete"
git push
```

**How to verify success:** Visit your GitHub repository and confirm `monitoring/push_custom_metrics.py`, `agents/predict_cluster.py`, `agents/evaluate_clustering.py`, and `agents/evaluation_pipeline.py` all appear, along with the updated `deployment/pipeline_service.py`.

---

# WEEK 6 COMPLETION CHECKLIST

- [ ] Cloud Trace is enabled, and Cloud Monitoring was confirmed active.
- [ ] Service account has 10 total IAM roles, including Cloud Trace Agent and Monitoring Metric Writer.
- [ ] `data/golden_tickets.json` exists with exactly 10 test cases, using my own real cluster IDs.
- [ ] `agents/predict_cluster.py` correctly predicts a cluster without modifying any Firestore data.
- [ ] `agents/evaluate_clustering.py` ran successfully, and BigQuery's `evaluation_results` table contains my test results with a clear pass/fail per row.
- [ ] `agents/evaluation_pipeline.py` successfully compiled and submitted a real Vertex AI Pipeline job, which completed with a green checkmark in the console.
- [ ] `deployment/pipeline_service.py` is instrumented with OpenTelemetry tracing and redeployed to Cloud Run.
- [ ] I triggered a workflow execution and confirmed 4 corresponding trace entries appear in Cloud Trace.
- [ ] `monitoring/push_custom_metrics.py` runs successfully and 3 custom metrics appear in Metrics Explorer.
- [ ] A Cloud Monitoring dashboard named "EPC Platform Overview" exists with 3 widgets, one per metric.
- [ ] Both `epc-admin-panel` and `epc-pipeline-service` show `min-instances: 0` and `max-instances: 2`.
- [ ] I completed the full end-to-end demonstration in Step 6.16 with no manual intervention beyond publishing and reviewing.
- [ ] I pushed all Week 6 code to GitHub.

---

## 🎉 Congratulations

If every box above is checked, you've built a complete, working Enterprise Problem Consolidation Platform — from a single structured-extraction script in Week 1, to a fully observable, multi-agent, cloud-deployed, enterprise-integrated AI system in Week 6.

Over six weeks, this platform learned to:
- **Understand** raw, messy tickets (Week 1)
- **Consolidate** them into real problems using retrieval and clustering (Week 2)
- **Remember** state persistently and protect itself with guardrails and human review (Week 3)
- **Judge** business impact and prepare escalations across four coordinated agents (Week 4)
- **Act** within the real enterprise, through Drive, Gmail, and Chat (Week 5)
- **Prove** its own quality and stay observable in production (Week 6)

This is the same shape — understanding, memory, judgment, coordination, integration, and evaluation — that any serious enterprise agentic AI system needs, regardless of the specific problem it's solving.
