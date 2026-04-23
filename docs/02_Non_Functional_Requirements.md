# Non-Functional Requirements (NFR)

## 1. Performance

- NFR-PERF-01: End-to-end latency (event ingestion to first notification dispatch) should be <= 10 seconds for P1/P2 under nominal load.
- NFR-PERF-02: 95th percentile processing latency should be <= 15 seconds.
- NFR-PERF-03: System should support burst ingestion of at least 200 events/minute during major incidents.
- NFR-PERF-04: Notification dispatch throughput should scale to at least 1000 messages/minute across channels.

## 2. Availability and Reliability

- NFR-REL-01: Target platform availability for MVP >= 99.5% monthly.
- NFR-REL-02: Failed event processing shall be retried with exponential backoff and dead-letter queue support.
- NFR-REL-03: Critical processing components shall be stateless and horizontally scalable.
- NFR-REL-04: At-least-once event handling with idempotency controls is required.

## 3. Security

- NFR-SEC-01: All in-transit communication shall use TLS 1.2+.
- NFR-SEC-02: API endpoints shall enforce OAuth2/JWT authentication and authorization.
- NFR-SEC-03: Sensitive payload fields shall be masked or tokenized before storage.
- NFR-SEC-04: Secrets shall be stored only in managed secret vault services.
- NFR-SEC-05: RBAC shall govern admin operations and policy modifications.

## 4. Privacy and Compliance

- NFR-PRV-01: PHI/PII should not be included in channel messages unless explicitly approved by policy.
- NFR-PRV-02: Data retention should follow enterprise policy (default recommendation: logs 90 days hot + 1 year archive).
- NFR-PRV-03: Audit logs must capture config changes, access events, and escalation actions.
- NFR-PRV-04: Model interaction records shall retain traceability metadata without exposing restricted content.

## 5. Maintainability and Operability

- NFR-MNT-01: Service components should be independently deployable.
- NFR-MNT-02: All services shall expose structured logs and metrics endpoints.
- NFR-MNT-03: Config-driven routing and templates shall avoid code changes for common policy updates.
- NFR-MNT-04: CI/CD should include linting, unit tests, integration tests, and policy validation checks.

## 6. Observability

- NFR-OBS-01: Metrics required: event throughput, processing latency, notification success rate, acknowledgement rate, escalation rate, model fallback rate.
- NFR-OBS-02: Distributed tracing shall correlate event_id to message_id across layers.
- NFR-OBS-03: Alerting shall be configured for queue depth, API failure, delivery failure spikes, and GenAI timeout anomalies.

## 7. Scalability

- NFR-SCL-01: Architecture shall support regional scaling (multi-geo recipients and policies).
- NFR-SCL-02: Message queue partitioning shall enable workload distribution by event type or assignment group.
- NFR-SCL-03: LLM calls shall be bounded by concurrency controls and budget guardrails.

## 8. Resilience and Disaster Recovery

- NFR-DR-01: Recovery Time Objective (RTO) <= 2 hours for core alerting service.
- NFR-DR-02: Recovery Point Objective (RPO) <= 15 minutes for alert metadata.
- NFR-DR-03: Fallback static templates shall maintain alert continuity during model downtime.

## 9. Usability

- NFR-UX-01: Alert message readability target: concise, actionable, <= 3 lines by default.
- NFR-UX-02: Language and tone should be role-appropriate and priority-aware.
- NFR-UX-03: Notification noise should be reduced through suppression and frequency controls.

## 10. Cost and Governance Constraints

- NFR-CST-01: Per-alert unit cost should be measurable by channel and model usage.
- NFR-CST-02: Configurable monthly budget thresholds shall trigger warning alerts for overspend.
- NFR-CST-03: Message channel fallback order should optimize reliability vs cost.

## 11. NFR Validation Criteria

- Load test and soak test reports with P95/P99 latency evidence.
- Failover drill evidence for queue and service recovery.
- Security test evidence: vulnerability scan and authz enforcement checks.
- Audit evidence for policy changes and message traceability.
- Model quality and fallback rate reporting under controlled incident replay.