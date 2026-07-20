# Instructor Lecture Notes — Week 5
## Two Complete Learning Journeys: Connect to Tools, Then Connect to Each Other

**Running Project:** Enterprise Problem Consolidation (EPC) Agent

---
---

# S-TIER 1 — Model Context Protocol (MCP) & Enterprise Context

---

## STEP 1 — Definition

### Objective
Introduce MCP in the simplest language possible, creating curiosity without touching servers, retrieval mechanics, or code.

### Slide Content
> **Model Context Protocol is teaching every tool to speak one language.**

### What To Say
Read that with me. Model Context Protocol is teaching every tool to speak one language.

For the last four weeks, everything our agents touched, we built ourselves. Our own memory, our own tools, our own coordination between agents. Today, we step outside our own system for the first time, and connect to things we didn't build. Drive. Docs. Gmail. Chat. Real systems the company already uses, that existed long before our agent did.

Here's the question worth sitting with. If every single one of those systems speaks its own language, does our agent need to personally learn all of them, one at a time, forever? Or is there a better way?

---

## STEP 2 — Create the Need

### Objective
Establish why custom, one-off integrations don't scale, using analogies, before returning to the EPC Agent.

### Slide Content
**Unresolved problem from last lecture:** Our agents coordinate beautifully with each other. But every connection to the outside world, so far, has been something we hand-built ourselves.

| Without MCP | With MCP |
|---|---|
| Every tool needs its own custom integration code | Every tool speaks through the same standard interface |
| Adding a new system means rewriting agent logic | Adding a new system means adding a new MCP server |
| Integration knowledge is trapped in one agent's code | Integration knowledge is reusable across every agent |
| A system swap breaks the agent | A system swap is invisible to the agent |

### What To Say
Here's the problem. Back in Week 2, we built our own tool-calling mechanism, custom-written, specifically for our own system. But a real company already has dozens of existing systems. Drive. Gmail. Chat. A ticketing tool. A CRM. If every single one needs its own hand-built, hand-maintained integration, that's not scaling. That's multiplying our workload every single time the company adds one more tool.

And here's what makes it worse. Say we finally finish writing custom code for Drive, Gmail, and Chat. Now the company adopts something new next quarter. Do we go back and rewrite our agent's actual reasoning logic, just to plug in one more tool? That would mean our agent's core intelligence is tangled up with every external system it happens to touch.

Let me give you two pictures.

Imagine every single appliance in your house needing its own unique, custom-shaped wall socket. The toaster needs one shape. The lamp needs another. The phone charger needs a third. And every time you buy something new, an electrician has to rewire your wall. This is exactly what we cannot do.

Without a standard protocol, every tool we connect our agent to is its own custom-shaped socket, requiring its own rewiring.

Here's a second picture. A translator who has personally learned Drive's language, Gmail's language, and Chat's language, separately, from scratch. The moment the company adopts a fourth system, that translator has to go back to school for years, from zero, before they can help with it. This is exactly what we cannot do.

MCP is what lets one universal translator work with any new system instantly, instead of learning each one from scratch.

You'll see this everywhere. A hospital's AI custom-built to talk to one electronic health record system, and the moment the hospital switches vendors, or adds a lab system, someone has to rewrite the integration from scratch. A law firm's AI custom-wired to one document system, where adding a second one, or a new e-signature tool, means starting the integration work over again.

Back to our own agent. Right now, our Escalation Agent needs to pull a runbook from Drive, create a summary in Docs, and draft a notification in Gmail. Three completely different systems. Three completely different wiring jobs. Unless something standardizes the connection.

---

## STEP 3 — Build the Core Idea

### Objective
Build intuition for standardized interfaces without implementation.

### Slide Content
**Running Example:** The EPC Agent, connecting to Drive, Docs, Gmail, and Chat, all through one standard interface.

### What To Say
Let's build some intuition.

A traveler who carries one universal power adapter, instead of a suitcase full of country-specific plugs. The adapter doesn't care whether the wall socket is in Japan, Germany, or Brazil. It just works, because both sides agreed on one standard interface.

Here's a second picture. A new employee joining a company doesn't need to personally learn the wiring of the building's electrical system just to plug in their laptop. They plug into the standard wall outlet, and the building's electricians handled everything else, invisibly, behind the wall.

Think about a USB port. Your mouse, your keyboard, your external drive, your phone charger, all completely different devices, but they all plug into the exact same shaped port, because USB is a shared standard everyone agreed to build to. Or think of a restaurant menu written in a universal format. Every kitchen might cook completely differently behind the scenes, but the process of ordering never changes, no matter which restaurant you're in.

