# Week 2 — Lab Notes
### Enterprise Problem Consolidation (EPC) Platform

**What we built this week:** The platform's memory for past problems. We built a RAG retrieval pipeline that finds similar past incidents for any new ticket, and a clustering agent that groups today's tickets by the underlying issue they describe.

---

**Step 2.1 — Verify Vertex AI Vector Search Is Available**
We confirmed the Vector Search feature was accessible under our existing Vertex AI API from Week 1, with no separate activation needed.

**Step 2.2 — Create a Cloud Storage Bucket**
We created a storage bucket in the cloud to temporarily hold our embedding files before Vector Search reads them and builds its index.

**Step 2.3 — Grant Storage Permissions**
We gave our service account permission to write files into the new storage bucket, so our scripts can upload data to it.

**Step 2.4 — Install New Python Libraries**
We installed the additional Python packages this week's code needs for embeddings and vector search.

**Step 2.5 — Expand Sample Tickets to 20**
We added 10 new tickets to last week's file, bringing the total to 20 — enough variety to form several believable clusters.

**Step 2.6 — Create Historical Incidents File**
We created an archive of 15 already-resolved past incidents. This is the dataset our RAG pipeline will search through for relevant history.

**Step 2.7 — Create the Embeddings Generation Script**
We wrote a script that converts each historical incident into a numeric vector (an embedding) and uploads it in the format Vector Search requires.

**Step 2.8 — Create the Vector Search Index**
We told Vertex AI to build an actual searchable index from our uploaded embeddings — the index doesn't exist until it's explicitly created.

**Step 2.9 — Create an Endpoint and Deploy the Index**
We created a live serving endpoint and deployed our index to it, making it possible to actually query it with real search requests.

**Step 2.10 — Verify Deployment**
We visually confirmed in the console that the index was live and ready before writing any query code against it.

**Step 2.11 — Create the RAG Retrieval Query Script**
We wrote a script that takes a new ticket, converts it to an embedding, and asks the deployed index for the most similar historical incidents.

**Step 2.12 — Create the BM25 Keyword Fallback Script**
We built a simple keyword-matching backup system, used as a second opinion whenever semantic similarity between two tickets looks borderline.

**Step 2.13 — Create the Ticket Clustering Agent**
We wrote the agent that groups today's 20 live tickets into clusters of related problems, using semantic similarity first and the keyword fallback for edge cases.

**Step 2.14 — Save the Clustering Results**
We saved the cluster output to a file so future weeks (impact scoring, escalation) can read and build on these results.

**Step 2.15 — Undeploy the Index to Stop Charges**
We shut down the live Vector Search endpoint once we confirmed it worked, since it bills hourly while deployed — an important cost-control step.

**Step 2.16 — Push Work to GitHub**
We committed and pushed all our Week 2 code and results to GitHub, keeping our project history up to date.

---

## ✅ Week 2 Outcome
By the end of the week, the platform could recognize when different tickets describe the same underlying problem and group them into clusters, while also being able to pull up relevant historical incidents for any new ticket.
