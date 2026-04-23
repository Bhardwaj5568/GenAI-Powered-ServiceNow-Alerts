# PHASE 0: Project Kickoff and Discovery
**Duration:** 3-4 Days (Week 1)  
**Objective:** Align stakeholders, finalize scope, and establish foundational decisions  
**Owner:** Solution Architect + Product Owner  

---

## 1. Overview and Goals

### 1.1 Phase Purpose
Phase 0 is the **discovery and alignment phase** where we:
- Define clear project scope and objectives
- Align all stakeholders on requirements and success metrics
- Make critical technical and architectural decisions
- Establish governance and team structure
- Identify risks and mitigation strategies

### 1.2 Success Criteria (Exit Gate)
All of the following must be **signed-off** before proceeding to Phase 1:

```
✅ SCOPE SIGN-OFF
   ├─ Functional requirements finalized
   ├─ Out-of-scope items clearly defined
   ├─ MVP pilot scope locked
   └─ Success metrics agreed by all stakeholders

✅ ARCHITECTURE APPROVAL
   ├─ Technical stack selected and approved
   ├─ Integration approach defined
   ├─ Security architecture reviewed
   └─ Data flow diagram approved

✅ SECURITY & COMPLIANCE BASELINE
   ├─ Threat model completed
   ├─ Compliance requirements identified
   ├─ Security controls mapped
   └─ Privacy impact assessment done

✅ RESOURCE & TEAM COMMITMENT
   ├─ Team members assigned and committed
   ├─ Budget approved
   ├─ Timeline agreed
   └─ Governance model defined

✅ DEPENDENCIES IDENTIFIED
   ├─ External system dependencies mapped
   ├─ ServiceNow readiness assessed
   ├─ On-call source decided
   └─ LLM provider selected
```

---

## 2. Stakeholder Identification and Alignment

### 2.1 Key Stakeholders

| Role | Name | Organization | Responsibilities |
|------|------|--------------|------------------|
| Executive Sponsor | TBD | [Org] | Budget approval, escalation |
| Product Owner | TBD | IT/Ops | Requirements, prioritization |
| Architecture Lead | TBD | IT/Ops | Technical design decisions |
| ServiceNow Admin | TBD | IT/Ops | ServiceNow configuration |
| InfoSec Lead | TBD | Security | Security requirements, audit |
| Operations Manager | TBD | IT/Ops | Runbooks, SLA ownership |
| L1/L2 Support Lead | TBD | Support | User requirements, adoption |
| Budget Owner | TBD | Finance | Cost management |

### 2.2 Stakeholder Engagement Plan

| Stakeholder | Meeting Frequency | Topics | Approval Gate |
|-------------|------------------|--------|---------------|
| Executive Sponsor | Weekly | Budget, timeline, risks | Final sign-off |
| Product Owner | Daily | Requirements, scope changes | Requirements approved |
| Architecture Lead | Daily | Technical decisions | Architecture approved |
| Security Lead | 2x Week | Security requirements | Security baseline approved |
| Operations | 2x Week | Runbooks, SLA, monitoring | Ops readiness |

### 2.3 Kickoff Meeting Agenda (Day 1, 2 hours)

```
1. Project Overview & Vision (15 min)
   └─ Why: Business impact, SLA improvement
   
2. Scope & Objectives (20 min)
   └─ What: P1/P2 incidents, SLA warnings
   └─ Out-of-scope: Change/Problem events
   
3. Timeline & Milestones (15 min)
   └─ 8 weeks total: Phase 0→1→2→3
   └─ Key dates: Go-live, pilot end, scale decision
   
4. Team Structure & Roles (15 min)
   └─ Team composition (8-10 people)
   └─ On-call rotation
   └─ Governance
   
5. Success Metrics (15 min)
   └─ SLA breach reduction (target: 5-15%)
   └─ MTTA improvement
   └─ Cost per alert
   
6. Q&A and Confirmation (10 min)
```

---

## 3. Scope Finalization (Day 1)

### 3.1 Functional Scope - FINAL

#### IN SCOPE (MVP)
```
EVENT TYPES:
✅ Incident creation (P1/P2 only)
✅ Incident priority changes
✅ SLA threshold warnings (30%, 15%, 5% remaining)
✅ Assignment group changes
✅ No-activity/stagnation detection (optional, depends on ServiceNow capability)

CHANNELS:
✅ WhatsApp (primary)
✅ SMS (fallback/secondary)
✅ Optional: Email for escalation summaries

FEATURES:
✅ GenAI-powered message generation
✅ Policy-driven routing
✅ Dual-channel P1 delivery
✅ Escalation workflows (no-ack after N minutes)
✅ Acknowledgement tracking
✅ Duplicate suppression
✅ Alert throttling (fatigue reduction)
✅ Admin policy configuration portal (basic)
✅ Dashboard (basic KPIs)
✅ Audit logging
✅ Fallback templates (when AI fails)
```

