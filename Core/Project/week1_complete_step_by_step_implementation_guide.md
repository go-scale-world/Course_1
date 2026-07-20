# Week 1 — Complete Step-by-Step Implementation Guide

### Enterprise Problem Consolidation (EPC) Platform

---

## How to Use This Guide

This guide assumes you have **never** used Google Cloud Platform (GCP), GitHub, or Python before. Every single click is written out. Do not skip steps, even if they look obvious. Work through this guide from top to bottom, in order.

This guide has two parts:

- **Part 0 — One-Time Setup**: Things you only ever do once for this entire course (accounts, tools, folders). You will not repeat these in Week 2 onward.
- **Part 1 onward — Week 1 Work**: The actual Week 1 build (turning on the AI service, writing the code, testing it).

At the very end is a **Week 1 Completion Checklist**. Do not move to Week 2 until every box on that checklist is true for you.

---

# PART 0 — ONE-TIME SETUP

## Step 0.1 — Create a Google Account

**Goal:** You need a Google account (Gmail) to use Google Cloud Platform.

**Why:** Google Cloud Platform is accessed using a normal Google login — the same kind you'd use for Gmail or YouTube.

**Exact steps:**
1. If you already have a Gmail address (something ending in `@gmail.com`), skip to Step 0.2.
2. Open your web browser (Google Chrome is recommended for this course).
3. Go to `https://accounts.google.com/signup`
4. Fill in your first name and last name.
5. Choose a username (this becomes your `@gmail.com` address).
6. Choose a password and confirm it.
7. Click the blue button labeled **Next**.
8. Follow the remaining on-screen prompts (phone number, birthday, etc.) and click **Next** after each one.

**Expected output:** You land on a Google account welcome/dashboard page.

**How to verify success:** Go to `https://mail.google.com` — you should see an empty or near-empty inbox with your new email address in the top-right circle icon.

**Common beginner mistake:** Using a work or school Google account that your organization controls. Use a **personal** Google account for this course so you have full administrative control over billing and permissions.

---

## Step 0.2 — Create a Google Cloud Platform (GCP) Account

**Goal:** Turn your Google account into a Google Cloud account so you can create cloud projects.

**Why:** GCP is Google's suite of cloud services (the EPC Platform will run on these). A Google account alone does not automatically have GCP access — you need to activate it once.

**Exact steps:**
1. Open Google Chrome.
2. Go to `https://console.cloud.google.com`
3. If asked to sign in, click **Sign in** and use the Google account from Step 0.1.
4. You will see a welcome screen with Google Cloud's Terms of Service.
5. Check the box labeled **"I agree to the Google Cloud Platform Terms of Service"**.
6. Click the blue button labeled **Agree and Continue**.

**Expected output:** You are taken to the **Google Cloud Console** — a dashboard with a blue top navigation bar, a search bar in the middle-top, and a left-hand hamburger menu (☰ icon, three horizontal lines) in the top-left corner.

**How to verify success:** The browser tab title should say "Google Cloud Platform" and you should see "Welcome" or a dashboard with cards like "Cloud overview".

**Common beginner mistake:** Some people get stuck at a billing/credit-card page and think something is broken. This is normal — Google requires a payment method on file even for free-tier usage, but will not charge you unless you explicitly upgrade. We will not need to spend money for Week 1.

**Troubleshooting:** If you see a page asking for a credit card and you're not comfortable proceeding yet, you can still browse the console, but you will not be able to enable most APIs until billing is set up. If prompted, click **"Start my free trial"** or **"Activate"** and follow Google's card verification steps — this does not charge you automatically.

---

## Step 0.3 — Create a New GCP Project

**Goal:** Create an isolated "container" called a **project** where all EPC Platform resources will live.

**Why:** In GCP, everything (databases, AI models, storage) must belong to a project. Think of a project like a labeled folder that keeps this course's work separate from anything else you might do on GCP later.

**Exact steps:**
1. At the very top of the Google Cloud Console page, next to the "Google Cloud" logo, you'll see a small dropdown that says either **"Select a project"** or shows an existing project name. Click it.
2. A popup window titled **"Select a project"** appears.
3. In the top-right of that popup, click the button labeled **New Project**.
4. A new page opens titled **"New Project"**.
5. In the field labeled **Project name**, type: `epc-platform`
6. Below that, you'll see a field labeled **Project ID** — Google auto-generates this (something like `epc-platform-123456`). Leave it as-is; write it down somewhere (Notepad or a sticky note) because you will need it later.
7. Leave the **Location** field as "No organization" unless you know you have one.
8. Click the blue button labeled **Create**.
9. Wait 10–30 seconds. A bell/notification icon (🔔) in the top-right will show a spinning icon, then a checkmark once done.

**Expected output:** A notification appears saying "Creating project epc-platform..." followed by "Created project epc-platform".

**How to verify success:** Click the project dropdown at the top again. You should see `epc-platform` listed. Click it to select it — the top bar should now display `epc-platform`.

**Common beginner mistake:** Forgetting to actually select the new project after creating it. GCP does not automatically switch you into a newly created project — you must click it in the dropdown.

**Troubleshooting:** If the **Create** button is greyed out, it usually means billing has not been linked to your account yet. Go to the hamburger menu (☰) → **Billing** and follow the setup steps, then return here and try again.

---

## Step 0.4 — Install Python

**Goal:** Install Python 3.10 or newer on your computer.

**Why:** Python is the programming language we will use to write the EPC Platform's code.

