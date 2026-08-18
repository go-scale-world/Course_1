# Week 5 — Complete Step-by-Step Implementation Guide
### Enterprise Problem Consolidation (EPC) Platform

---

## How to Use This Guide

This picks up exactly where Week 4 left off. You should already have a working 4-agent pipeline, with escalation reports being generated for high-impact clusters. We will not repeat that setup here.

If you haven't finished Week 4, stop and complete it first.

---

## Recap: What We're Building This Week

**Product Goal:** Connect the platform to real enterprise tools — Drive, Gmail, Chat — and sharpen retrieval accuracy with a re-ranking step.

**What we're building:**
1. **Google Drive integration** — pull a relevant runbook excerpt into escalation reports.
2. **Gmail integration** — auto-draft (never auto-send) a stakeholder notification email.
3. **Google Chat integration** — post a real-time alert to a team Chat space via webhook.
4. **A real MCP server** — expose these three tools using the actual Model Context Protocol, so any MCP-compatible client (including Claude Desktop) could use them.
5. **Hybrid RAG with re-ranking** — combine semantic + keyword search, then have Gemini re-rank the results for better relevance.

**An important architecture note before we start:** Google Drive and Gmail require you to personally authorize access (OAuth), tied to your own Google account. This is different from the service-account approach we've used since Week 1. Because of this, this week's Drive/Gmail/Chat work runs **locally** on your computer, not inside the Cloud Run pipeline — real companies typically use a more advanced enterprise-wide authorization pattern (domain-wide delegation) for server-to-server access, which requires a paid Google Workspace admin account not available in this course. We'll explain exactly why as we go.

---

## Step 5.1 — Enable the Google Drive API

**Exact steps:**
1. Go to `https://console.cloud.google.com`
2. Confirm the project selector shows `epc-platform`.
3. Search the top bar for: `Google Drive API`
4. Click the result named **Google Drive API**.
5. Click **Enable**.
6. Wait for it to finish.

**How to verify success:** The button changes to show the API is active (no "Enable" button remains).

---

## Step 5.2 — Enable the Gmail API

**Exact steps:**
1. Search the top bar for: `Gmail API`
2. Click the result named **Gmail API**.
3. Click **Enable**.
4. Wait for it to finish.

**How to verify success:** Same as above — no "Enable" button remains on the page.

---

## Step 5.3 — Configure the OAuth Consent Screen

**Goal:** Tell Google what your app is called and what permissions it will ask for, before anyone (including you) can authorize it.

**Why:** Drive and Gmail contain private personal data. Google requires every app to declare itself and its requested permissions through a "consent screen" before any user (even you, testing your own app) can grant access.

**Exact steps:**
1. Search the top bar for: `OAuth consent screen`
2. Click the result.
3. Under **User Type**, select **External** (this is required unless you have a paid Google Workspace account).
4. Click **Create**.
5. On the **App information** screen:
   - **App name**: type `EPC Platform`
   - **User support email**: select your email from the dropdown
   - Scroll down to **Developer contact information**, enter your email address
6. Click **Save and Continue**.
7. On the **Scopes** screen, click **Add or Remove Scopes**.
8. In the filter box that appears, paste this and press Enter: `https://www.googleapis.com/auth/drive.readonly`
9. Check the box next to it in the results list.
10. Repeat for: `https://www.googleapis.com/auth/gmail.compose`
11. Click **Update** at the bottom of the panel.
12. Click **Save and Continue**.
13. On the **Test users** screen, click **+ Add Users**.
14. Type your own Gmail address, press Enter, then click **Add**.
15. Click **Save and Continue**, then click **Back to Dashboard**.

**How to verify success:** The OAuth consent screen dashboard shows "Publishing status: Testing" and your email listed under "Test users."

**Common beginner mistake:** Skipping the "Test users" step. If you skip this, you (or anyone) will be blocked from authorizing the app later with an "access blocked" error, since the app isn't published publicly.

---

## Step 5.4 — Create an OAuth Client ID

**Exact steps:**
1. Search the top bar for: `Credentials`
2. Click the result under "APIs & Services."
3. Click **+ Create Credentials** near the top.
4. Click **OAuth client ID**.
5. Under **Application type**, select **Desktop app**.
6. In **Name**, type: `EPC Platform Desktop Client`
7. Click **Create**.
8. A popup shows your new Client ID and Client Secret. Click **Download JSON**.
9. Click **OK** to close the popup.

