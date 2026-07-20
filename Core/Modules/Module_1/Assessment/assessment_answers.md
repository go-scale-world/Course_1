# Week 1 Assessment — Answer Key
### Building Autonomous AI Agents · Running Project: EPC Agent

---

## Section A — Knowledge Check

**A1. Answer: B** — A) model size is irrelevant to autonomy; C) structured data isn't the defining trait; D) connectivity is unrelated to the concept.

**A2. Answer: B** — A) schemas don't produce creativity, they constrain it; C) clustering is a Week 2 capability; D) storage is a Week 3 capability.

**A3. Answer: B** — A) cost isn't the reason given; C) enterprise systems can handle creative output, that's not the issue; D) preference isn't the argument made.

**A4. Answer: C** — A) appearance isn't the goal; B) the opposite is true, structure reduces variance; D) token count isn't the stated purpose.

**A5. Answer: B** — A) temperature doesn't affect speed; C) clustering isn't controlled by temperature; D) JSON mode is a separate setting entirely.

**A6. Answer: C** — A) Pydantic never auto-corrects invalid values; B) it fails loudly, it never silently allows bad data through; D) there's no manual-fix step in this flow.

**A7. Answer: B** — A) that describes persistent memory, not working memory; C) no caching mechanism exists in Week 1; D) embeddings/vector indexes arrive in Week 2.

**A8. Answer: B** — A) reasoning doesn't affect response speed; C) temperature and reasoning style are unrelated settings; D) the schema still applies regardless of reasoning.

**A9. Answer: B** — A) they are distinct, complementary techniques; C) structured outputs apply to text/JSON here, not images; D) both matter for reliable machine consumption, not a human/machine split.

**A10. Answer: B** — A) the Firestore write doesn't exist until Week 3; C) Pub/Sub triggering is a Week 4 capability; D) deployment is a Week 3 capability.

**Difficulty mix:** A1, A2, A3, A6, A8, A10 = straightforward recall. A4, A5, A7, A9 = require distinguishing closely related concepts (structured prompting vs. general prompting, temperature vs. schema validation, working memory vs. persistent memory, prompt engineering vs. structured outputs).

---

## Section B — Applied Skill Check

**B1. Expected answer:**
The missing section is **FORMAT**. Without an explicit format specification (the exact field names and expected structure), Gemini has nothing to anchor its output shape to, so field names, ordering, or formatting can vary between runs — exactly the zero-shot inconsistency problem demonstrated in the lecture. CONTEXT is also technically absent, but FORMAT is the section whose removal most directly breaks consistency of the *output structure* itself.

**B2. Expected answer:**
The `urgency` field fails validation. The schema requires `urgency` to be exactly one of `"Low"`, `"Medium"`, `"High"`, or `"Critical"` (a `Literal` type in Pydantic). `"Really Urgent"` is not one of the four allowed values, so `TicketExtraction(**data)` raises a `ValidationError` even though the JSON itself is syntactically valid.

**B3. Expected answer:**
`region` should be extracted as **"Europe"**. The field defaults to `"Unspecified"` only when no region is mentioned in the ticket text — this ticket explicitly names a region, so the structured prompt's instruction ("region: value or Unspecified") requires the actual mentioned value to be extracted rather than the default.

**B4. Expected answer:**
```python
generation_config={"response_mime_type": "application/json"}
```
This tells Gemini to return valid JSON directly, which is the mechanism (alongside the FORMAT section in the prompt) that makes the response reliably parseable and consistent.

**B5. Expected answer:**
Using the **structured prompt (Role/Context/Task/Format)** at **low temperature**, rather than a zero-shot or loosely-specified prompt, is the fix. The lecture's Week 1 experiments showed that structured prompting with explicit field definitions produces matching `product_area` values across differently-worded tickets describing the same problem — which matters directly because Week 2's clustering agent depends on this consistency to group T002 and T003 together.

---

## Section C — Holistic Scope Check

**C1. Model answer:**
- The agent extracts structured fields (`product_area: Mobile App`, `issue_type` relating to upload/crash, `urgency`, `region: Unspecified`, `summary`) from the text that's actually present, using the structured prompt.
- The Week 1 agent has **no persistent memory** — it only has working memory scoped to this single call, so it cannot verify or retrieve "last week's ticket."
- The phrase "same as last week's ticket" should be reflected faithfully in the `summary` field as reported context, not resolved into an actual link to a specific past ticket.
- The agent must not invent or assume a specific past ticket ID or connection it cannot verify — that would be a hallucination.
- This limitation is intentional at this stage of the course; Week 2 (retrieval) and Week 3 (persistent memory) are what will actually make "same as last week" checkable.

**C2. Model answer:**
- `json.loads()` succeeds because the response is syntactically valid JSON.
- `TicketExtraction(**parsed_json)` then fails, because `urgency` is a required field and it's missing entirely — Pydantic raises a `ValidationError`.
- The agent's `extract_ticket_meaning()` function raises a `ValueError` with a clear message describing the missing field, rather than silently defaulting or guessing an urgency value.
- In a batch run, this ticket should be logged as failed and excluded from the successful results, not silently dropped or passed downstream with an invented value.
- Correct behavior throughout: fail loudly and traceably rather than quietly producing an incomplete or fabricated result.

**C3. Model answer:**
- The extraction prompt must still resolve to **one** structured decision — the schema does not allow two simultaneous values, so ambiguity has to be resolved, not passed through as-is.
- **Chain-of-thought prompting** is more appropriate here than a direct/zero-shot prompt, since it forces the model to reason through why one category fits better before committing to an answer.
- The schema still constrains the output to a single valid value from an allowed set, which forces a real decision rather than a vague or blended answer.
- Low temperature keeps this decision reproducible if the same filing is reprocessed later.
- If the ambiguity is genuinely unresolvable from the text alone, that's a signal for a future guardrail or human-review step (not yet built in Week 1) — the agent should not be designed to silently guess when a real decision can't be justified.

**C4. Model answer:**
- First check the two failed tickets' raw output directly — determine whether the failure was malformed JSON (a parsing failure) or a schema mismatch (e.g., an invalid `urgency` value).
- Confirm temperature was actually set to `0.0` for all 10 runs — an inconsistent or accidentally-elevated temperature setting could explain why only some tickets failed.
- Compare the exact prompt used against the structured Role/Context/Task/Format template — check for any drift or missing section that could have caused less reliable output on those two tickets.
- Re-run the two failed tickets individually to determine whether the failure is reproducible (a real prompt weakness) or was a one-off inconsistency.
- Document the failure pattern rather than simply re-running until it happens to pass — a documented, reproducible failure is far more useful for improving the prompt than a silently "fixed" one.

**C5. Model answer:**
- The agent reliably extracts clean, structured fields from **one ticket at a time** — that capability genuinely works and is the actual achievement of Week 1.
- Week 1 has **no persistent storage** — every extraction happens in isolation, and nothing about a previous ticket is remembered or retained afterward.
- Without stored history, there is no way to count, aggregate, or look back across past tickets — answering "how many this month" requires historical data the agent was never given anywhere to keep.
- This is not a flaw in the current build; it's an intentional boundary consistent with this stage of the course — Firestore and BigQuery, introduced in Week 3, are specifically what will make aggregation and history possible.
- Correct expectation-setting for the stakeholder: today's agent is the reliable foundation (consistent understanding of each individual ticket), not yet the analytics layer — that capability is coming, deliberately, in a later week.