**Exact steps (Windows):**
1. Open your browser and go to `https://www.python.org/downloads/`
2. Click the yellow button that says **Download Python 3.x.x** (the version number will be 3.10 or higher — any of these is fine).
3. Once downloaded, open the file (usually appears at the bottom of your browser, or in your **Downloads** folder).
4. On the first installer screen, **check the box at the bottom that says "Add python.exe to PATH"**. This step is critical — do not skip it.
5. Click **Install Now**.
6. Wait for the installation to finish, then click **Close**.

**Exact steps (Mac):**
1. Open your browser and go to `https://www.python.org/downloads/`
2. Click the button that says **Download Python 3.x.x for macOS**.
3. Open the downloaded `.pkg` file.
4. Click **Continue** through each screen, then **Install**.
5. Enter your Mac password if prompted, then click **Install Software**.
6. Click **Close** when finished.

**How to verify success:**
1. Open a terminal:
   - **Windows**: Press the Windows key, type `Command Prompt`, press Enter.
   - **Mac**: Press `Cmd + Space`, type `Terminal`, press Enter.
2. Type the following and press Enter:
```bash
python --version
```
3. You should see output like:
```
Python 3.11.5
```

**Common beginner mistake:** Forgetting to check "Add python.exe to PATH" on Windows. If you forget this, typing `python --version` will show an error like `'python' is not recognized`.

**Troubleshooting:** If `python --version` doesn't work on Mac, try:
```bash
python3 --version
```
If that works, use `python3` instead of `python` for every command in this guide.

---

## Step 0.5 — Install Git

**Goal:** Install Git, the tool that tracks changes to your code and connects to GitHub.

**Why:** Git lets us save versions of our code and upload (push) it to GitHub, where it's backed up and can be reviewed.

**Exact steps (Windows):**
1. Go to `https://git-scm.com/download/win`
2. The download should start automatically. If not, click **Click here to download**.
3. Open the downloaded file.
4. Click **Next** through every screen, leaving all default options as-is (do not change any checkboxes).
5. Click **Install**, then **Finish**.

**Exact steps (Mac):**
1. Open Terminal (`Cmd + Space`, type `Terminal`, press Enter).
2. Type the following and press Enter:
```bash
git --version
```
3. If Git is not installed, macOS will automatically prompt you to install "Command Line Developer Tools." Click **Install** on that popup and wait for it to finish.

**How to verify success:** In your terminal, type:
```bash
git --version
```
You should see something like:
```
git version 2.42.0
```

**Common beginner mistake:** Closing the terminal before the Mac developer tools finish installing. Wait for the popup to say "The software was installed" before continuing.

---

## Step 0.6 — Install a Code Editor (VS Code)

**Goal:** Install Visual Studio Code (VS Code), the application where you'll write and edit all your code files.

**Why:** You need a program designed for writing code — not Notepad or Word — because it understands Python and highlights errors for you.

**Exact steps:**
1. Go to `https://code.visualstudio.com/`
2. Click the big blue button labeled **Download for Windows** (or **Mac**, matching your operating system — the site detects this automatically).
3. Open the downloaded installer.
4. **Windows:** Check the box **"Add to PATH"** if shown, then click **Next** through all screens, then **Install**, then **Finish**.
5. **Mac:** Drag the VS Code icon into your **Applications** folder when prompted.

**How to verify success:** Open VS Code (search for it in your Start Menu on Windows, or Spotlight `Cmd + Space` on Mac). You should see a welcome screen with "Visual Studio Code" and a dark or light editor window.

**Common beginner mistake:** Confusing VS Code with "Visual Studio" (a completely different, much larger program). Make sure the download page says **"Visual Studio Code"**.

---

## Step 0.7 — Create a GitHub Account

**Goal:** Create a free GitHub account to store and back up your EPC Platform code.

**Why:** GitHub is where your code repository (repo) will live online. It's the industry-standard way to store and share code.

**Exact steps:**
1. Go to `https://github.com/signup`
2. Enter your email address, click **Continue**.
3. Create a password, click **Continue**.
4. Choose a username, click **Continue**.
5. Complete the "Verify your account" puzzle if shown.
6. Click **Create account**.
7. GitHub will email you a verification code — check your inbox, copy the code, paste it into the box on the GitHub page, and click **Continue**.
8. On the next screens, you can click **Skip personalization** if shown, to save time.

**How to verify success:** You land on `https://github.com` and see your username in the top-right circle icon.

---

## Step 0.8 — Create the EPC Platform Repository on GitHub

**Goal:** Create the empty online repository (repo) where all your code will be stored.

**Why:** A repository is like a project folder that lives on GitHub and tracks every change you make.

**Exact steps:**
1. While logged into GitHub, click the **+** icon in the top-right corner of the page.
2. From the dropdown, click **New repository**.
3. In the field labeled **Repository name**, type: `epc-platform`
4. Leave **Description** empty (optional).
5. Select **Private** (the radio button) — this keeps your code visible only to you.
6. Scroll down to **"Initialize this repository with"** and check the box labeled **Add a README file**.
7. Click the green button labeled **Create repository**.

**Expected output:** You are taken to a new page at `https://github.com/YOUR_USERNAME/epc-platform` showing one file: `README.md`.

**How to verify success:** The repository page shows the name `epc-platform` at the top, with one visible file called `README.md`.

**Common beginner mistake:** Selecting **Public** instead of **Private**. For this course, Private is recommended so your work isn't visible to strangers.

---

## Step 0.9 — Clone the Repository to Your Computer

**Goal:** Download (clone) a working copy of your GitHub repository onto your computer, so you can edit files locally.

**Why:** You cannot write code directly on GitHub's website in a practical way — you write it on your computer, then upload (push) it to GitHub.

