# Technical Design Document (TDD)

## 1. Objective

Design a production-ready, event-driven platform that converts ServiceNow incident/SLA signals into intelligent, low-latency alerts over WhatsApp and SMS.

## 2. Logical Architecture

### 2.1 Layers

1. Source Layer: ServiceNow incidents, task_sla, change signals.
2. Ingestion Layer: API Gateway + Auth + Request Validator.
3. Processing Layer: Event processor, enricher, risk scorer.
4. Intelligence Layer: Prompt builder, LLM adapter, response guardrails.
5. Orchestration Layer: Policy engine, dedupe, throttling, escalation scheduler.
6. Delivery Layer: WhatsApp connector + SMS connector.
7. Control and Insight Layer: Config service, audit, logs, metrics, dashboards.

### 2.2 Recommended MVP Runtime Stack

- API and workers: Containerized microservices or serverless functions.
- Queue: Managed message queue (for retry, ordering strategy, decoupling).
- Datastore: Relational store for policy/config + NoSQL or key-value for high-speed state.
- Cache: Redis for idempotency keys and short-lived suppression windows.
- Observability: OpenTelemetry + centralized logging + metrics backend.

## 3. Component Design

### 3.1 API Gateway / Webhook Receiver

Responsibilities:
- Validate signature/token and schema.
- Add correlation metadata (correlation_id, received_timestamp).
- Push accepted events to queue.

API Endpoint (example):
- POST /api/v1/events/servicenow

HTTP behavior:
- 202 Accepted for valid event reception.
- 4xx for auth/schema failures with error code.
- 5xx only for non-recoverable internal issues.

### 3.2 Event Processor Service

Responsibilities:
- Parse event_type and route to handler.
- Fetch enrichment details from ServiceNow and on-call source.
- Compute risk score.
- Produce normalized context object.

Pseudo flow:
1. Receive queue message.
2. Validate idempotency key.
3. Load incident details.
4. Calculate risk and impact tags.
5. Store processing state.
6. Trigger message generation pipeline.

### 3.3 GenAI Adapter Service

Responsibilities:
- Build prompt from policy template + context.
- Call approved LLM endpoint with latency budget.
- Run output validation and content guardrails.
- Return approved summary or fallback template.

Controls:
- Timeout budget (example 2.5 sec).
- Token limits.
- Prompt and output schema enforcement.
- PII/PHI redaction pass before and after model call.

### 3.4 Notification Orchestrator

Responsibilities:
- Resolve recipients by policy.
- Evaluate channel matrix (priority, business hours, on-call, region).
- Apply dedupe and throttle policies.
- Schedule follow-up escalations on no-ack.

Core engines:
- Rule evaluator.
- Escalation scheduler.
- Delivery status monitor.

### 3.5 Channel Connectors

WhatsApp Connector:
- Template mapping, provider auth, delivery callbacks.

SMS Connector:
- Gateway API integration, rate-limit controls, delivery status polling/callback.

Both connectors must:
- Return standardized delivery status model.
- Support retry for transient failures.
- Surface hard failures for escalation and audit.

## 4. Data Model (Suggested)

### 4.1 event_log
- event_id (PK)
- correlation_id
- event_type
- ticket_id
- priority
- payload_hash
- received_at
- processed_at
- status

### 4.2 incident_context
- context_id (PK)
- ticket_id
- assignment_group
- business_impact
- sla_remaining_minutes
- breach_risk_score
- enriched_at

### 4.3 notification_log
- notification_id (PK)
- correlation_id
- recipient_id
- channel
- message_version
- send_status
- delivery_status
- acknowledged_at
- escalated_flag

### 4.4 policy_config
- policy_id (PK)
- rule_type
- rule_expression
- channel_matrix
- retry_policy
- escalation_policy
- effective_from
- effective_to
- version

### 4.5 ai_trace
- trace_id (PK)
- correlation_id
- prompt_template_version
- model_name
- latency_ms
- validation_status
- fallback_used

## 5. Sequence (Textual)

1. ServiceNow sends incident or SLA event.
2. Gateway authenticates and validates payload.
3. Event queued with correlation metadata.
4. Processor enriches context and computes risk.
5. GenAI adapter returns compliant summary.
6. Orchestrator evaluates routing and channel strategy.
7. Connectors dispatch messages.
8. Delivery callbacks update status.
9. No-ack timer triggers escalation when required.
10. Metrics and audit records finalized.

## 6. Error Handling Strategy

- Validation failure: reject event, log reason, optional source callback.
- Enrichment dependency failure: retry with bounded attempts, then park in DLQ.
- LLM failure: fallback message template.
- Channel failure: channel retry and alternative channel fallback where policy permits.
- Persistent failure: create operational alert to platform support queue.

## 7. Deployment Topology (MVP)

- Single region primary deployment.
- Managed queue and managed database.
- Stateless services behind load balancer.
- Secure outbound egress to ServiceNow and messaging providers.

## 8. Configuration Strategy

Externalized configuration:
- Routing matrix by priority and assignment group.
- Escalation timeouts and depth.
- Business hours calendars.
- Prompt template versions and channel text constraints.

Change control:
- Versioned config with approval workflow for production updates.

## 9. Technical Debt Guardrails for MVP

Allowed:
- Simplified risk model (rule-based scoring).
- Single LLM provider.

Not allowed:
- Hardcoded routing in source code.
- Missing idempotency controls.
- Unencrypted storage of credentials.

## 10. Definition of Done (Technical)

- End-to-end flow tested with replayed ServiceNow sample events.
- All core APIs documented in OpenAPI.
- Minimum runbook and on-call procedure published.
- SLO dashboards available.
- Security review sign-off for external integrations.