#### OUT OF SCOPE (Future Phases)
```
❌ Bidirectional chatbot conversations (Phase 3)
❌ Auto-remediation execution from alert channel (Phase 3)
❌ Deep CMDB dependency analysis (Phase 3)
❌ Change request events (Phase 2)
❌ Problem events (Phase 2)
❌ Advanced voice/MS Teams channels (Phase 3)
❌ Multi-language support (Phase 3)
❌ Mobile app with push notifications (Phase 3)
❌ Custom incident resolution recommendations (Phase 3)
❌ Predictive analytics (Phase 3+)
```

### 3.2 User Stories and Acceptance Criteria

#### US-001: L1 Support Engineer Receives P1 Alert

```gherkin
GIVEN an incident with priority P1 is created in ServiceNow
WHEN the event is received by the alerting system
THEN the L1 support engineer should receive:
  - WhatsApp notification within 10 seconds
  - SMS notification simultaneously
  - Message containing: ticket ID, impact summary, SLA time, recommended action
  - One-click acknowledgement option

ACCEPTANCE CRITERIA:
✅ Alert delivered within 10 seconds
✅ Message is ≤ 3 lines (mobile-friendly)
✅ All required fields present
✅ Acknowledgement updates ServiceNow
✅ No duplicates within 5-minute window
```

#### US-002: Manager Gets Escalation After No Ack

```gherkin
GIVEN an L1 engineer receives a P1 alert
WHEN no acknowledgement received after 5 minutes
THEN the manager should receive:
  - WhatsApp message flagged as ESCALATION
  - Message includes L1 name and alert timestamp
  - Recommended escalation path

ACCEPTANCE CRITERIA:
✅ Escalation triggered at correct time
✅ Escalation message distinguishable from original
✅ Historical context visible to manager
✅ Escalation count tracked
```

#### US-003: SLA 15% Warning Generates Urgent Alert

```gherkin
GIVEN an incident is at 15% SLA remaining
WHEN SLA warning event is received
THEN the alert should:
  - Emphasize urgency (HIGH RISK / CRITICAL tone)
  - Show exact time remaining
  - Suggest next best action
  - Repeat at 5% if not acknowledged

ACCEPTANCE CRITERIA:
✅ Repeated notifications sent (configurable interval)
✅ Message tone is appropriately urgent
✅ No false urgency if already resolved
✅ Escalation tracked separately
```

### 3.3 Out-of-Scope Justification

| Item | Reason | Deferred to |
|------|--------|-------------|
| Chatbot | Requires NLP training, complex UX | Phase 3 Post-Pilot |
| Auto-Remediation | Requires deep security review, change management | Phase 3+ |
| CMDB Analysis | Requires complex graph queries, performance impact | Phase 2 Post-Pilot |
| Voice Channel | Not priority for MVP, limited audience | Phase 3 |
| Multi-Language | Not required for pilot, single language OK | Phase 3 |

---

## 4. Success Metrics Definition (Day 1)

### 4.1 Primary Success Metrics (SLA Impact)

| Metric | Baseline | Target (30-day pilot) | Measurement |
|--------|----------|----------------------|-------------|
| **SLA Breach Rate** | Current% | ↓ 5-15% | ServiceNow Reports |
| **Mean Time to Acknowledge (MTTA)** | X minutes | ↓ 50% | Alert log data |
| **Alert Acknowledgement Rate** | % | ≥ 80% | Notification log |
| **Escalation Accuracy** | NA | ≥ 95% (true escalations) | Manual audit |
| **False Alert Rate** | NA | < 5% | User feedback |

### 4.2 Operational Success Metrics

| Metric | Target |
|--------|--------|
| Platform Availability | ≥ 99.5% |
| P1 Event to Alert Latency | ≤ 10 seconds |
| Message Delivery Success Rate | ≥ 99% |
| GenAI Fallback Rate | < 5% |
| Cost per Alert | < $0.10 |

### 4.3 Business Success Metrics

| Metric | Target |
|--------|--------|
| User Adoption Rate | ≥ 75% of pilot group |
| User Satisfaction (NPS) | ≥ 7/10 |
| Support Ticket Reduction | ≥ 10% |
| Pilot Group Expansion Approval | Yes (go-live decision) |

---

## 5. Technical Stack Selection (Day 2)

### 5.1 Backend & API

