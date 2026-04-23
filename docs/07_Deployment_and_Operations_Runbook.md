# Deployment and Operations Runbook

## 1. Purpose

Provide deployment standards, operational procedures, and incident handling steps for reliable production operation.

## 2. Environment Strategy

- Environments: Dev, QA/UAT, Staging, Production.
- Promotion model: Dev -> QA -> Staging -> Prod with approval gates.
- Config isolation per environment using secure parameter store.

## 3. CI/CD Pipeline Requirements

Mandatory stages:
1. Build and static code checks.
2. Unit and integration test execution.
3. Security scan (SAST/dependency/container).
4. Deployment to staging.
5. Smoke test and canary checks.
6. Manual approval for production.

Rollback strategy:
- Blue/green or canary rollback to last stable release.
- Prompt template rollback independent of application rollback.

## 4. Pre-Deployment Checklist

- Secrets provisioned and rotated.
- ServiceNow endpoint/certs verified.
- Messaging provider credentials validated.
- Queue and DB capacity reviewed.
- Alerting dashboards and alarms configured.
- Runbook links and on-call contacts updated.

## 5. Day-1 Production Steps

1. Deploy gateway, processor, orchestrator, connectors.
2. Enable minimal routing policy (pilot groups only).
3. Run synthetic event tests (P1, P2, SLA15).
4. Verify delivery callbacks and acknowledgement capture.
5. Monitor error rate and latency for 2-hour hypercare window.

## 6. Operational Monitoring

### Core SLOs

- Event-to-notification latency SLO.
- Notification delivery success rate SLO.
- Processing success rate SLO.
- Escalation trigger accuracy SLO.

### Alerts to Configure

- Queue backlog > threshold.
- LLM timeout rate > threshold.
- Delivery failure spike by channel/provider.
- API auth failures.
- Database connectivity errors.

## 7. Incident Playbooks

### 7.1 LLM Outage

- Detect timeout/error anomaly.
- Switch policy to deterministic fallback mode.
- Notify platform owners and track impact metrics.

### 7.2 WhatsApp Provider Failure

- Mark provider unavailable.
- Trigger SMS fallback for critical alerts if policy allows.
- Raise vendor incident and continue monitoring.

### 7.3 Event Backlog Growth

- Scale processor replicas.
- Temporarily prioritize P1/P2 processing lanes.
- Investigate dependency slowness.

### 7.4 Duplicate Alert Burst

- Verify idempotency cache health.
- Enable emergency suppression window.
- Run RCA and patch dedupe key logic if needed.

## 8. Routine Operations

Daily:
- Review SLO dashboard and failed deliveries.
- Validate DLQ volume and retry outcomes.

Weekly:
- Review noisy route rules and adjust throttling.
- Analyze AI fallback and quality trends.

Monthly:
- Access review and secret rotation compliance.
- Cost report by channel and model usage.

## 9. Backup and Recovery

- Scheduled backups for config and metadata stores.
- Restore drills at defined frequency.
- RTO/RPO tracked with evidence.

## 10. Operational KPIs

- SLA breach reduction percentage.
- Mean time to acknowledge.
- Escalation rate by priority.
- Delivery success by channel.
- Cost per successful alert.

## 11. Support Model

- L1 Ops: monitor dashboards, run standard playbooks.
- L2 Platform Engineering: resolve integration and scaling issues.
- L3 Architecture/Security: handle complex incidents and governance exceptions.