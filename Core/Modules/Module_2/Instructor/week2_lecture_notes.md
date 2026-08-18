# Instructor Lecture Notes — Week 2
## Two Complete Learning Journeys: Retrieve Before You Decide, Decide Before You Act

**Running Project:** Enterprise Problem Consolidation (EPC) Agent

---
---

# S-TIER 1 — Retrieval-Augmented Generation (RAG)

---

## STEP 1 — Definition

### Objective
Introduce RAG in the simplest language possible, creating curiosity without touching embeddings, vector math, or code.

### Slide Content
> **Retrieval-Augmented Generation is looking up facts before answering a question.**

### What To Say
Read that sentence with me. Retrieval-Augmented Generation is looking up facts before answering a question.

Think about the last time you answered a hard question. Did you answer it purely from memory, or did you look something up first? Most of the time, when the question actually matters, we look something up first.

Last week, we built an agent that could understand a ticket, remember it, and make a decision. But everything it knew came from that one ticket alone. It never checked anything against the company's own history. Today, we ask a new question. What happens when the right answer depends on something the agent wasn't told directly, but that already exists somewhere in the company's records?

We are not going to talk about vectors or databases yet. Right now, I just want you to hold onto one idea. Looking something up first is a different skill from reasoning well. Today's lecture is about that first skill.

---

## STEP 2 — Create the Need

### Objective
Establish why an LLM's built-in knowledge is not enough, using analogies, before returning to the EPC Agent.

### Slide Content
**Unresolved problem from last lecture:** Our agent can perceive, understand, remember, and decide — but everything it "remembers" so far is only what happened inside one single ticket's own text.

| Without RAG | With RAG |
|---|---|
| Agent only knows general language patterns | Agent can look up the company's own history |
| Every ticket treated in total isolation | Related tickets recognized even with different wording |
| Cannot fit all historical tickets into one prompt | Retrieves only the few genuinely relevant pieces |
| Conclusions are generic, not evidence-based | Conclusions are grounded in real historical incidents |

### What To Say
Here is the problem we left unsolved last time. A large language model only knows general patterns from its training. It has no access to this specific company's actual tickets. So it cannot tell you whether today's complaint looks like something that happened last month, unless a person types that information into the prompt directly. And if a person has to do that every time, we haven't really built an autonomous agent. We've just built a very polite assistant.

You might think, why not just paste every past ticket into every new prompt? Because real companies have thousands of tickets. Far too many to fit into one prompt. So the agent needs a way to find the few tickets that actually matter, before it starts reasoning. Not all of them. Just the right ones.

Let me give you two ways to picture this.

Imagine a brand new employee on their very first morning at a company. You expect them to recognize that today's complaint is the same issue that happened three months ago. But nobody has given them access to any past records. All they have is general life experience. This is exactly what we cannot do.

An AI without retrieval is that new employee. Smart, but cut off from the company's own history.

Here's a second picture. Imagine a librarian who has read a huge number of books in her life. But she's been given no card catalog, no way to search the specific library she's standing in. She knows a lot in general, but she cannot find the one book on the shelf in front of her. This is exactly what we cannot do.

Retrieval is the card catalog. It doesn't teach the librarian anything new. It just lets her find what's already there.

You'll see the same gap in other industries. A doctor's AI assistant knows medicine broadly, but without retrieval, it cannot check whether this exact set of symptoms already showed up in this hospital last week. A legal AI reviewing a contract clause knows contract law in general, but without retrieval, it cannot check whether this clause resembles one this firm already flagged as risky.

Now let's bring this back to our own agent. A new ticket arrives. The question this entire lecture exists to answer is simple: does this ticket describe something that already exists in our history, or is it something new? Without retrieval, our agent can only guess from the ticket's own words. Just like the new employee. Just like the librarian with no catalog.

---

## STEP 3 — Build the Core Idea

### Objective
Build intuition for why retrieval matters, without touching embeddings or implementation.

### Slide Content
**Running Example:** The EPC Agent, asking one question about every new ticket: *"Does this describe an issue that already exists?"*

### What To Say
Let's build some intuition before we touch any technical detail.

Picture two tickets, filed three days apart. The first one says, "export keeps failing." The second one says, "can't get my PDF to download." If you read both of these, you'd probably guess they're the same underlying bug. But notice, they share almost no words in common. An agent that can only compare exact text would treat these as two completely separate, unrelated events.

Here's a second picture. A researcher writing a report doesn't recall every fact from memory. She walks to the shelf, pulls out the three most relevant books for her specific question, skims them, and writes her report grounded in what she found. Not purely from what she already knew walking in.

Think of it like a doctor. Before diagnosing an unusual symptom, a good doctor will quickly check similar past cases rather than relying purely on memory from medical school. Or think of a detective. Before settling on a theory, a good detective pulls the case files of similar past crimes to see if tonight's pattern matches something already solved.

Here's something even simpler. When you look up a fact on a search engine before answering a question yourself, that's retrieval. When a doctor pulls up your chart before diagnosing you today, instead of starting from nothing, that's retrieval too.