**How to verify success:** A `.json` file downloads, usually named something like `client_secret_123456-abc.apps.googleusercontent.com.json`.

**Move and rename it:**
1. Move this downloaded file into your `epc-platform` project root folder.
2. Rename it to exactly: `oauth_client_secret.json`

**Protect it:** Open `.gitignore` (created in Week 1) and add these two new lines at the bottom:
```
oauth_client_secret.json
token.json
```
Save the file.

**Why this matters:** Just like your service account key, this file grants access to your accounts and must never be committed to GitHub.

---

## Step 5.5 — Install the OAuth and Google API Libraries

**Exact steps:**
1. In VS Code's terminal, confirm `(venv)` is active and you're in the `epc-platform` root.
2. Run:
```bash
pip install google-auth-oauthlib google-api-python-client
```
3. Update your dependency list:
```bash
pip freeze > requirements.txt
```

**How to verify success:** `pip list` shows `google-auth-oauthlib` and `google-api-python-client`.

---

## Step 5.6 — Create 3 Reference Documents in Google Drive

**Goal:** Create the small, fixed set of enterprise reference documents the Escalation Preparation Agent will pull context from.

**Exact steps:**
1. Go to `https://drive.google.com`, signed in with the **same Google account** you'll authorize in Step 5.7.
2. Click **+ New** → **New Folder**. Name it: `EPC Platform Reference Docs`. Click **Create**.
3. Double-click to open that folder.
4. Click **+ New** → **Google Docs**.
5. Click the title "Untitled document" at the top-left and rename it to: `Runbook - Mobile App Crashes`
6. In the document body, type:
```
Runbook: Mobile App Crashes

When Android users report app crashes during photo or media upload, this is
typically caused by memory allocation issues in the image compression module.

Standard resolution steps:
1. Confirm the crash occurs specifically during upload, not general app usage.
2. Check the current app version against known affected versions (below 4.2.1).
3. If affected, direct the user to update to the latest version.
4. If already on the latest version, escalate to the mobile engineering team.

Known affected versions: 4.0.0 - 4.2.0
Fixed in version: 4.2.1
```
7. Close this document (it saves automatically). Back in the Drive folder, repeat steps 4–6 to create a second document titled `SLA - Response Time Commitments` with this content:
```
Service Level Agreement: Response Time Commitments

Severity: Critical - Initial response within 1 hour, resolution target 4 hours.
Severity: High - Initial response within 4 hours, resolution target 24 hours.
Severity: Medium - Initial response within 1 business day, resolution target 3 business days.
Severity: Low - Initial response within 3 business days, resolution target 2 weeks.

Any escalation classified as High or Critical must be routed to the on-call
engineering lead immediately upon approval.
```
8. Create a third document titled `Postmortem - Billing Duplicate Charge Incident` with this content:
```
Postmortem: Billing Duplicate Charge Incident (2025-12-19)

Summary: A race condition in the subscription renewal process caused a subset
of customers to be billed twice within the same billing cycle.

Root cause: Two renewal jobs processed the same subscription simultaneously
due to a missing database lock.

Resolution: Added a database-level lock during renewal processing to prevent
concurrent execution for the same subscription ID.

Follow-up action: Added automated monitoring to detect duplicate charge
patterns within 15 minutes of occurrence, rather than relying on customer
reports.
```

**How to verify success:** Your `EPC Platform Reference Docs` folder in Drive contains exactly 3 Google Docs with the titles above.

---

## Step 5.7 — Run the One-Time Authorization Script

**Goal:** Grant your local code permission to read your Drive (read-only) and create Gmail drafts (draft-only, never send) — a one-time authorization that gets saved locally for reuse.

**Exact steps:**
1. Right-click the `integrations` folder → **New File**.
2. Name it exactly: `authorize_google.py`
3. Paste the following:

```python
from google_auth_oauthlib.flow import InstalledAppFlow

SCOPES = [
    "https://www.googleapis.com/auth/drive.readonly",
    "https://www.googleapis.com/auth/gmail.compose",
]

CLIENT_SECRET_FILE = "../oauth_client_secret.json"
TOKEN_FILE = "../token.json"


def authorize():
    flow = InstalledAppFlow.from_client_secrets_file(CLIENT_SECRET_FILE, SCOPES)
    creds = flow.run_local_server(port=0)

    with open(TOKEN_FILE, "w") as token_file:
        token_file.write(creds.to_json())

    print(f"Authorization successful. Token saved to {TOKEN_FILE}")


if __name__ == "__main__":
    authorize()
```

