# Instructor Lecture Notes — Week 4
## Two Complete Learning Journeys: Divide the Problem, Then Make the Division Intelligent

**Running Project:** Enterprise Problem Consolidation (EPC) Agent

---
---

# S-TIER 1 — Multi-Agent Systems & Enterprise Orchestration

---

## STEP 1 — Definition

### Objective
Introduce multi-agent systems in the simplest language possible, creating curiosity without touching orchestration, handoffs, or code.

### Slide Content
> **Multi-Agent Systems is teaching several computers to share one job.**

### What To Say
Read that with me. Multi-Agent Systems is teaching several computers to share one job.

Over the last three weeks, we built one agent. It retrieves knowledge, it acts, it remembers, it runs safely in production. That's genuinely a lot for one agent to be good at. Today I want you to ask yourself a question. Is it actually a good idea for one agent to be excellent at every single one of those things at once? Or does it make more sense for different parts of the job to belong to different specialists?

That question is the entire subject of today's lecture.

---

## STEP 2 — Create the Need

### Objective
Establish why one agent handling every responsibility becomes a liability, using analogies, before returning to the EPC Agent.

### Slide Content
**Unresolved problem from last lecture:** Our agent thinks safely, remembers, and runs reliably in production. But it is still one agent, doing every job itself — extracting meaning, retrieving history, clustering, scoring, drafting escalations, all inside one tangled reasoning process.

| Without Multi-Agent Systems | With Multi-Agent Systems |
|---|---|
| One agent must be expert at every step | Each agent specializes in exactly one responsibility |
| A change to one job risks breaking every other job | Each agent can be improved independently |
| Every ticket bottlenecks through one tangled process | Specialized agents can be coordinated efficiently |
| Failure anywhere breaks the whole reasoning chain | A failure in one agent is isolated and handled at that handoff |

### What To Say
Here's the problem we've quietly built up over three weeks. Every week, we gave our one agent more responsibility. Understand the ticket. Retrieve history. Cluster it. Score its impact. Draft an escalation. Validate everything. That's five genuinely different jobs, all living inside one single reasoning process. And here's the risk. Being excellent at extracting meaning from messy text and being excellent at judging business impact require genuinely different kinds of judgment. Ask one mind to be great at both, every single time, and quality quietly starts to slip.

There's a second problem too. When every step lives inside one tangled process, you can't make clustering faster without touching scoring, because they're not separate pieces. They're all wound together inside one giant function.

Let me give you two pictures.

Imagine one single doctor in a hospital, expected to personally handle reception, triage, diagnosis, surgery, the pharmacy, and billing, for every single patient, because the hospital never hired any specialists. This is exactly what we cannot do.

Our one giant agent is that solo doctor. Technically capable of learning all of it, but stretched thin across responsibilities that genuinely deserve focused expertise.

Here's a second picture. Imagine a single chef in a restaurant kitchen who takes the order, chops the vegetables, cooks the meal, plates it, and serves it to the table, completely alone, for every single customer. A proper kitchen has a team, and each person masters their own station. This is exactly what we cannot do.

A multi-agent system is that proper kitchen. Not more people doing the same job. Each person owning a distinct piece of the outcome.

You'll see the same pattern elsewhere. A single AI trying to handle intake, triage, diagnosis, and billing all at once, versus a hospital where each is a distinct role, coordinated together. A single AI trying to detect fraud, assess risk, approve loans, and handle compliance reporting all at once, versus a bank where each of those is its own specialized team.

Now back to our own agent. Right now, one agent does everything. Extract, cluster, score, escalate. What if each of those became its own specialist, and something coordinated the handoffs between them?

---

## STEP 3 — Build the Core Idea

### Objective
Build intuition for specialization and coordination, without touching implementation.

### Slide Content
**Running Example:** The EPC Agent, evolving from one agent into four specialists: Extract, Cluster, Score, Escalate.

### What To Say
Let's build some intuition.

Picture a hospital emergency room. The intake nurse doesn't diagnose. The triage doctor doesn't perform surgery. The surgeon doesn't handle billing. Each person does their one job excellently, and the patient moves smoothly from one specialist to the next.

Here's a second picture. A relay race team. One runner doesn't try to run all four legs alone. Each runner trains specifically for their leg. And here's the part people miss. The handoff of the baton between runners is just as important as any individual runner's speed.

Think of an assembly line. One station attaches the wheels. The next station paints the car. The next station does the final inspection. Nobody does everything, but together, the car gets built. Or think of a newsroom. One reporter gathers facts. An editor shapes the story. A fact-checker verifies it before publishing. Three different jobs, one final article.

