# Week 2 — Complete Step-by-Step Implementation Guide
### Enterprise Problem Consolidation (EPC) Platform

---

## How to Use This Guide

This picks up exactly where Week 1 left off. You should already have: a GCP project (`epc-platform`), a service account with the **Vertex AI User** role, Python + VS Code + Git set up, and a working Meaning Extraction Agent. We will **not** repeat any of that setup here.

If you have not completed Week 1 yet, stop and complete that guide first — this week's code directly depends on files created in Week 1 (`schema.py`, `.env`, `service-account-key.json`).

---

## Recap: What We're Building This Week

**Product Goal:** Teach the platform to recognize when different tickets — written in completely different words — are actually describing the *same* underlying problem, and to pull up similar historical incidents for context.

**Two separate capabilities we're building:**
1. **RAG Retrieval Pipeline** — given a ticket, find the most similar *past, resolved* incidents from history (uses Vertex AI Vector Search).
2. **Ticket Clustering Agent** — given today's batch of live tickets, group the ones describing the same problem together (uses direct similarity comparison + a keyword fallback).

**By the end of this week**, feeding in 20 sample tickets should produce roughly 4–6 clusters, and querying any ticket should surface relevant historical incidents.

---

## ⚠️ Important Cost Warning — Read This First

This week uses **Vertex AI Vector Search**, a real, always-on cloud service. Once we **deploy** an index to a live endpoint, Google charges an hourly fee for as long as it stays deployed — even if you're not actively using it.

To keep this course-friendly:
- We will deploy the index only long enough to test it.
- **Step 2.15 near the end of this guide shows you exactly how to shut it down (undeploy).**
- Do not skip Step 2.15. Set a reminder on your phone right now if you tend to forget things — an undeployed-and-forgotten index can quietly rack up charges over days.

You can check current pricing any time at `https://cloud.google.com/vertex-ai/pricing` under "Vector Search."

---

## Step 2.1 — Verify Vertex AI Vector Search Is Available

**Goal:** Confirm the Vector Search feature is accessible under your existing Vertex AI API (enabled in Week 1).

**Why:** Vector Search is part of the same Vertex AI API you already turned on — there's no separate "enable" button, but we should confirm you can see the interface before writing code against it.

**Exact steps:**
1. Go to `https://console.cloud.google.com`
2. Confirm the project selector at the top shows `epc-platform`.
3. Click the search bar, type: `Vector Search`
4. Click the result named **Vector Search** (under Vertex AI).
5. You should land on a page titled **Vector Search** with tabs like "Indexes" and "Index Endpoints", both currently empty.

**How to verify success:** The page loads without any "API not enabled" banner. If you see an "Enable API" button anywhere on this page, click it and wait for it to finish (same process as Step 1.1 in Week 1).

**Common beginner mistake:** Confusing this with "Vertex AI Search" (a different, packaged product for building search apps). We specifically want **Vector Search**, found under the Vertex AI section.

---

## Step 2.2 — Create a Cloud Storage Bucket

**Goal:** Create a storage bucket — a container for files in the cloud — where we'll temporarily place the embedding data before Vector Search reads it.

**Why:** Vertex AI Vector Search builds its index by reading embedding files from Cloud Storage, not by receiving them directly from your script.

**Exact steps:**
1. Click the search bar at the top of the console, type: `Cloud Storage`
2. Click the result named **Cloud Storage** (it may also say "Buckets" underneath).
3. Click the blue button labeled **+ Create** (or **Create Bucket**).
4. In the field **Name your bucket**, type a name using your actual Project ID from Week 1, like this:
```
epc-platform-123456-embeddings
```
(Replace `123456` with your real project ID suffix — bucket names must be globally unique across all of Google Cloud, so using your project ID avoids collisions.)
5. Click **Continue**.
6. Under **"Choose where to store your data"**, select **Region**, and from the dropdown choose `us-central1 (Iowa)` — this must match the region we'll use for Vector Search later.
7. Click **Continue**.
8. Leave **Storage class** as the default (**Standard**). Click **Continue**.
9. Leave **Access control** as the default (**Uniform**). Click **Continue**.
10. Leave **Data protection** settings as default. Click **Create**.

**Expected output:** You're taken to your new bucket's (empty) file browser page.

**How to verify success:** The page title shows your bucket name, and the file list shows "This bucket has no objects."

**Common beginner mistake:** Choosing a region other than `us-central1`. If your bucket and your Vector Search index are in different regions, index creation will fail later with a location mismatch error.

**Write this down:** Your bucket name (e.g., `epc-platform-123456-embeddings`) — you'll need it in the code below.

---

## Step 2.3 — Grant Storage Permissions to Your Service Account

**Goal:** Allow your Week 1 service account to write files into this new bucket.