So here's the idea I want you to leave with. An AI's trained knowledge is not the same thing as a specific company's specific facts. Retrieval is the bridge between the two. Find the right evidence first. Then reason using that evidence.

---

## STEP 4 — MER Exploration (First Cycle)

### Objective
Present RAG through diagram, execution flow, and code — introducing Embeddings, Vector Databases, and Similarity Search only where the execution flow naturally requires them.

---

### Representation 1 — Concept Diagram

#### Slide Content
```
   QUERY  ──▶  SEARCH KNOWLEDGE  ──▶  RETRIEVE RELEVANT PIECES  ──▶  REASON WITH EVIDENCE
```

#### What To Say
Here's the shape of retrieval. A query comes in. We search the available knowledge. We retrieve the relevant pieces. We reason using that evidence. Notice, there's nothing here about tickets, or embeddings, or any specific industry. This is just the shape of retrieval itself.

Now let's change the setting and see what stays the same. In healthcare, the query is a symptom. The knowledge we search is past case files. What we retrieve is similar prior cases. The diagram hasn't changed at all.

In legal work, the query is a contract clause. The knowledge we search is a firm's past contracts. What we retrieve is similar prior clauses. Again, the diagram hasn't moved.

Why doesn't it change? Because this diagram describes behavior, not a specific kind of knowledge. Retrieving before reasoning is the behavior. What kind of knowledge, and how it's stored, that's a detail that belongs one level down. We'll get there next.

---

### Representation 2 — Execution Flow (EPC Agent)

#### Slide Content
```
1. TICKET ARRIVES
2. TEXT MUST BECOME COMPARABLE      → (Embeddings)
3. COMPARABLE VECTORS NEED A HOME   → (Vector Databases)
4. SEARCH FOR NEAREST MATCHES       → (Similarity Search)
5. RETRIEVED TICKETS INFORM A DECISION → (Ticket Clustering)
```

#### What To Say
Let's walk through what actually happens when a ticket arrives at our agent.

Step one, the ticket arrives. Simple enough.

Step two, here's a real question. How can our agent compare two tickets that use completely different words? Remember our earlier example: "export keeps failing" and "can't get my PDF to download." The answer is something called embeddings. An embedding turns text into a list of numbers, in such a way that texts with similar meaning end up with similar numbers, even when the actual words don't match at all. I want you to remember one sentence: embeddings are how meaning becomes measurable.

Step three, once every ticket has become a set of numbers, where do we keep them? That's where a vector database comes in. It's simply the place where these number lists live, organized so we can quickly find the ones that are closest to a new one, even across millions of entries. This isn't new infrastructure for its own sake. It exists purely to make retrieval possible.

Step four, this is the moment the vector database actually does its job. Given a new ticket's numbers, it returns the handful of stored tickets that sit closest to it. This is called similarity search. Don't worry about the exact math behind "closest." Just focus on what this step accomplishes: it finds the tickets that matter.

Step five, now that we have similar past tickets in hand, our agent has to decide something. Does this new ticket belong to a cluster of related problems we already know about, or is it something entirely new? This decision, ticket clustering, is what the rest of this lecture keeps coming back to.

Let's change industries and watch the same five steps happen again. In healthcare, a symptom arrives, gets turned into numbers, gets searched against a database of case files, the nearest cases come back, and a decision gets made about which diagnosis group this belongs to. Same five steps, same order.

In legal work, a new clause arrives, gets turned into numbers, gets searched against a database of past contracts, the nearest clauses come back, and a decision gets made about which risk group this belongs to. Same five steps again.

---

### Representation 3 — Code

#### Slide Content
```python
def retrieve_similar_tickets(new_ticket_text, vector_index, top_k=3):
    query_vector = embed(new_ticket_text)             # text -> comparable numbers
    return vector_index.search(query_vector, top_k)   # the few genuinely relevant matches
```

#### What To Say
Here's what that looks like in code. Three lines. The first line turns text into numbers, that's `embed`. The second line searches the vector database for the closest matches, that's `vector_index.search`. That's it. This code isn't here to teach you a specific library. It's here to prove that everything we just talked about is real, and it's small.

---

### Context Progression — Four Industries, Rapid Switch

#### Slide Content
| | Concept Diagram | Execution Flow | Code |
|---|---|---|---|
| **Healthcare** | *Unchanged* | Symptom → embed → case-file index → nearest cases → diagnosis grouping | Same function, different index |
| **Finance** | *Unchanged* | Transaction → embed → fraud-pattern index → nearest patterns → fraud grouping | Same function, different index |
| **HR** | *Unchanged* | Complaint → embed → case index → nearest complaints → case grouping | Same function, different index |
| **Cybersecurity** | *Unchanged* | Log entry → embed → threat index → nearest incidents → threat grouping | Same function, different index |