Here's something even simpler. A universal remote that works with your TV, your soundbar, and your streaming box, without you needing three separate remotes. A power strip that lets a lamp, a laptop charger, and a phone charger all plug in side by side, using the exact same kind of plug.

So here's the idea. MCP isn't another tool sitting alongside Drive, Gmail, and Chat. It's the shared standard that lets our agent talk to all of them the exact same way, without learning each one's private language.

---

## STEP 4 — MER Exploration (First Cycle)

### Objective
Present MCP through diagram, execution flow, and code, introducing Tool Use & Integration revisited, Advanced RAG, and Context Engineering as the execution flow naturally reaches each.

---

### Representation 1 — Concept Diagram

#### Slide Content
```
   AGENT  ──▶  STANDARD INTERFACE  ──▶  EXTERNAL SYSTEM  ──▶  STANDARD RESPONSE
```

#### What To Say
Here's the shape. An agent makes a request through a standard interface. The external system handles it. A standard response comes back. No mention of Drive, Gmail, or any specific industry yet, just the shape.

In healthcare, the agent goes through a standard interface to reach a lab system, and gets a standard response back. In legal work, the agent goes through the same standard interface to reach a document management system, and gets a standard response back. The diagram hasn't moved either time.

---

### Representation 2 — Execution Flow (EPC Agent)

#### Slide Content
```
1. AGENT NEEDS EXTERNAL INFORMATION OR ACTION
2. EVERY TOOL MUST LOOK IDENTICAL TO THE AGENT   → (Tool Use & Integration, revisited)
3. REQUEST GOES THROUGH THE MCP SERVER FOR THAT SYSTEM
4. RETRIEVAL RETURNS STRUCTURED SECTIONS, NOT WHOLE DOCUMENTS → (Advanced RAG)
5. THE BEST CONTEXT IS ASSEMBLED, NOT JUST THE MOST SIMILAR ONE → (Context Engineering)
6. STANDARD RESPONSE RETURNS TO THE AGENT
```

#### What To Say
Let's walk this through with our own agent.

Step one, the agent needs something from outside itself.

Step two, remember tool calling from Week 2? Back then, the question was, which tool should I call? Today the question changes. How can every tool look identical to the agent, no matter which system is actually behind it? Whether it's Drive, Gmail, or something we haven't connected yet, the agent's own decision process should look exactly the same.

Step three, the request goes through the MCP server built for that specific system.

Step four, here's a real upgrade from what we learned in Week 2. Instead of retrieving an entire document, what if we retrieved just the section that actually matters? A timeline. A root cause. A resolution. A severity rating. A product area. This is advanced retrieval. I want to be clear, the concept of RAG hasn't changed from what we learned before. What's changed is the granularity. We're not fetching whole documents anymore. We're fetching exactly the piece that's useful.

Step five, here's another real question. How do we make sure the agent receives the most useful context, not just the most similar-looking one? This is context engineering. It combines semantic similarity, keyword matching, a relevance score, and a ranking of the top few results. The emphasis here isn't a brand new retrieval trick. It's about constructing better context for the model to actually reason with.

Step six, a standard response comes back to the agent, no matter which system it came from.

---

### Representation 3 — Code

#### Slide Content
```python
def get_context(agent, query, mcp_server):
    raw_sections = mcp_server.retrieve_sections(query)      # structured, not whole docs
    ranked = rank_by_relevance(raw_sections, query)          # semantic + keyword + score
    return ranked[:3]                                        # top three, not everything
```

#### What To Say
Three lines. The first fetches structured sections instead of whole documents, that's advanced RAG. The second ranks them by actual relevance, combining more than one signal, that's context engineering. The third keeps only the top three. Notice, this function doesn't care which system `mcp_server` actually represents. That's the entire point.

---

### Context Progression — Four Industries, Rapid Switch

#### Slide Content
| | Concept Diagram | Execution Flow | Code |
|---|---|---|---|
| **Healthcare** | *Unchanged* | Query → MCP → lab system → structured sections → ranked context | Same function, different MCP server |
| **Legal** | *Unchanged* | Query → MCP → document system → structured sections → ranked context | Same function, different MCP server |
| **Finance** | *Unchanged* | Query → MCP → reporting system → structured sections → ranked context | Same function, different MCP server |
| **Retail** | *Unchanged* | Query → MCP → order system → structured sections → ranked context | Same function, different MCP server |

