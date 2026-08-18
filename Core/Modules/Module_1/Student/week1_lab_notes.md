# Week 1 — Lab Notes
### Enterprise Problem Consolidation (EPC) Platform

**What we built this week:** The foundation of the whole platform — our cloud environment, our code environment, and our first working agent that reads a messy support ticket and turns it into clean, structured data (product area, issue type, urgency, region, summary).

---

## Part 0 — One-Time Setup

**Step 0.1 — Create a Google Account**
We created a personal Google account, since GCP is accessed through a normal Google login. This account will be used for every GCP action for the rest of the course.

**Step 0.2 — Create a Google Cloud Platform Account**
We activated Google Cloud access using that Google account and accepted GCP's terms of service. This unlocked the Google Cloud Console, our main control panel going forward.

**Step 0.3 — Create a New GCP Project**
We created a dedicated GCP project called `epc-platform`. Every resource we build for this course — databases, AI models, storage — will live inside this one project.

**Step 0.4 — Install Python**
We installed Python 3.10+ on our computer, the programming language used to write every agent in this course. We confirmed it works by checking the version in a terminal.

**Step 0.5 — Install Git**
We installed Git, the tool that tracks changes to our code over time and connects our computer to GitHub. This lets us save and back up every version of our work.

**Step 0.6 — Install VS Code**
We installed Visual Studio Code, the code editor we'll use to write and organize every file in this project. It understands Python and flags errors as we type.

**Step 0.7 — Create a GitHub Account**
We created a free GitHub account, which is where our code repository will live online. This is the industry-standard place to store and back up code.

**Step 0.8 — Create the EPC Platform Repository**
We created a private repository named `epc-platform` on GitHub with a starter README file. This is the online home for all our code going forward.

**Step 0.9 — Clone the Repository**
We downloaded (cloned) a working copy of that GitHub repository onto our computer. This gives us a local folder we can edit, which stays connected to GitHub.

**Step 0.10 — Create the Base Folder Structure**
We created eight top-level folders (`agents`, `memory`, `rag`, `guardrails`, `integrations`, `monitoring`, `deployment`, `data`) to keep the project organized as it grows across all six weeks.

**Step 0.11 — Create a Python Virtual Environment**
We created and activated a virtual environment (`venv`) so this project's Python libraries stay isolated from anything else on our computer. This avoids version conflicts down the line.

**Step 0.12 — Install gcloud CLI and Authenticate**
We installed Google's command-line tool and logged in through it, linking our terminal directly to our GCP project for future command-line actions.

**Step 0.13 — Create a Service Account**
We created a dedicated "robot" identity called a service account, which our code will use to talk to Google Cloud — separate from our personal login, and with no permissions yet.

**Step 0.14 — Generate and Download a Service Account Key**
We generated a private key file for that service account, which acts like a password letting our code authenticate as that identity. We noted this file must never be shared publicly.

**Step 0.15 — Move the Key File Safely & Create .gitignore**
We placed the key file inside our project and created a `.gitignore` file listing it (and other sensitive files) so Git never uploads them to GitHub by accident.

**Step 0.16 — Create the .env File**
We created a `.env` file storing our project ID and the path to our credentials, so our code can find them without hardcoding sensitive details directly into scripts.

---

## Part 1 — Week 1 Build

**Step 1.1 — Enable the Vertex AI API**
We turned on Vertex AI for our project — this is Google's AI service that powers Gemini, the model that will read and interpret every support ticket.

**Step 1.2 — Grant the "Vertex AI User" Role**
We gave our service account permission to actually use Vertex AI, choosing the smallest role that does the job rather than a broader admin role.

**Step 1.3 — Install Required Python Libraries**
We installed the specific Python packages needed to talk to Vertex AI and validate data (`google-cloud-aiplatform`, `pydantic`, `python-dotenv`).

**Step 1.4 — Create requirements.txt**
We saved a list of every installed library and its version into `requirements.txt`, so this setup can be easily reproduced later.

**Step 1.5 — Create the Sample Ticket Data File**
We created a file with 10 realistic, fake support tickets to test our code against, each with a raw ticket text, source, and timestamp.

**Step 1.6 — Create the Extraction Schema File**
We defined, in code, the exact five fields every extraction must contain (product area, issue type, urgency, region, summary) using Pydantic, so results are always consistent.

**Step 1.7 — Create the Prompt Template File**
We wrote the exact instructions sent to Gemini, telling it precisely how to read a ticket and which fields to extract, and to respond only in JSON.

**Step 1.8 — Create the Meaning Extraction Agent Script**
We wrote the core script that connects to Gemini, sends each ticket through our prompt, and validates the structured result against our schema before accepting it.

**Step 1.9 — Run a Single-Ticket Test**
We ran the agent on one sample ticket and confirmed it returned a complete, correctly structured JSON output with all five required fields.

**Step 1.10 — Create the Batch Test Script**
We built a script to run all 10 sample tickets through the agent at once and save every result to an output file, confirming the agent works consistently at scale.

**Step 1.11 — Test Schema Validation**
We deliberately fed the system bad data (an invalid urgency value) to prove our validation logic actually rejects incorrect output instead of silently accepting it.

**Step 1.12 — Save Work to GitHub**
We committed and pushed all our Week 1 code to GitHub, and confirmed our secret key file was correctly excluded from the upload.

**Step 1.13 — Prompt Engineering Experiments**
We ran a set of experiments (zero-shot, few-shot, structured prompting, chain-of-thought, and temperature settings) to see how different prompting techniques affect Gemini's consistency and accuracy on the same tickets.

---

## ✅ Week 1 Outcome
By the end of the week, we had a fully working Meaning Extraction Agent: it takes any raw, messy support ticket and reliably turns it into clean, validated, structured JSON — the foundation every later week builds on.