#### What To Say
Let's move quickly through four industries. Healthcare, finance, HR, cybersecurity. In every single one, the diagram never moves. The execution flow keeps the same five steps, only the content running through it changes. The code stays exactly the same two lines. Only which vector index gets passed in is different.

Here's the sentence I want you to remember for the rest of this course. The diagram is the behavior. The execution flow is that behavior applied to a specific domain. The code is the execution flow, made real.

---

## STEP 5 — Return to the Definition

### Objective
Expand the Step 1 definition using only concepts just experienced.

### Slide Content
> **Retrieval-Augmented Generation is teaching a computer to turn text into comparable numbers, store them, search for the closest matches, and reason using only what it actually found — not just what it already knew.**

### What To Say
Let's go back to our original definition and add to it, using only what we just built.

Retrieval-Augmented Generation is teaching a computer to turn text into comparable numbers. That's embeddings. To store them. That's the vector database. To search for the closest matches. That's similarity search. And to reason using only what it actually found, not just what it already knew. That's the whole reason we started this lecture.

Nothing in this sentence is new to you. It's just the vocabulary attached to what you already understand.

---

## STEP 6 — Increase Scope: One Agent, More Responsibilities

### Objective
Move from "retrieve knowledge" to "retrieve knowledge, then decide what it means" — still one agent, no multi-agent systems.

### Slide Content
**The EPC Agent must now:**
- Understand the new ticket
- Retrieve related historical tickets
- Decide: existing cluster, or new one?
- Update the historical record with that decision

### What To Say
Let's grow our agent's responsibilities. To be clear, this is still one agent. We are not adding more agents. We're asking the same agent to do more.

It needs to understand the new ticket. It needs to retrieve related tickets from history. It needs to decide whether this belongs to an existing cluster or represents something new. And here's the new part: it needs to update the historical record with that decision, so the next ticket that comes in benefits from what we just learned.

Let's see this in two more industries. A manufacturing agent monitoring equipment sensors now has to decide whether a new reading matches a known failure pattern, or represents something new, and update that equipment's history. A cybersecurity agent monitoring logs now has to decide whether a new alert matches a known attack pattern, or represents something new, and update the incident history.

---

## STEP 7 — Repeat MER: Add the Cluster-Decision Layer

### Objective
Add exactly one new layer to the existing diagram — the clustering decision itself — without discarding anything already built.

---

### Representation 1 (Expanded) — Concept Diagram

#### Slide Content
```
   QUERY ──▶ EMBED ──▶ SEARCH ──▶ RETRIEVE NEIGHBOURS ──▶ CLUSTER DECISION
                                                                  │
                                        ┌─────────────────────────┴─────────────────────────┐
                                        ▼                                                     ▼
                              JOIN EXISTING CLUSTER                                  CREATE NEW CLUSTER
                                        │                                                     │
                                        └───────────────────► UPDATE HISTORY ◄─────────────────┘
```

#### What To Say
Look back at the diagram from earlier. Everything up through retrieval is untouched. We're adding exactly one new thing: a decision point right after retrieval, with two possible outcomes. Either this ticket joins an existing cluster, or it creates a new one.

Notice both outcomes lead to the same place: update history. Even joining an existing cluster changes the record. That cluster now has one more member, one more piece of evidence.

---

### Representation 2 (Expanded) — Execution Flow

#### Slide Content
```
1. TICKET ARRIVES
2. EMBED
3. SEARCH VECTOR INDEX
4. RETRIEVE TOP MATCHES
5. COMPARE TOP MATCH AGAINST A SIMILARITY THRESHOLD   ← NEW
6. ABOVE THRESHOLD → JOIN EXISTING CLUSTER
   BELOW THRESHOLD → CREATE NEW CLUSTER                 ← NEW
7. UPDATE CLUSTER HISTORY
```

#### What To Say
Steps one through four are exactly what we already built. Here's what's new. Step five, we compare the top match against a similarity threshold. Retrieval by itself doesn't decide anything. A threshold is what turns a similarity score into an actual yes or no.

Step six, if we're above the threshold, we join the existing cluster. If we're below it, something new has appeared, and we create a new cluster.

---

### Representation 3 (Expanded) — Code

#### Slide Content
```python
def assign_cluster(new_ticket_text, vector_index, threshold=0.75):
    neighbours = retrieve_similar_tickets(new_ticket_text, vector_index)
    if neighbours and neighbours[0].score >= threshold:
        return neighbours[0].cluster_id       # join existing cluster
    return create_new_cluster(new_ticket_text)  # describes something new
```

#### What To Say
Notice this function calls `retrieve_similar_tickets`, the exact same function from before. We didn't rewrite anything. We just added one new decision on top of it. That's the same story as the diagram. We added, we didn't replace.

---

### Four Additional Context Changes

#### Slide Content
| Industry | What "Joining a Cluster" Means | What "New Cluster" Means |
|---|---|---|
| Healthcare | Symptom matches a known case pattern | A genuinely new symptom pattern |
| Finance | Transaction matches a known fraud pattern | A genuinely new fraud pattern |
| Legal | Clause matches a known risk pattern | A genuinely new risk pattern |
| Manufacturing | Reading matches a known failure pattern | A genuinely new failure pattern |

