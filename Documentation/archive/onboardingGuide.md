# Onboarding Guide: AI-Powered Churn Prediction Platform

Welcome to the team! This guide is your starting point for understanding our product, our technology, and how we work together.

---

## 1. Product Vision & Mission

### Our Vision

To empower subscription-based businesses to proactively reduce customer churn by providing accurate, real-time, and actionable predictive insights through an intuitive and integrated platform.

### What We're Building

We are creating an AI-powered platform that connects to our customers' business data (CRM, payments) to predict which of their customers are likely to churn. The platform will not only provide a "churn risk score" but also explain the factors behind the score, allowing Customer Success Managers (CSMs) to take proactive, targeted action.

---

## 2. Product Features & Roadmap

Our development is split into three main phases, as detailed in the [Product Blueprint](Product_Blueprint.md).

### Phase 1: Minimum Viable Product (MVP)

The goal is to launch a core product that delivers immediate value.

- **Data Integration:** Connectors for Stripe, Salesforce, and HubSpot.
- **Core Prediction Model:** A baseline model to generate a daily "churn risk score" for each customer.
- **Customer Dashboard:** A simple UI to list and sort customers by their risk score.
- **Basic Analytics:** High-level view of the overall predicted churn rate.

### Phase 2: Core Product

The goal is to enrich the data and add more actionable insights.

- **Feature Importance:** Show _why_ a customer has a high churn score.
- **Customer Segmentation:** Allow users to filter and analyze specific customer groups.
- **Alerting System:** Proactive Slack/email alerts for at-risk customers.
- **Expanded Connectors:** Add Zendesk and Mixpanel for deeper insights.

### Phase 3: Advanced/Enterprise

The goal is to add enterprise-grade features and customization.

- **Actionable Recommendations:** Suggest specific retention actions.
- **"What-If" Simulation:** Model the impact of retention campaigns.
- **API Access:** Allow customers to pull data into their own systems.

---

## 3. Technical Architecture & Stack

We use a modern, cloud-native stack to build a scalable and maintainable platform.

- **Architecture:** Microservices
- **Cloud Provider:** Google Cloud Platform (GCP)
- **Containerization:** Docker & Kubernetes (GKE)
- **Backend:** Go / Node.js (APIs), **Python** (Data Science)
- **Frontend:** React / Vue.js
- **Databases:** PostgreSQL (Application DB), BigQuery (Data Warehouse), Redis (Cache)
- **ML Stack:** Scikit-learn, XGBoost, MLflow, Vertex AI

---

## 4. Team Roles & Responsibilities

Every role is critical to our success. Please find your role and understand your key responsibilities.

| Role                          | Count | Key Responsibilities                                              |
| :---------------------------- | :---- | :---------------------------------------------------------------- |
| **Product Owner**             | 1     | Defines vision, manages the backlog, prioritizes features.        |
| **Senior Software Architect** | 1     | Designs the overall system, makes high-level technical decisions. |
| **Data Scientist**            | 2     | Develops, trains, and evaluates the churn prediction models.      |
| **Backend Engineer**          | 3     | Builds APIs, data pipelines, and the core application logic.      |
| **Frontend Engineer**         | 2     | Builds the user dashboard and all client-facing interfaces.       |
| **DevOps Engineer**           | 1     | Manages cloud infrastructure, CI/CD pipelines, and deployment.    |
| **QA Specialist**             | 2     | Develops test plans and builds automated test suites.             |
| **Support Engineer**          | 1     | Manages beta customer issues and creates documentation.           |

---

## 5. Our Tools & Workflow

- **Project Management:** We use **Jira** for sprint planning and backlog management.
- **Documentation:** We use **Confluence** for internal documentation.
- **Code & CI/CD:** We use **GitHub** for source control and **GitHub Actions** for CI/CD.
- **Communication:** We use **Slack** for daily communication.

---

## 6. Sprint Plan Templates (Phase 1 - MVP)

Here is the proposed sprint plan for building the NeuroDesk MVP. Each sprint is two weeks.

### **Sprint 0: Foundation & Environment Setup**

_Goal: Establish the complete technical and operational foundation required for the development team to begin building the MVP efficiently in Sprint 1._
_Duration: 3-4 Weeks_