Here's something even simpler. A racing pit crew, where each person has exactly one job, tires, fuel, the jack, and the car is back on the track in seconds because nobody is doing everything alone. Or an assembly-line sandwich shop, where one person takes the order, another builds it, another wraps it, another handles the register.

So here's the idea to leave with. Multi-agent systems are not more chatbots. Each agent owns exactly one responsibility, and something has to coordinate how work moves between them.

---

## STEP 4 — MER Exploration (First Cycle)

### Objective
Present multi-agent coordination through diagram, execution flow, and code, introducing Task Decomposition, Orchestration, and the Handoff Pattern as the execution flow naturally reaches each.

---

### Representation 1 — Concept Diagram

#### Slide Content
```
   RECEIVE WORK  ──▶  SPECIALIST AGENT  ──▶  HANDOFF  ──▶  NEXT SPECIALIST AGENT  ──▶  ...  ──▶  COMPLETE
```

#### What To Say
Here's the shape. Work comes in. A specialist handles their piece. A handoff moves it to the next specialist. This repeats until the work is complete. No mention of tickets or industries yet, just the shape.

In healthcare, this is intake, then triage, then diagnosis, then billing. In manufacturing, this is inspect, then assemble, then test, then ship. The diagram doesn't move either time. Only what each specialist does changes.

---

### Representation 2 — Execution Flow (EPC Agent)

#### Slide Content
```
1. TICKET ARRIVES AT ONE GIANT AGENT (the old way)
2. RECOGNIZE THE OVERLOAD                → (Task Decomposition)
3. FOUR SPECIALISTS EMERGE: EXTRACT → CLUSTER → SCORE → ESCALATE
4. SOMETHING DECIDES WHICH AGENT RUNS NEXT → (Workflow Orchestration)
5. EACH TRANSITION MUST BE SAFE          → (Handoff Pattern)
6. WORKFLOW COMPLETES
```

#### What To Say
Let's walk through what happens to our own agent.

Step one, remember how things used to work. One ticket, one giant agent, doing everything itself.

Step two, here's the real question. How do we split this into responsibilities that are actually manageable? This is task decomposition. Instead of one giant agent doing everything, we break the problem into pieces small enough that each one can be handled well.

Step three, our four specialists appear. Extract handles understanding the raw ticket. Cluster handles grouping it with related history. Score handles judging business impact. Escalate handles preparing the final report. Each one focused on exactly one job.

Step four, another real question. Who actually decides which agent runs next? This is orchestration. I want to be clear about something. We are not introducing Cloud Workflows because it's a tool worth memorizing. We're introducing it because coordination is a real problem that needs solving, and Cloud Workflows happens to be how we solve it. The coordination problem comes first. The tool is just the implementation.

Step five, here's a question worth asking. How does one agent safely hand its work to the next one? This is the handoff pattern. And notice, the emphasis here isn't on sending a message. It's on preserving correctness. Did Extract actually produce something valid before Cluster starts working with it? A handoff checks that, every single time.

Step six, the workflow completes.

---

### Representation 3 — Code

#### Slide Content
```python
def orchestrate(ticket):
    extraction = extract_agent.run(ticket)
    validate_handoff(extraction)                 # safe transfer, not just messaging
    cluster = cluster_agent.run(extraction)
    validate_handoff(cluster)
    score = score_agent.run(cluster)
    validate_handoff(score)
    return escalate_agent.run(score)
```

#### What To Say
Look at this closely. Four specialists, called one after another. And notice, between every single one, there's a `validate_handoff` call. That's not decoration. That's the actual safety check, confirming each agent's output is genuinely ready before the next agent touches it.

---

### Context Progression — Four Industries, Rapid Switch

#### Slide Content
| | Concept Diagram | Execution Flow | Code |
|---|---|---|---|
| **Healthcare** | *Unchanged* | Intake → validate → triage → validate → diagnosis → validate → billing | Same four functions, different agents |
| **Finance** | *Unchanged* | Intake → validate → risk check → validate → approval → validate → reporting | Same four functions, different agents |
| **Legal** | *Unchanged* | Intake → validate → review → validate → risk flag → validate → routing | Same four functions, different agents |
| **Retail** | *Unchanged* | Intake → validate → categorize → validate → prioritize → validate → resolve | Same four functions, different agents |

#### What To Say
Four industries, moving quickly. The diagram never moves. The execution flow keeps the same shape, only the specialists change. The code stays the same structure, only which agents are being called changes underneath it.

The diagram is the behavior. The execution flow is that behavior applied to a domain. The code is the execution flow, made real.

---

## STEP 5 — Return to the Definition

### Objective
Expand the Step 1 definition using only concepts just experienced.

### Slide Content
> **Multi-Agent Systems is teaching several specialized computers to each own one responsibility, hand work safely to the next one, and let something coordinate the whole sequence.**