#### What To Say
Same threshold logic, same two outcomes, across four different industries. Here's the point I want you to take from this table. Clustering isn't a special idea that only applies to support tickets. It's simply what similarity search becomes the moment you attach a decision to it.

---

## STEP 8 — Expand the Definition Again

### Objective
Incorporate only the clustering-decision concept from the second MER cycle.

### Slide Content
> **Retrieval-Augmented Generation is teaching a computer to turn text into comparable numbers, store them, search for the closest matches, decide whether those matches are close enough to count as the same issue, and update its own memory of what it now knows — reasoning always from evidence, never from memory alone.**

### What To Say
One more addition to our definition. Everything up to "search for the closest matches" is exactly what we had before. What's new is this: decide whether those matches are close enough to count as the same issue, and update its own memory of what it now knows. That's the threshold and the update step we just built.

---

## STEP 9 — Break the System

### Objective
Walk through realistic RAG-specific failures the EPC Agent will encounter.

### Slide Content
| Failure | What Failed | Why | Detection | Recovery |
|---|---|---|---|---|
| Irrelevant Retrieval | Retrieved tickets share words but not meaning | Embedding captured surface similarity, not true intent | Human spot-check shows retrieved items are off-topic | Improve embedding prompt context, re-test on golden set |
| Stale Index | A true duplicate isn't found | The matching ticket hasn't been indexed yet | Known duplicate pair scores as "new" in evaluation | Index tickets immediately on arrival, not in a nightly batch |
| Threshold Too Loose | Unrelated tickets merged into one cluster | Threshold set too low | Cluster contains clearly unrelated summaries | Raise threshold, re-run evaluation |
| Threshold Too Strict | Same issue split into many small clusters | Threshold set too high | Many single-ticket clusters describing the same thing | Lower threshold, re-run evaluation |
| Empty Retrieval | Nothing similar found when something should exist | Indexing bug, or genuinely novel issue | Zero matches for a known repeat issue | Log the gap, fall back to a broader keyword search |
| Duplicate Cluster Creation | Two new clusters created for the same emerging issue | Two tickets processed at nearly the same moment, both saw "no match yet" | Two very similar clusters appear minutes apart | Periodic merge check; treat as a known race condition |
| Retrieval Ignored | Evidence was retrieved but the final decision didn't use it | Reasoning step and retrieval step became disconnected | Decision contradicts what was retrieved | Require the decision step to cite which retrieved item it used |

### What To Say
Now let's break this system on purpose, so we know what to watch for.

First, irrelevant retrieval. Sometimes two tickets share words but not meaning, or share meaning but not words, and the embedding gets it wrong. We catch this by spot-checking retrieved results against human judgment, and we fix it by improving what context goes into the embedding, not by assuming the first version is correct.

Second, a stale index. Retrieval can only find what's already been stored. If there's a delay between a ticket arriving and it being indexed, a true duplicate arriving in that gap becomes invisible. We catch this when known duplicate pairs fail to match during evaluation, and we fix it by indexing the moment a ticket arrives, not on a delay.

Third and fourth, the threshold problem, in both directions. Set it too loose, and unrelated tickets get merged into one cluster. Set it too strict, and one real problem gets split into many small clusters. Neither of these causes a crash. You catch both by actually reading what's inside each cluster and asking whether it makes sense.

Fifth, empty retrieval. Sometimes nothing comes back at all. That might mean the issue really is new, which is fine. Or it might mean something broke during indexing. We catch this by checking whether zero-match results correlate with issues we know are repeats, and we fix it with a broader fallback search.

Sixth, duplicate clusters. If two very similar tickets arrive close together, both might check the index before either one has been added, both find nothing, and both create their own new cluster. This is a race condition. We catch it by watching for near-duplicate clusters appearing minutes apart, and we fix it with a periodic merge check.

Seventh, and this one's subtle. Retrieval succeeds. Genuinely relevant evidence comes back. But the final decision doesn't actually use it. The reasoning step and the retrieval step became disconnected. We catch this by checking whether the final decision is consistent with what was retrieved, and we fix it by requiring the decision to explicitly reference which retrieved item it used.

---

## STEP 10 — Final Professional Definition

### Objective
Deliver the complete RAG definition, tracing every clause to its origin, and compress into one mental model.

### Slide Content
> **Retrieval-Augmented Generation is a capability that converts new information into comparable numbers, stores and searches those numbers to find genuinely relevant evidence, decides whether that evidence is close enough to represent the same underlying issue, and reasons only from what was actually found — keeping its own memory of what it knows continuously up to date.**

**The one mental model to leave with:**
```
   TICKET → EMBED → STORE/SEARCH → RETRIEVE → THRESHOLD DECISION → UPDATE MEMORY
```