- **Epic: Project & Team Setup**

  - **Task:** Procure and configure licenses for Jira, Confluence, GitHub, and Slack.
  - **Task:** Set up the Jira project with a Scrum board, define the workflow (Backlog, To Do, In Progress, In Review, QA, Done), and populate it with the MVP epics and stories.
  - **Task:** Set up the Confluence space and import initial documentation like the Product Blueprint and this Onboarding Guide.
  - **Task:** Create team-wide communication channels in Slack.

- **Epic: Cloud Infrastructure & Environment**

  - **Task (DevOps):** Create the Google Cloud Platform (GCP) project and set up billing.
  - **Task (DevOps):** Define and provision the core cloud infrastructure (VPC, subnets, firewall rules) using Infrastructure as Code (e.g., Terraform).
  - **Task (DevOps):** Configure Identity and Access Management (IAM) roles and grant permissions to the team based on their roles.
  - **Task (DevOps):** Provision initial instances of GKE clusters, Cloud SQL (PostgreSQL), BigQuery datasets, and Cloud Storage buckets for the development environment.

- **Epic: Development & CI/CD Foundation**

  - **Task (Architect/DevOps):** Create the initial GitHub repositories for the microservices (`frontend`, `backend-api`, `voice-service`, `workflow-engine`, `ai-service`).
  - **Task (Architect/DevOps):** Establish the branching strategy (e.g., GitFlow) and configure branch protection rules on the main branches.
  - **Task (DevOps):** Build a "Hello World" CI/CD pipeline in GitHub Actions that can build a container, run basic tests, and deploy to the dev GKE cluster.

- **Epic: Tooling & Licensing Procurement**

  - **Task:** Procure and configure licenses for monitoring tools (e.g., Datadog).
  - **Task:** Procure and configure licenses for support and ticketing tools (e.g., Zendesk).
  - **Task:** Set up developer access to all required third-party services and tools.

- **Epic: Architectural & Team Alignment**
  - **Task (Architect/Team):** Hold initial design sessions to agree on API contract standards (e.g., OpenAPI/Swagger).
  - **Task (Architect/Team):** Define and document initial coding standards, linting rules, and code review guidelines for each repository.
  - **Task (Backend/DevOps):** Conduct an initial technical spike to test connections to the Twilio and Google Speech-to-Text APIs.

### Sprint 1: Foundation & Data Ingestion

_Goal: Build the core voice ingestion pipeline._

- **User Story 1:** As an Admin, I want to connect my Twilio account to NeuroDesk using an API key so that voicemails can be received.
- **User Story 2:** As a Backend Engineer, I want the system to automatically transcribe incoming audio files from Twilio into text.
- **DevOps Task:** Ensure the `voice-service` is deployed and can receive webhooks from Twilio.

### Sprint 2: Expanding Data & Processing

_Goal: Implement the basic escalation logic._

- **User Story 3:** As a Backend Engineer, I want the system to perform basic keyword matching on the transcription to identify a preliminary intent.
- **User Story 4:** As a Backend Engineer, I want the system to create a ticket in our internal ticketing system if an intent cannot be identified.
- **User Story 5:** As a Support Agent, I want the created ticket to contain the full transcription and a link to the audio recording.

### Sprint 3: Model Training & Feature Engineering

_Goal: Build the basic frontend for ticket management._

- **User Story 7:** As a Support Agent, I want to log in to the NeuroDesk platform to see a simple list of all escalated tickets.
- **Frontend Task:** Create the login page and basic authentication flow.
- **Backend Task:** Build the `/tickets` API endpoint to serve the list of tickets.

### Sprint 4: Scoring & Backend API

_Goal: Build the ticket detail view._

- **User Story 8:** As a Support Agent, I want to click on a ticket in the list to view its details (transcription, audio link) on a separate page.
- **Backend Task:** Build the `/tickets/:id` API endpoint.
- **Frontend Task:** Create the ticket detail page component.

### Sprint 5: Frontend Dashboard - List View

_Goal: Finalize MVP functionality and prepare for internal demo._

- **User Story 9:** As a Support Agent, I want to be able to mark a ticket as "Resolved" or "Closed" from the UI.
- **QA Task:** Perform end-to-end testing of the full voice-to-ticket flow.
- **Team Task:** Prepare for and conduct the internal MVP demo.