```
SELECTED: Python 3.11 + FastAPI
├─ Why: Rapid development, excellent async support, AI/ML ecosystem
├─ Deployment: Container (Docker) + Kubernetes
├─ Framework: FastAPI (modern, async, auto-documentation)
├─ ASGI Server: Uvicorn (production-grade)
└─ Monitoring: Built-in OpenTelemetry support

ALTERNATIVES CONSIDERED:
├─ Node.js + NestJS (good, but less AI tooling)
├─ Go (excellent performance, but longer dev time)
└─ DECISION: FastAPI wins on time-to-MVP + AI ecosystem
```

### 5.2 Data Storage

```
PRIMARY: PostgreSQL 15
├─ Use Case: Policy config, audit logs, relational data
├─ Hosting: AWS RDS or Self-managed
├─ Replication: 2 replicas for HA
└─ Backup: Daily, 30-day retention

SECONDARY: Redis 7
├─ Use Case: Caching, idempotency keys, session state
├─ TTL: Auto-expiration for idempotency (5 min)
└─ Backup: Every 6 hours

OPTIONAL: MongoDB
├─ Use Case: Event archive (unstructured event history)
├─ Decision: TBD based on Phase 1 findings
└─ Deferred to Phase 1 if needed

DECISION: PostgreSQL + Redis (sufficient for MVP)
```

### 5.3 Message Queue

```
SELECTED: Celery + Redis
├─ Why: Proven, simple, event-driven
├─ Processing: Distributed workers (min 3 replicas)
├─ Failure Handling: Dead-letter queue + retry logic
└─ Monitoring: Flower (Celery monitoring tool)

ALTERNATIVES:
├─ RabbitMQ (robust, but more complex)
├─ Apache Kafka (overkill for MVP)
├─ AWS SQS (cloud-locked, less local control)
└─ DECISION: Celery + Redis for MVP simplicity
```

### 5.4 LLM & AI Integration

```
SELECTED: OpenAI GPT-4
├─ API: Official OpenAI API
├─ Model: gpt-4-turbo (fastest, cost-effective)
├─ Fallback: gpt-3.5-turbo if rate-limited
├─ Library: LangChain for prompt management
└─ Timeout: 2.5 seconds max per call

ALTERNATIVES:
├─ Azure OpenAI (if cloud-locked to Azure)
├─ Anthropic Claude (similar capability, less adoption)
├─ Local Llama 2 (requires GPU, more operational overhead)
└─ DECISION: OpenAI for MVP (proven, scalable)

COST ESTIMATE:
├─ Prompt: ~0.01¢ per call (3.5-turbo)
├─ Response: ~0.03¢ per 1k tokens
├─ Monthly budget (10K calls/day): ~$100
└─ Alert with AI: ~$0.0004 per message
```

### 5.5 Messaging Channels

```
WHATSAPP: Official WhatsApp Business API
├─ Provider: Meta / AWS / Twilio
├─ Cost: $0.0079 per message (volume-based discount)
├─ Template Approval: Lead time ~48 hours
└─ Capacity: 1000 msg/min easily scalable

SMS: AWS SNS or Twilio
├─ Provider: AWS SNS preferred (infrastructure integrated)
├─ Cost: $0.0075 per message
├─ Delivery: 100-200ms average
└─ Capacity: 200 msg/sec easily scalable

EMAIL: AWS SES (optional, Phase 1)
├─ Use Case: Manager summaries, escalation summaries
├─ Cost: Minimal (included in AWS)
└─ Deferred to Phase 2
```

### 5.6 Infrastructure & Deployment

```
ENVIRONMENT: Cloud (AWS recommended) or On-Premises Docker
├─ Primary: AWS ECS / EKS for managed Kubernetes
├─ Alternative: Docker Compose + EC2 for cost savings
├─ Database: AWS RDS PostgreSQL
├─ Cache: AWS ElastiCache Redis
├─ Secrets: AWS Secrets Manager
├─ Monitoring: Prometheus + Grafana (open-source)
└─ Logging: ELK Stack (open-source)

DOCKER CONTAINERIZATION:
├─ Base Image: python:3.11-slim
├─ Container Registry: AWS ECR
├─ Registry Scanning: Enabled for vulnerabilities
└─ Image Signing: Optional (Phase 2+)

KUBERNETES CONFIG:
├─ Cluster: EKS or self-managed
├─ Replicas: Minimum 3 for HA
├─ Resource Limits: CPU 500m, Memory 512Mi per pod
├─ Auto-scaling: HPA based on queue depth
└─ Service Mesh: Optional (Phase 3+)
```

### 5.7 Summary Table: Technology Stack