#### What To Say
Four industries, moving quickly. Diagram frozen. Execution flow keeps the same six steps, only which system is behind the MCP server changes. The code stays the same three lines, only which `mcp_server` gets passed in changes.

The diagram is the behavior. The execution flow is that behavior applied to a domain. The code is the execution flow, made real.

---

## STEP 5 — Return to the Definition

### Objective
Expand the Step 1 definition using only concepts just experienced.

### Slide Content
> **Model Context Protocol is teaching every external tool to speak through one standard interface, returning structured, ranked context instead of whole documents, so the agent's own reasoning never has to change.**

### What To Say
Let's expand our definition. Model Context Protocol is teaching every external tool to speak through one standard interface, that's the whole point of MCP. Returning structured, ranked context instead of whole documents, that's advanced RAG and context engineering together. So the agent's own reasoning never has to change. Nothing new here, just the words attached to what we already built.

---

## STEP 6 — Increase Scope: One Connection, More Responsibilities

### Objective
Expand responsibilities across multiple connected systems, setting up the shift from read-only to read-and-write.

### Slide Content
**The EPC Agent must now:**
- Connect to Drive, Docs, Gmail, and Chat, all through the same interface
- Retrieve only the relevant sections from each system
- Rank what's actually most useful before reasoning
- Do all of this without any change to its own internal logic

### What To Say
Let's grow this. Still one standard interface. We're not building four different integrations, we're using MCP four different times.

The agent connects to Drive, Docs, Gmail, and Chat, all through the exact same interface. It retrieves only the relevant sections from each. It ranks what's actually useful before it starts reasoning. And through all of this, its own internal logic never has to change.

A manufacturing agent connecting to an inspection log system and a maintenance scheduling system, both through the same MCP interface. A cybersecurity agent connecting to a threat intelligence feed and an incident logging system, both through the same interface. Different systems, same shape.

---

## STEP 7 — Repeat MER: Add Read AND Write

### Objective
Add exactly one new layer, MCP as a two-way interface that performs actions, not just retrieves knowledge.

---

### Representation 1 (Expanded) — Concept Diagram

#### Slide Content
```
   AGENT ──▶ STANDARD INTERFACE ──▶ EXTERNAL SYSTEM
                                          │
                             ┌────────────┴────────────┐
                             ▼                          ▼
                        READ (retrieve)             WRITE (create/act)
                     e.g. Drive lookup        e.g. Docs summary, Gmail draft
                             │                          │
                             └──────► STANDARD RESPONSE ◄──────┘
```

#### What To Say
Look at the diagram from before, still completely intact. What's new is one fork. So far, everything we've done through MCP has been reading, pulling knowledge out of a system. But MCP isn't just for retrieval. The exact same standard interface can perform an action. Creating a Docs summary. Drafting a Gmail notification. Posting a Chat alert. Same protocol. Different operation.

---

### Representation 2 (Expanded) — Execution Flow

#### Slide Content
```
1. AGENT NEEDS EXTERNAL INFORMATION OR ACTION (as before)
2. FOR RETRIEVAL: SAME READ PATH AS BEFORE
3. FOR ACTIONS: SAME MCP INTERFACE, DIFFERENT OPERATION   ← NEW
   e.g. create a Docs summary, draft a Gmail notification, post a Chat alert
4. STANDARD RESPONSE RETURNS EITHER WAY
```

#### What To Say
Steps one and two are exactly what we already had. Step three is new. When the agent needs to act, not just retrieve, it still goes through the same MCP interface. It just requests a different operation. Write, instead of read. The agent doesn't need a separate mental model for "getting information" versus "doing something." It's the same door, just used differently.

---

### Representation 3 (Expanded) — Code

#### Slide Content
```python
def mcp_call(agent, operation, mcp_server, **kwargs):
    if operation == "read":
        return mcp_server.retrieve_sections(**kwargs)
    return mcp_server.write(**kwargs)   # e.g. create_doc, draft_email, post_message
```

#### What To Say
One function, one new branch. Read goes one way, write goes the other, but both pass through the exact same `mcp_server`. Nothing about how the agent calls this function changes based on which system it's talking to.

---

### Four Additional Context Changes

#### Slide Content
| Industry | Read Example | Write Example |
|---|---|---|
| Healthcare | Retrieve a similar past case | Create a discharge summary |
| Finance | Retrieve a prior risk report | Draft a compliance notification |
| Legal | Retrieve a related past clause | Create a case summary document |
| Manufacturing | Retrieve a past maintenance log | Draft a technician alert |

