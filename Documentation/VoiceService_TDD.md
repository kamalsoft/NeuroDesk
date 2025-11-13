# Technical Design Document: NeuroDesk Voice Service

**Version:** 1.0
**Status:** Draft
**Related Epics:** "Core Voice Pipeline"

---

## 1. Introduction

### 1.1. Purpose

The NeuroDesk Voice Service is the primary entry point for all customer voice interactions with the NeuroDesk platform. Its core responsibilities are to receive audio from a third-party voice platform (e.g., Twilio), orchestrate the transcription process, and then forward the structured data to the appropriate downstream service (the Smart Workflow Engine) for processing.

### 1.2. Scope

**In Scope:**

- Exposing a secure webhook endpoint to receive audio recordings from a voice platform like Twilio.
- Integrating with a third-party speech-to-text service (e.g., Google Speech-to-Text) to transcribe the audio.
- Supporting real-time transcription streaming via WebSockets for live call analysis.
- Storing the raw audio file in a persistent object store (Google Cloud Storage).
- Storing the resulting transcription and associated metadata in the application database.
- Calling the `Smart Workflow Engine` with the final structured data payload.
- Basic error handling and logging for the transcription and handoff process.

**Out of Scope:**

- Handling the phone call itself (this is managed by the external voice platform).
- Intent recognition and confidence scoring (this is handled by the `AI Service`).
- Executing business logic or workflows (this is handled by the `Smart Workflow Engine`).

---

## 2. High-Level Design

The service will be a containerized Node.js application deployed on GKE. It is designed to be a stateless, event-driven component that reacts to incoming webhooks.

### 2.1. Architecture Diagram

```mermaid
graph TD
    subgraph "External World"
        VoicePlatform[<fa:fa-tty> Voice Platform<br>(e.g., Twilio)]
    end

    subgraph "NeuroDesk Platform (GCP)"
        subgraph "Voice Service (GKE)"
            WebhookEndpoint[Webhook Endpoint<br>/v1/ingest]
            WebSocketEndpoint[WebSocket Endpoint<br>/v1/stream]
        end

        subgraph "Downstream Services"
            WorkflowEngine[Smart Workflow Engine]
        end

        subgraph "Supporting Services"
            SpeechToText[<fa:fa-comment-dots> Google Speech-to-Text]
            AudioStore[<fa:fa-archive> Cloud Storage]
            AppDB[(<fa:fa-database> App DB: PostgreSQL)]
        end
    end

    VoicePlatform -- "1. POST Webhook w/ Audio URL" --> WebhookEndpoint
    VoicePlatform -- "Live Audio Stream" --> WebSocketEndpoint
    WebhookEndpoint -- "2. Download Audio" --> VoicePlatform
    WebhookEndpoint -- "3. Upload Audio" --> AudioStore
    WebhookEndpoint -- "4. Send Audio for Transcription" --> SpeechToText
    SpeechToText -- "5. Return Transcription" --> WebhookEndpoint
    WebhookEndpoint -- "6. Store Metadata & Text" --> AppDB
    WebhookEndpoint -- "7. POST to Execute Workflow" --> WorkflowEngine
```

### 2.2. Data Flow

1.  **Webhook Trigger:** Twilio (or another voice platform) is configured to call the `POST /v1/ingest` endpoint on this service after a voicemail recording is complete. The payload from Twilio includes a secure URL to the audio file.
2.  **Audio Handling:** The service downloads the audio file from the provided URL.
3.  **Persistent Storage:** The audio file is immediately uploaded to a Google Cloud Storage bucket for archival and auditing purposes. The GCS URI is saved.
4.  **Transcription:** The service sends the audio data to the Google Speech-to-Text API for transcription.
5.  **Data Persistence:** Upon receiving the transcription, the service saves a new record in the `calls` table in the main PostgreSQL database, including the GCS URI of the audio, the full transcription, the source phone number, and a timestamp.
6.  **Handoff:** The service constructs a JSON payload containing the transcription and other metadata and makes a `POST` request to the `Smart Workflow Engine` to trigger the appropriate workflow.

---

## 3. Low-Level Design

### 3.1. Technology Stack

- **Language:** Node.js (TypeScript)
- **Framework:** Express.js
- **Key Libraries:**
  - `@google-cloud/speech`: Client for Google Speech-to-Text.
  - `@google-cloud/storage`: Client for Google Cloud Storage.
  - `axios`: For making the downstream call to the workflow engine.
  - `pg`: To connect to the PostgreSQL database.

### 3.2. API Endpoint

`POST /v1/ingest`

- **Description:** The primary webhook endpoint for receiving new voice messages.
- **Security:** This endpoint must be secured to ensure that only legitimate requests from the voice platform are processed. This will be achieved by using Twilio's standard request validation middleware, which verifies the `X-Twilio-Signature` header.
- **Request Body (from Twilio):**
  ```json
  {
    "CallSid": "CA123...",
    "From": "+15551234567",
    "RecordingUrl": "https://api.twilio.com/...",
    "RecordingDuration": "32"
  }
  ```
- **Successful Response:** The service should respond with a `204 No Content` to acknowledge receipt immediately and process the audio asynchronously.

### 3.3. Database Schema

The service will interact with a `calls` table (schema to be finalized with the Backend API team).

**`calls` table (proposed)**

- `id` (UUID, PK)
- `created_at` (Timestamp)
- `source_phone_number` (String)
- `audio_gcs_uri` (String)
- `transcription` (Text)
- `status` (String: e.g., `RECEIVED`, `TRANSCRIBED`, `PROCESSED`, `FAILED`)
- `workflow_execution_id` (UUID, FK)

### 3.4. Error Handling

- **Transcription Failure:** If the Speech-to-Text API fails, the call record will be marked with a `FAILED` status, and an alert will be triggered in Cloud Monitoring.
- **Handoff Failure:** If the call to the `Smart Workflow Engine` fails, the service will implement a retry mechanism with exponential backoff. If it continues to fail, the call status will be updated accordingly, and an alert will be triggered.

---

## 4. Open Questions

1.  **Diarization:** Does the MVP require speaker diarization (identifying who is speaking)? **Decision:** No, this is a post-MVP feature.
2.  **Real-time Streaming:** Should we support real-time transcription streams in the future? **Decision:** Not for the MVP. The current design is for post-call voicemail processing only.

---

## 5. Changelog

| Version | Date       | Description                    |
| :------ | :--------- | :----------------------------- |
| 1.0     | 2025-11-13 | Initial draft of the document. |