### What To Say
Let's expand our definition. Multi-Agent Systems is teaching several specialized computers to each own one responsibility, that's task decomposition. To hand work safely to the next one, that's the handoff pattern. And to let something coordinate the whole sequence, that's orchestration.

Nothing new here. Just the words attached to what you already built.

---

## STEP 6 — Increase Scope: One System, More Responsibilities

### Objective
Expand responsibilities within the coordinated multi-agent system, still not yet adaptive, setting up the need for broader human supervision.

### Slide Content
**The EPC multi-agent system must now:**
- Decompose an incoming ticket correctly
- Run all four specialists in the right order
- Validate every handoff between them
- Allow a human to supervise the entire workflow, not just one agent's output

### What To Say
Let's grow this system's responsibilities. Still one coordinated system. We haven't added a fifth agent, we've just asked more of the four we have.

It needs to decompose an incoming ticket correctly. It needs to run all four specialists in the right order. It needs to validate every single handoff between them. And here's what's new. It needs to let a human supervise the entire workflow, not just approve one agent's final output, the way we did back in Week 3.

Think about a legal document pipeline, where a person needs visibility into every stage, intake, review, risk-flagging, routing, not just a single approval button at the very end. Or a retail support pipeline during a big sale, where someone needs to see the health of the whole system, not just individual tickets.

---

## STEP 7 — Repeat MER: Add the Human Supervision Dashboard

### Objective
Add exactly one new layer to the existing diagram, a dashboard that supervises the entire workflow rather than one agent's output.

---

### Representation 1 (Expanded) — Concept Diagram

#### Slide Content
```
                    ┌───────────────────────────────────────────────┐
                    │       HUMAN SUPERVISION DASHBOARD               │
                    │   Approval Queue · Agent Health ·               │
                    │   Processing Throughput · Workflow Status        │
                    └───────────────────────▲───────────────────────┘
                                             │ observes / intervenes
   TICKET → EXTRACT → HANDOFF → CLUSTER → HANDOFF → SCORE → HANDOFF → ESCALATE → COMPLETE
```

#### What To Say
Look at the chain running along the bottom, exactly what we built already, untouched. What's new sits above it, watching the whole thing. This is the dashboard. In Week 3, a human approved one agent's output. Today, that same human sees the health of all four agents, how many tickets are moving through the whole pipeline, and the overall status of the workflow, not just a single decision at the end.

---

### Representation 2 (Expanded) — Execution Flow

#### Slide Content
```
1. TICKET ENTERS THE PIPELINE
2. EACH AGENT REPORTS ITS STATUS AS IT RUNS         ← NEW
3. DASHBOARD TRACKS: APPROVAL QUEUE, AGENT HEALTH,
   PROCESSING THROUGHPUT, WORKFLOW STATUS            ← NEW
4. HANDOFFS PROCEED AS BEFORE, VALIDATED EACH TIME
5. HUMAN CAN INTERVENE AT ANY STAGE, NOT JUST THE END ← NEW
6. WORKFLOW COMPLETES
```

#### What To Say
Steps one and four are exactly what we already had. What's new is that as each agent runs, it now reports its own status, not just its final output. The dashboard is watching four separate things at once. Whether tickets are waiting for approval. Whether each agent is actually healthy and responding. How much work is moving through the system. And the overall status of the workflow as a whole.

And here's the real shift. A human can step in at any stage now, not just click approve or reject at the very end.

---

### Representation 3 (Expanded) — Code

#### Slide Content
```python
def orchestrate(ticket):
    for agent in [extract_agent, cluster_agent, score_agent, escalate_agent]:
        result = agent.run(ticket)
        dashboard.report_status(agent.name, result)   # supervise the whole workflow
        validate_handoff(result)
        ticket = result
    return ticket
```

#### What To Say
Notice this is nearly the same orchestrator function from before. We turned four separate calls into a loop, and we added one new line, `dashboard.report_status`. That single line is what makes the entire system observable, end to end, not just at the final step.

---

### Four Additional Context Changes

#### Slide Content
| Industry | What "Agent Health" Means | What "Workflow Status" Means |
|---|---|---|
| Healthcare | Is the triage agent responding normally? | How many patients are mid-workflow right now? |
| Finance | Is the risk-check agent up to date? | How many transactions are pending review? |
| Legal | Is the review agent keeping pace? | How many filings are still in progress? |
| Manufacturing | Is the inspection agent running normally? | How many units are mid-assembly right now? |

#### What To Say
Same dashboard concept, four industries. In every case, a human isn't watching one decision. They're watching the health and status of an entire coordinated system, live.

---

## STEP 8 — Expand the Definition Again

### Objective
Incorporate only the supervision concept from the second MER cycle.

