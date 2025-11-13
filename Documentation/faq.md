# NeuroDesk - Frequently Asked Questions (FAQ)

**Version:** 1.0

This document provides answers to frequently asked questions about the NeuroDesk project, compiled from our core documentation.

---

## Strategy & Vision

### **Q: What is the product vision for NeuroDesk?**

**A:** The vision is to be the bridge between customer voice requests and business action, ensuring no opportunity is missed. We empower businesses to automate their front desk, turning every call into an intelligent, actionable workflow.

_(Source: Product_Blueprint.md)_

---

### **Q: Who is the primary target audience for the product?**

**A:** The Ideal Customer Profile (ICP) is a small to medium-sized business in a service-oriented industry that relies on phone calls. The primary vertical is **Auto Dealerships** (service departments), with secondary verticals in Healthcare Practices and Professional Services.

_(Source: Product_Blueprint.md)_

---

### **Q: What are the key features planned for Phase 2 (Core Product)?**

**A:** Phase 2 focuses on building out automation and intelligence. Key features include the **Smart Workflow Engine** with a visual builder, **Advanced Intent Recognition** using NLP models, **CRM Integration**, and the ability for workflows to perform **Automated Actions** like sending a confirmation SMS.

_(Source: Product_Blueprint.md)_

---

### **Q: Summarize the Go-To-Market (GTM) strategy.**

**A:** The GTM strategy targets small to medium-sized service businesses, with a primary focus on Auto Dealerships. It uses a product-led growth (PLG) model centered around a 14-day free trial. Marketing efforts include content marketing (blogs, case studies), a Product Hunt launch, direct outreach to industry associations, and paid ads on LinkedIn and Google.

_(Source: Product_Blueprint.md)_

---

### **Q: What is the estimated total cost for the first year?**

**A:** The estimated grand total for the first year is approximately **$2,045,700**. This is composed of ~$2,013,000 in personnel costs and ~$32,700 in cloud infrastructure costs.

_(Source: Product_Blueprint.md)_

---

## Technical

### **Q: What is the primary responsibility of the AI Service?**

**A:** The AI Service's primary responsibility is to provide Natural Language Understanding (NLU). It receives transcribed text and analyzes it to determine the customer's **intent**, extract relevant **entities** (like names or dates), perform **sentiment analysis**, and calculate a **confidence score** for its predictions.

_(Source: AIService_TDD.md)_

---

### **Q: Explain the data flow for the Voice Service.**

**A:**

1.  A voice platform (like Twilio) sends a webhook with an audio URL to the Voice Service.
2.  The service downloads the audio and archives it in Google Cloud Storage.
3.  It sends the audio to Google Speech-to-Text for transcription.
4.  The transcription and metadata are saved to the PostgreSQL database.
5.  Finally, it sends the structured data to the Smart Workflow Engine to trigger a workflow.

_(Source: VoiceService_TDD.md)_

---

### **Q: What technology stack is used for the Backend API Service?**

**A:** The Backend API Service is built using **Node.js (TypeScript)** and the **Express.js** framework. It uses **Passport.js** for authentication, an ORM like **Prisma** or **TypeORM** for database interaction with PostgreSQL, and **Redis** for session management.

_(Source: BackendAPI_TDD.md)_

---

### **Q: How is a workflow triggered in the Smart Workflow Engine?**

**A:** A workflow is triggered when an upstream service, like the `NeuroDesk Voice Service`, makes a `POST` request to the `/execute` endpoint of the Smart Workflow Engine. This request must include a `workflowId` and a payload of input data (e.g., the transcription).

_(Source: SmartWorkflowEngine_TDD.md)_

---

### **Q: What are some key API endpoints for managing tickets?**

**A:** The `Backend API Service` provides several key endpoints for ticket management, including:

- `GET /api/tickets`: To list all tickets with filtering.
- `GET /api/tickets/:id`: To get the full details of a single ticket.
- `POST /api/tickets/:id/notes`: To add a note to a ticket.
- `PUT /api/tickets/:id/status`: To update a ticket's status.

_(Source: BackendAPI_TDD.md)_

---

## Onboarding & Process

### **Q: What are the goals for Sprint 1 of the MVP?**

**A:** The primary goal for Sprint 1 is to **successfully receive an audio file and transcribe it to text**. This involves receiving an audio file via a webhook, integrating with Google Speech-to-Text to get a plain text transcription, and storing the result in the database.

_(Source: NeuroDesk_Onboarding.md)_

---

### **Q: What is the testing strategy for the project?**

**A:** The strategy follows the testing pyramid, focusing heavily on **Unit Tests** (Jest, Pytest) with an 80% coverage target. This is supported by **Integration Tests** (Docker Compose, Postman) for service interactions and **End-to-End Tests** (Cypress) to validate user flows. The strategy also includes Performance, Load, and Security testing.

_(Source: TestingStrategy.md)_

---

### **Q: What tools are used for unit testing on the frontend?**

**A:** The frontend (React) uses **Jest** as the test runner and **React Testing Library** for rendering components and simulating user interactions.

_(Source: TestingStrategy.md)_

---

### **Q: Who is responsible for End-to-End testing?**

**A:** **QA Specialists** are responsible for End-to-End (E2E) testing. They use tools like Cypress to simulate real user scenarios and validate the entire system flow from the user's perspective.

_(Source: TestingStrategy.md)_

---

## Marketing & Sales

### **Q: What is the core marketing message?**

**A:** The core marketing message is: **"Never miss a customer opportunity again. Automate your front desk with AI."**

_(Source: Product_Blueprint.md, SalesSlick.md)_

---

### **Q: List the core benefits for a business using NeuroDesk.**

**A:** The three core benefits are:

1.  **Capture Every Lead:** Turn every voicemail into an actionable opportunity.

---

2.  **Increase Team Efficiency:** Automate routine tasks to free up skilled staff.
3.  **Elevate Customer Experience:** Provide instant, accurate responses and seamless escalations.

_(Source: SalesSlick.md)_

---

### **Q: What is the topic of the first blog post in the content calendar?**

**A:** The first blog post is the launch announcement, titled: **"Introducing NeuroDesk: Never Miss a Customer Opportunity Again."**

_(Source: content_calendar.md)_

---

### **Q: Summarize the pricing tiers for NeuroDesk.**

**A:** There are three tiers:

_(Source: Product_Blueprint.md)_