**Exact steps:**
1. Click the search bar, type: `IAM`
2. Click the result named **IAM**.
3. Find the row for `epc-platform-agent@epc-platform-123456.iam.gserviceaccount.com`.
4. Click the pencil/edit icon (✏️) on that row.
5. Click **+ Add Another Role**.
6. In the search box, type: `Storage Object Admin`
7. Click the result labeled **Storage Object Admin**.
8. Click **Save**.

**How to verify success:** Refresh the IAM page. Your service account's row should now list two roles: **Vertex AI User** and **Storage Object Admin**.

**Common beginner mistake:** Selecting "Storage Admin" (which manages entire buckets, including deleting them) instead of "Storage Object Admin" (which only manages files inside buckets). We want the smaller, safer permission.

---

## Step 2.4 — Install New Python Libraries

**Goal:** Install the additional libraries this week's code needs.

**Exact steps:**
1. Open VS Code, open the terminal (**Terminal** → **New Terminal**).
2. Confirm `(venv)` appears at the start of the line. If not, reactivate it:
   - Windows: `venv\Scripts\activate`
   - Mac: `source venv/bin/activate`
3. Confirm you're in the `epc-platform` root folder (not inside `agents`). If needed: `cd ..`
4. Run:
```bash
pip install google-cloud-storage rank_bm25 numpy
```
5. Wait for it to finish installing.
6. Update your dependency list:
```bash
pip freeze > requirements.txt
```

**How to verify success:** Run `pip list` and confirm `google-cloud-storage`, `rank-bm25`, and `numpy` are listed.

---

## Step 2.5 — Expand sample_tickets.json to 20 Tickets

**Goal:** Add 10 new tickets to last week's file, bringing the total to 20 — enough variety to form multiple believable clusters.

**Exact steps:**
1. In VS Code's sidebar, open `data` → `sample_tickets.json`.
2. Click right after the closing `}` of the last ticket object (`T010`), just before the final closing `]`.
3. Add a comma directly after that `}`, then paste the following 10 new tickets before the final `]`:

```json
  ,
  {
    "ticket_id": "T011",
    "raw_text": "My photos won't upload no matter how many times I try on my Samsung phone",
    "source": "chat",
    "timestamp": "2026-01-08T09:15:00Z"
  },
  {
    "ticket_id": "T012",
    "raw_text": "Getting charged two times a month for the same subscription plan",
    "source": "email",
    "timestamp": "2026-01-08T10:00:00Z"
  },
  {
    "ticket_id": "T013",
    "raw_text": "Blank white screen appears the moment I try to sign in from Firefox",
    "source": "web_form",
    "timestamp": "2026-01-08T11:20:00Z"
  },
  {
    "ticket_id": "T014",
    "raw_text": "Never received the email to reset my forgotten password",
    "source": "email",
    "timestamp": "2026-01-08T13:00:00Z"
  },
  {
    "ticket_id": "T015",
    "raw_text": "Dashboard is painfully slow to load, especially from our UK office",
    "source": "chat",
    "timestamp": "2026-01-08T15:40:00Z"
  },
  {
    "ticket_id": "T016",
    "raw_text": "Export to PDF button does nothing when I click it",
    "source": "web_form",
    "timestamp": "2026-01-09T08:30:00Z"
  },
  {
    "ticket_id": "T017",
    "raw_text": "PDF export feature is completely unresponsive on my end too",
    "source": "chat",
    "timestamp": "2026-01-09T09:10:00Z"
  },
  {
    "ticket_id": "T018",
    "raw_text": "Was billed twice again this month, this is the second time now",
    "source": "phone",
    "timestamp": "2026-01-09T10:05:00Z"
  },
  {
    "ticket_id": "T019",
    "raw_text": "App crashes immediately whenever I attach an image from my gallery",
    "source": "email",
    "timestamp": "2026-01-09T12:00:00Z"
  },
  {
    "ticket_id": "T020",
    "raw_text": "The customer support chat widget never loads on our internal browser",
    "source": "web_form",
    "timestamp": "2026-01-09T14:25:00Z"
  }
```

4. Save the file (`Ctrl + S` / `Cmd + S`).

**How to verify success:** No red underlines in VS Code (which would mean broken JSON). Count the objects — there should be exactly 20, with IDs `T001` through `T020`.

**Common beginner mistake:** Forgetting the comma between `T010`'s closing `}` and the new `T011` opening `{`. If VS Code shows an error, check exactly that spot first.

---

## Step 2.6 — Create historical_incidents.json

**Goal:** Create a small archive of 15 *already-resolved* past incidents. This is what our RAG pipeline will search through.

**Exact steps:**
1. Right-click the `data` folder → **New File**.
2. Name it exactly: `historical_incidents.json`
3. Paste the following:

```json
[
  {
    "incident_id": "H001",
    "summary": "Users on Android devices were unable to upload photos due to a memory allocation bug in the image compression module.",
    "outcome": "Resolved by patching the compression library in version 4.2.1.",
    "date": "2025-06-12"
  },
  {
    "incident_id": "H002",
    "summary": "A billing system error caused a subset of customers to be charged twice for their monthly subscription.",
    "outcome": "Resolved by fixing a duplicate webhook trigger in the payment processor integration.",
    "date": "2025-07-03"
  },
  {
    "incident_id": "H003",
    "summary": "The login page displayed a blank screen for users on outdated browser versions due to an unsupported JavaScript feature.",
    "outcome": "Resolved by adding a compatibility fallback for older browsers.",
    "date": "2025-07-20"
  },
  {
    "incident_id": "H004",
    "summary": "Password reset emails were delayed or lost due to an overloaded email delivery queue.",
    "outcome": "Resolved by scaling the email service and adding retry logic.",
    "date": "2025-08-05"
  },
  {
    "incident_id": "H005",
    "summary": "European users reported significantly slower dashboard load times during peak hours.",
    "outcome": "Resolved by deploying a regional caching layer closer to European users.",
    "date": "2025-08-22"
  },
  {
    "incident_id": "H006",
    "summary": "PDF export functionality silently failed for documents larger than 10MB.",
    "outcome": "Resolved by increasing the export service's file size limit and adding a progress indicator.",
    "date": "2025-09-01"
  },
  {
    "incident_id": "H007",
    "summary": "A subset of mobile users experienced app crashes when attaching images from their photo gallery.",
    "outcome": "Resolved by fixing a null pointer exception in the gallery attachment handler.",
    "date": "2025-09-15"
  },
  {
    "incident_id": "H008",
    "summary": "The customer support chat widget failed to load for users on certain internal corporate browser configurations.",
    "outcome": "Resolved by relaxing an overly strict content security policy setting.",
    "date": "2025-09-28"
  },
  {
    "incident_id": "H009",
    "summary": "Duplicate charges appeared on customer invoices following a batch billing job retry.",
    "outcome": "Resolved by adding idempotency keys to the billing job to prevent reprocessing.",
    "date": "2025-10-10"
  },
  {
    "incident_id": "H010",
    "summary": "Users reported the app freezing and closing unexpectedly while uploading media files on Android.",
    "outcome": "Resolved as part of the same fix as H001, released in version 4.2.1.",
    "date": "2025-10-18"
  },
  {
    "incident_id": "H011",
    "summary": "A visual bug caused the login screen to appear completely blank on Chrome for a small number of users.",
    "outcome": "Resolved by fixing a CSS rendering conflict introduced in a recent release.",
    "date": "2025-11-02"
  },
  {
    "incident_id": "H012",
    "summary": "Some users never received their password reset email despite requesting it multiple times.",
    "outcome": "Resolved by identifying and removing a spam filter misclassification on the sending domain.",
    "date": "2025-11-14"
  },
  {
    "incident_id": "H013",
    "summary": "Dashboard performance degraded for users accessing the platform from outside North America.",
    "outcome": "Resolved by expanding content delivery network coverage to additional regions.",
    "date": "2025-11-25"
  },
  {
    "incident_id": "H014",
    "summary": "The PDF export button became unresponsive after a recent frontend deployment.",
    "outcome": "Resolved by rolling back a broken event handler introduced in the deployment.",
    "date": "2025-12-05"
  },
  {
    "incident_id": "H015",
    "summary": "Customers were billed twice in the same billing cycle due to a race condition in the subscription renewal process.",
    "outcome": "Resolved by adding a database lock during renewal processing.",
    "date": "2025-12-19"
  }
]
```

4. Save the file.

**How to verify success:** The file should contain exactly 15 objects, each with 4 fields: `incident_id`, `summary`, `outcome`, `date`.

**Notice:** Several of these historical incidents (`H001`, `H010` about Android uploads; `H002`, `H009`, `H015` about double billing; `H003`, `H011` about blank login screens) deliberately mirror the *types* of problems in our 20 live tickets — this is what will make the RAG retrieval results feel meaningful when we test it.

---

## Step 2.7 — Create the Embeddings Generation Script

**Goal:** Write a script that converts each of the 15 historical incident summaries into a numeric vector (an "embedding") and saves them in the exact file format Vector Search requires, then uploads that file to your Cloud Storage bucket.

**Why:** Vector Search doesn't understand text directly — it compares numeric vectors. An embedding model converts meaning into numbers, where similar meanings produce similar numbers.

**Exact steps:**
1. Right-click the `rag` folder → **New File**.
2. Name it exactly: `generate_embeddings.py`
3. Paste the following code:

```python
import os
import json
from dotenv import load_dotenv
import vertexai
from vertexai.language_models import TextEmbeddingModel
from google.cloud import storage

load_dotenv()

PROJECT_ID = os.getenv("GCP_PROJECT_ID")
LOCATION = "us-central1"
EMBEDDING_MODEL_NAME = "text-embedding-004"

# CHANGE THIS to the exact bucket name you created in Step 2.2
BUCKET_NAME = "epc-platform-123456-embeddings"

INPUT_FILE = "../data/historical_incidents.json"
OUTPUT_LOCAL_FILE = "embeddings_output.json"
GCS_DESTINATION_FOLDER = "embeddings"

vertexai.init(project=PROJECT_ID, location=LOCATION)
embedding_model = TextEmbeddingModel.from_pretrained(EMBEDDING_MODEL_NAME)


def generate_and_save_embeddings():
    with open(INPUT_FILE, "r") as f:
        incidents = json.load(f)

    lines = []

    for incident in incidents:
        incident_id = incident["incident_id"]
        text_to_embed = incident["summary"]

        embeddings = embedding_model.get_embeddings([text_to_embed])
        vector = embeddings[0].values

        print(f"Embedded {incident_id}: vector length = {len(vector)}")

        # Vector Search requires this exact structure: one JSON object per line
        lines.append(json.dumps({"id": incident_id, "embedding": vector}))

    with open(OUTPUT_LOCAL_FILE, "w") as f:
        f.write("\n".join(lines))

    print(f"\nSaved {len(lines)} embeddings locally to {OUTPUT_LOCAL_FILE}")

    upload_to_gcs()


def upload_to_gcs():
    client = storage.Client(project=PROJECT_ID)
    bucket = client.bucket(BUCKET_NAME)
    blob_path = f"{GCS_DESTINATION_FOLDER}/embeddings_output.json"
    blob = bucket.blob(blob_path)
    blob.upload_from_filename(OUTPUT_LOCAL_FILE)

    print(f"Uploaded to gs://{BUCKET_NAME}/{blob_path}")


if __name__ == "__main__":
    generate_and_save_embeddings()
```

4. **Before saving**, find the line `BUCKET_NAME = "epc-platform-123456-embeddings"` and replace it with your actual bucket name from Step 2.2.
5. Save the file.

**Run it:**
```bash
cd rag
python generate_embeddings.py
```

**Expected output:**
```
Embedded H001: vector length = 768
Embedded H002: vector length = 768
...
Embedded H015: vector length = 768

Saved 15 embeddings locally to embeddings_output.json
Uploaded to gs://epc-platform-123456-embeddings/embeddings/embeddings_output.json
```

**How to verify success:**
1. Go to `https://console.cloud.google.com`, search `Cloud Storage`, click your bucket name.
2. Click into the `embeddings` folder.
3. Confirm `embeddings_output.json` is listed there.

**Common beginner mistake:** Forgetting to replace the placeholder `BUCKET_NAME` value — this causes a "bucket not found" error.

**Troubleshooting:** If you see `PermissionDenied` during upload, revisit Step 2.3 and confirm **Storage Object Admin** was actually saved to your service account.

---

## Step 2.8 — Create the Vector Search Index

**Goal:** Tell Vertex AI to build a searchable index from the embeddings file you just uploaded.

**Why:** An "index" is the actual searchable data structure — it doesn't exist until you explicitly create one pointing at your embeddings.

**Exact steps:**
1. Right-click the `rag` folder → **New File**.
2. Name it exactly: `create_index.py`
3. Paste the following code:

```python
import os
from dotenv import load_dotenv
from google.cloud import aiplatform

load_dotenv()

PROJECT_ID = os.getenv("GCP_PROJECT_ID")
LOCATION = "us-central1"

# CHANGE THIS to your actual bucket name from Step 2.2
BUCKET_NAME = "epc-platform-123456-embeddings"

aiplatform.init(project=PROJECT_ID, location=LOCATION)

print("Creating index. This typically takes 5-10 minutes...")

index = aiplatform.MatchingEngineIndex.create_tree_ah_index(
    display_name="epc-historical-incidents-index",
    contents_delta_uri=f"gs://{BUCKET_NAME}/embeddings/",
    dimensions=768,
    approximate_neighbors_count=10,
    distance_measure_type="COSINE_DISTANCE",
    index_update_method="BATCH_UPDATE",
)

print("Index created successfully.")
print(f"Index resource name: {index.resource_name}")
print("\nIMPORTANT: copy the line above (Index resource name) somewhere safe.")
print("You will need it in the next script.")
```

4. Replace `BUCKET_NAME` with your real bucket name again.
5. Save the file.

**Run it:**
```bash
python create_index.py
```

**Expected output (after a 5–10 minute wait):**
```
Creating index. This typically takes 5-10 minutes...
Index created successfully.
Index resource name: projects/123456789/locations/us-central1/indexes/1234567890123456789

IMPORTANT: copy the line above (Index resource name) somewhere safe.
You will need it in the next script.
```

**What to do while waiting:** This step runs quietly for several minutes with no visible progress — that's normal. Do not close the terminal or press `Ctrl + C`.

**How to verify success:** Copy the full **Index resource name** line into a text file or sticky note — you'll paste it into the next script.

