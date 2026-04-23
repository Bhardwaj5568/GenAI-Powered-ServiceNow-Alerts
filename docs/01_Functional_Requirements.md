# Functional Requirements Specification (FRS)

## 1. Purpose

Define detailed functional behavior for the GenAI-Powered ServiceNow Alerts MVP that sends intelligent, actionable alerts over WhatsApp and SMS to reduce SLA breaches and improve incident response time.

## 2. Product Scope

The system shall:
- Ingest near real-time ServiceNow events (incident and SLA related).
- Enrich and evaluate event context.
- Generate concise AI-assisted operational messages.
- Route and deliver notifications via WhatsApp and SMS.
- Track acknowledgements and drive escalation workflows.

Out of initial MVP scope:
- Full bidirectional chatbot conversations.
- Auto-remediation execution from alert channel.
- Deep CMDB dependency graph analysis (planned in phase 2).

## 3. User Roles

- L1 Support Engineer: Receives and acknowledges alerts.
- L2/Resolver Group Member: Receives escalated alerts for critical events.
- Incident Manager: Receives manager-level escalations.
- Service Owner: Receives reporting and SLA trend visibility.
- Platform Admin: Configures routing policies and templates.
- Security/Audit Reviewer: Reviews logs, access, and compliance artifacts.

## 4. Functional Requirements

### 4.1 Event Ingestion

- FR-001: System shall receive ServiceNow incident creation events for P1 and P2 priorities.
- FR-002: System shall receive SLA threshold warning events at configurable percentages (default: 30%, 15%, 5%).
- FR-003: System shall receive incident priority change events.
- FR-004: System shall receive assignment-group change and stagnation events.
- FR-005: System shall validate payload schema and reject malformed requests with reason codes.
- FR-006: System shall support webhook authentication using OAuth2/JWT and optional mTLS.

### 4.2 Data Enrichment

- FR-007: System shall enrich events with latest incident details from ServiceNow.
- FR-008: System shall attach on-call roster data (source configurable: schedule API, static mapping, or paging platform).
- FR-009: System shall derive breach risk score using SLA remaining time, priority, reassignment count, and historical handling patterns.
- FR-010: System shall classify business impact level (High/Medium/Low) using configurable rules.

### 4.3 GenAI Message Generation

- FR-011: System shall generate concise alert summaries with maximum 3 lines by default.
- FR-012: Each generated message shall include incident ID, priority, SLA remaining time, impact summary, and recommended next action.
- FR-013: Tone and urgency shall vary by priority and breach risk.
- FR-014: System shall apply prompt templates by event type.
- FR-015: If GenAI service is unavailable or fails policy checks, system shall send deterministic fallback template.
- FR-016: System shall maintain prompt/response metadata for audit (without storing sensitive free text beyond policy constraints).

### 4.4 Notification Orchestration

- FR-017: Routing shall be policy-driven and configurable without code deployment.
- FR-018: P1 events shall trigger WhatsApp and SMS simultaneously.
- FR-019: P2 events shall trigger WhatsApp by default; SMS optional by policy.
- FR-020: Events below SLA threshold (default <15%) shall trigger repeated escalation notifications at configured intervals.
- FR-021: If no acknowledgement within configured time window, system shall escalate to manager and/or L2.
- FR-022: System shall suppress duplicate alerts using idempotency keys and time-window correlation.
- FR-023: System shall throttle repeated non-critical alerts to reduce fatigue.

### 4.5 Delivery and Tracking

- FR-024: System shall support WhatsApp delivery using approved WhatsApp Business API provider.
- FR-025: System shall support SMS delivery using enterprise SMS gateway.
- FR-026: System shall track per-channel status: queued, sent, delivered, failed, read (if supported).
- FR-027: System shall capture acknowledgement events from users through supported mechanisms (link click, quick reply, or integrated callback).
- FR-028: System shall update notification history and escalation status in central datastore.

### 4.6 Admin and Configuration

- FR-029: Admin users shall configure routing rules by priority, assignment group, region, and support schedule.
- FR-030: Admin users shall configure business-hours and after-hours policies.
- FR-031: Admin users shall configure channel preference and fallback order.
- FR-032: Admin users shall configure throttle windows and duplicate suppression thresholds.
- FR-033: All policy changes shall be versioned with user, timestamp, and change reason.

### 4.7 Reporting and Observability

- FR-034: System shall provide dashboard metrics for alert volume, delivery success, acknowledgement latency, and escalation count.
- FR-035: System shall correlate alerts with SLA outcomes (breached vs saved).
- FR-036: System shall provide searchable logs by incident ID, ticket state, and recipient.
- FR-037: System shall expose operational health endpoints for integration services.

## 5. Functional Use Cases

### UC-01: P1 Incident Immediate Alert
1. ServiceNow emits P1 incident creation event.
2. Integration layer validates and enriches event.
3. GenAI summarizes and recommends action.
4. Orchestrator sends WhatsApp + SMS to L1 and on-call.
5. No acknowledgement for N minutes triggers manager escalation.

### UC-02: SLA 15% Remaining Escalation
1. SLA warning event arrives with 15% time remaining.
2. Risk score calculated as High.
3. AI message emphasizes urgency and next best step.
4. Repeat notifications sent according to policy until acknowledgement or ticket state changes.

### UC-03: GenAI Fallback Mode
1. LLM call times out or policy validator rejects generated output.
2. Orchestrator switches to approved static template.
3. Alert still delivered within SLA for notification latency.
4. Failure metrics logged for model reliability tracking.

## 6. Data Contract (Functional View)

Mandatory event fields:
- event_id
- event_type
- source_table
- ticket_id
- priority
- assignment_group
- sla_remaining_minutes
- event_timestamp

Mandatory notification fields:
- notification_id
- recipient_id
- channel
- message_text
- status
- correlation_id

## 7. Business Rules

- BR-01: P1 notifications are always dual-channel unless recipient explicitly opted out from one channel.
- BR-02: Security policy may redact free-form incident descriptions before GenAI processing.
- BR-03: Any ticket in Resolved/Closed state blocks new notifications except closure summary.
- BR-04: Escalation chain is regional and schedule-aware.
- BR-05: Reassignment resets no-response timer when owner changes.

## 8. Acceptance Criteria (Functional)

- AC-01: P1 event to first outbound notification <= 10 seconds in normal operating conditions.
- AC-02: 99% of valid events are processed without manual intervention.
- AC-03: AI summary includes all required fields in >= 98% of generated messages.
- AC-04: Duplicate suppression prevents repeated identical alert bursts within configured window.
- AC-05: Escalation triggers accurately when acknowledgement SLA is missed.

## 9. Open Functional Dependencies

- On-call source integration decision (ServiceNow schedule vs external system).
- WhatsApp template approval lead time.
- Acknowledgement mechanism standardization across channels.
- Final multilingual support requirements for message generation.