| Layer | Technology | Rationale |
|-------|-----------|-----------|
| **Language** | Python 3.11 | AI/ML ecosystem, rapid development |
| **Framework** | FastAPI | Modern, async, auto-docs |
| **Runtime** | Docker + K8s | Scalable, portable, industry-standard |
| **Database** | PostgreSQL + Redis | Proven, simple, sufficient |
| **Queue** | Celery + Redis | Event-driven, distributed |
| **LLM** | OpenAI GPT-4 | Best quality, proven, scalable |
| **Messaging** | WhatsApp + SMS | User-preferred channels |
| **Monitoring** | Prometheus + Grafana | Open-source, no vendor lock-in |
| **Logging** | ELK Stack | Centralized, full-text search |
| **Tracing** | Jaeger | OpenTelemetry native |

---

## 6. ServiceNow Integration Approach (Day 2)

### 6.1 Event Source Configuration

```
METHOD 1 (PREFERRED): ServiceNow Flow Designer
├─ Pros: Native UI, maintainable, audit trail
├─ Cons: Requires Flow Designer license
├─ Event Types: incident.created, incident.priority_changed, sla.threshold_warning
├─ Deployment: Production ServiceNow instance
└─ Timeline: 2 days configuration + testing

METHOD 2 (ALTERNATIVE): Business Rule + REST API
├─ Pros: Simpler, less licensing
├─ Cons: Code-based, harder to maintain
├─ Use if: Flow Designer not available
└─ Timeline: 1 day configuration

METHOD 3 (FALLBACK): MID Server
├─ Use if: Firewalls prevent direct outbound
├─ Complexity: Higher operational overhead
└─ Deferred unless required
```

### 6.2 Event Schema (Webhook Payload)

```json
{
  "event_id": "550e8400-e29b-41d4-a716-446655440000",
  "event_type": "incident.created|incident.priority_changed|sla.threshold_warning",
  "event_timestamp": "2026-04-23T10:15:00Z",
  "source": "servicenow",
  "correlation_id": "CORR-2026-04-23-001",
  
  "ticket": {
    "id": "INC123456",
    "sys_id": "abc123xyz789",
    "short_description": "Payment API outage affecting checkout",
    "description": "Customers reporting checkout failures",
    "priority": "P1",
    "assignment_group": "NOC-L1",
    "assigned_to": {
      "name": "John Doe",
      "email": "john.doe@company.com",
      "sys_id": "user123"
    },
    "created_on": "2026-04-23T10:00:00Z",
    "updated_on": "2026-04-23T10:15:00Z",
    "state": "new",
    "impact": "1 (High)",
    "urgency": "1 (High)"
  },
  
  "sla": {
    "name": "Resolve SLA",
    "remaining_minutes": 120,
    "total_minutes": 240,
    "threshold_percent": 15,
    "breach_time": "2026-04-23T12:15:00Z",
    "status": "active"
  },
  
  "change_meta": {
    "previous_priority": "P2",
    "current_priority": "P1",
    "changed_by": "Incident Bot",
    "change_timestamp": "2026-04-23T10:15:00Z"
  }
}
```

### 6.3 Webhook Endpoint Security

```
AUTHENTICATION: OAuth2 Client Credentials
├─ ServiceNow Outbound Auth: OAuth2 bearer token
├─ System: Generate credentials in ServiceNow
├─ Rotation: Every 90 days
└─ Alternative: Signed JWT tokens

SIGNATURE VERIFICATION:
├─ Method: HMAC-SHA256 signature in request headers
├─ Shared Secret: Stored in AWS Secrets Manager
├─ Header: X-ServiceNow-Signature
└─ Verification: On every request

TLS/SSL:
├─ Minimum: TLS 1.2
├─ Certificate: Valid, non-self-signed
├─ mTLS: Optional (Phase 2+ if required)
└─ Validation: Certificate pinning (Phase 2+)

RATE LIMITING:
├─ Limit: 1000 requests/minute per API key
├─ Burst: 100 requests/10 seconds
├─ Backoff: Exponential retry (Phase 1)
└─ Alert: If rate limits approached
```

### 6.4 On-Call Data Source Decision

| Source | Pros | Cons | Decision |
|--------|------|------|----------|
| **ServiceNow Schedule API** | Native, built-in | Requires configuration | ✅ PRIMARY |
| **PagerDuty Integration** | Accurate, real-time | External dependency | 🔄 SECONDARY |
| **Opsgenie Integration** | Similar to PagerDuty | External dependency | 🔄 FALLBACK |
| **Static CSV** | Simple, offline | Manual updates | ❌ MVP ONLY |

**Decision:** ServiceNow Schedule API (primary), with fallback to static configuration