#### What To Say
Same pattern, four industries. In every row, both the read and the write happen through the exact same MCP interface. That consistency is the entire value of this protocol.

---

## STEP 8 — Expand the Definition Again

### Objective
Incorporate only the read-and-write concept from the second MER cycle.

### Slide Content
> **Model Context Protocol is teaching every external tool to speak through one standard interface, returning structured, ranked context instead of whole documents, and performing real actions like creating a document or drafting a notification through that same interface — so the agent's own reasoning never has to change, whether it's retrieving or acting.**

### What To Say
One more addition. Everything through "whole documents" is exactly what we had before. What's new is this: performing real actions, like creating a document or drafting a notification, through that same interface. Read or write, it's still one protocol.

---

## STEP 9 — Break the System

### Objective
Walk through realistic MCP-specific failures.

### Slide Content
| Failure | What Failed | Why | Detection | Recovery |
|---|---|---|---|---|
| MCP Server Down | Agent can't reach Drive at all | The Drive MCP server is temporarily unavailable | Request times out or errors immediately | Retry with backoff, then fall back to cached context |
| Wrong Section Retrieved | Agent gets the wrong part of a long document | Section boundaries were misidentified during indexing | Retrieved content doesn't match the query's actual topic | Re-check section splitting logic, re-index |
| Stale Context | Retrieved section is outdated | The document was updated after it was indexed | Agent's reasoning contradicts the current live document | Re-index on document update, not on a fixed schedule |
| Write Conflict | Two agents try to update the same Doc at once | No coordination between concurrent writes | One agent's update silently overwrites another's | Use a lock or version check before writing |
| Over-Broad Retrieval | Too many loosely related sections rank as relevant | Ranking threshold too permissive | The genuinely useful section gets buried among noise | Tighten the relevance threshold, re-test ranking |
| Silent Write Failure | A Gmail draft never actually gets created | Write call failed but wasn't checked | Escalation report references a draft that doesn't exist | Confirm every write succeeded before proceeding |
| Protocol Mismatch | A new MCP server returns malformed responses | The new server doesn't correctly implement the expected interface | Agent receives unexpected response shape, reasoning breaks | Validate every MCP server against the protocol spec before connecting it |

### What To Say
Let's break this system on purpose.

First, the MCP server itself goes down. Drive is temporarily unreachable. We catch this the moment a request times out or errors immediately. We fix it with a retry and a backoff, and if that fails too, a fallback to whatever context we have cached.

Second, the wrong section gets retrieved. A long document gets split incorrectly during indexing, and the agent ends up with a piece that doesn't actually match what it asked for. We catch this when the retrieved content clearly doesn't match the query's topic. We fix it by rechecking how sections are being split, and re-indexing.

Third, stale context. A document gets updated in Drive, but our index still has the old version. We catch this when the agent's reasoning contradicts what's actually live right now. We fix it by re-indexing whenever a document changes, not on some fixed schedule.

Fourth, a write conflict. Two agents try to update the same Doc at nearly the same time, and one silently overwrites the other. We catch this by noticing an update that seems to have vanished. We fix it with a lock, or a version check, before any write goes through.

Fifth, over-broad retrieval. The ranking threshold is too loose, and too many loosely related sections all score as relevant, burying the one that actually matters. We catch this when useful context seems to get lost in noise. We fix it by tightening the threshold and re-testing.

Sixth, a silent write failure. A Gmail draft was supposed to get created, but the write call failed, and nobody checked. We catch this when a later step references a draft that never actually got made. We fix it by confirming every write actually succeeded before moving forward.

Seventh, a protocol mismatch. A brand new MCP server gets connected, but it doesn't correctly follow the expected interface, and the agent receives something malformed. We catch this when responses come back in an unexpected shape. We fix it by validating any new MCP server against the protocol specification before we ever connect it.

---

## STEP 10 — Final Professional Definition

### Objective
Deliver the complete definition, tracing every clause to its origin, and compress into one mental model.

### Slide Content
> **Model Context Protocol is the capability that lets an agent connect to any enterprise system, Drive, Docs, Gmail, Chat, or something not yet invented, through one standard interface that retrieves structured, ranked context and performs real actions, without the agent's own reasoning ever needing to change.**

**The one mental model to leave with:**
```
   AGENT → MCP (same interface, every time) → [READ: Drive/Docs] or [WRITE: Docs/Gmail/Chat] → STANDARD RESPONSE
```