### What To Say
Let's put the whole thing together. Retrieval-Augmented Generation converts new information into comparable numbers. That's embeddings. It stores and searches those numbers to find genuinely relevant evidence. That's the vector database and similarity search. It decides whether that evidence is close enough to represent the same underlying issue. That's our threshold decision. And it reasons only from what was actually found, keeping its own memory continuously up to date.

Here's a sentence worth remembering. RAG is not Vector Search. Vector Search is simply the retrieval engine that makes RAG possible.

And here's where we land it. Our agent will now use exactly this process, every single time, to decide whether a new ticket is an old friend or a brand new problem.

---
---

# S-TIER 2 — Tool Calling & Agent Decision Making

**Bridge from S-Tier 1:** The EPC Agent can now retrieve knowledge and decide whether a ticket is new or belongs to an existing cluster. Knowing that is not the same as doing anything about it.

---

## STEP 1 — Definition

### Objective
Introduce tool calling simply, creating curiosity, with no implementation detail.

### Slide Content
> **Tool Calling is teaching a computer to choose and use the right tool.**

### What To Say
Here's our second definition for the week. Tool Calling is teaching a computer to choose and use the right tool.

Our agent now knows a ticket belongs to a critical cluster. So what happens next? Sit with that question for a second. Nothing has actually happened yet. Knowing something changed nothing in the real world. That gap, between knowing and doing, is what today's lecture is about.

---

## STEP 2 — Create the Need

### Objective
Establish why retrieval and reasoning alone cannot produce real-world change.

### Slide Content
**Unresolved problem from the last lecture:** RAG lets the agent retrieve evidence and decide whether a ticket belongs to an existing cluster. But that decision, by itself, changes nothing in the real world.

| Without Tool Calling | With Tool Calling |
|---|---|
| Agent describes the right action in words | Agent actually performs the action |
| A human must read and manually execute every suggestion | The agent executes directly |
| Judgment and action are disconnected | Judgment leads directly to a real system change |
| An "autonomous" agent still needs manual follow-through | The agent is autonomous end-to-end |

### What To Say
A model that only produces text can describe the right action perfectly. It can write, "you should notify the on-call engineer." But writing that sentence and actually notifying the engineer are two different things. Nothing changes in the real system unless something bridges that gap.

You might think, fine, let a person read the model's suggestion and click the button. But think about what that actually means. We're back to a human standing between the AI's judgment and the actual outcome. That's the exact bottleneck we said we wanted to remove.

Here are two ways to picture this. Imagine a brilliant advisor who can tell you precisely which letter to send, exactly which button to click. But this advisor has no hands. They cannot type. They cannot click. No matter how correct their advice is, they can never act on it. This is exactly what we cannot do.

An AI without tool calling is that advisor. Always right, never able to act.

Here's a second picture. A translator knows exactly what to say in a foreign negotiation, word for word. But they're not allowed to step into the room and speak on your behalf. The correct words exist, but they're never spoken. This is exactly what we cannot do.

Tool calling is what lets the model's words become actions inside a real system.

You'll see the same problem elsewhere. A fraud model can correctly conclude that a transaction should be frozen. But if it can't actually call a function to freeze it, the fraud goes through anyway, while the correct answer sits unused in a sentence. An HR assistant can correctly conclude that a complaint needs to go to a specific manager. But if it can't create an actual case in the system, that correct conclusion never becomes anything real.

Back to our own agent. It now knows a ticket belongs to a cluster that just became critical. Knowing this changes nothing until the agent can actually update the record, log the decision, and notify the right person.

---

## STEP 3 — Build the Core Idea

### Objective
Build intuition for tool calling without implementation, using the EPC Agent as anchor.

### Slide Content
**Running Example:** The EPC Agent, having just decided a ticket belongs to a now-Critical cluster — what happens next?

### What To Say
Let's build intuition before we touch any code.

A doctor who diagnoses correctly has done nothing for the patient yet. The diagnosis matters only once the doctor writes the prescription and hands it to the nurse. Diagnosis is knowledge. Prescription is action. Only the second one changes anything for the patient.

Here's another picture. An assistant who notices an urgent scheduling conflict doesn't just think about it. They open the calendar, reserve the room, send the invite. Recognizing what's needed and actually doing it are two separate skills. Only the second one matters to the people waiting on the result.

Think of a chef who decides the dish needs more salt. A good chef reaches for the salt and adds it. They don't just think "this needs salt" and leave the dish as it is. Or think of a pilot who notices turbulence ahead. A good pilot adjusts the controls. They don't just write "turbulence ahead" in a logbook and fly straight through.

Even simple technology does this. A smart speaker doesn't just say, "I understand you want the lights off." It turns the lights off. A self-checkout machine doesn't just calculate your total. It opens the drawer and prints the receipt.

So here's what an agent actually needs. A defined set of things it's capable of doing, and the judgment to pick the right one at the right moment. That's the whole story of this lecture.

---

## STEP 4 — MER Exploration (First Cycle)