4. Save the file.

**Run it:**
```bash
cd integrations
python authorize_google.py
```

**Expected behavior:**
1. Your default web browser opens automatically to a Google sign-in page.
2. Sign in with the same Google account used in Step 5.6.
3. You'll likely see a warning screen: **"Google hasn't verified this app."** This is expected — your app is still in "Testing" mode (Step 5.3). Click **Advanced**, then click **Go to EPC Platform (unsafe)**.
4. A permissions screen appears, listing "See and download your Google Drive files" and "Manage drafts and send emails" (this actually only covers draft creation, not sending — Google's wording is broader than the specific permission granted). Click **Continue**.
5. The browser shows "The authentication flow has completed. You may close this window." Close it.
6. Back in your terminal:
```
Authorization successful. Token saved to ../token.json
```

**How to verify success:** A new file `token.json` appears at your project root in VS Code's sidebar (slightly dimmed, confirming `.gitignore` is protecting it).

**Common beginner mistake:** Clicking "Back to safety" instead of "Advanced" → "Go to EPC Platform (unsafe)" on the warning screen. This cancels the flow. The warning is expected and safe to bypass here — it's your own app, being tested by you.

---

## Step 5.8 — Build and Test the Drive Tool

**Exact steps:**
1. Right-click the `integrations` folder → **New File**.
2. Name it exactly: `drive_tool.py`
3. Paste the following:

```python
from google.oauth2.credentials import Credentials
from googleapiclient.discovery import build

TOKEN_FILE = "../token.json"


def get_credentials():
    return Credentials.from_authorized_user_file(TOKEN_FILE)


def get_drive_doc(keyword: str) -> str:
    """Searches Drive for a document whose name contains the keyword, and returns its text content."""
    creds = get_credentials()
    service = build("drive", "v3", credentials=creds)

    results = service.files().list(
        q=f"name contains '{keyword}'",
        fields="files(id, name, mimeType)",
    ).execute()

    files = results.get("files", [])
    if not files:
        return f"No document found matching '{keyword}'."

    file = files[0]
    content = service.files().export(fileId=file["id"], mimeType="text/plain").execute()
    return content.decode("utf-8")


if __name__ == "__main__":
    result = get_drive_doc("Runbook")
    print(result)
```

4. Save the file.

**Run it:**
```bash
python drive_tool.py
```

**Expected output:** The full text content of your "Runbook - Mobile App Crashes" document, printed to the terminal.

**How to verify success:** The printed text matches what you typed into the Google Doc in Step 5.6.

**Common beginner mistake:** Getting a `RefreshError` or `invalid_grant` error. This usually means `token.json` is missing or corrupted — delete it and re-run `authorize_google.py` (Step 5.7) to generate a fresh one.

---

## Step 5.9 — Build and Test the Gmail Draft Tool

**Exact steps:**
1. Right-click the `integrations` folder → **New File**.
2. Name it exactly: `gmail_tool.py`
3. Paste the following:

```python
import base64
from email.mime.text import MIMEText
from google.oauth2.credentials import Credentials
from googleapiclient.discovery import build

TOKEN_FILE = "../token.json"


def get_credentials():
    return Credentials.from_authorized_user_file(TOKEN_FILE)


def draft_gmail(to_email: str, subject: str, body: str) -> str:
    """Creates a Gmail draft (never sends it) and returns the draft ID."""
    creds = get_credentials()
    service = build("gmail", "v1", credentials=creds)

    message = MIMEText(body)
    message["to"] = to_email
    message["subject"] = subject

    raw_message = base64.urlsafe_b64encode(message.as_bytes()).decode()

    draft = service.users().drafts().create(
        userId="me",
        body={"message": {"raw": raw_message}},
    ).execute()

    return draft["id"]


if __name__ == "__main__":
    draft_id = draft_gmail(
        to_email="mobile-product-lead@example.com",
        subject="[EPC Platform] Escalation: Photo Upload Failures on Android",
        body="A cluster of 4 tickets reporting Android photo upload crashes has been escalated. Impact score: 78/100 (High).",
    )
    print(f"Draft created successfully. Draft ID: {draft_id}")
```

4. Save the file.

**Run it:**
```bash
python gmail_tool.py
```

**Expected output:**
```
Draft created successfully. Draft ID: r-1234567890123456789
```

**How to verify success:** Go to `https://mail.google.com`, click **Drafts** in the left sidebar. You should see a new draft addressed to `mobile-product-lead@example.com` with the subject and body shown above. Confirm it sits in **Drafts**, not **Sent**.

**Common beginner mistake:** Worrying that this actually emailed someone. It did not — we deliberately requested only the `gmail.compose` scope, which can create and modify drafts but cannot send mail. Check your **Sent** folder to confirm it's empty of this message.

---

## Step 5.10 — Set Up a Google Chat Space and Webhook

**Goal:** Create a Chat Space and generate a webhook URL your code can post messages to — no OAuth required for this part.

**Exact steps:**
1. Go to `https://chat.google.com`, signed in with the same Google account.
2. Click the **+** icon next to "Chat" in the left sidebar.
3. Click **New space**.
4. Name it: `EPC Platform Alerts`
5. Click **Create**.
6. Once inside the new space, click the space name at the top, then click **Apps & integrations**.
7. Click **Webhooks**.
8. Click **+ Add webhook**.
9. In **Name**, type: `EPC Escalation Alerts`
10. Click **Save**.
11. A URL appears next to your webhook name, starting with `https://chat.googleapis.com/v1/spaces/...`. Click the copy icon next to it.

**How to verify success:** You have a full webhook URL copied to your clipboard. Paste it somewhere safe temporarily (like a scratch text file) — you'll need it in the next step.

**Common beginner mistake:** Confusing a Chat **Space webhook** with a Chat **app** (which requires a much more complex setup involving Google Cloud Pub/Sub and app verification). We specifically want the simple **incoming webhook** option under "Apps & integrations."

---

## Step 5.11 — Build and Test the Chat Alert Tool

**Exact steps:**
1. In the terminal, confirm `(venv)` is active, and install the one additional library needed:
```bash
pip install requests
pip freeze > requirements.txt
```
(You likely already have `requests` installed indirectly — this ensures it's explicitly listed.)
2. Right-click the `integrations` folder → **New File**.
3. Name it exactly: `chat_tool.py`
4. Paste the following:

```python
import requests

# PASTE your webhook URL from Step 5.10
CHAT_WEBHOOK_URL = "PASTE_YOUR_WEBHOOK_URL_HERE"


def post_chat_alert(message_text: str) -> bool:
    """Posts a message to the team's Google Chat space via webhook. Returns True on success."""
    response = requests.post(CHAT_WEBHOOK_URL, json={"text": message_text})
    return response.status_code == 200


if __name__ == "__main__":
    success = post_chat_alert(
        "🚨 New Escalation: Photo Upload Failures on Android (Impact Score: 78/100, Severity: High)"
    )
    print("Message sent successfully." if success else "Failed to send message.")
```

5. Paste your real webhook URL, replacing the placeholder.
6. Save the file.

**Run it:**
```bash
python chat_tool.py
```

**Expected output:**
```
Message sent successfully.
```

**How to verify success:** Go back to `https://chat.google.com`, open the "EPC Platform Alerts" space. Your test message should appear there immediately.

**Common beginner mistake:** Leaving the placeholder URL in place — this produces a connection error, not a silent failure, so you'll notice immediately.

---

## Step 5.12 — Install the MCP SDK and Build the MCP Server

**Goal:** Expose your three tools (Drive, Gmail, Chat) as a real MCP (Model Context Protocol) server — the standardized way AI systems discover and call external tools.

**Exact steps:**
1. In the terminal:
```bash
pip install mcp
pip freeze > requirements.txt
```
2. Right-click the `integrations` folder → **New File**.
3. Name it exactly: `mcp_server.py`
4. Paste the following:

```python
from mcp.server.fastmcp import FastMCP
from drive_tool import get_drive_doc
from gmail_tool import draft_gmail
from chat_tool import post_chat_alert

mcp = FastMCP("EPC Enterprise Tools")


@mcp.tool()
def get_drive_document(keyword: str) -> str:
    """Search Google Drive for a reference document whose name matches the keyword,
    and return its full text content."""
    return get_drive_doc(keyword)


@mcp.tool()
def draft_gmail_notification(to_email: str, subject: str, body: str) -> str:
    """Create a draft (unsent) Gmail notification. Returns the draft ID."""
    return draft_gmail(to_email, subject, body)


@mcp.tool()
def send_chat_alert(message_text: str) -> str:
    """Post an alert message to the team's Google Chat space. Returns 'sent' or 'failed'."""
    success = post_chat_alert(message_text)
    return "sent" if success else "failed"


if __name__ == "__main__":
    mcp.run()
```

5. Save the file.

**How to verify success:** No red underlines. We'll test this properly in the next step — running it directly (`python mcp_server.py`) will appear to "hang" with no output, because MCP servers communicate over a special protocol channel (stdio), not a normal terminal interface. That's expected; press `Ctrl + C` if you try running it directly just to check for errors, then move to Step 5.13 to test it properly.

---

## Step 5.13 — Test the MCP Server With a Python Client

**Goal:** Write a small client that connects to your MCP server exactly the way a real MCP-compatible AI assistant would — listing available tools and calling one.

**Exact steps:**
1. Right-click the `integrations` folder → **New File**.
2. Name it exactly: `test_mcp_client.py`
3. Paste the following:

```python
import asyncio
from mcp import ClientSession, StdioServerParameters
from mcp.client.stdio import stdio_client

server_params = StdioServerParameters(command="python", args=["mcp_server.py"])


async def main():
    async with stdio_client(server_params) as (read, write):
        async with ClientSession(read, write) as session:
            await session.initialize()

            tools_response = await session.list_tools()
            print("Available MCP tools:")
            for tool in tools_response.tools:
                print(f"  - {tool.name}: {tool.description}")

            print("\nCalling get_drive_document(keyword='Runbook')...")
            result = await session.call_tool(
                "get_drive_document", arguments={"keyword": "Runbook"}
            )
            print("Result:")
            print(result.content[0].text)


if __name__ == "__main__":
    asyncio.run(main())
```

4. Save the file.

**Run it:**
```bash
python test_mcp_client.py
```

**Expected output:**
```
Available MCP tools:
  - get_drive_document: Search Google Drive for a reference document whose name matches the keyword,...
  - draft_gmail_notification: Create a draft (unsent) Gmail notification. Returns the draft ID.
  - send_chat_alert: Post an alert message to the team's Google Chat space. Returns 'sent' or 'failed'.

Calling get_drive_document(keyword='Runbook')...
Result:
Runbook: Mobile App Crashes
...
```

**How to verify success:** All 3 tools are listed with their descriptions, and calling `get_drive_document` returns your actual runbook text — proving the MCP server correctly exposes and executes your tools using the real protocol.

**Common beginner mistake:** Running `test_mcp_client.py` from a different folder than `mcp_server.py`. Both files must be in the same `integrations` folder, and you must run the client from inside that folder, since it launches the server as a subprocess using a relative path (`"mcp_server.py"`).

---

## Step 5.14 — Build Hybrid RAG With Re-Ranking

**Goal:** Upgrade Week 2's simple semantic search into a hybrid pipeline: semantic search + keyword search, merged, then re-ranked by Gemini for genuine relevance — all without needing to redeploy the costly Vector Search endpoint from Week 2.

**Why we can skip redeploying Vector Search:** We already have the 15 historical incident embeddings saved locally from Week 2's `rag/embeddings_output.json`. At this small scale, comparing against 15 vectors directly in Python is instant and free — the deployed ANN index exists to handle millions of vectors, which we don't need here.

**Exact steps:**
1. Right-click the `rag` folder → **New File**.
2. Name it exactly: `hybrid_retrieval.py`
3. Paste the following:

```python
import os
import json
import numpy as np
from dotenv import load_dotenv
import vertexai
from vertexai.language_models import TextEmbeddingModel
from vertexai.generative_models import GenerativeModel
from rank_bm25 import BM25Okapi

load_dotenv()

PROJECT_ID = os.getenv("GCP_PROJECT_ID")
LOCATION = "us-central1"
EMBEDDING_MODEL_NAME = "text-embedding-004"
GEMINI_MODEL_NAME = "gemini-2.0-flash-001"

EMBEDDINGS_FILE = "embeddings_output.json"       # created in Week 2, Step 2.7
INCIDENTS_FILE = "../data/historical_incidents.json"

vertexai.init(project=PROJECT_ID, location=LOCATION)
embedding_model = TextEmbeddingModel.from_pretrained(EMBEDDING_MODEL_NAME)
gemini_model = GenerativeModel(GEMINI_MODEL_NAME)


def load_stored_embeddings() -> dict:
    """Loads the 15 pre-computed embeddings saved locally in Week 2."""
    embeddings = {}
    with open(EMBEDDINGS_FILE, "r") as f:
        for line in f:
            record = json.loads(line)
            embeddings[record["id"]] = record["embedding"]
    return embeddings


def load_incidents() -> dict:
    with open(INCIDENTS_FILE, "r") as f:
        return {i["incident_id"]: i for i in json.load(f)}


def cosine_similarity(vec_a, vec_b) -> float:
    a = np.array(vec_a)
    b = np.array(vec_b)
    return float(np.dot(a, b) / (np.linalg.norm(a) * np.linalg.norm(b)))


def semantic_search(query: str, embeddings: dict, top_k: int = 5) -> list[str]:
    query_embedding = embedding_model.get_embeddings([query])[0].values
    scored = [(iid, cosine_similarity(query_embedding, vec)) for iid, vec in embeddings.items()]
    scored.sort(key=lambda x: x[1], reverse=True)
    return [iid for iid, _ in scored[:top_k]]


def keyword_search(query: str, incidents: dict, top_k: int = 5) -> list[str]:
    ids = list(incidents.keys())
    corpus = [incidents[i]["summary"].lower().split() for i in ids]
    bm25 = BM25Okapi(corpus)
    scores = bm25.get_scores(query.lower().split())
    scored = list(zip(ids, scores))
    scored.sort(key=lambda x: x[1], reverse=True)
    return [iid for iid, _ in scored[:top_k]]


def rerank_with_gemini(query: str, candidate_ids: list[str], incidents: dict, keep_top: int = 3) -> list[dict]:
    """Uses Gemini as a re-ranker: given a merged candidate pool, reorder by true relevance."""
    candidates_text = "\n".join(
        f"[{cid}] {incidents[cid]['summary']}" for cid in candidate_ids
    )

    prompt = f"""
You are re-ranking search results for relevance.

Query: "{query}"

Candidate historical incidents:
{candidates_text}

Rank these candidates from MOST to LEAST relevant to the query. Respond with ONLY a
valid JSON array of incident IDs in ranked order, most relevant first, like:
["H001", "H010", "H007"]
"""
    response = gemini_model.generate_content(
        prompt,
        generation_config={"response_mime_type": "application/json"},
    )
    ranked_ids = json.loads(response.text)

    results = []
    for cid in ranked_ids[:keep_top]:
        if cid in incidents:
            results.append({
                "incident_id": cid,
                "summary": incidents[cid]["summary"],
                "outcome": incidents[cid]["outcome"],
            })
    return results


def hybrid_retrieve(query: str, top_k_per_method: int = 5, final_top_k: int = 3) -> list[dict]:
    embeddings = load_stored_embeddings()
    incidents = load_incidents()

    semantic_ids = semantic_search(query, embeddings, top_k_per_method)
    keyword_ids = keyword_search(query, incidents, top_k_per_method)

    merged_ids = list(dict.fromkeys(semantic_ids + keyword_ids))  # de-duplicated union

    reranked = rerank_with_gemini(query, merged_ids, incidents, keep_top=final_top_k)
    return reranked


if __name__ == "__main__":
    test_query = "customers charged twice for their subscription"

    print("=== Semantic search only (Week 2 baseline) ===")
    embeddings = load_stored_embeddings()
    incidents = load_incidents()
    semantic_only = semantic_search(test_query, embeddings, top_k=3)
    for iid in semantic_only:
        print(f"  [{iid}] {incidents[iid]['summary']}")

    print("\n=== Hybrid search + Gemini re-ranking (Week 5 upgrade) ===")
    hybrid_results = hybrid_retrieve(test_query)
    for r in hybrid_results:
        print(f"  [{r['incident_id']}] {r['summary']}")
```

4. Save the file.

**Run it:**
```bash
cd ../rag
python hybrid_retrieval.py
```

**Expected output (illustrative — your exact ranking may vary slightly):**
```
=== Semantic search only (Week 2 baseline) ===
  [H002] A billing system error caused a subset of customers to be charged twice...
  [H015] Customers were billed twice in the same billing cycle due to a race condition...
  [H009] Duplicate charges appeared on customer invoices following a batch billing job retry.

=== Hybrid search + Gemini re-ranking (Week 5 upgrade) ===
  [H015] Customers were billed twice in the same billing cycle due to a race condition...
  [H002] A billing system error caused a subset of customers to be charged twice...
  [H009] Duplicate charges appeared on customer invoices following a batch billing job retry.
```

**How to verify success:** Both sections return relevant billing-related incidents (`H002`, `H009`, `H015`). The value of the hybrid + re-ranking step becomes more visible on trickier or more ambiguous queries — try changing `test_query` to something more indirect, like `"weird stuff happening with people's monthly payments"`, and compare how each method handles vaguer phrasing.

**Common beginner mistake:** Running this from the wrong folder — it expects `embeddings_output.json` to exist in the current directory, which was created by Week 2's `generate_embeddings.py` inside the `rag` folder. If you get a `FileNotFoundError`, confirm you're running this from inside `rag`, and that Week 2's file is still there.

---

## Step 5.15 — Upgrade the Escalation Preparation Agent

**Goal:** Wire the Drive, Gmail, Chat tools and the new hybrid RAG retrieval into the Escalation Preparation Agent, replacing Week 4's "pass all 15 incidents" shortcut with proper targeted retrieval.

**Exact steps:**
1. Open `agents/escalation_preparation_agent.py`.
2. At the top of the file, find these lines:
```python
from google.cloud import firestore, bigquery
```
3. Replace that single line with:
```python
import sys
from google.cloud import firestore

sys.path.append("../rag")
sys.path.append("../integrations")

from hybrid_retrieval import hybrid_retrieve
from drive_tool import get_drive_doc
from gmail_tool import draft_gmail
from chat_tool import post_chat_alert
```
4. Find this line further down:
```python
bq_client = bigquery.Client(project=PROJECT_ID)
```
Delete it entirely (we no longer query BigQuery directly here — `hybrid_retrieve` handles historical context now).
5. Find the entire `get_all_historical_incidents` function and delete it completely.
6. Find this line inside `prepare_escalation`:
```python
    historical_context = get_all_historical_incidents()
```
Replace it with:
```python
    top_matches = hybrid_retrieve(cluster["summary"])
    historical_context = "\n".join(
        f"- {m['summary']} (Outcome: {m['outcome']})" for m in top_matches
    )

    runbook_excerpt = get_drive_doc("Runbook")
```
7. Find the `prompt = REPORT_PROMPT_TEMPLATE.format(...)` block and add one new argument, `runbook_excerpt=runbook_excerpt[:500],` — the full updated block should look like:
```python
    prompt = REPORT_PROMPT_TEMPLATE.format(
        summary=cluster["summary"],
        ticket_count=cluster["ticket_count"],
        impact_score=cluster["impact_score"],
        severity=cluster["severity"],
        rationale=cluster.get("scoring_rationale", ""),
        historical_context=historical_context,
        runbook_excerpt=runbook_excerpt[:500],
    )
```
8. Now update `REPORT_PROMPT_TEMPLATE` near the top of the file to include the runbook excerpt. Replace the existing template with:
```python
REPORT_PROMPT_TEMPLATE = """
You are preparing a business escalation report for a human reviewer.

Problem cluster summary: {summary}
Number of affected tickets: {ticket_count}
Impact score: {impact_score}/100
Severity: {severity}
Scoring rationale: {rationale}

Relevant historical incidents:
{historical_context}

Relevant internal runbook excerpt (use only if genuinely applicable):
{runbook_excerpt}

Write a short escalation report in Markdown, under 200 words, with these sections:
## Problem
## Business Impact
## Recommended Action

Be factual and concise. Reference the runbook or historical incidents only if truly relevant.
"""
```
9. Finally, find the end of the `prepare_escalation` function, where `db.collection("escalations").document(cluster_id).set(escalation_doc)` is called. Just **before** that line, add:
```python
    gmail_draft_id = draft_gmail(
        to_email="stakeholder@example.com",
        subject=f"[EPC Platform] Escalation: {cluster['summary'][:60]}",
        body=report_markdown,
    )

    chat_alert_text = f"🚨 New Escalation ({cluster['severity']}): {cluster['summary']} — Impact Score: {cluster['impact_score']}/100"
    post_chat_alert(chat_alert_text)
```
10. Update the `escalation_doc` dictionary (just above the `.set()` call) to also save these two new pieces of information:
```python
    escalation_doc = {
        "cluster_id": cluster_id,
        "report_markdown": report_markdown,
        "severity": cluster["severity"],
        "impact_score": cluster["impact_score"],
        "created_at": datetime.now(timezone.utc).isoformat(),
        "review_status": "pending",
        "gmail_draft_id": gmail_draft_id,
        "chat_message": chat_alert_text,
    }
```
11. Save the file.

**How to verify success:** Read through the file once top to bottom — confirm there are no leftover references to `bigquery` or `get_all_historical_incidents` anywhere.

---

## Step 5.16 — Run the Upgraded Agent End-to-End

**Exact steps:**
1. In the terminal, navigate to the `agents` folder: `cd ../agents`
2. Run:
```bash
python escalation_preparation_agent.py
```

**Expected output:**
```
C001: escalation report created.
C002: escalation report created.
C003: below threshold (45), skipping escalation.
...
```

**How to verify success:**
1. In Firestore, open the `escalations` collection, open `C001`.
2. Confirm it now has two new fields: `gmail_draft_id` and `chat_message`.
3. Read `report_markdown` — confirm it references specific historical incident details or the runbook, not a generic list of all 15 incidents like Week 4 produced.
4. Go to `https://mail.google.com` → **Drafts** — confirm a new draft exists referencing this escalation.
5. Go to your `EPC Platform Alerts` Chat space — confirm a new alert message appeared.

**Common beginner mistake:** Forgetting that this script re-runs against **all** clusters each time, which will create duplicate Gmail drafts and duplicate Chat messages for clusters you've already escalated before. This is fine for testing, but be aware — in a real system, we'd only prepare an escalation once per cluster, which is exactly the kind of business rule Week 6's evaluation and monitoring work will help you check for.

---

## Step 5.17 — Push Your Work to GitHub

**Exact steps:**
```bash
cd ..
git add .
git commit -m "Week 5: Drive/Gmail/Chat integrations, MCP server, and hybrid RAG re-ranking complete"
git push
```

**How to verify success:** Confirm `integrations/` and the updated `rag/hybrid_retrieval.py` and `agents/escalation_preparation_agent.py` appear on GitHub. Confirm `oauth_client_secret.json` and `token.json` are **not** present.

---

# WEEK 5 COMPLETION CHECKLIST

- [ ] Google Drive API and Gmail API both show as enabled in the console.
- [ ] OAuth consent screen is configured with both required scopes and my email listed as a test user.
- [ ] `oauth_client_secret.json` and `token.json` both exist locally but are absent from GitHub.
- [ ] My `EPC Platform Reference Docs` Drive folder contains exactly 3 documents with the exact titles specified.
- [ ] `integrations/drive_tool.py` successfully retrieves and prints the Runbook document's text.
- [ ] `integrations/gmail_tool.py` successfully creates a Gmail draft (visible in Drafts, not Sent).
- [ ] My Google Chat space `EPC Platform Alerts` has a working webhook, and `integrations/chat_tool.py` successfully posts a message to it.
- [ ] `integrations/mcp_server.py` exists and defines all 3 tools using `@mcp.tool()`.
- [ ] `integrations/test_mcp_client.py` successfully lists all 3 tools and calls one of them through the real MCP protocol.
- [ ] `rag/hybrid_retrieval.py` runs successfully and shows both a semantic-only result set and a hybrid + re-ranked result set.
- [ ] `agents/escalation_preparation_agent.py` has been fully upgraded — no remaining references to BigQuery or the old `get_all_historical_incidents` function.
- [ ] Running the upgraded escalation agent produces escalation documents in Firestore with new `gmail_draft_id` and `chat_message` fields.
- [ ] I pushed all Week 5 code to GitHub, and confirmed no secret files were uploaded.

Once every box is checked, Week 5 is complete. Let me know when you're ready for Week 6 — the final week.