### What To Say
Let's put the whole thing together. MCP lets an agent connect to any enterprise system, whether that's Drive, Docs, Gmail, Chat, or something the company hasn't even adopted yet, through one standard interface. It retrieves structured, ranked context, that's our advanced RAG and context engineering. And it performs real actions through that same interface. Through all of it, the agent's own reasoning never has to change.

Here's the sentence I want you to remember. MCP is not another tool. It's the universal language that lets any agent use any enterprise system.

---
---

# S-TIER 2 — Agent-to-Agent Communication & Capability Discovery

**Bridge from S-Tier 1:** Our agent now connects beautifully to Drive, Docs, Gmail, and Chat, all through one standard interface. But MCP connects an agent to tools. What about connecting one agent to another agent?

---

## STEP 1 — Definition

### Objective
Introduce A2A simply, creating curiosity, with no implementation detail.

### Slide Content
> **Agent-to-Agent Protocol is teaching agents to talk to each other directly.**

### What To Say
Here's our second definition for the week. Agent-to-Agent Protocol is teaching agents to talk to each other directly.

Right now, our four agents work together because we personally wired them together in Week 4. What happens the day a completely different team, somewhere else in the company, builds their own agent, with no idea our system even exists? Can our agents find it? Can they use it? Today's lecture answers that.

---

## STEP 2 — Create the Need

### Objective
Establish why hard-coded connections between agents don't scale into a real organization.

### Slide Content
**Unresolved problem from the last lecture:** MCP lets our agent talk to any tool through a standard interface. But our four agents only know about each other because we hard-wired that connection ourselves.

| Without A2A | With A2A |
|---|---|
| Agents only know about coworkers they were hard-wired to | Agents can discover any registered agent dynamically |
| A new agent requires manually rewriting existing code | A new agent registers itself and becomes discoverable |
| An upgraded agent silently breaks old connections | Agents can discover the current version automatically |
| Capabilities live locked inside one specific agent | Capabilities exist as reusable skills any agent can call |

### What To Say
Here's the problem. Right now, our four agents, Extract, Cluster, Score, Escalate, only work together because we personally wired them together inside one orchestrator. If a different team builds their own specialized Compliance Agent, completely independently, our agents have no way to even find it, let alone ask it for help, unless someone manually goes back and hard-codes a new connection.

And even if we did that, here's a second problem. What happens when that Compliance Agent gets upgraded next month? Our hard-coded wiring doesn't know an upgrade happened. It's still pointing at the old version, or worse, it just breaks, because something about the interface quietly changed underneath it.

Picture a company where every employee can only ever talk to the exact coworkers they were personally introduced to on their first day. If someone new joins next month with exactly the skill you need, you have no way of finding out they even exist, let alone asking them for help. This is exactly what we cannot do.

Agents wired together only by hard-coded connections are exactly this. Blind to anyone they weren't personally introduced to.

Here's a second picture. A phone system with no directory at all. To call anyone, you'd need to already have memorized their exact number in advance. If that person got a new number, or someone new joined the company, you'd have no way to reach them. This is exactly what we cannot do.

A2A, with a discovery registry, is exactly what a phone directory gives a phone system. The ability to find someone you didn't already know the number for.

You'll see this elsewhere. A hospital's diagnostic agent with no way to discover a newly deployed specialist agent for a rare disease, because nobody manually wired the two together. A fraud-detection agent that can't discover a newly built compliance-reporting agent, so every flagged transaction still needs a human to manually bridge the two systems.

Back to our own agent. A new Compliance Agent gets built by another team. Our existing Escalation Agent needs to find it and hand it work, without anyone rewriting our orchestrator.

---

## STEP 3 — Build the Core Idea

### Objective
Build intuition for discovery and reusable skills, without implementation.

### Slide Content
**Running Example:** The EPC Agent ecosystem, now including a newly built Compliance Agent nobody manually wired in.

### What To Say
Let's build some intuition.

A new specialist joining a hospital doesn't need the entire hospital staff to individually meet them one by one before they can be useful. They get added to the hospital directory, and the moment a nurse needs that specialty, they look them up and reach out.

Here's a second picture. A freelance contractor with a valuable, specific skill, say expert contract review, doesn't need to be hired full-time by every company that might ever need that skill. They list their capability somewhere findable, and different companies discover and engage them exactly when needed.

A2A is like a company directory. You don't need to have personally met someone to find out they exist, what they do, and how to reach them. Agent Skills are like professional certifications. A "Root Cause Analysis" certification means the same thing no matter who holds it, and someone with that skill can be swapped in or out without changing what the certification itself guarantees.