### Slide Content
> **Multi-Agent Systems is teaching several specialized computers to each own one responsibility, hand work safely to the next one, coordinate the whole sequence, and let a human supervise the health and status of the entire workflow — not just approve one agent's output.**

### What To Say
One more addition. Everything through "coordinate the whole sequence" is exactly what we had before. What's new is this: let a human supervise the health and status of the entire workflow, not just approve one agent's output. That's this cycle's entire contribution.

---

## STEP 9 — Break the System

### Objective
Walk through realistic multi-agent coordination failures.

### Slide Content
| Failure | What Failed | Why | Detection | Recovery |
|---|---|---|---|---|
| Bad Handoff Accepted | Cluster agent works from malformed data | Extract's output wasn't actually validated | Cluster produces nonsensical groupings | Enforce handoff validation strictly, reject and retry the source agent |
| Wrong Next Agent | Ticket routed to the wrong specialist | Orchestrator logic bug | Escalate runs before Score has finished | Add explicit sequencing checks in the orchestrator |
| Silent Agent Failure | Pipeline continues with missing data | An agent failed but didn't raise an error | Later stage receives an empty or default result | Require every agent to explicitly confirm success or failure |
| Cascading Failure | A small mistake compounds through all four agents | No stage caught the original error early | Final escalation is completely wrong, traced back to step one | Validate at every handoff, not just at the very end |
| Dashboard Blind Spot | A stuck agent looks healthy | Health check doesn't measure actual responsiveness | Ticket stuck for hours, dashboard shows no alert | Add a timeout-based health check, not just a heartbeat |
| Duplicate Execution | An agent runs twice for one ticket | Retry logic re-ran an already-completed step | Two identical outputs for the same ticket ID | Idempotency check before re-running any agent |
| Lost Context | A later agent is missing information an earlier one had | Handoff passed a summary instead of full context | Escalate agent's report contradicts the original ticket | Pass full relevant context forward, not a lossy summary |

### What To Say
Let's break this system on purpose.

First, a bad handoff gets accepted. Cluster starts working from data that was never actually validated. We catch this when Cluster produces groupings that don't make sense. We fix it by enforcing handoff validation strictly, and rejecting the source agent's output if it fails.

Second, the wrong next agent runs. A bug in the orchestrator sends work to the wrong specialist, maybe Escalate runs before Score has even finished. We catch this by noticing the sequence is out of order. We fix it with explicit sequencing checks inside the orchestrator itself.

Third, a silent agent failure. An agent fails, but it doesn't raise an actual error, so the pipeline just continues with missing or empty data. We catch this when a later stage receives something clearly incomplete. We fix it by requiring every agent to explicitly confirm success or failure, never assume silence means success.

Fourth, a cascading failure. A small mistake in the first agent compounds through all four, and by the time it reaches the final escalation, it's completely wrong. We catch this by tracing the final bad output all the way back. We fix it by validating at every single handoff, not just checking the final result.

Fifth, a dashboard blind spot. An agent is actually stuck, but its health check still shows green, because the check only measures whether it's running, not whether it's actually responding. We catch this when a ticket sits for hours with no alert. We fix it with a timeout-based health check, not just a simple heartbeat.

Sixth, duplicate execution. Retry logic accidentally re-runs an agent that already completed successfully. We catch this when we see two identical outputs for the same ticket. We fix it with an idempotency check before re-running anything.

Seventh, lost context. A handoff passes along a summary instead of the full picture, and a later agent is missing something an earlier one actually knew. We catch this when the final escalation report contradicts the original ticket. We fix it by passing forward the actual relevant context, not a lossy shortcut.

---

## STEP 10 — Final Professional Definition

### Objective
Deliver the complete definition, tracing every clause to its origin, and compress into one mental model.

### Slide Content
> **Multi-Agent Systems is the capability that divides one enterprise problem into specialized agents, each owning exactly one responsibility, coordinates their execution through an orchestrator, validates every handoff between them, and lets a human supervise the health and status of the entire workflow — not just one agent's output.**

**The one mental model to leave with:**
```
   TICKET → [ORCHESTRATOR: EXTRACT → HANDOFF → CLUSTER → HANDOFF → SCORE → HANDOFF → ESCALATE] → COMPLETE
                          (supervised end-to-end by a human dashboard)
```

### What To Say
Let's bring it together. Multi-Agent Systems divides one enterprise problem into specialized agents, each owning exactly one responsibility, that's task decomposition. Coordinates their execution through an orchestrator, that's orchestration. Validates every handoff between them, that's the handoff pattern. And lets a human supervise the health and status of the entire workflow, that's this cycle's dashboard.

Here's the sentence I want you to remember. Multi-agent systems are not multiple chatbots. Each agent owns one responsibility, and together they solve one larger business problem.

