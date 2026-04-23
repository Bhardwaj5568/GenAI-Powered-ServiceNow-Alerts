# Solution Architecture Diagram

This diagram represents the end-to-end architecture for the GenAI-Powered ServiceNow Alerts platform.

## 1. High-Level Architecture

```mermaid
flowchart LR
    subgraph SN[ServiceNow Source Layer]
        A1[incident table]
        A2[task_sla table]
        A3[Business Rules / Flow Designer]
        A1 --> A3
        A2 --> A3
    end

    subgraph ING[Ingestion and Integration Layer]
        B1[API Gateway]
        B2[Auth: OAuth2/JWT + optional mTLS]
        B3[Schema Validator]
        B4[Event Queue]
        B1 --> B2 --> B3 --> B4
    end

    subgraph PROC[Processing and Enrichment Layer]
        C1[Event Processor]
        C2[Context Enricher]
        C3[Risk Scoring Engine]
        C4[Idempotency and Dedupe]
        B4 --> C1 --> C2 --> C3 --> C4
    end

    subgraph AI[GenAI Intelligence Layer]
        D1[Prompt Builder]
        D2[LLM Adapter]
        D3[Policy and Output Guardrails]
        D4[Fallback Template Engine]
        C4 --> D1 --> D2 --> D3
        D3 -->|pass| E1
        D3 -->|fail/timeout| D4 --> E1
    end

    subgraph ORCH[Notification Orchestration Layer]
        E1[Routing Policy Engine]
        E2[Escalation Scheduler]
        E3[Throttle and Fatigue Control]
        E4[Recipient Resolver: L1/L2/Manager]
        E1 --> E2 --> E3 --> E4
    end

    subgraph DEL[Delivery Layer]
        F1[WhatsApp Business API Connector]
        F2[Enterprise SMS Gateway Connector]
        F3[Delivery Callback Handler]
        E4 --> F1
        E4 --> F2
        F1 --> F3
        F2 --> F3
    end

    subgraph OBS[Monitoring, Security, Governance]
        G1[Centralized Logs and Audit Trail]
        G2[Metrics and Dashboards]
        G3[Alerting and SLO Monitoring]
        G4[Secrets Vault and KMS]
        G5[RBAC and Config Versioning]
    end

    C1 --> G1
    C3 --> G2
    D2 --> G2
    F3 --> G2
    F3 --> G3
    B1 --> G4
    E1 --> G5

    subgraph USERS[Recipients]
        H1[L1 Engineer]
        H2[L2 Support]
        H3[Incident Manager]
    end

    F1 --> H1
    F1 --> H2
    F2 --> H1
    F2 --> H3
```

## 2. Data and Control Notes

- Event flow is asynchronous after gateway validation to improve resilience.
- AI generation is guarded by policy checks with deterministic fallback.
- Routing and escalation are config-driven to avoid code redeploys for policy updates.
- Delivery status callbacks feed observability and escalation decisions.
- Security controls apply across all layers with encryption, RBAC, and auditability.

## 3. Optional Deployment View (MVP)

```mermaid
flowchart TB
    N1[ServiceNow] --> N2[Public API Gateway]
    N2 --> N3[Queue]
    N3 --> N4[Processor Service]
    N4 --> N5[GenAI Service]
    N5 --> N6[Orchestrator Service]
    N6 --> N7[WhatsApp Provider]
    N6 --> N8[SMS Provider]

    N4 --> N9[(Operational DB)]
    N6 --> N9
    N5 --> N9

    N2 --> N10[(Secrets Vault)]
    N4 --> N10
    N5 --> N10

    N4 --> N11[Monitoring Stack]
    N5 --> N11
    N6 --> N11
    N7 --> N11
    N8 --> N11
```

## 4. Suggested Use in Design Reviews

- Use section 1 in business and architecture walkthroughs.
- Use section 3 in infra and DevOps planning sessions.
- Pair this with the integration contract in `04_Integration_Design_ServiceNow.md` and technical details in `03_Technical_Design.md`.