You can also confirm visually: go to the console, search `Vector Search`, click the **Indexes** tab. You should see `epc-historical-incidents-index` listed.

**Common beginner mistake:** Closing the terminal window because "nothing seems to be happening." Index creation genuinely takes several minutes with no output — this is expected.

---

## Step 2.9 — Create an Index Endpoint and Deploy the Index

**Goal:** Create a live "serving endpoint" and deploy your index to it, so it can actually respond to search queries.

**Why:** A created index is just data sitting in the background — it must be deployed to an endpoint before you can query it, similar to how a website's files must be deployed to a server before anyone can visit it.

**⚠️ This step takes 20–40 minutes.** This is the step that starts incurring hourly charges — proceed, but remember Step 2.15 at the end of this guide.

**Exact steps:**
1. Right-click the `rag` folder → **New File**.
2. Name it exactly: `deploy_index.py`
3. Paste the following code:

```python
import os
from dotenv import load_dotenv
from google.cloud import aiplatform

load_dotenv()

PROJECT_ID = os.getenv("GCP_PROJECT_ID")
LOCATION = "us-central1"

# PASTE the full "Index resource name" you copied from Step 2.8
INDEX_RESOURCE_NAME = "PASTE_YOUR_INDEX_RESOURCE_NAME_HERE"

aiplatform.init(project=PROJECT_ID, location=LOCATION)

index = aiplatform.MatchingEngineIndex(index_name=INDEX_RESOURCE_NAME)

print("Creating index endpoint...")

index_endpoint = aiplatform.MatchingEngineIndexEndpoint.create(
    display_name="epc-historical-incidents-endpoint",
    public_endpoint_enabled=True,
)

print(f"Endpoint created: {index_endpoint.resource_name}")
print("\nDeploying index to endpoint. This takes 20-40 minutes. Please wait...")

index_endpoint.deploy_index(
    index=index,
    deployed_index_id="epc_incidents_deployed",
)

print("\nDeployment complete!")
print(f"Index Endpoint resource name: {index_endpoint.resource_name}")
print("Deployed Index ID: epc_incidents_deployed")
print("\nIMPORTANT: copy both lines above. You will need them in the query script.")
```

4. Replace `INDEX_RESOURCE_NAME` with the value you copied in Step 2.8.
5. Save the file.

**Run it:**
```bash
python deploy_index.py
```

**Expected output (after waiting):**
```
Creating index endpoint...
Endpoint created: projects/123456789/locations/us-central1/indexEndpoints/9876543210987654321

Deploying index to endpoint. This takes 20-40 minutes. Please wait...

Deployment complete!
Index Endpoint resource name: projects/123456789/locations/us-central1/indexEndpoints/9876543210987654321
Deployed Index ID: epc_incidents_deployed
```

**How to verify success:** Copy both output lines somewhere safe. Then go to the console, search `Vector Search`, click **Index Endpoints** tab — you should see `epc-historical-incidents-endpoint` with a green "Deployed" status next to `epc_incidents_deployed`.

**Common beginner mistake:** Assuming the script has frozen or crashed because nothing prints for 20+ minutes. This is completely normal — the Python script is simply waiting for Google Cloud to finish provisioning serving infrastructure in the background.

**Troubleshooting:** If the script errors immediately with `NotFound`, double-check you pasted the entire `INDEX_RESOURCE_NAME` string exactly as printed in Step 2.8, with no extra spaces.

---

## Step 2.10 — Verify Deployment in the Console

**Goal:** Visually confirm the index is live before writing query code against it.

**Exact steps:**
1. Go to `https://console.cloud.google.com`
2. Search `Vector Search`, click it.
3. Click the **Index Endpoints** tab.
4. Click `epc-historical-incidents-endpoint`.
5. Under "Deployed indexes," confirm `epc_incidents_deployed` shows a green checkmark or "Deployed" status (not "Deploying").

**How to verify success:** Status is fully "Deployed," not still in progress.

---

## Step 2.11 — Create the RAG Retrieval Query Script

**Goal:** Write a script that takes a new ticket, embeds it, and asks the deployed index for the most similar historical incidents.

**Exact steps:**
1. Right-click the `rag` folder → **New File**.
2. Name it exactly: `query_similar_incidents.py`
3. Paste the following code:

```python
import os
import json
from dotenv import load_dotenv
import vertexai
from vertexai.language_models import TextEmbeddingModel
from google.cloud import aiplatform

load_dotenv()

PROJECT_ID = os.getenv("GCP_PROJECT_ID")
LOCATION = "us-central1"
EMBEDDING_MODEL_NAME = "text-embedding-004"

# PASTE your values from Step 2.9
INDEX_ENDPOINT_RESOURCE_NAME = "PASTE_YOUR_INDEX_ENDPOINT_RESOURCE_NAME_HERE"
DEPLOYED_INDEX_ID = "epc_incidents_deployed"

INCIDENTS_FILE = "../data/historical_incidents.json"

vertexai.init(project=PROJECT_ID, location=LOCATION)
embedding_model = TextEmbeddingModel.from_pretrained(EMBEDDING_MODEL_NAME)
index_endpoint = aiplatform.MatchingEngineIndexEndpoint(
    index_endpoint_name=INDEX_ENDPOINT_RESOURCE_NAME
)

with open(INCIDENTS_FILE, "r") as f:
    incidents_lookup = {i["incident_id"]: i for i in json.load(f)}


def find_similar_incidents(ticket_text: str, top_k: int = 3):
    query_embedding = embedding_model.get_embeddings([ticket_text])[0].values

    response = index_endpoint.find_neighbors(
        deployed_index_id=DEPLOYED_INDEX_ID,
        queries=[query_embedding],
        num_neighbors=top_k,
    )

    neighbors = response[0]
    results = []
    for neighbor in neighbors:
        incident = incidents_lookup.get(neighbor.id, {})
        results.append({
            "incident_id": neighbor.id,
            "similarity_score": round(neighbor.distance, 4),
            "summary": incident.get("summary", "Unknown"),
            "outcome": incident.get("outcome", "Unknown"),
        })
    return results


if __name__ == "__main__":
    test_ticket = "App keeps crashing when I try to upload photos on Android"
    print(f"Query ticket: {test_ticket}\n")

    matches = find_similar_incidents(test_ticket)
    for m in matches:
        print(f"[{m['incident_id']}] score={m['similarity_score']} - {m['summary']}")
        print(f"    Outcome: {m['outcome']}\n")
```

4. Paste your real `INDEX_ENDPOINT_RESOURCE_NAME` from Step 2.9.
5. Save the file.

**Run it:**
```bash
python query_similar_incidents.py
```

**Expected output:**
```
Query ticket: App keeps crashing when I try to upload photos on Android

[H001] score=0.87 - Users on Android devices were unable to upload photos due to a memory allocation bug...
    Outcome: Resolved by patching the compression library in version 4.2.1.

[H010] score=0.83 - Users reported the app freezing and closing unexpectedly while uploading media files on Android.
    Outcome: Resolved as part of the same fix as H001, released in version 4.2.1.

[H007] score=0.61 - A subset of mobile users experienced app crashes when attaching images from their photo gallery.
    Outcome: Resolved by fixing a null pointer exception in the gallery attachment handler.
```

(Exact scores will vary slightly — what matters is that `H001` and `H010`, the two genuinely related incidents, rank at the top.)

**How to verify success:** The top result(s) should be topically related to your test ticket. If you query a billing-related ticket instead, `H002`, `H009`, or `H015` should surface at the top.

**Common beginner mistake:** Pasting the **Index** resource name (from Step 2.8) instead of the **Index Endpoint** resource name (from Step 2.9) — these are two different resources with similar-looking names. Double check which one you're pasting where.

---

## Step 2.12 — Create the BM25 Keyword Fallback Script

**Goal:** Build a simple keyword-matching backup system, used when semantic similarity between two tickets is borderline.

**Why:** Semantic similarity is powerful but not perfect — a keyword overlap check acts as a second opinion for ambiguous cases.

**Exact steps:**
1. Right-click the `agents` folder → **New File**.
2. Name it exactly: `bm25_matcher.py`
3. Paste the following:

```python
from rank_bm25 import BM25Okapi


def build_bm25_index(ticket_texts: list[str]) -> BM25Okapi:
    """Builds a BM25 keyword index from a list of raw ticket texts."""
    tokenized_corpus = [text.lower().split() for text in ticket_texts]
    return BM25Okapi(tokenized_corpus)


def get_bm25_scores(bm25_index: BM25Okapi, query_text: str) -> list[float]:
    """Returns a keyword-overlap score for the query against every ticket in the index."""
    tokenized_query = query_text.lower().split()
    return bm25_index.get_scores(tokenized_query)
```

4. Save the file.

**How to verify success:** No red underlines. This file has no `if __name__` block because it's a helper module — we'll test it indirectly in the next step.

---

## Step 2.13 — Create the Ticket Clustering Agent

**Goal:** Write the agent that groups today's 20 live tickets into clusters of related problems, using semantic similarity as the primary signal and BM25 as a fallback for borderline cases.

**Exact steps:**
1. Right-click the `agents` folder → **New File**.
2. Name it exactly: `ticket_clustering_agent.py`
3. Paste the following:

```python
import os
import json
import numpy as np
from dotenv import load_dotenv
import vertexai
from vertexai.language_models import TextEmbeddingModel

from bm25_matcher import build_bm25_index, get_bm25_scores

load_dotenv()

PROJECT_ID = os.getenv("GCP_PROJECT_ID")
LOCATION = "us-central1"
EMBEDDING_MODEL_NAME = "text-embedding-004"

SIMILARITY_THRESHOLD = 0.75      # primary signal: cosine similarity cutoff
BORDERLINE_LOWER_BOUND = 0.60    # below this, never cluster even with BM25 support
BM25_FALLBACK_CUTOFF = 3.0       # BM25 score needed to confirm a borderline case

TICKETS_FILE = "../data/sample_tickets.json"

vertexai.init(project=PROJECT_ID, location=LOCATION)
embedding_model = TextEmbeddingModel.from_pretrained(EMBEDDING_MODEL_NAME)


def cosine_similarity(vec_a, vec_b) -> float:
    a = np.array(vec_a)
    b = np.array(vec_b)
    return float(np.dot(a, b) / (np.linalg.norm(a) * np.linalg.norm(b)))


def embed_all_tickets(tickets: list[dict]) -> dict:
    """Returns a dict mapping ticket_id -> embedding vector."""
    embeddings = {}
    for ticket in tickets:
        vector = embedding_model.get_embeddings([ticket["raw_text"]])[0].values
        embeddings[ticket["ticket_id"]] = vector
    return embeddings


def cluster_tickets(tickets: list[dict]) -> list[dict]:
    embeddings = embed_all_tickets(tickets)
    raw_texts = [t["raw_text"] for t in tickets]
    bm25_index = build_bm25_index(raw_texts)
    ticket_ids = [t["ticket_id"] for t in tickets]

    clusters = []  # each cluster: {"cluster_id": str, "ticket_ids": [...], "representative_embedding": [...]}

    for i, ticket in enumerate(tickets):
        ticket_id = ticket["ticket_id"]
        ticket_embedding = embeddings[ticket_id]

        best_cluster = None
        best_score = 0.0

        for cluster in clusters:
            similarity = cosine_similarity(ticket_embedding, cluster["representative_embedding"])

            if similarity >= SIMILARITY_THRESHOLD:
                if similarity > best_score:
                    best_cluster = cluster
                    best_score = similarity
            elif BORDERLINE_LOWER_BOUND <= similarity < SIMILARITY_THRESHOLD:
                # Borderline case: check BM25 keyword overlap as a tiebreaker
                bm25_scores = get_bm25_scores(bm25_index, ticket["raw_text"])
                cluster_member_index = ticket_ids.index(cluster["ticket_ids"][0])
                bm25_score_for_cluster = bm25_scores[cluster_member_index]

                if bm25_score_for_cluster >= BM25_FALLBACK_CUTOFF:
                    if similarity > best_score:
                        best_cluster = cluster
                        best_score = similarity

        if best_cluster:
            best_cluster["ticket_ids"].append(ticket_id)
        else:
            new_cluster = {
                "cluster_id": f"C{len(clusters) + 1:03d}",
                "ticket_ids": [ticket_id],
                "representative_embedding": ticket_embedding,
            }
            clusters.append(new_cluster)

    # Strip embeddings before returning (not needed in the final output)
    output = []
    for cluster in clusters:
        output.append({
            "cluster_id": cluster["cluster_id"],
            "ticket_count": len(cluster["ticket_ids"]),
            "ticket_ids": cluster["ticket_ids"],
        })
    return output


if __name__ == "__main__":
    with open(TICKETS_FILE, "r") as f:
        tickets = json.load(f)

    clusters = cluster_tickets(tickets)

    print(f"Formed {len(clusters)} clusters from {len(tickets)} tickets:\n")
    for cluster in clusters:
        print(f"{cluster['cluster_id']} ({cluster['ticket_count']} tickets): {cluster['ticket_ids']}")
```

4. Save the file.

**Run it:**
```bash
python ticket_clustering_agent.py
```

**Expected output (approximate — exact groupings may vary slightly):**
```
Formed 5 clusters from 20 tickets:

C001 (4 tickets): ['T001', 'T002', 'T003', 'T011']
C002 (3 tickets): ['T004', 'T005', 'T012']
C003 (3 tickets): ['T006', 'T007', 'T013']
C004 (2 tickets): ['T008', 'T014']
C005 (2 tickets): ['T009', 'T010']
...
```

**How to verify success:** You should see somewhere between **4 and 6 clusters**. Tickets `T001`, `T002`, `T003` (all photo upload issues) should land in the same cluster. Tickets `T004`, `T005` (billing double-charge) should land in a different, separate cluster together.

**Common beginner mistake:** Getting 20 separate clusters (nothing grouped at all). This usually means `SIMILARITY_THRESHOLD` is set too high, or the embedding call is failing silently — check the terminal for hidden errors above the final output.

**Troubleshooting:** If everything ends up in one giant cluster, your `SIMILARITY_THRESHOLD` may have been accidentally lowered — confirm it reads exactly `0.75` in your file.

---

## Step 2.14 — Save the Clustering Results

**Goal:** Persist the cluster output to a file so future weeks (impact scoring, escalation) can read it.

