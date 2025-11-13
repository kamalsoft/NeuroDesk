# Onboarding Guide: NeuroDesk

Welcome to the team! This guide is your starting point for understanding the NeuroDesk product, our technology, and our development process.

---

## 1. Product Vision

NeuroDesk is an AI-powered automation platform that transforms customer voice requests into actionable workflows and seamless human handoffs. Our mission is to ensure businesses never miss an opportunity just because they couldn't answer the phone.

---

## 2. Core Technologies

- **Frontend**: React.js, Tailwind CSS
- **Backend**: Node.js (TypeScript), Express
- **AI/ML**: Python, Google Speech-to-Text, HuggingFace
- **Database**: PostgreSQL, Redis
- **DevOps**: Docker, Kubernetes (GKE), GitHub Actions

---

## 3. Sprint Plan: MVP Development

Here is the proposed sprint plan for building the NeuroDesk MVP. Each sprint is two weeks.

### **Sprint 0: Foundation & Environment Setup (3 Weeks)**

_Goal: Establish the complete technical and operational foundation for the development team._

- **Epic: Project & Team Setup**

  - **Task:** Procure and configure licenses for Jira, Confluence, GitHub, and Slack.
  - **Task:** Set up the Jira project with a Scrum board and populate it with the MVP epics and stories from the `NeuroDesk_Blueprint.md`.

- **Epic: Cloud Infrastructure & Environment**

  - **Task (DevOps):** Create the GCP project, set up billing, and configure IAM roles.
  - **Task (DevOps):** Provision GKE clusters, Cloud SQL (PostgreSQL), and Secret Manager using Terraform.

- **Epic: Development & CI/CD Foundation**

  - **Task (Architect/DevOps):** Create GitHub repos (`frontend`, `backend-api`, `voice-service`, `ai-service`, `workflow-engine`).
  - **Task (DevOps):** Build a "Hello World" CI/CD pipeline in GitHub Actions that can deploy a service to the dev GKE cluster.

- **Epic: Architectural & Team Alignment**
  - **Task (Architect/Team):** Hold design sessions to agree on API contract standards (OpenAPI).
  - **Task (Data Science):** Conduct a technical spike to test the Google Speech-to-Text API and a baseline HuggingFace model for intent classification.

### **Sprint 1: Voice Intake & Transcription**

_Goal: Successfully receive an audio file and transcribe it to text._

- **User Story 1:** As a System, I want to receive an audio file from a voice platform via a webhook so that I can begin processing a customer request.
- **User Story 2:** As a Backend Engineer, I want to integrate with Google Speech-to-Text to transcribe incoming audio files into plain text.
- **Task:** Store the transcription and a link to the audio file in the database.

### **Sprint 2: Intent Recognition & Basic Escalation**

_Goal: Categorize the request and create a ticket for unknown intents._

- **User Story 3:** As a Data Scientist, I want to build a basic NLP model that can categorize a transcription into one of three intents: "schedule_appointment," "sales_inquiry," or "unknown."
- **User Story 6:** As a Backend Engineer, I want the system to execute the "escalation" workflow for any "unknown" intent, creating a generic ticket for the main support queue.
- **User Story 7 (Context):** As a Support Agent, I want every created ticket to include the full transcription and a link to the audio recording.

### **Sprint 3: MVP Workflow Implementation**

_Goal: Implement the automated workflows for recognized intents._

- **User Story 4:** As a Backend Engineer, I want to create a workflow that, for the "schedule_appointment" intent, automatically creates a ticket in Zendesk assigned to the "Scheduling" group.
- **User Story 5:** As a Backend Engineer, I want to create a workflow that, for the "sales_inquiry" intent, automatically creates a ticket in Zendesk assigned to the "Sales" group.

### **Sprint 4: Admin Dashboard & Logging**

_Goal: Create a UI for admins to see system activity._

- **User Story 8:** As an Admin, I want to see a simple, real-time log of all processed calls, including the timestamp, source phone number, and the final action taken (e.g., "Ticket #123 Created").
- **Task:** Build the frontend UI for the log viewer and connect it to the backend API.

### **Sprint 5: End-to-End Testing & Beta Prep**

_Goal: Stabilize the MVP and prepare for the beta launch._

- **Task (QA):** Perform end-to-end testing of the full voice-to-ticket lifecycle.
- **Task (DevOps):** Prepare the production environment and finalize monitoring dashboards.
- **Task (Support):** Write initial documentation for beta customers on how to configure their voice platform webhook.
