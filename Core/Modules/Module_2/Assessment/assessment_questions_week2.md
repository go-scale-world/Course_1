# Week 2 Assessment — Retrieve Before You Decide, Decide Before You Act
### Running Project: Enterprise Problem Consolidation (EPC) Agent

---

## Section A — Knowledge Check (10 MCQs)

**A1.** What is the core capability RAG adds to an agent that pure LLM reasoning cannot provide alone?
A) Faster response times
B) The ability to ground reasoning in the company's own historical data
C) Ability to summarize text
D) Ability to generate JSON

**A2.** Which statement correctly distinguishes RAG from Vector Search?
A) RAG and Vector Search are the same thing
B) Vector Search is the retrieval engine; RAG is the broader capability of retrieving before reasoning
C) RAG only works with images
D) Vector Search replaces the need for embeddings

**A3.** What do embeddings actually do to text?
A) They compress it for storage
B) They convert it into a list of numbers where similar meanings produce similar numbers
C) They translate it into another language
D) They summarize it into one sentence

**A4.** In the EPC Agent's Ticket Clustering Agent, what does the `SIMILARITY_THRESHOLD` constant (0.75) actually control?
A) How many tickets can be processed per minute
B) The cutoff score that decides whether a new ticket joins an existing cluster or starts a new one
C) The maximum number of clusters allowed
D) The temperature setting for the embedding model

**A5.** Why does the Ticket Clustering Agent use BM25 as a fallback alongside semantic similarity?
A) BM25 is faster than embeddings, so it always runs first
B) BM25 catches keyword-based matches for borderline cases where semantic similarity alone is inconclusive
C) BM25 replaces the need for a vector index entirely
D) BM25 is only used for generating summaries

**A6.** What is Function Calling's core purpose in an agentic system?
A) To make the model generate longer responses
B) To convert a model's conclusion into a structured instruction that actually triggers a real action
C) To retrieve historical documents
D) To increase the model's creativity

**A7.** Which best distinguishes Tool Calling from Tool Use & Integration?
A) They are unrelated concepts
B) Tool Calling is the mechanism of turning a decision into an action; Tool Use & Integration is choosing correctly among several available tools
C) Tool Use & Integration only applies to Gmail and Chat
D) Tool Calling requires no reasoning

**A8.** What does the ReAct pattern actually add on top of basic tool calling?
A) A way to skip validation steps
B) An alternating loop of reasoning, acting, and observing, repeating until the task is genuinely finished
C) A faster embedding model
D) Automatic clustering of tickets

**A9.** What is the key difference between a tool-calling agent triggered by a human prompt and an autonomous workflow?
A) Autonomous workflows use a different LLM
B) An autonomous workflow starts itself from a real-world event, with no human prompt involved
C) Autonomous workflows can only run once per day
D) There is no meaningful difference

**A10.** In the EPC Agent's autonomous pipeline, what role does Pub/Sub play?
A) It stores ticket embeddings
B) It triggers the entire pipeline automatically the moment a new ticket is published, with no manual step
C) It scores ticket urgency
D) It replaces the need for Cloud Workflows

---

## Section B — Applied Skill Check (5 questions)

**B1.** Below is a simplified snippet from the Ticket Clustering Agent. One line is missing before the similarity comparison can happen. What line belongs there?
```python
def cosine_similarity(vec_a, vec_b):
    a = np.array(vec_a)
    b = np.array(vec_b)
    return float(np.dot(a, b) / (np.linalg.norm(a) * np.linalg.norm(b)))

for ticket in tickets:
    ticket_embedding = ______________________
    similarity = cosine_similarity(ticket_embedding, cluster["representative_embedding"])
```

**B2.** The RAG retrieval script returns these three matches for a new ticket about Android photo uploads:
```
[H001] score=0.87
[H007] score=0.61
[H015] score=0.34
```
Which incident should be treated as the strongest match, and what does a score of 0.34 suggest about H015's relevance?

**B3.** A new ticket's embedding produces a cosine similarity of 0.68 against Cluster C002's representative ticket. `SIMILARITY_THRESHOLD = 0.75`. Using only the semantic similarity check, what happens to this ticket — and what mechanism from the lecture exists specifically to double-check a case like this?

**B4.** Complete the missing line so the agent actually executes the tool it chose, not just decides on it:
```python
def choose_and_run_tool(conclusion, available_tools):
    tool_name, args = model_pick_tool(conclusion, available_tools)
    return ______________________
```

**B5.** A function call arrives for the `update_cluster` tool:
```json
{"tool": "update_cluster", "arguments": {"impact_score": 78}}
```
The `update_cluster` tool requires both `cluster_id` and `impact_score`. What will happen when this call is executed, and why?

---

## Section C — Holistic Scope Check (5 questions)

**C1.** A legal team reuses this Week 2 architecture for filing intake. A new filing arrives that shares several keywords with a past filing, but is actually about a completely unrelated matter. Trace what should happen, combining semantic search, BM25, and the threshold decision.

**C2.** A ticket is mid-way through a ReAct loop when the `update_cluster` tool call times out. Trace what should happen next, step by step, referencing the ReAct pattern and failure recovery.

**C3.** Two tickets describing the same brand-new, never-before-seen problem arrive within seconds of each other. Trace what's likely to go wrong, why it happens, and what part of the system is responsible for catching it afterward.

**C4.** A cybersecurity team reuses this architecture. A new log entry arrives that retrieval finds ambiguous — some historical matches suggest a real attack, others suggest a known false positive. Trace the full decision process, end to end.

**C5.** A stakeholder asks why the platform occasionally creates two separate clusters for what turns out to be the same emerging issue. Trace a full, accurate explanation, referencing the specific mechanisms responsible.