---

## 7. Security & Compliance Baseline (Day 3)

### 7.1 Data Classification

```
INCIDENT DATA CLASSIFICATION:
├─ Ticket Number (INC123456): INTERNAL
├─ Short Description: INTERNAL (may contain business-sensitive info)
├─ Technical Details: INTERNAL (error messages, configs)
├─ Customer Impact: CONFIDENTIAL (PII/PHI if mentioned)
├─ Assignment Info: INTERNAL (employee names, emails)
└─ SLA Data: INTERNAL (business rules)

PII/PHI HANDLING:
├─ DO NOT include: Customer names, credit cards, SSN, phone numbers
├─ DO NOT include: Patient health info, medical records
├─ DO include: Incident ID, priority, impact summary
├─ Masking: Automatic before LLM processing
└─ Policy: Redact known patterns (email, phone, SSN)
```

### 7.2 Threat Model (STRIDE)

```
SPOOFING (Fake Events):
├─ Threat: Attacker sends fake ServiceNow events
├─ Mitigation: OAuth2 + signed payloads + source IP validation
└─ Impact: HIGH (could cause false escalations)

TAMPERING (Event Modification):
├─ Threat: Man-in-middle modifies event payload
├─ Mitigation: TLS 1.2+ + signature verification
└─ Impact: MEDIUM (could alter incident details)

REPUDIATION (Deny Actions):
├─ Threat: Admin denies making policy changes
├─ Mitigation: Immutable audit logs + MFA
└─ Impact: LOW (compliance issue)

INFORMATION DISCLOSURE (Data Leakage):
├─ Threat: Sensitive data in messages/logs
├─ Mitigation: PII masking + DLP scanning + log retention
└─ Impact: HIGH (compliance violation)

DENIAL OF SERVICE:
├─ Threat: Flood API with requests
├─ Mitigation: Rate limiting + queue backpressure + auto-scaling
└─ Impact: HIGH (service unavailable)

ELEVATION OF PRIVILEGE:
├─ Threat: Unauthorized policy/template changes
├─ Mitigation: RBAC + approval workflow + MFA
└─ Impact: CRITICAL (could enable malicious actions)
```

### 7.3 Compliance Requirements

```
GDPR (If EU data involved):
├─ Data subject rights: Access, deletion, portability
├─ Data retention: Maximum 3 years (define policy)
├─ Consent: Opt-in for WhatsApp/SMS notifications
├─ Privacy notice: User communication required
└─ Processor agreement: With all vendors

SOC 2 Type II (If required):
├─ Security controls: Encryption, RBAC, audit logs
├─ Availability: 99.5% uptime target
├─ Integrity: Change management, version control
└─ Confidentiality: Access controls, data masking

HIPAA (If healthcare data):
├─ Encryption: At-rest and in-transit
├─ Access controls: Role-based, MFA required
├─ Audit logs: Retention for 6 years
└─ Business associate agreement: With all vendors

INTERNAL POLICIES:
├─ Data retention: 90 days hot, 1 year archive
├─ Access policy: Least privilege
├─ Incident response: Define SLA for security incidents
└─ Disaster recovery: RTO ≤ 2 hours, RPO ≤ 15 min
```

### 7.4 Security Controls Matrix

| Control | Requirement | Implementation | Responsibility |
|---------|------------|-----------------|-----------------|
| **Authentication** | OAuth2/JWT | Service-to-service auth | Backend team |
| **Encryption (Transit)** | TLS 1.2+ | All API endpoints | Infrastructure |
| **Encryption (Rest)** | AES-256 | DB, backups, logs | Infrastructure |
| **RBAC** | Role-based access | Admin operations | Backend team |
| **Audit Logs** | Immutable audit trail | All sensitive operations | Backend team |
| **Secret Rotation** | Every 90 days | Automated via Secrets Manager | DevOps |
| **Vulnerability Scan** | Regular scanning | Container images, dependencies | CI/CD |
| **Penetration Testing** | Annual test | By certified firm | Security team |

---

## 8. Resource Planning and Team Structure (Day 3)

### 8.1 Recommended Team (MVP)