---
---

# S-TIER 2 — Intelligent Agent Coordination & Design Patterns

**Bridge from S-Tier 1:** Our four agents now coordinate through one fixed pipeline. Every ticket goes Extract, then Cluster, then Score, then Escalate, in that exact order, every single time. Is that actually the smartest way to handle every ticket?

---

## STEP 1 — Definition

### Objective
Introduce agent design patterns simply, creating curiosity, with no implementation detail.

### Slide Content
> **Agent Design Patterns is teaching a system to choose its own strategy.**

### What To Say
Here's our second definition for the week. Agent Design Patterns is teaching a system to choose its own strategy.

Right now, our system has exactly one strategy. Every ticket follows the same four steps, in the same order, one ticket at a time. Ask yourself, is a company-wide outage really the same kind of problem as someone forgetting their password? Should they really travel through the exact same process, at the exact same speed? Today, we teach our system to answer that question for itself.

---

## STEP 2 — Create the Need

### Objective
Establish why a rigid, fixed pipeline is insufficient for real, varied enterprise problems.

### Slide Content
**Unresolved problem from the last lecture:** Our four agents coordinate reliably, but every ticket travels through the exact same fixed sequence, at the exact same pace, regardless of how urgent or complex it actually is.

| Without Adaptive Patterns | With Adaptive Patterns |
|---|---|
| Every ticket follows the identical fixed path | Tickets are routed based on their actual severity |
| Tickets processed one at a time, in sequence | Independent tickets are processed simultaneously |
| An agent's first draft is treated as final | An agent can critique and improve its own output |
| Every extraction follows one rigid fixed sequence | The plan itself adapts to how complex the ticket is |

### What To Say
Here's the gap. A rigid pipeline treats a trivial, low-priority ticket exactly the same as a company-wide outage. Both wait in the same queue. Both travel through the same four steps, in the same order. But the outage clearly deserves to skip ahead, and the trivial ticket doesn't need the same level of scrutiny.

There's a second gap. A fixed pipeline processes tickets one at a time, in sequence. If five hundred tickets arrive at once during a real incident, they queue up single file through the same four stages, even though most of them have nothing to do with each other and could easily be handled at the same time.

Picture an emergency room that makes every patient, whether they have a paper cut or a heart attack, wait in the exact same line, see the exact same sequence of staff, in the exact same order, with no fast lane for anything. This is exactly what we cannot do.

A rigid pipeline is that emergency room. Technically thorough, but blind to urgency.

Here's a second picture. A toll booth with a single lane, forcing every car through one at a time, even at rush hour, when a hundred cars could easily pass through ten open lanes at once. This is exactly what we cannot do.

A system with no parallel processing is that single-lane toll booth. Correct, but needlessly slow the moment real load arrives.

You'll see this elsewhere too. A fixed intake process that makes a stroke patient wait behind routine paperwork, exactly like everyone else, instead of being fast-tracked. A fixed fraud pipeline that processes a suspicious high-value wire transfer in the same slow queue as a routine low-risk purchase.

Back to our own agent. A critical, company-wide outage ticket should not wait behind a routine password reset. And two hundred tickets arriving during that outage shouldn't be forced through one at a time.

---

## STEP 3 — Build the Core Idea

### Objective
Build intuition for adaptive strategies without implementation.

### Slide Content
**Running Example:** The EPC Agent's fixed pipeline, learning to adapt to what each ticket actually needs.

### What To Say
Let's build some intuition.

A hospital triage nurse who sees a patient clutching their chest doesn't make them wait in the regular line. She immediately routes them to a faster path, while a patient with a mild cough waits normally. That's routing.

Here's a second picture. A writer doesn't submit their very first draft straight to the publisher. They read it back, notice what's weak, revise it, and only then send the improved version forward. That's reflection.

Routing is like an airport security line. Passengers with pre-approved status take a faster lane. Everyone else takes the standard one. Same destination, different path, based on who's actually in front of you. Reflection is like a student proofreading their own essay before turning it in, catching mistakes themselves instead of submitting the first version and just hoping for the best.

Here's something simpler still. A GPS app that recalculates your route the moment traffic changes, instead of forcing you down the same fixed road no matter what. A kitchen that cooks multiple orders at the same time on different burners, instead of finishing one dish completely before even starting the next.

So here's the idea. An intelligent multi-agent system doesn't follow one fixed script for everything. It adapts its path, that's routing. Its speed, that's parallel execution. Its own quality, that's reflection. And, as we'll see later today, even its own plan.

---

## STEP 4 — MER Exploration (First Cycle)

### Objective
Present routing, parallelization, and reflection through diagram, execution flow, and code.

---

### Representation 1 — Concept Diagram