**Exact steps:**
1. On your repository page (`https://github.com/YOUR_USERNAME/epc-platform`), click the green button labeled **Code**.
2. A dropdown appears with a tab labeled **HTTPS** already selected. Click the small clipboard/copy icon next to the URL (it looks like `https://github.com/YOUR_USERNAME/epc-platform.git`).
3. Open your terminal (Command Prompt on Windows, Terminal on Mac).
4. Decide where you want this project to live on your computer. For simplicity, we'll use your Desktop. Type:
```bash
cd Desktop
```
Press Enter.
5. Type the following, then paste the URL you copied by right-clicking (Windows) or pressing `Cmd + V` (Mac) right after `git clone `:
```bash
git clone https://github.com/YOUR_USERNAME/epc-platform.git
```
Press Enter.
6. You should see output like:
```
Cloning into 'epc-platform'...
remote: Enumerating objects...
Receiving objects: 100% ...
```

**How to verify success:** Type:
```bash
cd epc-platform
dir
```
(On Mac, use `ls` instead of `dir`.)
You should see `README.md` listed.

**Common beginner mistake:** Typing `git clone` and forgetting to paste the actual URL — this produces an error like `fatal: You must specify a repository to clone.`

---

## Step 0.10 — Create the Base Folder Structure

**Goal:** Create the empty folders that will organize the EPC Platform's code, matching the product's functional modules.

**Why:** A clean folder structure keeps agents, memory logic, and integrations separated and easy to find as the project grows over 6 weeks.

**Exact steps:**
1. Open VS Code.
2. Click **File** (top-left menu) → **Open Folder**.
3. Navigate to `Desktop` → `epc-platform` and click **Select Folder** (Windows) or **Open** (Mac).
4. You should now see `epc-platform` in the left-hand sidebar, with `README.md` underneath it.
5. Right-click on `epc-platform` in the sidebar and click **New Folder**.
6. Type `agents` and press Enter.
7. Repeat step 5–6 to create these additional folders, one at a time, at the same top level as `agents`:
   - `memory`
   - `rag`
   - `guardrails`
   - `integrations`
   - `monitoring`
   - `deployment`
   - `data`

**Expected output:** Your sidebar now shows 8 folders: `agents`, `memory`, `rag`, `guardrails`, `integrations`, `monitoring`, `deployment`, `data`, plus the `README.md` file.

**How to verify success:** Every folder name should be spelled exactly as above, all lowercase, no spaces.

**Common beginner mistake:** Creating folders as subfolders of each other by accident (e.g., `agents` inside `memory`). If this happens, right-click the misplaced folder → **Cut**, then right-click `epc-platform` at the top → **Paste**.

---

## Step 0.11 — Create a Python Virtual Environment

**Goal:** Create an isolated space on your computer where this project's Python libraries are installed, separate from any other Python projects.

**Why:** Without this, installing libraries for one project can conflict with another. A virtual environment ("venv") keeps this project self-contained.