```
MANAGEMENT (1-2):
├─ Product Owner: 1 FTE
│  ├─ Responsibilities: Requirements, prioritization, stakeholder management
│  ├─ Level: Senior PM or Business Analyst
│  └─ Availability: Full-time
│
└─ Project Manager (Optional): 0.5 FTE
   ├─ Responsibilities: Timeline tracking, risk management
   └─ Availability: Part-time (if experienced PO available)

ARCHITECTURE & LEAD (1):
├─ Solution Architect: 1 FTE
│  ├─ Responsibilities: Design, technical decisions, reviews
│  ├─ Level: Senior Engineer
│  ├─ Availability: Full-time (weeks 1-2), then 50% (weeks 3+)
│  └─ Critical: Must be available for Phase 0 & 1

BACKEND/INTEGRATION (2-3):
├─ Senior Backend Engineer: 1 FTE
│  ├─ Lead: API, orchestration, core services
│  └─ Level: 5+ years experience
│
├─ Backend Engineer (Mid-level): 1 FTE
│  ├─ Support: Integration, helper services
│  └─ Level: 3-5 years experience
│
└─ Integration Engineer: 0.5 FTE
   ├─ ServiceNow Flow configuration
   └─ Level: ServiceNow-skilled

GENAI/ML (1):
├─ GenAI Engineer: 1 FTE
│  ├─ Responsibilities: Prompts, LLM integration, quality
│  ├─ Level: ML/AI experience with LLMs
│  ├─ Availability: Full-time
│  └─ Must know: LangChain, OpenAI API, prompt engineering

DEVOPS/INFRASTRUCTURE (1):
├─ DevOps Engineer: 1 FTE
│  ├─ Responsibilities: Docker, Kubernetes, CI/CD, monitoring
│  ├─ Level: 3+ years ops experience
│  └─ Availability: Full-time

QA/TESTING (1):
├─ QA Engineer: 1 FTE
│  ├─ Responsibilities: Test strategy, automation, E2E tests
│  ├─ Level: 2+ years QA experience
│  └─ Availability: Full-time (heavy weeks 3-4)

SECURITY/COMPLIANCE (0.5):
├─ Security Engineer (Part-time): 0.5 FTE
│  ├─ Responsibilities: Security review, compliance
│  ├─ Level: AppSec experience
│  └─ Availability: As-needed during design & implementation

TOTAL CAPACITY: 8-9 FTE

RAMP-UP:
├─ Week 0 (Phase 0): 3-4 people (Architect, PO, Lead Dev)
├─ Week 1-3 (Phase 1): 8-9 people (Full team)
├─ Week 4-6 (Phase 2): 7 people (Architect reduces)
└─ Week 7-8 (Phase 3): 6 people (Engineering reduces)
```

### 8.2 Team Responsibilities Matrix (RACI)

| Activity | PM | Architect | Backend | GenAI | DevOps | QA | Security |
|----------|----|-----------|---------|----|--------|----|----|
| **Phase 0** | | | | | | | |
| Scope definition | R/A | C | I | I | I | I | C |
| Architecture design | C | R/A | C | C | C | I | C |
| Security baseline | C | C | I | I | C | I | R/A |
| Team charter | R/A | C | I | I | I | I | I |
| **Phase 1** | | | | | | | |
| API development | C | C | R/A | I | I | C | I |
| GenAI integration | I | C | I | R/A | I | C | C |
| Database schema | I | C | R/A | I | C | I | I |
| Deployment config | I | I | I | I | R/A | I | C |
| Unit tests | I | I | R/A | R | I | R/A | I |
| Security review | C | C | C | I | C | I | R/A |

R=Responsible, A=Accountable, C=Consulted, I=Informed

### 8.3 Effort Estimation

```
PHASE 0 (Discovery): 1-2 weeks
├─ Architect: 5 days
├─ PO: 5 days
├─ Lead Dev: 2 days (reviews)
└─ Security: 1 day
└─ Total: ~13 person-days

PHASE 1 (MVP Build): 10-12 days of implementation
├─ Backend Engineers: 16 person-days (2 × 8 days)
├─ GenAI Engineer: 8 person-days
├─ DevOps: 4 person-days
├─ QA: 6 person-days
├─ Architecture: 3 person-days (reviews)
└─ Total: ~40 person-days

PHASE 2 (Pilot): 20-25 days
├─ Backend: 12 person-days (tuning, bug fixes)
├─ GenAI: 6 person-days (prompt optimization)
├─ DevOps: 8 person-days (monitoring, optimization)
├─ QA: 4 person-days (smoke tests)
└─ Total: ~30 person-days

PHASE 3 (Scale): 10-15 days
├─ Backend: 8 person-days (enhancements)
├─ DevOps: 4 person-days (scaling)
├─ QA: 2 person-days (verification)
└─ Total: ~15 person-days

GRAND TOTAL: ~98 person-days (~20 weeks of 1 FTE, or 8 weeks of 2-3 FTE)
```

### 8.4 Budget Estimation (MVP 8 Weeks)

