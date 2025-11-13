# NeuroDesk - AI Voice Automation Platform

**Version:** 1.0

NeuroDesk is an AI-powered voice automation platform that transforms customer voice requests into intelligent, actionable workflows and provides seamless human handoffs. Our mission is to ensure businesses never miss an opportunity.

---

## Core Features

- **Voice Intelligence:** Automatically transcribes voice messages, understands customer intent using NLP, and analyzes sentiment to prioritize urgent requests.
- **Smart Workflow Engine:** A visual builder allows users to create custom, conditional automation rules that integrate with their existing business tools (CRMs, Schedulers, etc.).
- **Intelligent Escalation:** Uses a confidence score to determine when to execute an automated action versus when to escalate to a human agent, ensuring a seamless handoff with full context.
- **Centralized Ticketing:** Provides a simple UI for agents to manage all escalated voice requests in one place.

---

## 🚀 Tech Stack

| Category     | Technologies                                        |
| :----------- | :-------------------------------------------------- |
| **Frontend** | React.js, Tailwind CSS                              |
| **Backend**  | Node.js (TypeScript), Express                       |
| **AI/ML**    | Python, HuggingFace, Google Speech-to-Text          |
| **Database** | PostgreSQL, Redis                                   |
| **DevOps**   | Docker, Kubernetes (GKE), Terraform, GitHub Actions |

---

## 📚 Project Documentation

This project's documentation is cataloged in the [`.gitmcp.json`](.gitmcp.json) file. Below is a summary for quick navigation.

### Strategy & Requirements

| Document Name                                                              | Description                                                                                    |
| :------------------------------------------------------------------------- | :--------------------------------------------------------------------------------------------- |
| [Product Blueprint](Documentation/Product_Blueprint.md)                    | The master document for product vision, features, roadmap, GTM strategy, and budget.           |
| [Onboarding Guide](Documentation/NeuroDesk_Onboarding.md)                  | The starting point for new team members, including the MVP sprint plan and setup instructions. |
| [Product Features](Documentation/Product_Feature.md)                       | A high-level introduction and feature breakdown of the NeuroDesk platform.                     |
| [User Story: Contextual Escalation](Documentation/UserStory_Escalation.md) | Detailed user story and acceptance criteria for the contextual human escalation feature.       |

### Technical Design

| Document Name                                                         | Description                                                                                         |
| :-------------------------------------------------------------------- | :-------------------------------------------------------------------------------------------------- |
| [Backend API TDD](Documentation/BackendAPI_TDD.md)                    | Technical Design Document for the primary API service that serves the frontend.                     |
| [AI Service TDD](Documentation/AIService_TDD.md)                      | Technical Design Document for the microservice responsible for NLP, intent, and sentiment analysis. |
| [Smart Workflow Engine TDD](Documentation/SmartWorkflowEngine_TDD.md) | Technical Design Document for the core service that executes automated business logic.              |
| [Voice Service TDD](Documentation/VoiceService_TDD.md)                | Technical Design Document for the service that ingests and transcribes voice calls.                 |

### Marketing

| Document Name                                         | Description                                                         |
| :---------------------------------------------------- | :------------------------------------------------------------------ |
| [Sales Slick](Documentation/SalesSlick.md)            | A one-page marketing document for sales and promotional activities. |
| [Content Calendar](Documentation/content_calendar.md) | The blog content calendar for the first month post-launch.          |

### Quality

| Document Name                                        | Description                                                                           |
| :--------------------------------------------------- | :------------------------------------------------------------------------------------ |
| [Testing Strategy](Documentation/TestingStrategy.md) | The comprehensive strategy for ensuring product quality, from unit tests to security. |

---

## ⚙️ Getting Started

1.  **Clone the Repository:**
    ```sh
    git clone https://github.com/your-org/neurodesk.git
    ```
2.  **Review the Onboarding Guide:** Start with the **Onboarding Guide** for detailed setup instructions, repository structure, and the MVP sprint plan.

3.  **Explore the Architecture:** For a deep dive into the system's components, review the documents in the **Technical Design** section above.

### Documentation Visibility and MCP Requests

This `README.md` serves as the primary interface for handling "MCP" (Master Catalog of Project) requests. The tables above are generated from the `.gitmcp.json` file and provide direct visibility into the project's documentation structure.

**To update the documentation catalog:**

1.  Add or modify the relevant markdown file in the `/Documentation` directory.
2.  Update the `.gitmcp.json` file to reflect the changes.
3.  Regenerate this `README.md` to ensure the documentation links are current.
