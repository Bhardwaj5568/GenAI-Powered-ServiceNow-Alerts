# Risks, Assumptions, and Decisions (RAD)

## 1. Assumptions

- ServiceNow can emit required events in near real-time.
- Messaging providers support target geographies and required throughput.
- Recipient contact mappings are maintained and accurate.
- Security teams approve data handling model for channel delivery.
- Pilot teams will provide operational feedback within defined cadence.

## 2. Risk Register

### R-01: Event Quality Issues from Source
- Impact: Incorrect or missing alerts.
- Likelihood: Medium.
- Mitigation: Strong schema validation, replay tools, reconciliation checks.

### R-02: Messaging Provider Instability
- Impact: Delayed or failed delivery.
- Likelihood: Medium.
- Mitigation: Multi-channel fallback, retry policy, provider monitoring.

### R-03: GenAI Hallucination or Low-Quality Summaries
- Impact: Wrong guidance, reduced trust.
- Likelihood: Medium.
- Mitigation: Strict output schema, guardrails, deterministic fallback.

### R-04: Alert Fatigue
- Impact: Reduced acknowledgement and response quality.
- Likelihood: High.
- Mitigation: Dedupe, throttling, recipient tuning, feedback loop.

### R-05: Compliance Violation in Outbound Text
- Impact: Regulatory and reputational risk.
- Likelihood: Low to Medium.
- Mitigation: Redaction pipeline, policy checks, audit sampling.

### R-06: Underestimated Operational Load During Major Incident
- Impact: Backlog and latency spikes.
- Likelihood: Medium.
- Mitigation: Queue buffering, auto-scaling, P1-first prioritization.

### R-07: Incomplete Escalation Contact Data
- Impact: Escalation chain failure.
- Likelihood: Medium.
- Mitigation: Data validation job and admin exception dashboard.

## 3. Decision Log

### D-01: Event-Driven Architecture with Queue Decoupling
- Decision: Adopt asynchronous processing pipeline.
- Rationale: Improves resilience and burst handling.
- Date: 2026-04-23

### D-02: Config-Driven Routing and Escalation
- Decision: Store routing policies externally and version them.
- Rationale: Faster policy updates without code deploy.
- Date: 2026-04-23

### D-03: GenAI with Deterministic Fallback
- Decision: GenAI default, template fallback mandatory.
- Rationale: Preserve reliability and governance.
- Date: 2026-04-23

### D-04: MVP Scope Restriction to P1/P2 and SLA Warnings
- Decision: Narrow scope for pilot.
- Rationale: Faster measurable value and reduced complexity.
- Date: 2026-04-23

## 4. Constraints

- Enterprise procurement timelines for messaging providers.
- WhatsApp template approval turnaround.
- Security review and network whitelisting lead times.
- Budget limits for model usage in early phase.

## 5. Immediate Follow-Up Actions

1. Confirm event contract with ServiceNow team.
2. Finalize provider selection and throughput SLAs.
3. Define recipient mapping ownership process.
4. Approve baseline prompt templates and fallback content.
5. Lock pilot KPIs and reporting cadence.

## 6. Governance Review Cadence

- Weekly risk review in pilot phase.
- Fortnightly decision log review.
- Monthly architecture and compliance checkpoint.