# Technical Design Document: AI Service

**Version:** 2.0
**Status:** Draft
**Related Epics:** "Core Voice Pipeline", "Intelligent Escalation"

---

## 1. Introduction

### 1.1. Purpose

The AI Service is a backend microservice that provides natural language understanding (NLU) capabilities to the NeuroDesk platform. Its primary responsibility is to receive transcribed text from the `Voice Service` and analyze it to determine the customer's intent, extract relevant entities (like names or dates), and calculate a confidence score for its predictions. This structured output is then passed to the `Smart Workflow Engine` to drive business logic.

### 1.2. Scope

**In Scope:**

- Exposing a secure REST API endpoint to receive text for analysis.
- Loading and running a pre-trained NLP model for intent classification.
- Loading and running a pre-trained NLP model for Named Entity Recognition (NER).
- Performing sentiment analysis on the text (e.g., positive, neutral, negative).
- Detecting and redacting Personally Identifiable Information (PII) from transcripts.
- Calculating a confidence score for the predicted intent.
- Returning a structured JSON response containing the intent, entities, and confidence score.
- Caching model predictions for identical inputs to reduce latency and cost.

**Out of Scope:**

- Voice transcription (handled by the `Voice Service`).
- Model training, fine-tuning, and evaluation (this is an offline MLOps process).
- Executing business workflows (handled by the `Smart Workflow Engine`).

---

## 2. High-Level Design

The service will be a containerized Python application deployed on GKE, likely requiring GPU-enabled nodes for efficient model inference.

### 2.1. Architecture Diagram

```mermaid
graph TD
    subgraph "NeuroDesk Platform (GCP)"
        VoiceService[NeuroDesk Voice Service]
        WorkflowEngine[Smart Workflow Engine]

        subgraph "AI Service (GKE)"
            ApiEndpoint[API Endpoint<br>/v1/analyze] --> ModelInference[Model Inference Engine]
        end

        subgraph "Supporting Services"
            ModelRegistry[<fa:fa-archive> Cloud Storage<br>(Model Registry)]
            Cache[(<fa:fa-bolt> Redis)]
        end
    end

    VoiceService -- "1. POST /v1/analyze with text" --> ApiEndpoint
    ModelInference -- "2. Check Cache" --> Cache
    ModelInference -- "3. Load Model (on startup)" --> ModelRegistry
    ModelInference -- "4. Perform Inference" --> ModelInference
    ApiEndpoint -- "5. Return Full Analysis" --> WorkflowEngine
```

### 2.2. Data Flow

1.  **Trigger:** The `Voice Service` (or `Workflow Engine`) makes a `POST` request to `/v1/analyze` with the transcribed text.
2.  **Cache Check:** The service first creates a hash of the input text and checks Redis for a cached result to avoid re-processing.
3.  **Model Loading:** On startup, the service loads the specified intent classification and NER models from the Model Registry (a GCS bucket).
4.  **Inference:** The text is passed to the loaded models to predict intent and extract entities. The confidence score is derived from the model's output softmax layer.
5.  **Response:** The service returns a structured JSON object to the caller. The result is also stored in the Redis cache with a TTL.

---

## 3. Low-Level Design

### 3.1. Technology Stack

- **Language:** Python 3.10+
- **Framework:** FastAPI for high-performance async API.
- **Key Libraries:**
  - `transformers`: To load and use models from the HuggingFace ecosystem.
  - `torch` or `tensorflow`: As the backend for the transformer models.
  - `redis`: For caching.

### 3.2. API Endpoint

`POST /v1/analyze`

- **Description:** Analyzes a block of text to determine intent and entities.
- **Request Body:**
  ```json
  {
    "text": "Hi, I'd like to book a service appointment for my car for next Tuesday."
  }
  ```
- **Success Response (200 OK):**
  ```json
  {
    "intent": "schedule_appointment",
    "confidence": 0.98,
    "entities": [
      { "type": "SERVICE", "value": "service appointment" },
      { "type": "DATE", "value": "next Tuesday" }
    ],
    "model_version": "v1.2.0"
  }
  ```
- **Security:** Internal endpoint, secured via service-to-service authentication.

### 3.3. Model Management

- **Registry:** Models will be stored in a GCS bucket, versioned by name (e.g., `intent-model-v1.2.0/`).
- **Loading:** The service will load the model specified by an environment variable at startup. This allows for easy model updates by simply changing the environment variable and restarting the pods.
- **Initial Intents (MVP):** `schedule_appointment`, `sales_inquiry`, `check_status`, `unknown`.

### 3.4. Error Handling

- **Invalid Input:** If the input text is empty or too long, the service will return a `422 Unprocessable Entity` error.
- **Inference Failure:** If the model fails during prediction, the service will log the error and return a default `500 Internal Server Error` response, which will trigger a standard escalation workflow downstream.

---

## 4. Monitoring & Logging

- **Logging:** Structured JSON logs will include `model_version`, `predicted_intent`, `confidence_score`, and `inference_latency_ms`.
- **Metrics:** Prometheus metrics will track:
  - `predictions_total{intent="schedule_appointment"}`
  - `prediction_latency_seconds`
  - `prediction_confidence` (Histogram)
- **Alerting:** Alerts will be configured for a significant drop in average confidence score, a spike in inference latency, or a high rate of `500` errors.

---

## 5. Future Considerations

- **A/B Testing:** The architecture should eventually support routing a fraction of traffic to a new candidate model to evaluate its performance before a full rollout.
- **Fine-Tuning Pipeline:** An MLOps pipeline needs to be designed for continuous fine-tuning of the models based on agent feedback from escalated tickets.

---

## 6. Changelog

| Version | Date       | Description                    |
| :------ | :--------- | :----------------------------- |
| 1.0     | 2025-11-13 | Initial draft of the document. |