### Objective
Present tool calling through diagram, execution flow, and code — introducing Function Calling, Tool Use & Integration, and ReAct as the execution flow naturally reaches each.

---

### Representation 1 — Concept Diagram

#### Slide Content
```
   REASON  ──▶  CHOOSE TOOL  ──▶  EXECUTE  ──▶  OBSERVE RESULT
```

#### What To Say
Here's the shape of taking action. Reason. Choose a tool. Execute it. Observe the result. No specific tool, no specific industry, just the shape.

In healthcare, the agent reasons about a lab result, chooses the tool to order a new test, executes it, and observes the confirmation. Diagram hasn't moved.

In cybersecurity, the agent reasons about a suspicious login, chooses the tool to lock the account, executes it, and observes the confirmation. Diagram still hasn't moved.

Reason, choose, execute, observe. That's the invariant shape of taking action. Which tool exists, and what it actually does, that's a detail we handle one level down.

---

### Representation 2 — Execution Flow (EPC Agent)

#### Slide Content
```
1. AGENT HAS A CONCLUSION (from RAG: "belongs to Cluster C001, now Critical")
2. CONCLUSION BECOMES A STRUCTURED ACTION     → (Function Calling)
3. AGENT CHOOSES AMONG SEVERAL CAPABILITIES   → (Tool Use & Integration)
4. TOOL EXECUTES, RESULT RETURNS
5. AGENT REASONS AGAIN, MAY CHOOSE ANOTHER TOOL → (ReAct)
6. CONTINUE UNTIL FINISHED
```

#### What To Say
Let's walk through this using our own agent.

Step one, the agent has a conclusion from last lecture. This ticket belongs to Cluster C001, and that cluster just became critical.

Step two, here's the real question. How does the agent actually perform an action, instead of just describing one? The answer is function calling. Instead of writing another paragraph, the model produces a structured instruction, naming exactly which function to run and what to give it. This is the first real mechanism that turns words into action.

Step three, one tool is rarely enough. Our agent needs access to several. Searching cluster history. Looking up known issues. Updating a cluster. Storing an audit log. Triggering a notification. The agent's job is no longer to answer a question. Its job is to decide which of these capabilities should run next.

Step five, sometimes one tool call isn't enough. The agent updates the cluster, observes the result, and realizes it also needs to send a notification. That means reasoning again after acting, not just once at the start. This back and forth, reason, choose a tool, observe, reason again, choose another, continue, finish, is called ReAct. I want to be clear about one thing. ReAct is not another type of tool. It's a smarter pattern for deciding when tools should be used, possibly more than once.

---

### Representation 3 — Code

#### Slide Content
```python
def choose_and_run_tool(conclusion, available_tools):
    tool_name, args = model_pick_tool(conclusion, available_tools)  # model decides
    return available_tools[tool_name](**args)                      # actually runs it


def react_loop(conclusion, available_tools, max_steps=3):
    for _ in range(max_steps):
        result = choose_and_run_tool(conclusion, available_tools)
        if result.is_final:
            return result
        conclusion = reason_again(conclusion, result)   # observe, then reason again
```

#### What To Say
Six lines of code. The first function, `choose_and_run_tool`, covers steps two through four. It picks a tool and runs it. The second function, `react_loop`, is step five. Look closely. It just calls the first function again, inside a loop. That's it. That's ReAct. A pattern wrapped around the same building block we already had.

---

### Context Progression — Four Industries, Rapid Switch

#### Slide Content
| | Concept Diagram | Execution Flow | Code |
|---|---|---|---|
| **Healthcare** | *Unchanged* | Choose among `order_lab_test` / `notify_physician` / `update_chart` | Same functions, different tool registry |
| **Finance** | *Unchanged* | Choose among `freeze_transaction` / `flag_review` / `log_audit` | Same functions, different tool registry |
| **HR** | *Unchanged* | Choose among `create_case` / `notify_manager` / `log_complaint` | Same functions, different tool registry |
| **Cybersecurity** | *Unchanged* | Choose among `lock_account` / `alert_soc` / `log_incident` | Same functions, different tool registry |

#### What To Say
Four industries, moving quickly. The diagram never changes. The execution flow keeps the same stages, only the names of the tools change. The code stays the same two functions. Only the list of available tools passed in is different.

Same sentence as before. The diagram is the behavior. The execution flow is that behavior applied to a domain. The code is the execution flow, made real.

---

## STEP 5 — Return to the Definition

### Objective
Expand the Step 1 definition using only what's been experienced so far.

### Slide Content
> **Tool Calling is teaching a computer to turn a conclusion into a structured action, choose correctly among several available capabilities, execute it directly, and reason again if one action wasn't enough — without a human clicking the button.**

### What To Say
Let's expand our definition. Tool calling is teaching a computer to turn a conclusion into a structured action, that's function calling. To choose correctly among several available capabilities, that's tool use and integration. To execute it directly. And to reason again if one action wasn't enough, that's ReAct. All without a human clicking a button.

Nothing new here. Just names attached to what you already built.

---

