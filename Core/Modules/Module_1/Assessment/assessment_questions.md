# Week 1 Assessment — Building Autonomous AI Agents
### Running Project: Enterprise Problem Consolidation (EPC) Agent

---

## Section A — Knowledge Check (10 MCQs)

**A1.** What fundamentally distinguishes an autonomous agent from a standard chatbot?
A) It uses a larger language model
B) It decides and acts without being individually asked each time
C) It only works with structured data
D) It requires no internet connection

**A2.** In the EPC Agent's Week 1 build, what does the `TicketExtraction` schema exist to guarantee?
A) That Gemini always gives a creative answer
B) That every extraction has exactly 5 fields in a consistent, typed shape
C) That tickets are automatically clustered
D) That tickets are stored in Firestore

**A3.** Why does Enterprise AI thinking prioritize consistency across thousands of runs over one impressive answer?
A) Consistency is cheaper to compute
B) A mistake that repeats at scale becomes a compounding, expensive failure
C) Enterprise systems can't handle creative output
D) Users prefer boring answers

**A4.** What is the primary purpose of using a Role / Context / Task / Format structure in a prompt?
A) To make the prompt look more professional
B) To produce more creative, varied responses
C) To make Gemini's output format more consistent and predictable
D) To reduce the number of tokens used

**A5.** In the temperature experiment comparing ticket T002 and T003, what did lowering temperature to 0.0 demonstrate?
A) Lower temperature makes responses faster
B) Lower temperature produces more consistent extraction across repeated runs
C) Lower temperature makes tickets cluster automatically
D) Lower temperature disables JSON output

**A6.** What does schema validation (via Pydantic) actually do when Gemini returns an invalid `urgency` value like `"Very High"`?
A) It automatically corrects the value to the nearest valid option
B) It logs a warning but allows the ticket through
C) It rejects the extraction with a validation error
D) It asks the user to manually fix it

**A7.** Which best describes "working memory" as used in the Week 1 agent?
A) A permanent database storing all past tickets
B) Only what the agent needs during one single task, with no memory across tickets
C) A cache of the last 100 tickets processed
D) A vector index of ticket embeddings

**A8.** Why does chain-of-thought prompting help on ambiguous tickets, like one mentioning both "mobile app" and "dashboard"?
A) It makes the model respond faster
B) It forces the model to reason through the ambiguity before committing to an answer
C) It automatically increases the temperature
D) It skips the need for a schema

**A9.** What is the key difference between "prompt engineering" and "structured outputs" in this lecture?
A) They are the same technique with different names
B) Prompt engineering shapes what the model is asked; structured outputs enforce what shape the response must take
C) Structured outputs only apply to images
D) Prompt engineering is for humans, structured outputs are for machines only

**A10.** In the "Perceive → Understand → Decide → Act → Remember" loop, what does "Understand" correspond to in the EPC Agent's code?
A) The Firestore write
B) The Gemini call using the structured extraction prompt
C) The Pub/Sub trigger
D) The Cloud Run deployment

---

## Section B — Applied Skill Check (5 questions)

**B1.** Below is a prompt template with one required section removed. What section is missing, and why does removing it hurt consistency?
```
ROLE: You are a support ticket analyst at an enterprise software company.
TASK: Extract exactly 5 fields: product_area, issue_type, urgency, region, summary.
TICKET: {ticket_text}
```

**B2.** Given this extraction, identify which single field would fail Pydantic validation, and why:
```json
{
  "product_area": "Billing",
  "issue_type": "Billing Error",
  "urgency": "Really Urgent",
  "region": "Unspecified",
  "summary": "Customer billed twice."
}
```

**B3.** Ticket: *"The mobile app is extremely slow when loading the dashboard in Europe."* Using the structured prompt, what should the `region` field be extracted as, and why is it not `"Unspecified"`?

**B4.** Complete the missing line so Gemini is asked to return consistently structured output:
```python
response = model.generate_content(
    prompt,
    ______________________
)
```

**B5.** Ticket T002 ("Cannot upload pictures, the app just freezes and closes") extracts `product_area: "Mobile App"`. Ticket T003 ("Photo upload fails every single time on my phone") extracts `product_area: "Photo Upload Feature"`. Both describe the same bug. Which single lecture concept, applied consistently, would most likely fix this mismatch?

---

## Section C — Holistic Scope Check (5 questions)

**C1.** A ticket arrives: *"guys the app is doing that thing again where photos don't upload, same as last week's ticket."* Trace what the Week 1 agent **can** and **cannot** do with the "same as last week" reference, and why.

**C2.** Gemini returns the following for a ticket, but `urgency` is missing entirely from the JSON:
```json
{"product_area": "Login", "issue_type": "Access Issue", "region": "Unspecified", "summary": "User cannot log in."}
```
Trace the full sequence of what happens, step by step, from the moment this response arrives at the agent.

**C3.** A legal document intake team wants to reuse this Week 1 architecture. A filing arrives where two `product_area` values are equally plausible: `"Contract Review"` or `"Compliance Filing"`. Trace what should happen, and which Week 1 techniques are most relevant to resolving this well.

**C4.** A batch of 10 tickets is run through the extraction agent overnight. In the morning, 8 succeeded and 2 failed validation. Design the trace of what you'd check and do next, referencing consistency, temperature, and prompt design.

**C5.** You're asked to explain to a non-technical stakeholder why the Week 1 agent, despite being "smart," can't yet tell them "how many photo upload complaints we got this month." Trace a full, accurate explanation touching on memory, structure, and what's needed next.