#### Slide Content
```
   TASK  ──▶  CHOOSE STRATEGY  ──▶  EXECUTE (possibly in parallel)  ──▶  SELF-CHECK  ──▶  OUTPUT
```

#### What To Say
Here's the shape. A task arrives. A strategy gets chosen for it. It executes, possibly alongside other tasks at the same time. It gets checked before it's considered finished. Then the output goes out.

In healthcare, this is choosing fast-track versus normal triage, executing, then double-checking a diagnosis before it's sent. In finance, this is choosing priority review versus standard review, executing, then double-checking a risk assessment before it's sent. Diagram hasn't moved either time.

---

### Representation 2 — Execution Flow (EPC Agent)

#### Slide Content
```
1. TICKET ARRIVES AT THE FIXED PIPELINE (the old way)
2. SHOULD EVERY TICKET FOLLOW THE SAME PATH?     → (Routing)
3. WHAT IF HUNDREDS ARRIVE AT ONCE?               → (Parallelization)
4. AGENT 4 DRAFTS AN ESCALATION, THEN CHECKS ITSELF → (Reflection)
5. WORKFLOW COMPLETES
```

#### What To Say
Step one, remember the old way. Every ticket, same four steps, same order.

Step two, here's a real question. Should every ticket follow the same path? No. This is routing. A critical, severe ticket gets routed straight to immediate escalation. A low-severity ticket follows the normal pipeline. Same system, different path, chosen based on what the ticket actually needs.

Step three, another real question. What happens when hundreds of tickets arrive at once, during a real incident? This is parallelization. Independent tickets that have nothing to do with each other don't need to wait behind one another. They execute together.

Step four, here's a question that changes how our escalation agent works. How does an agent improve its own output before passing it on? This is reflection. Instead of Agent Four writing one draft and sending it immediately, it now drafts, critiques its own draft, improves it, validates the improvement, and only then sends it.

Step five, the workflow completes, having adapted along the way instead of following one fixed script.

---

### Representation 3 — Code

#### Slide Content
```python
def route(ticket):
    return escalate_immediately(ticket) if ticket.severity == "Critical" else normal_pipeline(ticket)

async def process_batch(tickets):
    return await asyncio.gather(*(route(t) for t in tickets))   # parallel execution

def reflect(draft):
    critique = critique_agent.review(draft)
    return improve(draft, critique) if critique.has_issues else draft
```

#### What To Say
Three small functions, matching the three ideas we just covered. `route` decides the path. `process_batch` runs many tickets at the same time instead of one by one, that's `asyncio.gather` doing the parallel work. `reflect` checks a draft and improves it only if the critique actually finds something worth fixing.

---

### Context Progression — Four Industries, Rapid Switch

#### Slide Content
| | Concept Diagram | Execution Flow | Code |
|---|---|---|---|
| **Healthcare** | *Unchanged* | Route by urgency → parallel intake during a mass event → reflect on a draft diagnosis note | Same three functions, different rules |
| **Finance** | *Unchanged* | Route by transaction risk → parallel review during volatility → reflect on a risk report | Same three functions, different rules |
| **Retail** | *Unchanged* | Route by order value → parallel processing during a flash sale → reflect on a customer reply | Same three functions, different rules |
| **Manufacturing** | *Unchanged* | Route by defect severity → parallel inspection during a batch run → reflect on a quality report | Same three functions, different rules |

#### What To Say
Four industries, moving quickly. The diagram never moves. The execution flow keeps the same three ideas, routing, parallel execution, reflection, only what they're applied to changes. The code stays the same three functions, only the actual rules inside them change.

---

## STEP 5 — Return to the Definition

### Objective
Expand the Step 1 definition using only what's been experienced so far.

### Slide Content
> **Agent Design Patterns is teaching a system to route each problem down the right path, handle many problems at once, and check its own work before passing it along.**

### What To Say
Agent Design Patterns is teaching a system to route each problem down the right path, that's routing. To handle many problems at once, that's parallelization. And to check its own work before passing it along, that's reflection. Nothing new, just vocabulary attached to what you already built.

---

## STEP 6 — Increase Scope: One System, More Responsibilities

### Objective
Expand responsibilities within the same four-agent system, still no new agents added, setting up the need for dynamic planning.

### Slide Content
**The EPC system must now:**
- Route tickets based on actual severity
- Process independent tickets in parallel
- Reflect on and improve its own drafts
- Recognize when a ticket is more complex than a fixed sequence can handle

### What To Say
Let's grow this system's responsibilities again. Still the same four agents. We're not adding a fifth. We're making the existing four smarter about how they operate.

The system routes tickets based on actual severity. It processes independent tickets in parallel. It reflects on and improves its own drafts. And here's what's new. It needs to recognize when a ticket is genuinely more complex than our fixed four-step sequence can actually handle.