**Exact steps:**
1. Open `ticket_clustering_agent.py` again.
2. Find this block at the bottom:
```python
    print(f"Formed {len(clusters)} clusters from {len(tickets)} tickets:\n")
    for cluster in clusters:
        print(f"{cluster['cluster_id']} ({cluster['ticket_count']} tickets): {cluster['ticket_ids']}")
```
3. Replace it with:
```python
    print(f"Formed {len(clusters)} clusters from {len(tickets)} tickets:\n")
    for cluster in clusters:
        print(f"{cluster['cluster_id']} ({cluster['ticket_count']} tickets): {cluster['ticket_ids']}")

    with open("../data/clusters_output.json", "w") as f:
        json.dump(clusters, f, indent=2)
    print("\nSaved cluster results to ../data/clusters_output.json")
```
4. Save the file and run it again:
```bash
python ticket_clustering_agent.py
```

**How to verify success:** Open `data/clusters_output.json` in VS Code and confirm it lists your clusters with `cluster_id`, `ticket_count`, and `ticket_ids` fields.

---

## Step 2.15 — ⚠️ Undeploy the Index to Stop Charges

**Goal:** Shut down the live, billable Vector Search endpoint now that you've confirmed it works.

**Do this now, even if you plan to keep working — you can always redeploy in a future week if needed.**

**Exact steps:**
1. Right-click the `rag` folder → **New File**.
2. Name it exactly: `undeploy_index.py`
3. Paste:

```python
import os
from dotenv import load_dotenv
from google.cloud import aiplatform

load_dotenv()

PROJECT_ID = os.getenv("GCP_PROJECT_ID")
LOCATION = "us-central1"

# PASTE your Index Endpoint resource name from Step 2.9
INDEX_ENDPOINT_RESOURCE_NAME = "PASTE_YOUR_INDEX_ENDPOINT_RESOURCE_NAME_HERE"
DEPLOYED_INDEX_ID = "epc_incidents_deployed"

aiplatform.init(project=PROJECT_ID, location=LOCATION)

index_endpoint = aiplatform.MatchingEngineIndexEndpoint(
    index_endpoint_name=INDEX_ENDPOINT_RESOURCE_NAME
)

print("Undeploying index... this takes a few minutes.")
index_endpoint.undeploy_index(deployed_index_id=DEPLOYED_INDEX_ID)
print("Undeployed successfully. Billing for the deployed endpoint has stopped.")
```

4. Paste your real endpoint resource name.
5. Save and run:
```bash
python undeploy_index.py
```

**Expected output:**
```
Undeploying index... this takes a few minutes.
Undeployed successfully. Billing for the deployed endpoint has stopped.
```

**How to verify success:** Go to the console → **Vector Search** → **Index Endpoints** → click your endpoint. The "Deployed indexes" section should now be empty.

**Note:** The **index definition itself** (created in Step 2.8) still exists and costs effectively nothing while undeployed. Only the **deployment** was billable. When you need RAG retrieval again in a future week, you can re-run `deploy_index.py` using the same `INDEX_RESOURCE_NAME`.

---

## Step 2.16 — Push Your Work to GitHub

**Exact steps:**
1. In the terminal, navigate to the project root:
```bash
cd ..
```
2. Run:
```bash
git add .
git commit -m "Week 2: RAG retrieval pipeline and Ticket Clustering Agent complete"
git push
```

**How to verify success:** Visit `https://github.com/YOUR_USERNAME/epc-platform` and confirm the `rag` folder and its new files appear, along with the updated `data` folder.

**Reminder:** Your `.env` and `service-account-key.json` should still be absent from GitHub — the `.gitignore` from Week 1 protects them automatically.

---

# WEEK 2 COMPLETION CHECKLIST

- [ ] I created a Cloud Storage bucket and can see it in the console.
- [ ] My service account has the **Storage Object Admin** role in addition to Vertex AI User.
- [ ] `data/sample_tickets.json` now contains exactly 20 tickets (T001–T020).
- [ ] `data/historical_incidents.json` exists with exactly 15 incidents (H001–H015).
- [ ] `rag/generate_embeddings.py` ran successfully and uploaded a file to my bucket's `embeddings/` folder.
- [ ] I created a Vector Search index and copied its resource name.
- [ ] I deployed the index to an endpoint and confirmed "Deployed" status in the console.
- [ ] `rag/query_similar_incidents.py` returns topically relevant historical incidents for a test ticket (e.g., Android upload ticket → H001/H010 rank highest).
- [ ] `agents/ticket_clustering_agent.py` runs successfully and produces between 4 and 6 clusters from the 20 tickets.
- [ ] Tickets T001, T002, T003 (photo upload issues) landed in the same cluster.
- [ ] `data/clusters_output.json` was created and contains my cluster results.
- [ ] I ran `undeploy_index.py` and confirmed the endpoint shows no deployed indexes in the console.
- [ ] I pushed my code to GitHub and confirmed the new files are visible there.

Once every box is checked, Week 2 is complete. Let me know when you're ready for Week 3.