Here's something simpler. An app store, where any app can be found and used by anyone, without the developer needing a personal relationship with every single user. A search platform for freelancers, where you post the skill you need, and anyone qualified, whoever they turn out to be, can be discovered and hired for that task.

So here's the distinction I want you to hold onto. Orchestration, from Week 4, is "who runs next in a sequence I personally designed." A2A is "how does one agent even find and talk to another agent I didn't personally wire in."

---

## STEP 4 — MER Exploration (First Cycle)

### Objective
Present A2A through diagram, execution flow, and code, introducing Agent Discovery & Registry and Agent Skills as the execution flow naturally reaches each.

---

### Representation 1 — Concept Diagram

#### Slide Content
```
   AGENT 1  ──▶  A2A  ──▶  AGENT 2  ──▶  A2A  ──▶  AGENT 3
```

#### What To Say
Here's the shape. One agent talks to another through A2A. That agent talks to a third, also through A2A. No specific industry, no specific agent names yet, just the shape of agent-to-agent communication.

In healthcare, a diagnostic agent talks to a specialist agent through A2A. In finance, a fraud agent talks to a compliance agent through A2A. The diagram doesn't move either time.

---

### Representation 2 — Execution Flow (EPC Agent)

#### Slide Content
```
1. A NEW AGENT (COMPLIANCE AGENT) COMES ONLINE
2. HOW DOES ANYONE KNOW IT EXISTS?          → (Agent Discovery & Registry)
3. OTHER AGENTS DISCOVER IT DYNAMICALLY
4. HOW DOES IT SAY WHAT IT CAN ACTUALLY DO?  → (Agent Skills)
5. COMMUNICATION BEGINS THROUGH A2A
```

#### What To Say
Step one, a brand new Compliance Agent comes online, built by a team we've never worked with.

Step two, here's a real question. How does anyone even know it exists? This is agent discovery. Instead of hard-coding its address somewhere, it registers itself with a shared registry the moment it starts up.

Step three, our existing agents don't need to know its address in advance. They query the registry, and discover it dynamically, whenever they actually need it.

Step four, another real question. Once it's discoverable, how does it say what it can actually do? This is agent skills. A skill is a reusable, versioned capability, something like "Compliance Check version one," that describes exactly what this agent offers, in a way any other agent can understand without reading its internal code.

Step five, communication begins through A2A, using that discovered skill.

---

### Representation 3 — Code

#### Slide Content
```python
def register_agent(agent, skills):
    registry.add(agent.id, skills)               # e.g. ["compliance_check_v1"]

def discover_agent_for_skill(skill_name):
    return registry.find(skill_name)              # dynamic lookup, no hard-coded address

def call_via_a2a(skill_name, payload):
    agent = discover_agent_for_skill(skill_name)
    return agent.invoke(skill_name, payload)
```

#### What To Say
Three small functions. The first registers an agent and its skills. The second looks up who currently offers a given skill, no hard-coded address anywhere. The third combines both, discovering the right agent and calling it. Our Escalation Agent never needs to know the Compliance Agent's actual address. It just asks for the skill by name.

---

### Context Progression — Four Industries, Rapid Switch

#### Slide Content
| | Concept Diagram | Execution Flow | Code |
|---|---|---|---|
| **Healthcare** | *Unchanged* | New specialist agent registers → discovered by diagnostic agent → invoked via A2A | Same three functions, different skill names |
| **Finance** | *Unchanged* | New compliance agent registers → discovered by fraud agent → invoked via A2A | Same three functions, different skill names |
| **Legal** | *Unchanged* | New risk-review agent registers → discovered by intake agent → invoked via A2A | Same three functions, different skill names |
| **Manufacturing** | *Unchanged* | New calibration agent registers → discovered by inspection agent → invoked via A2A | Same three functions, different skill names |

#### What To Say
Four industries, moving quickly. Diagram frozen. The execution flow keeps the same five steps, only which new agent shows up changes. The code stays the same three functions, only the skill names being registered and discovered change underneath it.

---

## STEP 5 — Return to the Definition

### Objective
Expand the Step 1 definition using only what's been experienced so far.

### Slide Content
> **Agent-to-Agent Protocol is teaching agents to register what they can do, discover each other dynamically, and communicate directly, without anyone hard-coding the connection in advance.**

### What To Say
Agent-to-Agent Protocol is teaching agents to register what they can do, that's the registry. To discover each other dynamically, that's discovery. And to communicate directly, without anyone hard-coding the connection in advance. Nothing new, just vocabulary attached to what you already built.

---

## STEP 6 — Increase Scope: One Ecosystem, More Responsibilities