```
PERSONNEL COST (8 weeks at US rates):
├─ Senior Architect (1 × $200/hr × 200 hrs): $40,000
├─ Product Owner (1 × $150/hr × 300 hrs): $45,000
├─ Senior Backend Dev (1 × $130/hr × 320 hrs): $41,600
├─ Mid Backend Dev (1 × $100/hr × 320 hrs): $32,000
├─ GenAI Engineer (1 × $150/hr × 200 hrs): $30,000
├─ DevOps Engineer (1 × $120/hr × 200 hrs): $24,000
├─ QA Engineer (1 × $90/hr × 160 hrs): $14,400
├─ Security (0.5 × $140/hr × 40 hrs): $2,800
└─ Total Personnel: ~$230,000

INFRASTRUCTURE & TOOLS (Monthly, 8 weeks):
├─ AWS/Cloud Infrastructure: $2,000/month × 2 = $4,000
├─ OpenAI API (LLM calls): $500/month × 2 = $1,000
├─ WhatsApp/SMS (messages): $1,000/month × 2 = $2,000
├─ Development Tools/Licenses: $200/month × 2 = $400
├─ Monitoring Tools: $500/month × 2 = $1,000
└─ Total Infrastructure: ~$8,400

TOTAL PROJECT COST (Phase 0-3): ~$240,000

COST BREAKDOWN BY PHASE:
├─ Phase 0 (Discovery): $15,000 (overhead)
├─ Phase 1 (Build): $100,000 (main dev work)
├─ Phase 2 (Pilot): $80,000 (hypercare, tuning)
└─ Phase 3 (Scale): $45,000 (expansion, docs)
```

---

## 9. Risk Assessment and Mitigation (Day 3)

### 9.1 Risk Register

| # | Risk | Probability | Impact | Mitigation |
|---|------|-----------|--------|-----------|
| R1 | ServiceNow API rate limits during spike | High | Medium | Pre-load cache, queue batching, contact SN support |
| R2 | LLM latency exceeds 3-sec budget | Medium | High | Fallback templates, timeout guards, model selection |
| R3 | WhatsApp template approval delayed | Medium | High | Apply early, pre-design templates, have email fallback |
| R4 | On-call data source unavailable | Medium | Medium | Cache + static fallback, multiple data sources |
| R5 | Security/compliance findings delay go-live | Medium | Critical | Early security review, compliance baseline done |
| R6 | Team member unavailable (sick leave) | Low | Medium | Cross-training, documentation, backup assignments |
| R7 | Data privacy violation in LLM output | Low | Critical | Content filtering, PII detection, testing before MVP |
| R8 | Pilot group rejection (UX issues) | Low | Medium | User testing, feedback loops, iterate on design |
| R9 | Cost overruns (LLM, messaging) | Medium | Medium | Budget limits, monitoring, channel optimization |
| R10 | Infrastructure/database failure | Low | High | HA setup, backups, disaster recovery testing |

### 9.2 Risk Mitigation Actions

```
TOP PRIORITY ACTIONS:
1. R5 (Security): Schedule security review for end of Phase 0
   └─ Owner: Security Lead, Timeline: Day 3 Phase 0
   
2. R3 (WhatsApp): Submit templates for approval TODAY
   └─ Owner: Architect, Timeline: Immediate (Day 1)
   
3. R2 (LLM): Benchmark LLM latency in dev environment
   └─ Owner: GenAI Engineer, Timeline: Phase 1 Day 1
   
4. R1 (ServiceNow): Test at scale with ServiceNow
   └─ Owner: Integration Engineer, Timeline: Phase 1 Day 3
```

---

## 10. Governance and Decision-Making (Day 3)

### 10.1 Decision-Making Framework

```
TYPES OF DECISIONS:

STRATEGIC (Multi-phase impact):
├─ Owner: Executive Sponsor + Architecture Lead
├─ Approval: Consensus or Sponsor override
├─ Timeline: Max 48 hours
└─ Examples: Tech stack, deployment model, team structure

TACTICAL (Phase-level impact):
├─ Owner: Product Owner + Architecture Lead
├─ Approval: Consensus
├─ Timeline: Max 24 hours
└─ Examples: Feature prioritization, scope adjustments, tool selection

OPERATIONAL (Day-to-day):
├─ Owner: Team Leads (Engineering, QA, Ops)
├─ Approval: Asynchronous if no blocking issues
├─ Timeline: Decisions made during daily standups
└─ Examples: Code review decisions, test cases, documentation
```

### 10.2 Escalation Path

```
LEVEL 1 (Team Lead): 24 hours to resolve
└─ Example: Code review dispute

LEVEL 2 (Architecture Lead): 48 hours to resolve
└─ Example: Design disagreement

LEVEL 3 (Executive Sponsor): 48 hours to resolve
└─ Example: Scope change request

If unresolved after Level 3: Executive decision + document rationale
```

