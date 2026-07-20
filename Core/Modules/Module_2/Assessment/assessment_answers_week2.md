# Week 2 Assessment — Answer Key
### Retrieve Before You Decide, Decide Before You Act · Running Project: EPC Agent

---

## Section A — Knowledge Check

**A1. Answer: B** — A) speed isn't the stated benefit; C) summarization isn't RAG's defining purpose; D) JSON generation is a structured-output concern, not RAG.

**A2. Answer: B** — A) they are related but not identical, this is the exact distinction the lecture makes; C) RAG works with text/tickets here, not images; D) embeddings are still required, Vector Search doesn't remove them.

**A3. Answer: B** — A) embeddings aren't primarily for compression; C) no translation occurs; D) embeddings aren't a summarization mechanism.

**A4. Answer: B** — A) throughput isn't controlled by this constant; C) there's no cap on cluster count; D) temperature is a separate model parameter entirely.

**A5. Answer: B** — A) BM25 runs as a fallback, not first; C) BM25 supplements, not replaces, the vector index; D) BM25 has nothing to do with summary generation.

**A6. Answer: B** — A) response length is unrelated; C) retrieval is RAG's job, not function calling's; D) function calling constrains output, it doesn't increase creativity.

**A7. Answer: B** — A) they are directly related, not unrelated; C) Tool Use & Integration applies to any registered tool, not just Gmail/Chat; D) choosing and executing a tool still requires reasoning.

**A8. Answer: B** — A) ReAct doesn't skip validation; C) embedding speed is unrelated to ReAct; D) clustering isn't what ReAct governs.

**A9. Answer: B** — A) the LLM itself doesn't need to change; C) there's no fixed frequency limit described; D) the distinction is central to this week's lecture.

**A10. Answer: B** — A) embeddings are stored via the vector index, not Pub/Sub; C) scoring is a Week 4 agent's job; D) Pub/Sub triggers Cloud Workflows, it doesn't replace it.

**Difficulty mix:** A1, A3, A4, A5, A6, A10 = straightforward recall. A2, A7, A8, A9 = require distinguishing closely related concepts (RAG vs. Vector Search, Tool Calling vs. Tool Use & Integration, ReAct vs. basic tool calling, prompted vs. autonomous execution).

---

## Section B — Applied Skill Check

**B1. Expected answer:**
```python
embedding_model.get_embeddings([ticket["raw_text"]])[0].values
```
The raw ticket text must be converted into an embedding before it can be numerically compared to the cluster's representative embedding — `cosine_similarity` operates on vectors, not raw text.

**B2. Expected answer:**
**H001** (score 0.87) is the strongest match. A score of 0.34 for H015 suggests it is only weakly related — likely not genuinely relevant context, and probably shouldn't be surfaced to the agent as meaningful supporting evidence.

**B3. Expected answer:**
0.68 is below the 0.75 threshold, so on semantic similarity alone the ticket would **not** join Cluster C002. However, this falls into a genuinely borderline range — this is exactly why **BM25 keyword matching** exists as a fallback check, to confirm or override a borderline semantic result before deciding to create a brand-new cluster.

**B4. Expected answer:**
```python
available_tools[tool_name](**args)
```
This looks up the actual tool function the model selected and calls it with the arguments the model provided — without this line, the agent would decide on a tool but never actually execute it.

**B5. Expected answer:**
The call will fail. `cluster_id` is a required argument for `update_cluster` and is missing from the arguments object. This should be caught by argument validation before execution — the same principle as Week 1's schema validation, now applied to tool call arguments instead of ticket extraction fields.

---

## Section C — Holistic Scope Check

**C1. Model answer:**
- The new filing gets embedded, and both semantic similarity and BM25 keyword search run against the historical index.
- A strong BM25 (keyword) match combined with a weak semantic similarity score is a warning sign — it means the two filings share vocabulary but not actual meaning.
- The threshold decision should treat semantic similarity as the primary signal — a keyword overlap alone should not be sufficient to merge two genuinely unrelated filings into one cluster.
- This is exactly why hybrid retrieval exists: either signal alone can be misled, but requiring semantic similarity above threshold as the deciding factor guards against surface-level, keyword-only false matches.
- Correct outcome: despite shared keywords, the filing should most likely create its own new cluster, because true topical similarity didn't actually match.

**C2. Model answer:**
- The `update_cluster` tool call timing out is a **Tool Execution Failure**, not a reasoning failure.
- The system should not silently continue as though the update succeeded.
- Following the ReAct pattern, the timeout result should be observed, and the agent should reason again based on that failure, rather than blindly proceeding to the next planned step.
- A retry with backoff is the appropriate first response, consistent with the lecture's failure-handling approach.
- If retries are exhausted, the agent should fall back to a safe, logged state (e.g., flag the ticket for manual review) rather than assuming the cluster update actually happened.

**C3. Model answer:**
- Both tickets query the vector index for a match before either one has finished being written back to the index.
- Each ticket independently sees "no sufficiently similar match yet" and creates its own new cluster — this is a classic race condition.
- This is directly called out in the lecture as **Duplicate Cluster Creation**.
- Retrieval alone cannot prevent this, because at the exact moment both checks run, the index genuinely doesn't yet contain the other ticket.
- A periodic merge check — comparing newly created clusters against each other after the fact — is the mechanism responsible for catching and correcting this, rather than trying to prevent it entirely at query time.

**C4. Model answer:**
- The new log entry is embedded and retrieved against historical incidents and known false-positive patterns through RAG.
- If retrieval returns mixed evidence — some matches suggesting a real attack pattern, others suggesting a known false positive — the agent should not pick a side arbitrarily based on the single top similarity score.
- Using a ReAct-style loop, the agent can choose a tool to gather additional context (for example, looking up related system logs) before committing to a classification.
- Only after that additional reasoning and tool-use step should the agent decide whether to escalate (lock the account, alert the security team) or log the entry and continue monitoring.
- The key lesson: retrieval supplies evidence, but the agent still needs an explicit reasoning step to interpret conflicting evidence — it shouldn't just trust the highest similarity score blindly.

**C5. Model answer:**
- Two nearly-simultaneous tickets about the same emerging issue can each independently query the vector index before the other has been written back to it.
- Both see "no sufficiently similar match yet" and each creates its own new cluster — this is the exact Duplicate Cluster Creation race condition covered in the lecture.
- This isn't a flaw in the similarity math itself — the embeddings and threshold logic worked correctly at the exact moment each check ran; it's purely a timing issue.
- The platform is deliberately designed to catch this after the fact through a periodic merge check, rather than trying to eliminate the possibility entirely, which would require locking or serializing every single write and add unnecessary complexity for a genuinely rare edge case.
- Correct expectation-setting: this will happen occasionally under real ticket volume, and the platform is designed to detect and correct it, not to make it impossible.
