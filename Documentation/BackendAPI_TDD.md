# Technical Design Document: Backend API Service

**Version:** 2.0
**Status:** Draft
**Related Epics:** "Basic User Interface", "Smart Workflow Engine"

---

## 1. Introduction

### 1.1. Purpose

The Backend API Service is the primary interface between the NeuroDesk frontend application and the core business logic and data of the platform. It provides authenticated REST endpoints for agents and administrators to manage tickets, configure workflows, and view system activity. It acts as the "system of record" for user-facing entities.

### 1.2. Scope

**In Scope:**

- User authentication and authorization (login, logout, session management).
- CRUD (Create, Read, Update, Delete) operations for `Tickets`.
- CRUD operations for `Workflows` (the JSON definitions).
- CRUD operations for `Users` and `Agents`.
- Exposing aggregated data for the Analytics Dashboard.
- Exposing read-only endpoints for call logs and audit trails.
- Providing endpoints to support the agent dashboard UI.

**Out of Scope:**

- Direct handling of voice calls (done by `Voice Service`).
- Direct execution of workflows (this is delegated to the `Smart Workflow Engine`).
- Real-time communication with the frontend (e.g., WebSockets), which can be a future consideration.

---

## 2. High-Level Design

The service will be a containerized Node.js application deployed on GKE. It will be the main service interacting with the central PostgreSQL database.

### 2.1. Architecture Diagram

```mermaid
graph TD
    subgraph "External World"
        Agent[<fa:fa-user> Human Agent]
    end

    subgraph "NeuroDesk Platform (GCP)"
        Frontend[Frontend Service<br>(React UI)]
        WorkflowEngine[Smart Workflow Engine]

        subgraph "Backend API Service (GKE)"
            ApiEndpoints[REST API Endpoints<br>/api/*]
        end

        subgraph "Data Stores"
            AppDB[(<fa:fa-database> App DB: PostgreSQL)]
            Cache[(<fa:fa-bolt> Redis)]
        end
    end

    Agent -- HTTPS --> Frontend
    Frontend -- "1. API Calls" --> ApiEndpoints
    ApiEndpoints -- "2. CRUD Operations" --> AppDB
    ApiEndpoints -- "3. Session Mgmt" --> Cache
    WorkflowEngine -- "4. Triggered by other services" --> WorkflowEngine
    WorkflowEngine -- "5. Reads Workflow JSON" --> ApiEndpoints
```

### 2.2. Data Flow

1.  **Agent Interaction:** A human agent uses the React `Frontend` application in their browser.
2.  **API Calls:** The frontend makes authenticated API calls to the `Backend API Service` to fetch or modify data (e.g., `GET /api/tickets`, `POST /api/tickets/123/notes`).
3.  **Database Interaction:** The service validates the request, performs the necessary business logic, and executes the corresponding CRUD operation against the PostgreSQL `AppDB`.
4.  **Workflow Reading:** When the `Smart Workflow Engine` needs to execute a workflow, it calls this service's `/api/workflows/:id` endpoint to fetch the workflow's JSON definition.

---

## 3. Low-Level Design

### 3.1. Technology Stack

- **Language:** Node.js (TypeScript)
- **Framework:** Express.js
- **Key Libraries:**
  - `pg` or an ORM like `Prisma` / `TypeORM`: For database interaction.
  - `passport.js` with `passport-jwt`: For authentication strategy.
  - `jsonwebtoken`: To sign and verify JWTs.
  - `bcrypt`: To hash passwords.
  - `redis`: For session/cache interaction.

### 3.2. Key API Endpoints

All endpoints are prefixed with `/api`.

`POST /auth/login`

- Authenticates a user and returns a JWT.

`GET /tickets`

- Returns a paginated list of tickets. Supports filtering by status, assignee, etc.

`GET /tickets/:id`

- Returns the full details of a single ticket, including its associated call log, transcription, and notes.

`POST /tickets/:id/notes`

- Adds an internal note to a ticket.

`PUT /tickets/:id/status`

- Updates the status of a ticket (e.g., from 'New' to 'In Progress').

`GET /workflows`

- Returns a list of all defined workflows.

`POST /workflows`

- Creates a new workflow from a JSON definition provided by the frontend workflow builder.

`PUT /workflows/:id`

- Updates an existing workflow's JSON definition.

### 3.3. Authentication & Authorization

- **Strategy:** JWTs (JSON Web Tokens) will be used for stateless authentication.
- **Flow:**
  1. A user logs in via `POST /auth/login` with email/password.
  2. The service verifies credentials and returns a short-lived access token and a long-lived refresh token.
  3. The frontend stores these tokens securely (e.g., access token in memory, refresh token in an HttpOnly cookie).
  4. Every subsequent API request to a protected endpoint must include the access token in the `Authorization: Bearer <token>` header.
- **Authorization:** Middleware will be used to check for a valid JWT and verify user roles/permissions before allowing access to certain endpoints (e.g., only an 'admin' can create a new user).
- **Role-Based Access Control (RBAC):** User roles ('agent', 'manager', 'admin') will be stored in the JWT payload and checked by middleware to restrict access to specific endpoints and resources.

### 3.4. Database Schema (Key Tables)

- **`users`**: id, name, email, password_hash, role
- **`tickets`**: id, status, priority, created_at, updated_at, assigned_to_id (FK to users)
- **`calls`**: id, source_phone_number, audio_gcs_uri, transcription, ticket_id (FK to tickets)
- **`ticket_notes`**: id, content, created_at, author_id (FK to users), ticket_id (FK to tickets)
- **`workflows`**: id, name, definition (JSONB)

---

## 4. Monitoring & Logging

- **Logging:** Structured logs for all incoming requests and outgoing database queries, including latency.
- **Metrics:** Prometheus metrics for API request latency (by route), status codes (2xx, 4xx, 5xx), and database query performance.
- **Alerting:** Alerts for high 4xx/5xx error rates, high API latency, and database connection issues.

---

## 5. Changelog

| Version | Date       | Description                    |
| :------ | :--------- | :----------------------------- |
| 1.0     | 2025-11-13 | Initial draft of the document. |
