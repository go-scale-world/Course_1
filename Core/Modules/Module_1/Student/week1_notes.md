# Week 01 — Agentic AI Foundations, LLM Basics & Data Pipelines

---

## 1. Building Autonomous AI Agents
**Definition:** An autonomous AI agent is a system built on an LLM that can perceive a task, decide what to do, take actions (often using tools), and keep going with minimal human input — as opposed to just answering one prompt and stopping.

**Types (agentic system spectrum):**
- **Simple automation** — fixed if-this-then-that rules, no reasoning.
- **Generative AI** — produces content/answers on request but doesn't act or persist.
- **Agentic AI** — reasons, plans, calls tools, remembers state, and pursues a goal across multiple steps.

**Examples:**
- A chatbot that answers a question = generative AI. An agent that reads a support ticket, classifies it, updates a database, *and* escalates it if needed = agentic AI.
- A script that resizes every image in a folder = automation, not an agent, because it never "decides" anything.
- A travel agent that books flights, checks your calendar, and rebooks automatically if a flight is cancelled = agentic.

---

## 2. Enterprise AI Overview
**Definition:** The practice of mapping real business problems (support tickets, invoices, onboarding, etc.) to the right AI solution — automation, generative AI, or a full agentic system — based on complexity and need for judgment.

**Types of enterprise problems (by fit):**
- **Rule-based fit** — repetitive, no ambiguity → simple automation.
- **Content-generation fit** — needs drafting/summarizing → generative AI.
- **Judgment + multi-step fit** — needs classification, memory, escalation → agentic AI.

**Examples:**
- Auto-replying "Your order has shipped" = automation.
- Drafting a marketing email from bullet points = generative AI.
- Triaging thousands of support tickets by urgency and routing them to the right team = agentic AI candidate.

---

## 3. Prompt Engineering
**Definition:** The practice of designing the wording, structure, and context of a prompt so an LLM reliably produces the output you want.

**Types (common techniques):**
- **Zero-shot** — just ask directly.
- **Few-shot** — give examples of input/output pairs.
- **Chain-of-thought** — ask the model to reason step by step before answering.
- **Role/system prompting** — set persona and constraints upfront.

**Examples:**
- "Extract product area, urgency, region, and confidence from this ticket and return JSON" is a structured zero-shot prompt.
- Giving Gemini 3 sample tickets with correct JSON outputs before the real one = few-shot prompting.
- Asking "think through why this ticket is urgent, then give your answer" = chain-of-thought.

---

## 4. Structured Outputs
**Definition:** Forcing an LLM to return data in a fixed, machine-readable format (usually JSON) that matches a defined schema, instead of free-form text.

**Types:**
- **Schema-constrained JSON** — model output validated against a strict schema (required fields, types).
- **Function/tool-call style output** — model returns arguments matching a function signature.

**Examples:**
- A ticket about a billing error always returns `{"product_area": "billing", "urgency": "high", "region": "US", "confidence": 0.92}` no matter how the customer phrased it.
- If a field is missing, the system flags an error instead of silently accepting broken JSON.
- A vague, angry ticket and a calm, detailed one both produce the same well-formed schema.

---

## 5. Agent Memory and State
**Definition:** The mechanism by which an agent keeps track of what's happened so far — past tickets, results, decisions — so it doesn't treat every input as if it's starting from zero.

**Types:**
- **Short-term/session memory** — remembers within one interaction.
- **Long-term/persistent memory** — stored in a database, survives across sessions (e.g., raw data, results, audit logs).

**Examples:**
- Every processed ticket is written to a `raw`, `results`, and `audit_log` table, so nothing is lost if the system crashes.
- If extraction fails halfway, you can trace exactly which ticket and which stage failed.
- A returning customer's past complaints are recalled instead of the agent asking the same questions again.