**Exact steps:**
1. In VS Code, open the built-in terminal: click **Terminal** (top menu) → **New Terminal**.
2. A terminal panel opens at the bottom of VS Code. Confirm it shows a path ending in `epc-platform`.
3. Type the following and press Enter:
```bash
python -m venv venv
```
(If `python` doesn't work, try `python3` instead, as noted in Step 0.4.)
4. Wait a few seconds. This creates a new folder called `venv` inside your project.
5. Activate the virtual environment:
   - **Windows:**
   ```bash
   venv\Scripts\activate
   ```
   - **Mac:**
   ```bash
   source venv/bin/activate
   ```
6. Press Enter.

**Expected output:** Your terminal prompt line now starts with `(venv)`, for example:
```
(venv) C:\Users\You\Desktop\epc-platform>
```

**How to verify success:** You see `(venv)` at the start of the terminal line. This means the virtual environment is active.

**Common beginner mistake:** Forgetting to activate the venv every time you open a new terminal window. If you don't see `(venv)` at the start of the line, repeat step 5 before running any Python commands.

**Troubleshooting (Windows only):** If you see an error like "running scripts is disabled on this system," type this once and press Enter, then try activating again:
```bash
Set-ExecutionPolicy -Scope CurrentUser -ExecutionPolicy RemoteSigned
```

---

## Step 0.12 — Install the Google Cloud CLI (gcloud) and Authenticate

**Goal:** Install Google's command-line tool so your computer can securely talk to your GCP project, and log it into your Google account.

**Why:** Later steps (creating a service account key, enabling APIs from the terminal) rely on this tool.

**Exact steps:**
1. Go to `https://docs.cloud.google.com/sdk/docs/install-sdk`
2. Click the download link matching your operating system (Windows or macOS).
3. Run the downloaded installer.
   - **Windows:** Double-click the `.exe` file, click **Next** through the screens (leave defaults checked), click **Install**, then **Finish**. A checkbox for "Start Google Cloud SDK Shell" may appear — leave it checked and click **Finish**; a new terminal window opens automatically.
   - **Mac:** Open the downloaded `.tar.gz` file (it extracts automatically), then open Terminal, navigate to the extracted folder, and run `./google-cloud-sdk/install.sh`, pressing Enter/Y to accept defaults.
4. In the terminal that opens (or a fresh one), type:
```bash
gcloud init
```
Press Enter.
5. It will ask: "Would you like to log in?" Type `Y` and press Enter.
6. A browser window opens automatically. Choose the Google account you used for GCP (Step 0.1).
7. Click **Allow** on the permissions screen.
8. Return to your terminal — it should say "You are now logged in."
9. It will then ask you to pick a cloud project. You should see `epc-platform` listed with a number next to it. Type that number and press Enter.

**How to verify success:** Type:
```bash
gcloud config list
```
You should see:
```
[core]
account = your-email@gmail.com
project = epc-platform-123456
```

**Common beginner mistake:** Selecting the wrong Google account when the browser window opens, if you're logged into multiple Google accounts. Double-check the email shown matches Step 0.1.

**Troubleshooting:** If `gcloud` is "not recognized" after installation, close and reopen your terminal completely (this reloads your system's PATH), then try again.

---

## Step 0.13 — Create a Service Account (No Roles Yet)

**Goal:** Create a special, non-human Google account called a "service account" that your code will use to talk to GCP — without needing your personal login.

**Why:** It's best practice to never use your personal Google credentials inside code. A service account is a secure, revocable identity created specifically for this application.

**Exact steps:**
1. In Google Chrome, go to `https://console.cloud.google.com`
2. Confirm the project selector at the top says `epc-platform`. If not, click it and select `epc-platform`.
3. Click the search bar at the very top of the page (it says "Search" with a magnifying glass icon 🔍).
4. Type: `Service Accounts`
5. Click the result named **Service Accounts** (under the "IAM & Admin" category).
6. Click the blue button labeled **+ Create Service Account** near the top.
7. In the field **Service account name**, type: `epc-platform-agent`
8. The **Service account ID** field auto-fills below it — leave it as-is.
9. Click **Create and Continue**.
10. On the next screen, titled **"Grant this service account access to project"**, click **Continue** without selecting any role. (We will add specific roles only when a specific week needs them — this keeps permissions minimal and secure.)
11. On the next screen, click **Done**.

**Expected output:** You're returned to the Service Accounts list, showing `epc-platform-agent@epc-platform-123456.iam.gserviceaccount.com` with no roles listed yet.

**How to verify success:** The service account appears in the list with the name you gave it.

**Common beginner mistake:** Accidentally granting a broad role like "Owner" at this stage. We deliberately skip roles here — we'll add only the minimum required role in Part 1 below.

---

## Step 0.14 — Generate and Download a Service Account Key

**Goal:** Download a small file (a "key") that acts like a password, allowing your code to authenticate as the service account.

**Why:** This key file is what your Python code will use to prove its identity to Google Cloud.

**Exact steps:**
1. Still on the **Service Accounts** page, click on the email address `epc-platform-agent@...` (this opens its details page).
2. Click the tab labeled **Keys** near the top.
3. Click **Add Key** → **Create new key**.
4. A popup appears asking for key type. Confirm **JSON** is selected (it's the default).
5. Click **Create**.
6. Your browser automatically downloads a file, typically named something like `epc-platform-123456-a1b2c3d4e5f6.json`. Note where it downloads (usually your **Downloads** folder).
7. Click **Close** on the popup.

**Expected output:** A `.json` file is now saved on your computer.

**How to verify success:** Open your Downloads folder and confirm the `.json` file exists.

**Extremely important security note:** This file is like a password. **Never** upload it to GitHub or share it publicly. We will explicitly exclude it from GitHub in the next step.

---

## Step 0.15 — Move the Key File Into Your Project (Safely)

**Goal:** Place the downloaded key file inside your project folder, in a way that Git will never accidentally upload it.

**Exact steps:**
1. In your file explorer (Windows) or Finder (Mac), locate the downloaded `.json` key file.
2. Rename it to exactly: `service-account-key.json`
   - **Windows:** Right-click the file → **Rename** → type the new name → press Enter.
   - **Mac:** Click once on the file name → type the new name → press Enter.
3. Move (cut and paste, or drag) this renamed file into your `epc-platform` folder, at the top level (the same level as the `agents` folder, not inside it).
4. In VS Code, click **File** → **New File**.
5. Name it exactly: `.gitignore` (note the dot at the start, and no file extension after).
6. Paste the following into this new empty file:
```
service-account-key.json
.env
venv/
__pycache__/
*.pyc
```
7. Save the file: press `Ctrl + S` (Windows) or `Cmd + S` (Mac).

**Why this matters:** The `.gitignore` file tells Git "never upload these files." This protects your secret key and your local environment settings from ever being pushed to GitHub, even by accident.

**How to verify success:** In VS Code's left sidebar, you should now see `service-account-key.json` and `.gitignore` listed, but `service-account-key.json` should appear slightly greyed out/dimmed once `.gitignore` is saved — this confirms Git is ignoring it.

**Common beginner mistake:** Naming the file `gitignore.txt` instead of `.gitignore`. It must start with a dot and have no other extension.

---

## Step 0.16 — Create the .env File for Local Credentials

**Goal:** Create a file that tells your Python code where to find the service account key, without hardcoding the path inside your actual code.

**Exact steps:**
1. In VS Code, click **File** → **New File**.
2. Name it exactly: `.env`
3. Paste the following line into it:
```
GOOGLE_APPLICATION_CREDENTIALS=service-account-key.json
GCP_PROJECT_ID=epc-platform-123456
```
4. Replace `epc-platform-123456` with your actual Project ID from Step 0.3 (check your sticky note or Notepad).
5. Save the file (`Ctrl + S` / `Cmd + S`).

**How to verify success:** The `.env` file appears in your sidebar (also slightly dimmed, since `.gitignore` protects it too).

---

**🎉 Part 0 is complete.** You now have: a GCP project, Python, Git, VS Code, a GitHub repository, a base folder structure, a virtual environment, and a securely stored service account key. This setup will never need to be repeated for the rest of the course.

---

# PART 1 — WEEK 1 IMPLEMENTATION

## Recap: What We're Building This Week

**Product Goal (from the roadmap):** Teach the platform to read a messy, free-form support ticket and turn it into clean, structured data — the foundation every later week depends on.

**What you will have by the end of this week:** A working script that takes a raw ticket like *"App keeps crashing when I try to upload photos on Android"* and outputs structured data like:
```json
{
  "product_area": "Mobile App",
  "issue_type": "Crash",
  "urgency": "High",
  "region": "Unspecified",
  "summary": "User reports app crash during photo upload on Android."
}
```

---

## Step 1.1 — Enable the Vertex AI API

**Goal:** Turn on Google's Vertex AI service for your project — this is the AI engine (Gemini) that will read and interpret tickets.

**Why:** GCP services are off by default in a new project. You must explicitly enable each one you plan to use — and we only enable what Week 1 actually needs.

**Exact steps:**
1. Go to `https://console.cloud.google.com`
2. Confirm the top project selector shows `epc-platform`.
3. Click the search bar at the top, type: `Vertex AI`
4. Click the result named **Vertex AI**.
5. You land on the Vertex AI dashboard. If the API is not yet enabled, you'll see a blue button labeled **Enable All Recommended APIs** or simply **Enable**.
6. Click that blue **Enable** button.
7. Wait 15–60 seconds. You'll see a progress spinner.

**Expected output:** The button changes from **Enable** to a page showing the Vertex AI dashboard with menu options like "Model Garden", "Generative AI Studio", etc.

**How to verify success:** Go to the search bar again, type `APIs & Services`, click it, then click **Enabled APIs & services** in the left sidebar. You should see **Vertex AI API** listed with status "Enabled".

**Common beginner mistake:** Clicking around before the enabling process finishes. Wait for the spinner to complete fully before moving to the next step.

**Troubleshooting:** If you see a billing-related error, go to the hamburger menu (☰) → **Billing**, confirm a payment method is linked to the `epc-platform` project, then retry.

---

## Step 1.2 — Grant the "Vertex AI User" Role to Your Service Account

**Goal:** Give your service account (created in Step 0.13) permission to actually use Vertex AI — nothing more, nothing less.

**Why:** Creating a service account doesn't automatically give it permissions. We now grant the **one specific role** Week 1 needs.

**Exact steps:**
1. Go to the search bar, type: `IAM`
2. Click the result named **IAM** (under "IAM & Admin").
3. You'll see a table of accounts with columns "Name", "Email", "Role".
4. Find the row for `epc-platform-agent@epc-platform-123456.iam.gserviceaccount.com`.
5. Click the pencil/edit icon (✏️) at the right end of that row.
6. A panel slides in from the right, titled **"Edit access"**.
7. Click **+ Add Another Role**.
8. Click the dropdown that appears, and in the search box type: `Vertex AI User`
9. Click the result labeled **Vertex AI User**.
10. Click the blue button labeled **Save** at the bottom of the panel.

**Expected output:** The IAM table now shows two roles listed under your service account's row: whatever default role existed (if any) plus **Vertex AI User**.

**How to verify success:** Refresh the IAM page and confirm **Vertex AI User** appears next to `epc-platform-agent@...`.

**Common beginner mistake:** Searching for "Vertex AI Admin" instead of "Vertex AI User" — Admin grants far more permission than needed. Always pick the smallest role that does the job.

---

## Step 1.3 — Install Required Python Libraries

**Goal:** Install the specific Python packages your code needs to talk to Vertex AI and validate data.

**Exact steps:**
1. Return to VS Code.
2. Open the terminal (**Terminal** → **New Terminal** if it's not already open).
3. Confirm you see `(venv)` at the start of the terminal line. If not, reactivate it (see Step 0.11, sub-step 5).
4. Type the following and press Enter:
```bash
pip install google-cloud-aiplatform pydantic python-dotenv
```
5. Wait for the installation to complete — you'll see multiple lines of text scroll by ending with something like `Successfully installed google-cloud-aiplatform-1.71.1 pydantic-2.9.2 python-dotenv-1.0.1`.

**How to verify success:** Type:
```bash
pip list
```
Scroll through the output and confirm you see `google-cloud-aiplatform`, `pydantic`, and `python-dotenv` listed.

**Common beginner mistake:** Running `pip install` without `(venv)` showing in the terminal — this installs the libraries globally on your computer instead of inside this project, which can cause confusing conflicts later.

---

## Step 1.4 — Create requirements.txt

**Goal:** Save a list of exactly which libraries this project depends on, so anyone (including future-you) can reinstall them easily.

**Exact steps:**
1. In VS Code's terminal, type:
```bash
pip freeze > requirements.txt
```
2. Press Enter.

**How to verify success:** In the VS Code sidebar, a new file called `requirements.txt` appears at the top level of `epc-platform`. Click it to open it — you should see a list of package names and version numbers, including `google-cloud-aiplatform`, `pydantic`, and `python-dotenv`.

---

## Step 1.5 — Create the Sample Ticket Data File

**Goal:** Create a small file containing 10 fake support tickets to test your code against.

**Exact steps:**
1. In VS Code's left sidebar, right-click the `data` folder (created in Step 0.10).
2. Click **New File**.
3. Name it exactly: `sample_tickets.json`
4. Paste the following content exactly into the empty file:

```json
[
  {
    "ticket_id": "T001",
    "raw_text": "App keeps crashing when I try to upload photos on Android",
    "source": "email",
    "timestamp": "2026-01-05T09:12:00Z"
  },
  {
    "ticket_id": "T002",
    "raw_text": "Cannot upload pictures, the app just freezes and closes",
    "source": "chat",
    "timestamp": "2026-01-05T10:45:00Z"
  },
  {
    "ticket_id": "T003",
    "raw_text": "Photo upload fails every single time on my phone",
    "source": "web_form",
    "timestamp": "2026-01-05T11:30:00Z"
  },
  {
    "ticket_id": "T004",
    "raw_text": "I was charged twice for my monthly subscription this week",
    "source": "email",
    "timestamp": "2026-01-06T08:00:00Z"
  },
  {
    "ticket_id": "T005",
    "raw_text": "Double billing on my credit card, please refund the extra charge",
    "source": "phone",
    "timestamp": "2026-01-06T08:20:00Z"
  },
  {
    "ticket_id": "T006",
    "raw_text": "Login page shows a blank white screen on Chrome browser",
    "source": "web_form",
    "timestamp": "2026-01-06T14:10:00Z"
  },
  {
    "ticket_id": "T007",
    "raw_text": "I can't log in, the page just stays white and never loads",
    "source": "chat",
    "timestamp": "2026-01-06T14:45:00Z"
  },
  {
    "ticket_id": "T008",
    "raw_text": "Password reset email never arrives in my inbox",
    "source": "email",
    "timestamp": "2026-01-07T09:00:00Z"
  },
  {
    "ticket_id": "T009",
    "raw_text": "The mobile app is extremely slow when loading the dashboard in Europe",
    "source": "chat",
    "timestamp": "2026-01-07T16:30:00Z"
  },
  {
    "ticket_id": "T010",
    "raw_text": "Dashboard takes over a minute to load on my phone in Germany",
    "source": "web_form",
    "timestamp": "2026-01-07T17:00:00Z"
  }
]
```

5. Save the file (`Ctrl + S` / `Cmd + S`).

**How to verify success:** The file should show no red squiggly underlines in VS Code (which would indicate broken JSON formatting). The file should contain exactly 10 ticket objects, each with 4 fields: `ticket_id`, `raw_text`, `source`, `timestamp`.

**Common beginner mistake:** Missing a comma between two ticket objects, or leaving a trailing comma after the last one. If VS Code shows a red underline, look closely for a missing or extra comma near that line.

---

## Step 1.6 — Create the Extraction Schema File

**Goal:** Define, in code, exactly what "structured output" means — the 5 fields every extraction must contain.

**Why:** This schema is what Step 1.9's "schema validation" checks every extraction against, and it's what forces Gemini's output into a consistent, predictable shape.

**Exact steps:**
1. Right-click the `agents` folder in the sidebar.
2. Click **New File**.
3. Name it exactly: `schema.py`
4. Paste the following code into the file:

```python
from pydantic import BaseModel, Field
from typing import Literal


class TicketExtraction(BaseModel):
    """
    Defines the exact structure every extracted ticket must follow.
    If Gemini's output doesn't match this shape, validation will fail
    and the ticket will be rejected instead of silently accepted.
    """

    product_area: str = Field(
        description="The product or system area the issue relates to, e.g. 'Mobile App', 'Billing', 'Login'."
    )
    issue_type: str = Field(
        description="The category of the issue, e.g. 'Crash', 'Billing Error', 'Performance', 'Access Issue'."
    )
    urgency: Literal["Low", "Medium", "High", "Critical"] = Field(
        description="How urgent this issue appears to be, based on the ticket's language."
    )
    region: str = Field(
        description="The geographic region mentioned in the ticket, or 'Unspecified' if none is mentioned."
    )
    summary: str = Field(
        description="A one-sentence, neutral summary of what the user is reporting."
    )
```

5. Save the file (`Ctrl + S` / `Cmd + S`).

**How to verify success:** No red underlines should appear anywhere in the file. The file should be 25–30 lines long.

**Common beginner mistake:** Mismatched indentation. Python is strict about spacing — make sure every line inside the class is indented with exactly 4 spaces, matching what's shown above.

---

## Step 1.7 — Create the Prompt Template File

**Goal:** Write the exact instructions that will be sent to Gemini, telling it how to read a ticket and what to extract.

**Exact steps:**
1. Right-click the `agents` folder.
2. Click **New File**.
3. Name it exactly: `prompts.py`
4. Paste the following code:

```python
EXTRACTION_PROMPT_TEMPLATE = """
You are a support ticket analyst for an enterprise software company.

Read the raw support ticket text below and extract the following information:

1. product_area — Which product or system area does this relate to? (e.g. "Mobile App", "Billing", "Login", "Dashboard")
2. issue_type — What kind of issue is this? (e.g. "Crash", "Billing Error", "Performance", "Access Issue")
3. urgency — How urgent does this sound? Choose exactly one of: "Low", "Medium", "High", "Critical"
4. region — What geographic region is mentioned, if any? If no region is mentioned, respond with exactly "Unspecified"
5. summary — Write one neutral, factual sentence summarizing what the user is reporting.

Respond with ONLY a valid JSON object matching this exact structure, and nothing else:

{{
  "product_area": "...",
  "issue_type": "...",
  "urgency": "...",
  "region": "...",
  "summary": "..."
}}

Here is the raw ticket text:
\"\"\"
{ticket_text}
\"\"\"
"""
```

5. Save the file.

**How to verify success:** The file should be about 25 lines long, with no red underlines.

**Common beginner mistake:** Using single curly braces `{ }` instead of double `{{ }}` around the example JSON. In Python, this specific template style requires doubling any curly braces that are NOT meant to be filled in by code — this prevents Python from getting confused about what to insert. Only `{ticket_text}` should use single braces.

---

## Step 1.8 — Create the Meaning Extraction Agent Script

**Goal:** Write the main script that connects to Gemini, sends each ticket through the prompt, and validates the structured result.

**Exact steps:**
1. Right-click the `agents` folder.
2. Click **New File**.
3. Name it exactly: `meaning_extraction_agent.py`
4. Paste the following code:

```python
import os
import json
from dotenv import load_dotenv
import vertexai
from vertexai.generative_models import GenerativeModel
from pydantic import ValidationError

from schema import TicketExtraction
from prompts import EXTRACTION_PROMPT_TEMPLATE

# Load environment variables from the .env file
load_dotenv()

PROJECT_ID = os.getenv("GCP_PROJECT_ID")
LOCATION = "us-central1"
MODEL_NAME = "gemini-2.0-flash-001"

# Initialize connection to Vertex AI using the service account credentials
vertexai.init(project=PROJECT_ID, location=LOCATION)
model = GenerativeModel(MODEL_NAME)


def extract_ticket_meaning(raw_ticket_text: str) -> dict:
    """
    Sends one raw ticket to Gemini and returns a validated, structured dictionary.
    Raises a ValueError if Gemini's output does not match our required schema.
    """
    prompt = EXTRACTION_PROMPT_TEMPLATE.format(ticket_text=raw_ticket_text)

    response = model.generate_content(
        prompt,
        generation_config={"response_mime_type": "application/json"},
    )

    raw_output_text = response.text

    try:
        parsed_json = json.loads(raw_output_text)
    except json.JSONDecodeError:
        raise ValueError(f"Gemini did not return valid JSON. Raw output was:\n{raw_output_text}")

    try:
        validated = TicketExtraction(**parsed_json)
    except ValidationError as validation_error:
        raise ValueError(f"Gemini's output failed schema validation:\n{validation_error}")

    return validated.model_dump()


if __name__ == "__main__":
    # Quick manual test with a single hardcoded ticket
    sample_text = "App keeps crashing when I try to upload photos on Android"
    result = extract_ticket_meaning(sample_text)
    print(json.dumps(result, indent=2))
```

5. Save the file.

**How to verify success:** No red underlines anywhere. The file should be about 45 lines long.

**Common beginner mistake:** Typing `LOCATION = "us-central1"` incorrectly (e.g. missing quotes, or a typo). Copy it exactly as shown.

---

## Step 1.9 — Run a Single-Ticket Test

**Goal:** Confirm your agent can successfully talk to Gemini and produce valid structured output for one ticket.

**Exact steps:**
1. In VS Code's terminal, confirm `(venv)` is showing and you're inside the `epc-platform` folder.
2. Navigate into the `agents` folder:
```bash
cd agents
```
3. Run the script:
```bash
python meaning_extraction_agent.py
```

**Expected output:** After a few seconds (Gemini is "thinking"), you should see something like:
```json
{
  "product_area": "Mobile App",
  "issue_type": "Crash",
  "urgency": "High",
  "region": "Unspecified",
  "summary": "User reports app crash during photo upload on Android."
}
```

**How to verify success:** All 5 fields are present, `urgency` is one of exactly `Low`, `Medium`, `High`, or `Critical`, and the JSON is properly formatted (no errors printed above it).

**Common beginner mistake / Troubleshooting:**
- **Error: `DefaultCredentialsError`** — This means your code can't find your service account key. Go back to your `epc-platform` root folder (not `agents`) and confirm `service-account-key.json` and `.env` both exist there. Also confirm you ran the script from a location where `.env` is discoverable — if this error persists, temporarily add this line near the top of `meaning_extraction_agent.py`, just under `load_dotenv()`:
  ```python
  os.environ["GOOGLE_APPLICATION_CREDENTIALS"] = "../service-account-key.json"
  ```
- **Error: `PermissionDenied`** — Go back to Step 1.2 and confirm the **Vertex AI User** role was actually saved on your service account.
- **Error: `Project not found`** — Double check `GCP_PROJECT_ID` inside your `.env` file matches the exact Project ID from Step 0.3 (not the project *name*, the project *ID*, which includes numbers).
- **Error: model not found** — Vertex AI's exact available model names occasionally change. Go to `https://console.cloud.google.com`, search `Model Garden`, click it, search "Gemini", and copy the exact model ID shown there into the `MODEL_NAME` variable in your script.

---

## Step 1.10 — Create the Batch Test Script (All 10 Tickets)

**Goal:** Run all 10 sample tickets through the agent at once and save the results to a file.

**Exact steps:**
1. Right-click the `agents` folder.
2. Click **New File**.
3. Name it exactly: `test_batch_extraction.py`
4. Paste the following code:

```python
import json
from meaning_extraction_agent import extract_ticket_meaning

INPUT_FILE = "../data/sample_tickets.json"
OUTPUT_FILE = "../data/extracted_output.json"


def run_batch_extraction():
    with open(INPUT_FILE, "r") as f:
        tickets = json.load(f)

    results = []

    for ticket in tickets:
        ticket_id = ticket["ticket_id"]
        raw_text = ticket["raw_text"]

        print(f"Processing {ticket_id}: {raw_text[:50]}...")

        try:
            extraction = extract_ticket_meaning(raw_text)
            extraction["ticket_id"] = ticket_id
            results.append(extraction)
            print(f"  -> Success: {extraction['product_area']} / {extraction['issue_type']} / {extraction['urgency']}")
        except ValueError as error:
            print(f"  -> FAILED validation for {ticket_id}: {error}")

    with open(OUTPUT_FILE, "w") as f:
        json.dump(results, f, indent=2)

    print(f"\nDone. {len(results)} of {len(tickets)} tickets extracted successfully.")
    print(f"Results saved to {OUTPUT_FILE}")


if __name__ == "__main__":
    run_batch_extraction()
```

5. Save the file.

**Run it:**
```bash
python test_batch_extraction.py
```

**Expected output:** 10 lines of progress in your terminal, each showing a ticket ID and its extracted category, ending with:
```
Done. 10 of 10 tickets extracted successfully.
Results saved to ../data/extracted_output.json
```

**How to verify success:**
1. In VS Code's sidebar, open `data` → `extracted_output.json`.
2. Confirm it contains 10 objects.
3. Confirm ticket `T001`, `T002`, and `T003` (all about photo upload issues) all have the **same or very similar** `product_area` and `issue_type` values — this confirms the extraction is meaningfully consistent, which matters for clustering in Week 2.

**Common beginner mistake:** Running this script from the wrong folder. You must be inside the `agents` folder when you run it (check with `pwd` on Mac or `cd` alone on Windows to see your current path), because the file paths (`../data/...`) are written relative to that location.

---

## Step 1.11 — Test Schema Validation (Confirm Rejection Works)

**Goal:** Prove that your validation logic actually rejects bad data, not just accepts everything blindly.

**Why:** A validator that never rejects anything isn't actually validating — this test proves yours works.

**Exact steps:**
1. Right-click the `agents` folder.
2. Click **New File**.
3. Name it exactly: `test_validation_failure.py`
4. Paste the following code:

```python
from pydantic import ValidationError
from schema import TicketExtraction

# Deliberately broken data: "urgency" has an invalid value not in our allowed list
bad_data = {
    "product_area": "Mobile App",
    "issue_type": "Crash",
    "urgency": "Super Duper Urgent",  # invalid - not one of Low/Medium/High/Critical
    "region": "Unspecified",
    "summary": "Test summary"
}

try:
    TicketExtraction(**bad_data)
    print("ERROR: Validation incorrectly PASSED. This should not happen.")
except ValidationError as e:
    print("SUCCESS: Validation correctly REJECTED the bad data.")
    print(e)
```

5. Save the file, then run it:
```bash
python test_validation_failure.py
```

**Expected output:**
```
SUCCESS: Validation correctly REJECTED the bad data.
1 validation error for TicketExtraction
urgency
  Input should be 'Low', 'Medium', 'High' or 'Critical' ...
```

**How to verify success:** You see the word "SUCCESS" and a validation error message describing exactly what was wrong (`urgency`). If instead you see "ERROR: Validation incorrectly PASSED," go back to Step 1.6 and confirm you used `Literal["Low", "Medium", "High", "Critical"]` exactly as written for the `urgency` field.

---

## Step 1.12 — Save Your Work to GitHub

**Goal:** Upload (push) all your Week 1 code to your GitHub repository as a backup and version history.

**Exact steps:**
1. In VS Code's terminal, navigate back to the root project folder:
```bash
cd ..
```
2. Confirm you're in `epc-platform` (not `agents`) by checking the terminal path.
3. Type:
```bash
git add .
```
4. Type:
```bash
git commit -m "Week 1: Meaning Extraction Agent complete"
```
5. Type:
```bash
git push
```
6. If this is your first push, Git may open a browser window asking you to authorize. Click **Authorize** and sign in with your GitHub account from Step 0.7.

**Expected output:**
```
Enumerating objects: ...
Writing objects: 100% ...
To https://github.com/YOUR_USERNAME/epc-platform.git
   abc1234..def5678  main -> main
```

**How to verify success:** Go to `https://github.com/YOUR_USERNAME/epc-platform` in your browser and refresh the page. You should see your `agents` and `data` folders listed, along with `requirements.txt`. You should **NOT** see `service-account-key.json` or `.env` anywhere in this list (this confirms `.gitignore` worked correctly).

**Common beginner mistake:** Panicking if `service-account-key.json` does appear on GitHub. If this happens: immediately go to `https://console.cloud.google.com`, navigate back to your service account (Step 0.13), click the **Keys** tab, delete that key, and generate a new one, then fix your `.gitignore` file and re-push.

---

# WEEK 1 COMPLETION CHECKLIST

Go through this list one item at a time. Do not proceed to Week 2 until every item is checked.

- [ ] I have a GCP project named `epc-platform` and I can see it in the Cloud Console project selector.
- [ ] The **Vertex AI API** shows as "Enabled" under APIs & Services → Enabled APIs & services.
- [ ] My service account `epc-platform-agent@...` has exactly the **Vertex AI User** role (visible under IAM).
- [ ] `service-account-key.json` and `.env` exist in my project folder, but do **not** appear on GitHub.
- [ ] My virtual environment activates correctly (`(venv)` shows in the terminal) and `pip list` shows `google-cloud-aiplatform`, `pydantic`, `python-dotenv`.
- [ ] `data/sample_tickets.json` exists and contains exactly 10 tickets, each with 4 fields.
- [ ] `agents/schema.py` defines the 5-field `TicketExtraction` schema.
- [ ] `agents/prompts.py` contains the extraction prompt template.
- [ ] `agents/meaning_extraction_agent.py` runs successfully on a single ticket and prints valid structured JSON.
- [ ] `agents/test_batch_extraction.py` runs successfully and produces `data/extracted_output.json` with 10 entries.
- [ ] Tickets T001, T002, and T003 (all photo-upload issues) were extracted with consistent `product_area` and `issue_type` values.
- [ ] `agents/test_validation_failure.py` runs and prints "SUCCESS: Validation correctly REJECTED the bad data."
- [ ] I ran `git push` successfully, and my code (minus secrets) is visible on GitHub.

Once every box above is checked, Week 1 is complete. Tell me when you're ready, and we'll begin Week 2.