### 10.3 Change Management

```
CHANGE CONTROL BOARD (CCB): Meets 2x/week
├─ Members: PO, Architect, Lead Dev, Security
├─ Process:
│  1. Change request submitted (JIRA ticket)
│  2. Impact analysis (CCB reviews)
│  3. Approval/Rejection (documented)
│  4. Implementation (if approved)
│  └─ Backlog priority adjusted
└─ Scope changes: Must be approved by Executive Sponsor

CHANGE TYPES:
├─ CRITICAL (blocks go-live): Immediate CCB
├─ HIGH (major work): Next CCB meeting
├─ MEDIUM (normal work): Backlog, next sprint
└─ LOW (nice-to-have): Deferred to Phase 3+
```

---

## 11. Phase 0 Deliverables (Completion Checklist)

```
✅ DOCUMENTATION (All signed off):
   ├─ Phase 0: Kickoff and Discovery (this document)
   ├─ Scope Baseline Document
   ├─ Technical Architecture Approved
   ├─ Security & Compliance Baseline
   ├─ Resource & Budget Plan
   ├─ Risk Register & Mitigation Plan
   ├─ Team Charter & RACI Matrix
   └─ Success Metrics Defined

✅ APPROVALS (All stakeholders):
   ├─ Executive Sponsor sign-off
   ├─ Product Owner sign-off
   ├─ Architecture Lead sign-off
   ├─ Security Lead sign-off
   ├─ Operations Lead sign-off
   └─ Finance sign-off (budget)

✅ PREPARATION (Ready for Phase 1):
   ├─ Team members assigned and committed
   ├─ Development environment setup guide
   ├─ GitHub repository initialized
   ├─ CI/CD pipeline skeleton
   ├─ Monitoring tools selected and documented
   ├─ WhatsApp templates submitted for approval
   ├─ ServiceNow integration approach finalized
   └─ On-call data source decision made

✅ ARTIFACTS:
   ├─ Architecture diagram (approved)
   ├─ Data flow diagram
   ├─ Security threat model (documented)
   ├─ Team org chart
   ├─ Gantt chart (8-week timeline)
   ├─ Budget spreadsheet
   └─ Risk register (tracked in Jira)
```

---

## 12. Next Steps - Transition to Phase 1

### 12.1 Phase 1 Kickoff (Day 1 of Phase 1)

```
ACTIVITIES:
├─ Team onboarding (4 hrs)
│  ├─ Git repository access
│  ├─ Development environment setup
│  ├─ Architecture walkthrough (1 hr)
│  └─ Codebase orientation
│
├─ Sprint Planning (2 hrs)
│  ├─ Break down Phase 1 into sprints
│  ├─ Assign tasks to teams
│  └─ Set up sprint board (Jira)
│
├─ Development Environment Setup (4 hrs)
│  ├─ Python venv setup
│  ├─ Docker Compose installation
│  ├─ Database initialization
│  └─ First "Hello World" API call
│
└─ Communication & Kickoff (1 hr)
   ├─ Announce Phase 1 start to stakeholders
   ├─ First daily standup
   └─ Set expectations

TIMELINE: 1 day of preparation
```

---

## 13. Appendices

### Appendix A: Glossary
- **SLA**: Service Level Agreement
- **P1/P2**: Priority 1/2 incidents (critical/high)
- **MTTA**: Mean Time to Acknowledge
- **MTTR**: Mean Time to Resolve
- **LLM**: Large Language Model
- **GenAI**: Generative Artificial Intelligence
- **HA**: High Availability
- **RTO**: Recovery Time Objective
- **RPO**: Recovery Point Objective
- **RBAC**: Role-Based Access Control
- **PII**: Personally Identifiable Information
- **PHI**: Protected Health Information

### Appendix B: Reference Documents
- ServiceNow Event Schema (provided separately)
- OpenAI API Documentation
- FastAPI Documentation
- Kubernetes Best Practices
- AWS Well-Architected Framework

### Appendix C: Contact Information

| Role | Name | Email | Phone |
|------|------|-------|-------|
| Executive Sponsor | TBD | TBD | TBD |
| Product Owner | TBD | TBD | TBD |
| Architecture Lead | TBD | TBD | TBD |
| Project Manager | TBD | TBD | TBD |

---

**Document Status:** DRAFT - Pending Stakeholder Sign-off  
**Last Updated:** 2026-04-23  
**Next Review:** Upon completion of Phase 0 (Day 3)  
**Approved By:** [Signatures to be added]