Think about a legal filing that's unusually ambiguous, where the normal review steps don't quite fit. Or a manufacturing defect report where the usual inspection sequence doesn't match what's actually being described. Some tickets need more than a faster path or a better draft. They need an entirely different plan.

---

## STEP 7 — Repeat MER: Add the Dynamic Planning Layer

### Objective
Add exactly one new layer to the existing diagram, generating a plan specific to each ticket instead of following one fixed sequence.

---

### Representation 1 (Expanded) — Concept Diagram

#### Slide Content
```
   TICKET ──▶ UNDERSTAND ──▶ GENERATE PLAN (varies per ticket) ──▶ EXECUTE STEP BY STEP ──▶ EVALUATE ──▶ FINISH
                                        │
                          ┌─────────────┼─────────────┐
                          ▼             ▼             ▼
                       PLAN A        PLAN B        PLAN C
                     (simple)      (complex)    (ambiguous, needs
                                                  clarification first)
```

#### What To Say
Look at the diagram from before. Routing, parallel execution, and reflection are all still there, untouched, happening inside "execute step by step." What's new sits right before execution even begins. The system now generates a plan specific to this exact ticket, instead of always running the same fixed sequence.

A simple ticket gets a simple plan. A complex ticket gets a more thorough plan. And a genuinely ambiguous ticket might get a plan that starts by asking a clarifying question before doing anything else.

---

### Representation 2 (Expanded) — Execution Flow

#### Slide Content
```
1. TICKET ARRIVES
2. AGENT UNDERSTANDS THE TICKET FIRST         → (Plan-and-Solve)
3. AGENT GENERATES A PLAN SPECIFIC TO THIS TICKET
4. EXECUTE STEP 1 → EVALUATE → EXECUTE STEP 2 → ... → FINISH
5. DIFFERENT TICKETS RECEIVE DIFFERENT PLANS   → (Planning Algorithms)
```

#### What To Say
Step two is the new idea. Should every extraction follow one fixed sequence? No. The agent understands the ticket first, before deciding how to proceed.

Step three, based on that understanding, it generates a plan. Not the same plan every time. A plan shaped by what this specific ticket actually needs.

Step four, it executes that plan, one step at a time, checking as it goes, rather than blindly running through a script.

Step five, here's the bigger idea behind all of this. What if every ticket genuinely requires a different plan? A simple ticket gets Plan A. A complex one gets Plan B. An ambiguous one gets Plan C. This is what planning algorithms actually give us. Adaptation based on how complicated or unclear a ticket really is, instead of one static workflow trying to fit everything.

---

### Representation 3 (Expanded) — Code

#### Slide Content
```python
def plan_and_solve(ticket):
    understanding = understand(ticket)
    plan = generate_plan(understanding)      # different per ticket, not fixed
    result = understanding
    for step in plan:
        result = execute_step(step, result)
        if not evaluate(result):
            plan = generate_plan(result)     # re-plan if something's off
    return result
```

#### What To Say
Notice `generate_plan` gets called twice in this function. Once at the start, based on what we understood about the ticket. And again, inside the loop, if something along the way doesn't check out. That second call is important. It means the system doesn't just plan once and blindly follow it. It's allowed to notice something's wrong and re-plan.

---

### Four Additional Context Changes

#### Slide Content
| Industry | Plan A (Simple) | Plan B (Complex) | Plan C (Ambiguous) |
|---|---|---|---|
| Healthcare | Standard symptom check | Multi-condition review | Ask a clarifying question first |
| Finance | Standard transaction check | Multi-account risk review | Request more transaction detail first |
| Legal | Standard clause review | Multi-clause risk analysis | Request the missing document section first |
| Cybersecurity | Standard log check | Multi-system correlation | Request additional log context first |

#### What To Say
Same idea, four industries. In every case, the plan itself is shaped by how complicated or unclear the actual problem is, not forced into one workflow that pretends every case looks the same.

---

## STEP 8 — Expand the Definition Again

### Objective
Incorporate only the dynamic planning concept from the second MER cycle.

### Slide Content
> **Agent Design Patterns is teaching a system to route each problem down the right path, handle many problems at once, check its own work before passing it along, and generate a plan specific to how complex or ambiguous each problem actually is — rather than following one fixed sequence for everything.**

### What To Say
One more addition. Everything through "check its own work before passing it along" is exactly what we had before. What's new is this: generate a plan specific to how complex or ambiguous each problem actually is, rather than following one fixed sequence for everything. That's this cycle's contribution.

---

## STEP 9 — Break the System

### Objective
Walk through realistic failures in adaptive, pattern-based coordination.

