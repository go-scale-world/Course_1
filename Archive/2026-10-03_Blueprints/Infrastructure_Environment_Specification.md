# Infrastructure Environment Specification: Agentic AI for Enterprise Problem Resolution (EPR)

## 1. Learner Lab Profile

- **Required Student Tooling:** Google Cloud Shell (browser-based terminal and code editor), VS Code Web via Cloud Shell Editor, Google Colab Enterprise for embedding and fine-tuning experimentation, and Google Chrome for the Gradio dashboard and Google Workspace integrations. No local installation is required for any module; all compute-heavy tasks are executed against provisioned GCP resources rather than the student's local machine.

- **Dataset Access Needed:** A synthetic support-ticket dataset (raw text, varied tone and length) provisioned in Cloud Storage for Weeks 1 to 2, a labelled incident-history dataset for Week 4 (Weeks 3, and 5 rely on subsets of this same base). A sample Google Workspace domain (Drive, Docs, Gmail, Chat) pre-populated with mock incident documentation for Weeks 5 to 6. No external or third-party datasets are required; all data is internally generated or synthetic to avoid data-licensing and privacy exposure.

- **Evidence / Proof Source:** The entire lab environment runs in a browser-based, cloud-hosted setup on the GCP stack, so no local compute, GPU, or storage is required on the student's end. A laptop with 4GB RAM or higher, sufficient to run Chrome smoothly, is adequate for every module in the course.

## 2. GCP Deployment Schema

- **Primary Compute Engine Tier:** e2-standard-4 for general lab workloads.

- **GCP Vertex AI Services Provisioned:** Vertex AI Gemini API (structured generation and function calling), Vertex AI Embeddings API, Vertex AI Vector Search, Vertex AI Safety filters and Model Armor, Vertex AI Agent Builder, Vertex AI Tuning for Gemini, and Vertex AI Search. Supporting services: Cloud Storage, BigQuery or Cloud SQL, Firestore, Cloud Pub/Sub, Cloud Functions, Cloud Run, Cloud Build, Cloud Workflows, Cloud Monitoring, Cloud Logging, Cloud Scheduler, and IAM.

- **Evidence / Proof Source:** Estimated per-student cost across the core services provisioned for the course will all get covered i the 3 month GCP free activation plan.
