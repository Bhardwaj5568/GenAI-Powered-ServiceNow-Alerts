# ServiceNow Integration Design

## 1. Objective

Define the technical integration pattern between ServiceNow and the alerting platform for low-latency and reliable event delivery.

## 2. Source Tables and Events

Primary tables:
- incident
- task_sla
- change_request (future phase)

Event types:
- incident.created
- incident.priority_changed
- incident.reassigned
- incident.stagnation_detected
- sla.threshold_warning
- sla.breach_imminent

## 3. ServiceNow Configuration Approach

Preferred method:
- Flow Designer + Integration Hub Spoke for maintainability.

Alternative method:
- Business Rules + Scripted REST call for custom logic.

Restricted environment option:
- MID Server relay for outbound network constraints.

## 4. Outbound Event Contract

### 4.1 Payload Schema (example)

{
  "event_id": "uuid",
  "event_type": "sla.threshold_warning",
  "event_timestamp": "2026-04-23T10:15:00Z",
  "source": "servicenow",
  "ticket": {
    "id": "INC123456",
    "sys_id": "abc123",
    "priority": "P1",
    "short_description": "Payment API outage",
    "assignment_group": "NOC-L1",
    "assigned_to": "user.sys_id"
  },
  "sla": {
    "name": "Resolve SLA",
    "remaining_minutes": 18,
    "threshold_percent": 15,
    "breach_time": "2026-04-23T10:33:00Z"
  },
  "change_meta": {
    "previous_priority": "P2",
    "current_priority": "P1"
  }
}

### 4.2 Validation Rules

- event_id must be unique UUID.
- ticket.id must match incident ticket format.
- priority must be in allowed set (P1..P5).
- sla.remaining_minutes must be non-negative integer.
- event_timestamp must be UTC ISO-8601.

## 5. Security Controls

- OAuth2 client credentials or signed JWT for auth.
- Optional mTLS for high-security environments.
- Source IP allow-list (if static egress available).
- Request signature hash validation for tamper resistance.

## 6. Integration Reliability

- ServiceNow outbound retries for transient 5xx responses.
- Receiver returns 202 after durable queue write only.
- Dead-letter handling for repeatedly failing events.
- Reconciliation job to compare ServiceNow event count vs processed count.

## 7. Event Filtering and Noise Reduction at Source

- Send only high-priority events in MVP (P1/P2).
- Trigger on SLA thresholds: 30/15/5 (configurable).
- Optional suppression for duplicate updates within short window.

## 8. Data Enrichment API Calls Back to ServiceNow

Suggested APIs:
- Incident detail by sys_id.
- SLA details for ticket.
- Activity stream summary (bounded depth).

Best practices:
- Use bounded timeouts.
- Cache recent incident context for short interval.
- Avoid heavy synchronous calls during critical burst periods.

## 9. Integration Test Scenarios

- Valid P1 creation event accepted and processed.
- Invalid signature rejected with explicit error code.
- Duplicate event_id ignored by idempotency gate.
- SLA 15% event triggers correct downstream routing.
- ServiceNow outage simulation handled with retries and alerting.

## 10. Integration Readiness Checklist

- ServiceNow flow configured and promoted.
- API credentials provisioned in secret vault.
- Connectivity and cert validation complete.
- Event schema signed off by both teams.
- Replay test suite passed.
- Runbook approved for integration incident response.