### Objective
Expand responsibilities across a growing, multi-agent ecosystem, still keeping Week 4's orchestrated pipeline intact.

### Slide Content
**The EPC ecosystem must now:**
- Support multiple agents registering at once
- Support multiple skills existing across different agents
- Let agents get upgraded without breaking existing callers
- Keep Week 4's orchestrated four-agent pipeline working alongside all of this

### What To Say
Let's grow this. The ecosystem now supports multiple agents registering at once, not just one new Compliance Agent, potentially dozens over time. It supports multiple skills, spread across different agents, not one agent trying to do everything.

Here's what's genuinely new. It needs to let an agent get upgraded, a new version deployed, without breaking whoever was already calling it. And through all of this, our original four-agent pipeline from Week 4, Extract, Cluster, Score, Escalate, still needs to keep working exactly as it did.

A legal ecosystem where a risk-review skill gets a new version, while the intake agent that calls it keeps working without any changes. A manufacturing ecosystem where a calibration skill gets upgraded, while the inspection agent that relies on it never notices.

---

## STEP 7 — Repeat MER: Add Skill Versioning & Safe Upgrades

### Objective
Add exactly one new layer, letting skills evolve safely without breaking agents that already depend on them.

---

### Representation 1 (Expanded) — Concept Diagram

#### Slide Content
```
   AGENT 1 ──▶ A2A ──▶ DISCOVER SKILL ──▶ [Registry resolves current version] ──▶ AGENT 2 (v2, current)
                                                                                        │
                                                                          (Agent 2 v1 is retired,
                                                                           old callers unaffected)
```

#### What To Say
Look at the diagram from before, untouched. What's new is what happens the moment discovery resolves a skill. The registry doesn't just say "here's an agent." It says "here's the current version of this skill." When the skill provider gets upgraded, the registry starts pointing to the new version automatically, and any agent that was simply asking for the skill by name, not a specific version, keeps working without any changes on its end.

---

### Representation 2 (Expanded) — Execution Flow

#### Slide Content
```
1. AGENT REQUESTS A SKILL BY NAME (not a specific agent, not a specific version)
2. REGISTRY RESOLVES TO THE CURRENT VERSION            ← NEW
3. IF THE PROVIDER IS UPGRADED, THE REGISTRY POINTS TO THE NEW VERSION AUTOMATICALLY  ← NEW
4. OLD CALLERS KEEP WORKING, BECAUSE THEY ASKED FOR THE SKILL, NOT A SPECIFIC AGENT
5. COMMUNICATION PROCEEDS THROUGH A2A AS BEFORE
```

#### What To Say
Step one is the important habit. An agent asks for a skill by name, never for a specific agent or version directly. Step two, the registry does the work of resolving that to whatever is current right now. Step three, when a new version ships, the registry updates, quietly, without anyone needing to touch the calling agent's code. Step four is the actual payoff. Because the caller only ever asked for the skill, it never breaks when the version underneath it changes.

---

### Representation 3 (Expanded) — Code

#### Slide Content
```python
def discover_agent_for_skill(skill_name, version="latest"):
    return registry.find(skill_name, version)   # resolves to current version automatically
```

#### What To Say
One small change to a function we already had. A version parameter, defaulting to "latest." Every caller that doesn't specify a version automatically gets routed to whatever is current, without ever needing to know a new version even shipped.

---

### Four Additional Context Changes

#### Slide Content
| Industry | Skill Upgraded | What Changed | Who Was Unaffected |
|---|---|---|---|
| Healthcare | Root Cause Analysis v1 → v2 | Improved accuracy on rare conditions | Every agent that called it by name |
| Finance | Risk Scoring v1 → v2 | Added a new fraud pattern | Every agent that called it by name |
| Legal | Clause Review v1 → v2 | Expanded to new contract types | Every agent that called it by name |
| Cybersecurity | Threat Correlation v1 → v2 | Faster cross-system matching | Every agent that called it by name |

#### What To Say
Same story, four industries. In every single row, something genuinely improved underneath the hood, and every agent that simply asked for the skill by name never had to change a single line of code because of it.

---

## STEP 8 — Expand the Definition Again

### Objective
Incorporate only the versioning and safe-upgrade concept from the second MER cycle.

### Slide Content
> **Agent-to-Agent Protocol is teaching agents to register what they can do, discover each other dynamically, communicate directly, and safely receive upgrades to the skills they depend on — without anyone hard-coding the connection, and without breaking anyone already relying on it.**