## STEP 6 — Increase Scope: One Agent, More Responsibilities

### Objective
Expand responsibilities within one agent — still no multi-agent systems.

### Slide Content
**The EPC Agent must now:**
- Reason about a conclusion
- Choose among several tools
- Execute the chosen tool
- Observe the result
- Reason again if the result isn't sufficient
- Handle a tool that fails

### What To Say
Let's grow this agent's responsibilities. Again, to be clear, this is one agent doing more, not several agents working together.

It reasons about a conclusion. It chooses among several tools. It executes the chosen tool. It observes the result. It reasons again if that result isn't enough. And here's what's new: it has to handle a tool that fails. That last one is going to lead us into our next MER cycle, because it raises a bigger question. What does this agent look like once nobody is triggering it at all?

Picture a legal document agent that has to handle the case where its clause-flagging tool times out, instead of just stalling. Or a manufacturing agent that has to handle the case where its maintenance-scheduling tool returns an error, instead of silently losing the request.

---

## STEP 7 — Repeat MER: Add the Autonomous Workflow Layer

### Objective
Remove the human trigger entirely. The agent now starts itself from an event, folding RAG retrieval back in as one stage — unifying both S-Tier lectures into one system.

---

### Representation 1 (Expanded) — Concept Diagram

#### Slide Content
```
   EVENT  ──▶  REASON  ──▶  CHOOSE TOOL  ──▶  EXECUTE  ──▶  OBSERVE  ──▶  FINISH
     ▲                                                            │
     └────────────────────── (no human anywhere in this loop) ────┘
```

#### What To Say
Look at the diagram from before. Everything from "reason" onward is exactly the same. We're only adding what comes before it. Instead of a human giving the agent a prompt, an event starts the whole thing on its own.

Here's the point I want you to sit with. Until now, every example in this course assumed a human asked the AI to do something. Starting now, nothing does.

---

### Representation 2 (Expanded) — Execution Flow

#### Slide Content
```
1. PUB/SUB EVENT ARRIVES           ← NEW (no human involved)
2. AGENT STARTS AUTOMATICALLY      ← NEW
3. REASON
4. RETRIEVE KNOWLEDGE (RAG)        ← ties back to S-Tier 1
5. CHOOSE TOOL
6. EXECUTE
7. UPDATE MEMORY
8. TRIGGER NOTIFICATION
9. FINISH
```

#### What To Say
Steps one and two are new. A Pub/Sub event, simply a new ticket arriving in the system, starts this entire cycle. No person typed a prompt.

Look at step four. This is where retrieval, everything we covered in the first half of today, comes back. It's not a separate skill sitting off to the side. It's one stage inside this bigger loop.

By the time you reach step nine, every single stage should look familiar. The only genuinely new ingredient is that nothing here waits for a human to begin.

---

### Representation 3 (Expanded) — Code

#### Slide Content
```python
def on_new_ticket_event(event):                       # triggered by Pub/Sub, not a human
    conclusion = reason(event.ticket_text)
    evidence = retrieve_similar_tickets(event.ticket_text, vector_index)  # from S-Tier 1
    return react_loop(conclusion, available_tools)     # from S-Tier 2
```

#### What To Say
Three lines. And notice something. Every function called here already existed. `retrieve_similar_tickets` is from our first lecture today. `react_loop` is from earlier in this one. Nothing new was written there. The only new piece is `on_new_ticket_event` itself, the part that starts everything without a human involved.

---

### Four Additional Context Changes

#### Slide Content
| Industry | Event | RAG Stage Retrieves | Tool Chosen |
|---|---|---|---|
| Healthcare | New patient message arrives | Similar past cases | `order_lab_test` or `notify_physician` |
| Finance | New transaction stream event | Similar fraud patterns | `freeze_transaction` or `flag_review` |
| Legal | New filing arrives | Similar past clauses | `flag_clause` or `notify_reviewer` |
| Cybersecurity | New log entry arrives | Similar past incidents | `lock_account` or `alert_soc` |

#### What To Say
In every one of these four rows, the same nine-step flow runs, on its own, triggered purely by something happening in the world. Here's the sentence that closes out this week. The agent has become autonomous, not because it got smarter, but because retrieval and action finally got wired together into one uninterrupted loop.

---

## STEP 8 — Expand the Definition Again

### Objective
Incorporate only the autonomous, event-driven concept from the second MER cycle.

### Slide Content
> **Tool Calling is teaching a computer to wake itself from a real-world event, retrieve the knowledge it needs, turn its conclusion into a structured action, choose correctly among several capabilities, execute, and reason again if needed — completing the entire cycle without a human anywhere inside it.**

### What To Say
One more addition. Tool calling is teaching a computer to wake itself from a real-world event, that's the new part, retrieve the knowledge it needs, turn its conclusion into a structured action, choose correctly among several capabilities, execute, and reason again if needed, completing the entire cycle without a human anywhere inside it.

Notice how little is actually new each time we expand this. That's on purpose.

---