### Slide Content
| Failure | What Failed | Why | Detection | Recovery |
|---|---|---|---|---|
| Routing Misclassification | A critical ticket gets routed as low severity | Severity check misjudged the ticket | The ticket sits unescalated despite clear urgency | Add a second, independent severity check before routing |
| Parallel Race Condition | Two related tickets processed in parallel don't notice each other | They were assumed independent but weren't | Two separate clusters form for what's really one issue | Recheck for relationships after parallel execution completes |
| Endless Reflection Loop | An agent keeps critiquing and revising, never sends anything | No stopping condition on the critique cycle | A draft never actually reaches the handoff stage | Hard cap on reflection cycles, then send the best version so far |
| Reflection Makes It Worse | The "improved" draft is actually worse than the original | Critique introduced a new error while fixing another | Human reviewer flags the final version as lower quality | Compare draft versions before accepting an "improvement" |
| Bad Plan, No Re-Planning | Agent commits to a wrong plan and never notices | Evaluation step wasn't actually checking the right thing | Final result clearly doesn't match what the ticket asked for | Strengthen the evaluation step so it can actually catch a bad plan |
| Planning Overhead on Simple Tickets | A trivial ticket spends too long being planned | Planning step didn't check ticket complexity first | Simple tickets take unexpectedly long to resolve | Skip full planning for tickets that are clearly simple |
| Plan Drifts From Original Intent | Multi-step execution loses track of what was originally asked | Later steps only saw partial context from earlier steps | Final output doesn't actually answer the original ticket | Keep the original ticket visible at every step, not just the latest result |

### What To Say
Let's break this system too.

First, a routing misclassification. A genuinely critical ticket gets judged as low severity and sits unescalated. We catch this when clear urgency goes unaddressed. We fix it with a second, independent severity check before routing commits to a decision.

Second, a race condition in parallel execution. Two tickets get processed at the same time, assumed to be unrelated, but they're actually describing the same emerging issue. We catch this when two separate clusters form for what's really one problem. We fix it by rechecking for relationships after parallel execution finishes, not just trusting the initial assumption.

Third, an endless reflection loop. The agent keeps critiquing and revising its own draft, never satisfied, never actually sending anything. We catch this when a draft never reaches the handoff stage. We fix it with a hard cap on reflection cycles, sending the best version available once that cap is hit.

Fourth, and this one's worth sitting with. Reflection can actually make things worse. The critique fixes one issue but introduces a new one, and the "improved" draft is genuinely worse than the original. We catch this when a human reviewer flags the final version as lower quality. We fix it by comparing versions before accepting an improvement as real.

Fifth, a bad plan with no re-planning. The agent commits to the wrong plan from the start, and because the evaluation step wasn't actually checking the right thing, it never notices. We catch this when the final result clearly doesn't match what the ticket asked for. We fix it by strengthening evaluation so it can genuinely catch a bad plan, not just rubber-stamp it.

Sixth, planning overhead on simple tickets. A trivial ticket gets the full planning treatment, and takes far longer than it should. We catch this when simple tickets take unexpectedly long. We fix it by checking complexity first, and skipping full planning when a ticket is clearly simple.

Seventh, the plan drifts from the original intent. Across several steps, the system loses track of what the ticket actually asked for in the first place. We catch this when the final output doesn't actually answer the original question. We fix it by keeping the original ticket visible at every step, not just the most recent result.

---

## STEP 10 — Final Professional Definition

### Objective
Deliver the final, complete definition, tracing every clause to its origin, and compress the entire S-Tier 2 lecture into one mental model.

### Slide Content
> **Agent Design Patterns is the capability that lets a multi-agent system route each ticket down the right path based on severity, execute independent tickets in parallel for speed, reflect on and improve its own drafts before handing them off, and generate a plan specific to each ticket's actual complexity — rather than following one fixed sequence for everything.**

**The one mental model to leave with:**
```
   TICKET → ROUTE (by severity) → PLAN (per ticket) → EXECUTE (parallel where possible) → REFLECT → HANDOFF
```

### What To Say
Let's bring the whole week together. Agent Design Patterns routes each ticket down the right path based on severity, that's routing. Executes independent tickets in parallel for speed, that's parallelization. Reflects on and improves its own drafts before handing them off, that's reflection. And generates a plan specific to each ticket's actual complexity, that's this cycle's contribution.

Here's the connection I want you to hold onto. None of this replaces the orchestrator or the handoff validation we built this morning. Routing, parallel execution, reflection, and planning are strategies that same orchestrator now uses. They don't remove the need for coordination. They make that coordination smarter.

That's the real shape of this week. First, we divided one overloaded agent into specialists who coordinate reliably. Then, we taught that coordination to adapt, choosing the right path, the right speed, the right level of self-checking, and the right plan, for whatever problem actually walks through the door.