### What To Say
One more addition. Everything through "communicate directly" is exactly what we had before. What's new is this: safely receive upgrades to the skills they depend on, without breaking anyone already relying on it. That's this cycle's contribution.

---

## STEP 9 — Break the System

### Objective
Walk through realistic A2A and discovery failures.

### Slide Content
| Failure | What Failed | Why | Detection | Recovery |
|---|---|---|---|---|
| Stale Registry Entry | Registry lists an agent that's actually offline | The agent crashed without deregistering | Calls to it consistently fail or time out | Require periodic heartbeat, remove entries that stop responding |
| Duplicate Registration | Same agent registers twice | A restart didn't clean up the old entry first | Registry shows two entries for one real agent | Deregister on shutdown, and de-duplicate on registration |
| Skill Name Collision | Two different agents register the same skill name | No naming convention was enforced | Wrong agent gets called for a given skill | Enforce unique, namespaced skill names |
| Version Mismatch | A caller expects one response shape, gets another | Caller assumed a version it didn't actually pin | Caller's code errors on an unexpected response | Encourage explicit version pinning where compatibility matters |
| Discovery Timeout | A lookup takes too long, the request stalls | Registry itself is under heavy load | Requests hang noticeably before failing | Add a timeout and a clear fallback if discovery is slow |
| Circular Discovery | Agent A calls Agent B, which calls back to Agent A | No cycle detection in the call chain | The system hangs or loops indefinitely | Track the call chain, reject a call that would create a cycle |
| Silent Capability Loss | A registered skill fails on its very first real call | The skill was registered before it was actually tested | First real caller receives a broken response | Require a working health check before a skill can register |

### What To Say
Let's break this system too.

First, a stale registry entry. An agent crashes without properly deregistering, and the registry still lists it as available. We catch this when calls to it consistently fail or time out. We fix it by requiring a periodic heartbeat, and removing entries that stop responding.

Second, duplicate registration. An agent restarts, but its old registry entry never got cleaned up, so now there are two. We catch this when the registry shows two entries for what's really one agent. We fix it by deregistering properly on shutdown, and de-duplicating on registration.

Third, a skill name collision. Two completely different agents register a skill using the same name. We catch this when the wrong agent ends up getting called for a given skill. We fix it by enforcing unique, namespaced skill names from the start.

Fourth, a version mismatch. A caller assumed a certain response shape, but didn't actually pin the version it wanted, and got something different back. We catch this when the caller's own code errors on an unexpected shape. We fix it by encouraging explicit version pinning wherever real compatibility matters.

Fifth, a discovery timeout. The registry itself is under heavy load, and a lookup takes too long. We catch this when requests visibly hang before eventually failing. We fix it with a clear timeout, and a fallback if discovery is running slow.

Sixth, circular discovery. Agent A calls Agent B through a skill, and that skill's own logic ends up calling back to Agent A, looping forever. We catch this when the system simply hangs. We fix it by tracking the call chain, and rejecting any call that would create a cycle.

Seventh, silent capability loss. A skill gets registered before it was actually properly tested, and it fails the moment a real caller uses it. We catch this when the first genuine caller gets a broken response. We fix it by requiring a working health check before a skill is even allowed to register.

---

## STEP 10 — Final Professional Definition

### Objective
Deliver the final, complete definition, tracing every clause to its origin, and compress the entire S-Tier 2 lecture into one mental model.

### Slide Content
> **Agent-to-Agent Protocol is the capability that lets independently built agents register what they can do, discover each other dynamically without hard-coded connections, communicate directly through a shared protocol, and safely evolve their capabilities through versioned skills, so the ecosystem can grow without anyone rewriting the agents that already exist.**

**The one mental model to leave with:**
```
   AGENT → REGISTER SKILL → REGISTRY → DISCOVER → A2A COMMUNICATION → (version resolved automatically)
```

### What To Say
Let's bring the whole week together. A2A lets independently built agents register what they can do, that's the registry. Discover each other dynamically, without hard-coded connections, that's discovery. Communicate directly through a shared protocol. And safely evolve their capabilities through versioned skills, that's this cycle's contribution.

Here's the distinction I want you to carry forward. MCP connects an agent to tools. A2A connects an agent to other agents. Together, they mean nothing our system ever needs, a tool, or a colleague, requires hard-coded wiring anymore. Everything is either discoverable, or reachable through one standard interface.

That's the shape of a real enterprise AI platform. Not one giant agent that knows everything. Not even four agents wired together by hand. A living ecosystem, where new tools and new agents can join, get discovered, and get used, without anyone going back and rewriting what already works.