## STEP 9 — Break the System

### Objective
Walk through realistic tool-calling and autonomous-workflow failures.

### Slide Content
| Failure | What Failed | Why | Detection | Recovery |
|---|---|---|---|---|
| Wrong Tool Chosen | Agent calls `log_audit` when it should call `trigger_notification` | Ambiguous conclusion, tool descriptions too similar | Output audit shows tool doesn't match intended outcome | Sharpen tool descriptions, add few-shot tool examples |
| Malformed Arguments | Function call has missing or wrong-typed arguments | Model guessed a field it wasn't given | Function call fails schema validation before execution | Reject the call, retry with an explicit argument reminder |
| Tool Execution Failure | Chosen tool times out or errors | Downstream system unavailable | Exception raised by the tool itself | Retry with backoff, then fall back to logging only |
| Infinite ReAct Loop | Agent keeps reasoning and choosing tools, never finishes | No stopping condition triggered | Step counter exceeds a maximum | Hard cap on steps, then force a fallback finish |
| Duplicate Action | Same notification sent twice for one event | Event delivered more than once (common in Pub/Sub) | Two identical audit log entries for one ticket ID | Idempotency check before executing any tool |
| Missing Observation | Result of a tool call never gets fed back into reasoning | Loop moved on without recording what happened | Next reasoning step contradicts the tool's actual result | Force every tool result into the next reasoning step explicitly |
| Wrong Recipient | Notification sent to the wrong person or team | Tool argument built from stale or incorrect routing data | Recipient doesn't match the cluster's actual owner | Validate recipient against current routing table before sending |

### What To Say
Let's break this system on purpose too.

First, the wrong tool gets chosen. When two tools sound similar, "log this" versus "notify about this," the model can pick the less useful one, even with a completely correct conclusion. We catch this by checking whether the tool actually chosen matches what was intended, and we fix it by sharpening each tool's description and giving worked examples of when to use it.

Second, malformed arguments. A model can choose the right tool and still fill in the arguments wrong. Missing a field, using the wrong type, inventing a value it was never given. We catch this the same way we caught bad JSON last week, with schema validation before anything runs. We fix it by rejecting the call and retrying with a clearer reminder of what's required.

Third, the tool itself fails. Maybe it times out. Maybe the system behind it is down. We catch this the moment the tool raises an error, and we fix it the same way we handled failures last week. Retry with a limit, then fall back to a safe, logged state.

Fourth, an infinite ReAct loop. Without a stopping point, an agent can reason, act, and reason again forever, never quite satisfied. We catch this by counting steps, and we fix it with a hard maximum, after which the agent stops and hands off.

Fifth, duplicate actions. Systems like Pub/Sub can occasionally deliver the same event twice. An agent with no memory of "have I already handled this" will happily send the same notification twice. We catch this by noticing two identical log entries for the same ticket, and we fix it with a check before executing any tool, not just before reasoning.

Sixth, a missing observation. The whole value of ReAct depends on actually using what a tool returns. If the loop moves on without really using the result, then reasoning again is just theater. We catch this by checking whether the next reasoning step is consistent with what the tool actually reported. We fix it by making that result a required input to the next step.

Seventh, the wrong recipient. A tool can run successfully and still cause real harm if it's aimed at the wrong person, especially when routing information has gone stale. We catch this by validating the recipient against the current routing table at the moment we send, not trusting a value that might be days old.

Here's a question worth asking yourself. Which of these seven worries you most, for a system that sends real notifications, completely unattended, based on an event that nobody personally reviewed first?

---

## STEP 10 — Final Professional Definition

### Objective
Deliver the final, complete definition, tracing every clause to its origin, and compress the entire S-Tier 2 lecture into one mental model.

### Slide Content
> **Tool Calling is the capability that lets an autonomous agent wake itself from a real-world event, retrieve the knowledge it needs, convert its reasoning into a structured action, choose correctly among several available capabilities, execute that action directly, observe and reason again if needed, and complete the entire cycle safely — without a human anywhere inside the loop.**

**The one mental model to leave with:**
```
   EVENT → REASON → RETRIEVE (RAG) → CHOOSE TOOL → EXECUTE → OBSERVE → (loop or finish)
```

### What To Say
Let's bring the whole thing together. Tool calling is the capability that lets an autonomous agent wake itself from a real-world event, that's our Pub/Sub layer. Retrieve the knowledge it needs, that's a direct callback to our first lecture today. Convert its reasoning into a structured action, that's function calling. Choose correctly among several available capabilities, that's tool use and integration. Observe and reason again if needed, that's ReAct.

Here's how I want you to leave this week. Retrieval and action are not two separate skills we bolted together. They're two halves of one loop. An agent that can only retrieve is a well-read advisor with no hands. An agent that can only act, without retrieving first, is dangerous. It acts confidently on incomplete knowledge.

The real lesson of this week is this. Our agent only becomes trustworthy once both halves are wired into one uninterrupted cycle. Retrieve before you decide. Decide before you act.
