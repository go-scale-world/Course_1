# Week 4 Assessment — Divide the Problem, Then Make the Division Intelligent
### Running Project: Enterprise Problem Consolidation (EPC) Agent

---

## Section A — Knowledge Check (10 MCQs)

**A1.** What problem does Task Decomposition solve in the EPC Platform?
A) It makes the code run faster
B) It splits an overloaded single agent's responsibilities into focused specialists, each owning one job
C) It removes the need for an orchestrator
D) It replaces Firestore with BigQuery

**A2.** In the four-agent EPC pipeline, what is the orchestrator's actual job?
A) It performs the extraction, clustering, scoring, and escalation itself
B) It coordinates which specialized agent runs next, without doing their work itself
C) It stores the historical archive
D) It replaces the need for guardrails

**A3.** What does the Handoff Pattern specifically check between two agents?
A) Whether the network connection is fast enough
B) Whether the previous agent's output is actually valid before the next agent starts using it
C) Whether the human has logged in
D) Whether the ticket has a region field

**A4.** How does the Human Supervision Dashboard introduced in Week 4 differ from the single-output approval panel built in Week 3?
A) It's identical, just renamed
B) It supervises the health and status of the entire multi-agent workflow, not just one agent's output
C) It removes the need for human approval entirely
D) It only works for the Escalation Agent

**A5.** Why does splitting one overloaded agent into four specialists tend to improve output quality?
A) More agents always means more accuracy, regardless of what they do
B) Being excellent at extraction and being excellent at business-impact scoring require genuinely different judgment, which is hard for one agent to master at once
C) Specialist agents use a different, more powerful LLM
D) Specialization removes the need for validation

**A6.** What question does the Routing Pattern answer?
A) How does one agent hand off work to another?
B) Should every ticket follow the exact same path, or should the path depend on the ticket's actual severity?
C) How do we store historical incidents?
D) How does the agent choose which tool to call?

**A7.** What problem does the Parallelization Pattern solve?
A) It improves the accuracy of a single ticket's extraction
B) It allows independent tickets to be processed at the same time, instead of queuing one behind another
C) It removes the need for an orchestrator
D) It validates handoffs between agents

**A8.** In the Reflection Pattern, what does the Escalation Agent do differently compared to Week 3's version?
A) It skips validation entirely
B) It drafts, critiques its own draft, improves it, and only then sends the final version
C) It sends the first draft immediately, without any review
D) It asks a human to write the entire report

**A9.** How does the Plan-and-Solve pattern differ from the fixed four-step pipeline built earlier in the week?
A) It removes Extract and Cluster from the pipeline
B) Instead of always running the same fixed sequence, the agent first generates a plan specific to how complex or ambiguous the actual ticket is
C) It only applies to critical-severity tickets
D) It eliminates the need for an orchestrator

**A10.** What is the key difference between Routing and Orchestration?
A) They are the same concept with different names
B) Routing decides which path a ticket takes based on its properties; Orchestration coordinates the actual execution sequence between agents
C) Routing only applies to healthcare tickets
D) Orchestration only runs after routing has failed

---

## Section B — Applied Skill Check (5 questions)

**B1.** Below is a simplified orchestrator. One line is missing between two agent calls — without it, a malformed extraction could reach the Cluster Agent unchecked. What line belongs there?
```python
def orchestrate(ticket):
    extraction = extract_agent.run(ticket)
    ______________________
    cluster = cluster_agent.run(extraction)
```

**B2.** A cluster has `severity: "Critical"`. According to the Routing Pattern, what path should this ticket take? What path would a cluster with `severity: "Low"` take instead?

**B3.** Complete the missing line so multiple independent tickets are processed at the same time, instead of one after another:
```python
async def process_batch(tickets):
    return await ______________________
```

**B4.** Complete the missing line so the Escalation Agent's reflection step actually checks its own draft before sending it:
```python
def reflect(draft):
    critique = ______________________
    return improve(draft, critique) if critique.has_issues else draft
```

**B5.** Below is a simplified orchestrator loop. One line is missing — without it, the dashboard has no visibility into how each agent is doing. What line belongs there?
```python
def orchestrate(ticket):
    for agent in [extract_agent, cluster_agent, score_agent, escalate_agent]:
        result = agent.run(ticket)
        ______________________
        validate_handoff(result)
        ticket = result
    return ticket
```

---

## Section C — Holistic Scope Check (5 questions)

**C1.** A retail team reuses this architecture. 300 order-related tickets arrive simultaneously during a flash sale, with mixed severities. Trace what should happen, combining routing, parallelization, and orchestration together.

**C2.** The Cluster Agent produces malformed output — its `cluster_id` field is missing — and the Score Agent is about to receive it. Trace what should happen, referencing the Handoff Pattern specifically.

**C3.** The Escalation Agent's reflection loop keeps critiquing and revising the same draft, never satisfied, for ten minutes straight on one ticket. Trace what's actually wrong here and how the system should be designed to prevent it.

**C4.** A legal team reuses this architecture. A filing arrives that's unusually ambiguous — the existing fixed pipeline steps don't quite fit it. Trace how the Plan-and-Solve pattern should handle this differently than the standard four-agent flow.

**C5.** A new engineer asks why the orchestrator and the human supervision dashboard are two separate things that both need to exist. Explain the distinction, and what would break if the platform only had one of them.
