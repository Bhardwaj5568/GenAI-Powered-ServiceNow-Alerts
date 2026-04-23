# PHASE 1: MVP BUILD - Complete Implementation Guide
**Duration:** 10-12 Days (2 Weeks)  
**Objective:** Build fully functional end-to-end alerting system  
**Owner:** Architecture Lead + Backend Team Lead  

---

## 1. Phase 1 Overview

### 1.1 Phase Objectives

```
PRIMARY GOALS:
✅ Build end-to-end event processing pipeline
✅ Implement GenAI message generation with fallback
✅ Deploy WhatsApp + SMS notification channels
✅ Create policy-driven routing engine
✅ Achieve ≤10 second P1 latency
✅ Reach 80% test coverage
✅ Pass security baseline assessment
✅ Enable staging environment for UAT
```

### 1.2 Success Criteria (Phase 1 Gate)

```
FUNCTIONAL:
✅ Webhook receives P1/P2 events from ServiceNow
✅ Event processed and enriched within 5 seconds
✅ AI summary generated or fallback template sent
✅ Message delivered via WhatsApp + SMS within 10 seconds
✅ Acknowledgement tracked and updates ServiceNow
✅ Policy engine correctly routes by priority/group
✅ Dedup suppresses true duplicates
✅ Escalation triggers after no-ack timeout

QUALITY:
✅ Unit test coverage ≥ 80%
✅ Integration tests all critical flows
✅ E2E test: full flow webhook→delivered
✅ Load test: 200 events/min sustained
✅ All P0/P1 bugs fixed
✅ Security scan: 0 critical/high vulnerabilities

OPERATIONAL:
✅ Docker image built and scanned
✅ Kubernetes manifests for 3-replica deployment
✅ Monitoring dashboards operational
✅ Alert rules configured (non-firing in test)
✅ Runbooks created for common issues
✅ Documentation complete (API, setup, architecture)

PERFORMANCE:
✅ P1 event→notification latency ≤ 10 sec
✅ GenAI timeout <2.5 sec
✅ Queue processing throughput ≥ 1000 msg/min
✅ Database queries <500ms
✅ Memory usage <512MB per pod
✅ CPU usage <500m under normal load
```

---

## 2. Sprint Planning & Breakdown

### 2.1 Sprint Structure

```
SPRINT 1 (Days 1-3): Foundation & API
├─ Goal: Complete API gateway, authentication, event ingestion
├─ Team: Backend Lead + Architect
├─ Deliverable: Working webhook endpoint
└─ Deployment: Dev environment

SPRINT 1B (Days 4-6): Event Processing
├─ Goal: Complete enrichment + risk scoring
├─ Team: Backend Engineers (both)
├─ Deliverable: Enriched event context
└─ Deployment: Dev environment

SPRINT 2 (Days 7-9): GenAI & Messaging
├─ Goal: Complete LLM integration + channels
├─ Team: GenAI Engineer + Backend
├─ Deliverable: Messages sent via WhatsApp/SMS
└─ Deployment: Staging environment

SPRINT 2B (Days 10-12): Orchestration & Testing
├─ Goal: Complete routing, policy engine, E2E testing
├─ Team: Full team (backend, QA, DevOps)
├─ Deliverable: End-to-end working product
└─ Deployment: Staging environment ready
```

---

## 3. Sprint 1: Foundation & API (Days 1-3)

### 3.1 Day 1: Project Setup & Scaffolding

#### Tasks:

```
1. REPOSITORY SETUP (2 hrs)
   ├─ Initialize git repository structure
   ├─ Create branch protection rules
   ├─ Set up .gitignore
   ├─ Create main development branches (main, develop, feature/*)
   └─ Add README with quick start guide

2. PYTHON PROJECT SCAFFOLD (2 hrs)
   ├─ Create project folder structure:
   │  ├─ src/
   │  │  ├─ main.py
   │  │  ├─ config/
   │  │  ├─ api/
   │  │  ├─ services/
   │  │  ├─ models/
   │  │  ├─ connectors/
   │  │  └─ utils/
   │  ├─ tests/
   │  ├─ infra/
   │  ├─ requirements.txt
   │  ├─ .env.example
   │  └─ Dockerfile
   ├─ Initialize virtual environment
   └─ Install base dependencies

3. DOCKER COMPOSE SETUP (1.5 hrs)
   ├─ Create docker-compose.yml with:
   │  ├─ PostgreSQL 15
   │  ├─ Redis 7
   │  ├─ Prometheus (for metrics)
   │  ├─ Grafana (for dashboards)
   │  └─ Volume mappings for data persistence
   ├─ Create Dockerfile for app
   ├─ Test local environment startup
   └─ Verify all services accessible

4. CI/CD SKELETON (1.5 hrs)
   ├─ Create GitHub Actions workflow:
   │  ├─ .github/workflows/ci.yml
   │  ├─ Trigger: on PR and push to develop
   │  ├─ Steps: lint, test, build, scan
   │  └─ Status: blocking on failures
   ├─ Add branch protection rules
   └─ Test pipeline execution

5. MONITORING INITIALIZATION (1 hr)
   ├─ Configure Prometheus scrape config
   ├─ Create basic Grafana dashboard
   ├─ Set up logging to console (structured JSON)
   └─ Test metric collection

DELIVERABLE: Working local development environment
OWNER: DevOps Engineer
TESTING: Run `docker-compose up` and verify all services healthy
```

#### Code Files to Create:

```python
# requirements.txt
fastapi==0.104.1
uvicorn==0.24.0
pydantic==2.5.0
pydantic-settings==2.1.0
python-jose==3.3.0
cryptography==41.0.7
sqlalchemy==2.0.23
psycopg2-binary==2.9.9
redis==5.0.1
celery==5.3.4
openai==1.3.5
langchain==0.1.1
requests==2.31.0
pytest==7.4.3
pytest-asyncio==0.21.1
httpx==0.25.2
prometheus-client==0.19.0
python-json-logger==2.0.7
python-dotenv==1.0.0
pytz==2023.3

# .env.example
# ServiceNow Configuration
SERVICENOW_INSTANCE_URL=https://dev-instance.service-now.com
SERVICENOW_CLIENT_ID=your-client-id
SERVICENOW_CLIENT_SECRET=your-client-secret
SERVICENOW_WEBHOOK_SECRET=your-webhook-secret

# OpenAI Configuration
OPENAI_API_KEY=sk-your-key-here
OPENAI_MODEL=gpt-4-turbo
LLM_TIMEOUT_SEC=2.5

# WhatsApp Configuration
WHATSAPP_BUSINESS_ACCOUNT_ID=your-account-id
WHATSAPP_PHONE_NUMBER_ID=your-phone-number-id
WHATSAPP_API_TOKEN=your-api-token
WHATSAPP_VERIFY_TOKEN=verify-token

# SMS Configuration
SMS_GATEWAY_API_KEY=your-api-key
SMS_GATEWAY_URL=https://api.sms-provider.com

# Database
DATABASE_URL=postgresql://user:password@localhost:5432/servicenow_alerts
MONGODB_URL=mongodb://localhost:27017/servicenow_alerts
REDIS_URL=redis://localhost:6379/0

# Server
APP_ENV=development
APP_PORT=8000
LOG_LEVEL=INFO

# Security
SECRET_KEY=change-me-in-production
JWT_ALGORITHM=HS256
JWT_EXPIRATION_HOURS=24
```

### 3.2 Day 2: API Gateway & Authentication

#### Tasks:

```
1. FASTAPI APP INITIALIZATION (1.5 hrs)
   ├─ Create src/main.py with:
   │  ├─ FastAPI app instance
   │  ├─ CORS configuration
   │  ├─ Error handlers
   │  └─ Health check endpoint
   ├─ Add environment configuration loading
   ├─ Configure structured logging
   └─ Test: GET /health returns 200

2. AUTHENTICATION MIDDLEWARE (2 hrs)
   ├─ Implement OAuth2/JWT authentication:
   │  ├─ Token validation middleware
   │  ├─ Scope-based authorization
   │  ├─ Service-to-service authentication
   │  └─ Token rotation mechanism
   ├─ Add request signing verification:
   │  ├─ HMAC-SHA256 signature validation
   │  ├─ Nonce/timestamp validation
   │  └─ Replay attack prevention
   ├─ Create auth utilities module
   └─ Test: Invalid token returns 401

3. REQUEST VALIDATION MIDDLEWARE (1 hr)
   ├─ Validate Content-Type
   ├─ Validate JSON schema
   ├─ Size limits (max 1MB payload)
   ├─ Rate limiting (1000 req/min)
   └─ Test: Invalid request returns 400

4. ERROR HANDLING & RESPONSES (1 hr)
   ├─ Define standard error response format:
   │  {
   │    "error_code": "INVALID_REQUEST",
   │    "message": "Human readable message",
   │    "correlation_id": "uuid",
   │    "timestamp": "iso-8601"
   │  }
   ├─ Handle exceptions gracefully
   ├─ Log all errors with correlation_id
   └─ Test: Exceptions return appropriate status codes

5. MONITORING & INSTRUMENTATION (1 hr)
   ├─ Add Prometheus metrics:
   │  ├─ http_requests_total (counter)
   │  ├─ http_request_duration_seconds (histogram)
   │  ├─ http_requests_in_progress (gauge)
   │  └─ Expose /metrics endpoint
   ├─ Add structured logging (JSON format)
   ├─ Add OpenTelemetry tracing
   └─ Test: Metrics visible in Prometheus

DELIVERABLE: Secure, monitored FastAPI app
OWNER: Backend Lead
TESTING: API tests for auth, validation, errors
```

#### Code Structure:

```python
# src/config/settings.py
from pydantic_settings import BaseSettings
from functools import lru_cache

class Settings(BaseSettings):
    # App Config
    APP_ENV: str = "development"
    APP_PORT: int = 8000
    APP_HOST: str = "0.0.0.0"
    LOG_LEVEL: str = "INFO"
    SECRET_KEY: str
    
    # ServiceNow
    SERVICENOW_INSTANCE_URL: str
    SERVICENOW_CLIENT_ID: str
    SERVICENOW_CLIENT_SECRET: str
    SERVICENOW_WEBHOOK_SECRET: str
    
    # Database
    DATABASE_URL: str
    REDIS_URL: str
    
    # LLM
    OPENAI_API_KEY: str
    OPENAI_MODEL: str = "gpt-4-turbo"
    LLM_TIMEOUT_SEC: float = 2.5
    
    # Channels
    WHATSAPP_API_TOKEN: str
    SMS_GATEWAY_API_KEY: str
    
    class Config:
        env_file = ".env"
        case_sensitive = True

@lru_cache()
def get_settings():
    return Settings()

# src/main.py
from fastapi import FastAPI
from fastapi.middleware.cors import CORSMiddleware
from src.config.settings import get_settings
from src.api import health, events

settings = get_settings()
app = FastAPI(title="GenAI ServiceNow Alerts API", version="1.0.0")

# CORS Configuration
app.add_middleware(
    CORSMiddleware,
    allow_origins=["https://your-servicenow-instance.com"],
    allow_credentials=True,
    allow_methods=["POST", "GET"],
    allow_headers=["*"],
)

# Include routers
app.include_router(health.router)
app.include_router(events.router)

if __name__ == "__main__":
    import uvicorn
    uvicorn.run(
        app,
        host=settings.APP_HOST,
        port=settings.APP_PORT,
        log_level=settings.LOG_LEVEL.lower()
    )
```

### 3.3 Day 3: ServiceNow Webhook Receiver

#### Tasks:

```
1. WEBHOOK ENDPOINT IMPLEMENTATION (2 hrs)
   ├─ Create POST /api/v1/events/servicenow endpoint
   ├─ Validate request signature (HMAC-SHA256)
   ├─ Validate OAuth2 bearer token
   ├─ Parse and validate JSON payload schema
   ├─ Return 202 Accepted immediately
   └─ Queue event for async processing

2. EVENT SCHEMA VALIDATION (1.5 hrs)
   ├─ Create Pydantic models for event schema
   ├─ Validate all required fields present
   ├─ Validate priority is in allowed set (P1-P5)
   ├─ Validate sla_remaining_minutes >= 0
   ├─ Validate timestamp is UTC ISO-8601
   └─ Reject invalid events with reason code

3. QUEUE INTEGRATION (1 hr)
   ├─ Push accepted events to Celery queue
   ├─ Use correlation_id for tracing
   ├─ Set retry policy (exponential backoff)
   ├─ Handle queue failures gracefully
   └─ Log all enqueued events

4. IDEMPOTENCY & DEDUP (1 hr)
   ├─ Implement idempotency key checking (Redis)
   ├─ Store processed event_ids for 5 minutes
   ├─ Return 202 for duplicate event_ids
   ├─ Log duplicate detection
   └─ Test: Same event_id twice → only one processed

5. TESTING & DOCUMENTATION (1.5 hrs)
   ├─ Unit tests for validation
   ├─ Integration tests for queue
   ├─ Swagger/OpenAPI documentation
   ├─ Example curl commands
   └─ Error scenario testing

DELIVERABLE: Working webhook endpoint that safely ingests events
OWNER: Backend Lead
TESTING: 50+ unit/integration tests
```

#### Code:

```python
# src/models/events.py
from pydantic import BaseModel, Field, validator
from typing import Optional
from datetime import datetime

class TicketData(BaseModel):
    id: str = Field(..., description="Incident ticket ID (e.g., INC123456)")
    sys_id: str = Field(..., description="ServiceNow sys_id")
    short_description: str
    description: Optional[str] = None
    priority: str = Field(..., description="P1-P5")
    assignment_group: str
    assigned_to: str
    created_on: datetime
    state: str  # new, in_progress, resolved, closed
    
    @validator('priority')
    def validate_priority(cls, v):
        if v not in ["P1", "P2", "P3", "P4", "P5"]:
            raise ValueError("Priority must be P1-P5")
        return v

class SLAData(BaseModel):
    name: str
    remaining_minutes: int = Field(..., ge=0)
    total_minutes: int = Field(..., ge=0)
    threshold_percent: int = Field(..., ge=0, le=100)
    breach_time: datetime
    status: str  # active, breached, met

class ServiceNowEvent(BaseModel):
    event_id: str = Field(..., description="UUID of this event")
    event_type: str = Field(...)  # incident.created, sla.warning, etc.
    event_timestamp: datetime
    source: str = "servicenow"
    correlation_id: str
    
    ticket: TicketData
    sla: Optional[SLAData] = None
    change_meta: Optional[dict] = None

# src/api/events.py
from fastapi import APIRouter, Depends, HTTPException, Request, status
from src.services.event_processor import process_event_async
from src.models.events import ServiceNowEvent
from src.config.settings import get_settings
import hmac
import hashlib
import logging
import uuid

router = APIRouter(prefix="/api/v1", tags=["events"])
logger = logging.getLogger(__name__)
settings = get_settings()

@router.post("/events/servicenow")
async def receive_servicenow_event(
    request: Request,
    event: ServiceNowEvent
):
    """
    Receive events from ServiceNow webhook.
    
    Returns 202 Accepted to indicate event received for processing.
    """
    correlation_id = event.correlation_id or str(uuid.uuid4())
    logger.info(
        f"Event received",
        extra={
            "event_id": event.event_id,
            "event_type": event.event_type,
            "correlation_id": correlation_id,
            "ticket_id": event.ticket.id,
            "priority": event.ticket.priority
        }
    )
    
    # Verify signature
    signature = request.headers.get("X-ServiceNow-Signature")
    if not signature:
        raise HTTPException(status_code=401, detail="Missing signature")
    
    # TODO: Verify signature validity
    
    # Check idempotency
    # TODO: Check if event_id already processed (Redis)
    
    # Enqueue for processing
    try:
        process_event_async.delay(
            event.dict(),
            correlation_id=correlation_id
        )
    except Exception as e:
        logger.error(f"Failed to enqueue event: {str(e)}", extra={"correlation_id": correlation_id})
        raise HTTPException(status_code=500, detail="Failed to process event")
    
    return {"status": "accepted", "event_id": event.event_id, "correlation_id": correlation_id}
```

---

## 4. Sprint 1B: Event Processing (Days 4-6)

### 4.1 Day 4: Event Processor Service

#### Tasks:

```
1. CELERY WORKER SETUP (1.5 hrs)
   ├─ Configure Celery to use Redis broker
   ├─ Create worker initialization
   ├─ Set up task discovery
   ├─ Configure logging for workers
   └─ Test: Worker starts and consumes tasks

2. EVENT ROUTER (1 hr)
   ├─ Route based on event_type:
   │  ├─ incident.created → new_incident_handler
   │  ├─ sla.threshold_warning → sla_warning_handler
   │  ├─ incident.priority_changed → priority_change_handler
   │  └─ fallback: unhandled_event_handler
   ├─ Extract event context
   └─ Call next stage (enrichment)

3. ERROR HANDLING & RETRIES (1 hr)
   ├─ Retry failed tasks (exponential backoff)
   ├─ Max retries: 3
   ├─ Dead-letter queue for failed events
   ├─ Log all failures with context
   └─ Alert on repeated failures

4. STATE MANAGEMENT (1 hr)
   ├─ Store processing state in Redis
   ├─ Track: received → processing → enriched → completed
   ├─ Timeout handling (max 60 seconds)
   └─ Rollback on failure

DELIVERABLE: Reliable event processing pipeline
OWNER: Backend Engineer #1
TESTING: Unit tests for routing, error handling
```

### 4.2 Day 5: Enrichment Service

#### Tasks:

```
1. SERVICENOW API CLIENT (2 hrs)
   ├─ Create ServiceNow REST API client:
   │  ├─ Table API queries (incident table)
   │  ├─ Authentication (OAuth2)
   │  ├─ Request timeout: 5 seconds
   │  ├─ Retry logic for transient failures
   │  └─ Circuit breaker pattern
   ├─ Methods:
   │  ├─ get_incident_details(ticket_id)
   │  ├─ get_activity_stream(ticket_id, limit=10)
   │  ├─ get_assignment_history(ticket_id)
   │  └─ get_related_tickets(ticket_id)
   ├─ Caching: Redis cache (TTL 5 minutes)
   └─ Test: Mock ServiceNow responses

2. ON-CALL ROSTER INTEGRATION (1.5 hrs)
   ├─ Implement on-call data source:
   │  ├─ ServiceNow schedule API (primary)
   │  ├─ Fetch current on-call engineer
   │  ├─ Phone number lookup
   │  └─ Fallback to static configuration
   ├─ Caching: Redis (TTL 15 minutes)
   └─ Graceful degradation if unavailable

3. CONTEXT BUILDING (1 hr)
   ├─ Combine ServiceNow data + on-call + event data
   ├─ Create normalized context object:
   │  {
   │    "incident_id": "INC123456",
   │    "priority": "P1",
   │    "description": "...",
   │    "assigned_engineer": {...},
   │    "on_call_engineer": {...},
   │    "sla_remaining_minutes": 120,
   │    "business_impact": "HIGH",
   │    "related_tickets": []
   │  }
   └─ Store in database

DELIVERABLE: Enriched incident context
OWNER: Backend Engineer #2
TESTING: Mock ServiceNow integration tests
```

### 4.3 Day 6: Risk Scorer Service

#### Tasks:

```
1. RISK SCORING ALGORITHM (2 hrs)
   ├─ Calculate breach risk score (0-100):
   │  ├─ SLA remaining %: weight 40%
   │  ├─ Priority level: weight 30%
   │  ├─ Reassignment count: weight 15%
   │  ├─ Historical patterns: weight 15%
   │  └─ Formula:
   │     score = (sla_pct * 0.4) + (priority_score * 0.3) + 
   │             (reassign_weight * 0.15) + (history_weight * 0.15)
   │
   ├─ Score interpretation:
   │  ├─ 0-30: Low risk (GREEN)
   │  ├─ 31-70: Medium risk (YELLOW)
   │  └─ 71-100: High risk (RED)
   │
   ├─ Priority scoring:
   │  ├─ P1: 100 points
   │  ├─ P2: 70 points
   │  ├─ P3: 40 points
   │  └─ P4/P5: 20 points
   │
   └─ Reassignment penalty: +5 per reassignment (max 20)

2. BUSINESS IMPACT CLASSIFICATION (1 hr)
   ├─ Classify as HIGH/MEDIUM/LOW based on:
   │  ├─ Priority level
   │  ├─ Assignment group (critical groups = high)
   │  ├─ SLA remaining time
   │  └─ Pattern matching against templates
   └─ Store classification for use in messaging

3. BREACH PREDICTION (1 hr)
   ├─ Flag if likely to breach SLA:
   │  ├─ If remaining < 15 minutes: likely breach
   │  ├─ Historical MTTR vs remaining time
   │  └─ Current workload on assigned group
   ├─ Used for escalation decisions
   └─ Trigger additional alerts if needed

DELIVERABLE: Risk scores and breach predictions
OWNER: Backend Engineer #2
TESTING: Unit tests for scoring algorithm
```

---

## 5. Sprint 2: GenAI & Messaging (Days 7-9)

### 5.1 Day 7: GenAI Adapter Service

#### Tasks:

```
1. LLM INTEGRATION (2 hrs)
   ├─ OpenAI API client setup:
   │  ├─ API key management (from Secrets Manager)
   │  ├─ Model selection (gpt-4-turbo)
   │  ├─ Timeout enforcement: 2.5 seconds
   │  ├─ Rate limiting: Respect API limits
   │  └─ Retry logic: Exponential backoff
   ├─ Error handling:
   │  ├─ Timeout → Fallback template
   │  ├─ API error → Fallback template
   │  ├─ Rate limit → Queue retry
   │  └─ Log all failures
   └─ Test: Mock OpenAI responses

2. PROMPT TEMPLATE ENGINE (1.5 hrs)
   ├─ Create prompt templates by:
   │  ├─ Event type (new incident, SLA warning, escalation)
   │  ├─ Priority (P1, P2)
   │  ├─ Recipient role (L1, L2, Manager)
   │  └─ Language (English, future: Hindi, etc.)
   ├─ Template structure:
   │  ```
   │  You are a ServiceNow incident alerting system.
   │  Generate a concise 3-line alert message.
   │  
   │  Incident: {{ticket_id}} - {{short_description}}
   │  Priority: {{priority}}
   │  SLA Remaining: {{sla_remaining_minutes}} minutes
   │  Impact: {{business_impact}}
   │  Current Status: {{status}}
   │  
   │  Format output as:
   │  Line 1: Issue summary (max 50 chars)
   │  Line 2: Urgency & SLA (max 50 chars)
   │  Line 3: Recommended action (max 50 chars)
   │  
   │  Keep language plain, operational, and actionable.
   │  ```
   ├─ Version templates (store in DB)
   └─ Enable A/B testing

3. OUTPUT VALIDATION (1 hr)
   ├─ Validate LLM output:
   │  ├─ Has all required fields
   │  ├─ Respects character limits
   │  ├─ No PII/PHI present
   │  ├─ No prohibited terms
   │  └─ Passes regex guards
   ├─ Confidence scoring
   └─ Reject if validation fails → Fallback

DELIVERABLE: AI message generation capability
OWNER: GenAI Engineer
TESTING: Unit tests for prompting, validation
```

#### Code:

```python
# src/services/genai_adapter.py
from openai import OpenAI
from langchain.prompts import PromptTemplate
from src.config.settings import get_settings
import logging
import time

logger = logging.getLogger(__name__)
settings = get_settings()

class GenAIAdapter:
    def __init__(self):
        self.client = OpenAI(api_key=settings.OPENAI_API_KEY)
        self.model = settings.OPENAI_MODEL
        self.timeout = settings.LLM_TIMEOUT_SEC
    
    def generate_alert_message(self, context: dict, recipient_role: str) -> dict:
        """
        Generate AI-powered alert message.
        
        Args:
            context: Enriched incident context
            recipient_role: L1, L2, or Manager
            
        Returns:
            {
                "message": "Alert text",
                "fallback_used": False,
                "confidence": 0.95,
                "tokens_used": 150,
                "latency_ms": 1234
            }
        """
        start_time = time.time()
        
        try:
            # Select template based on role and event type
            template = self._select_template(context["event_type"], recipient_role)
            prompt = template.format(**context)
            
            # Call LLM with timeout
            response = self.client.chat.completions.create(
                model=self.model,
                messages=[{"role": "user", "content": prompt}],
                max_tokens=150,
                temperature=0.3,  # Low temperature for consistency
                timeout=self.timeout
            )
            
            message_text = response.choices[0].message.content
            
            # Validate output
            validation_result = self._validate_output(message_text, context)
            if not validation_result["valid"]:
                logger.warning(f"Output validation failed: {validation_result['reason']}")
                return self._get_fallback_template(context)
            
            latency_ms = (time.time() - start_time) * 1000
            
            return {
                "message": message_text,
                "fallback_used": False,
                "confidence": 0.95,
                "tokens_used": response.usage.total_tokens,
                "latency_ms": latency_ms
            }
            
        except TimeoutError:
            logger.error("LLM call timed out")
            return self._get_fallback_template(context)
        except Exception as e:
            logger.error(f"LLM error: {str(e)}")
            return self._get_fallback_template(context)
    
    def _select_template(self, event_type: str, recipient_role: str) -> PromptTemplate:
        # TODO: Load from database
        template_str = """
        You are generating a ServiceNow incident alert.
        
        Incident: {incident_id}
        Description: {short_description}
        Priority: {priority}
        SLA Remaining: {sla_remaining_minutes} minutes
        
        Format output as 3 lines, max 50 chars each.
        """
        return PromptTemplate(template=template_str, input_variables=[...])
    
    def _validate_output(self, message: str, context: dict) -> dict:
        import re
        
        lines = message.strip().split('\n')
        if len(lines) != 3:
            return {"valid": False, "reason": "Output must be 3 lines"}
        
        for i, line in enumerate(lines):
            if len(line) > 60:
                return {"valid": False, "reason": f"Line {i+1} exceeds 60 chars"}
        
        # Check for PII patterns
        pii_patterns = [
            r'\b\d{3}-\d{2}-\d{4}\b',  # SSN
            r'\b\d{16}\b',  # Credit card
        ]
        for pattern in pii_patterns:
            if re.search(pattern, message):
                return {"valid": False, "reason": "PII detected in output"}
        
        return {"valid": True}
    
    def _get_fallback_template(self, context: dict) -> dict:
        template = f"""[{context['priority']} - {context['incident_id']}]
{context['short_description'][:45]}
SLA: {context['sla_remaining_minutes']} min | Risk: HIGH"""
        
        return {
            "message": template,
            "fallback_used": True,
            "confidence": 0.5,
            "tokens_used": 0,
            "latency_ms": 100
        }
```

### 5.2 Day 8: WhatsApp & SMS Connectors

#### Tasks:

```
1. WHATSAPP CONNECTOR (2 hrs)
   ├─ WhatsApp Business API integration:
   │  ├─ Phone number setup
   │  ├─ Message template registration
   │  ├─ Delivery callback handling
   │  ├─ Status polling
   │  └─ Error handling per WhatsApp docs
   ├─ Message sending:
   │  ├─ Recipient validation
   │  ├─ Message length validation (4096 chars)
   │  ├─ Template vs text mode
   │  ├─ Retry on failure
   │  └─ Track delivery status
   ├─ Metrics:
   │  ├─ Send success rate
   │  ├─ Delivery latency
   │  ├─ Cost tracking
   │  └─ Failure reasons
   └─ Test: Mock WhatsApp API responses

2. SMS CONNECTOR (1.5 hrs)
   ├─ SMS gateway integration (AWS SNS / Twilio):
   │  ├─ Phone number validation
   │  ├─ Message length (160 chars, GSM encoding)
   │  ├─ DND (Do Not Disturb) checking
   │  ├─ Delivery confirmation
   │  └─ Error handling per provider
   ├─ Message sending:
   │  ├─ Segmentation (long messages → multiple SMS)
   │  ├─ Retry logic
   │  ├─ Delivery tracking
   │  └─ Status updates
   └─ Test: Mock SMS provider responses

3. UNIFIED CHANNEL INTERFACE (1 hr)
   ├─ Abstract interface for all channels
   ├─ Common methods:
   │  ├─ send_message(recipient, message, channel)
   │  ├─ get_delivery_status(message_id)
   │  ├─ process_delivery_callback(webhook)
   │  └─ get_channel_metrics()
   ├─ Fallback logic (WhatsApp → SMS → Email)
   └─ Test: Interface contract tests

DELIVERABLE: Message delivery capability
OWNER: Backend Engineer #1 + Backend Engineer #2
TESTING: Mock API integration tests
```

### 5.3 Day 9: Integration & E2E Testing

#### Tasks:

```
1. SERVICE INTEGRATION (1.5 hrs)
   ├─ Wire all services together:
   │  ├─ Webhook → Processor → Enricher
   │  ├─ Enricher → Risk Scorer
   │  ├─ Risk Scorer → Orchestrator
   │  ├─ Orchestrator → GenAI Adapter
   │  ├─ GenAI Adapter → Channel Connectors
   │  └─ Channel Connectors → External APIs
   ├─ Error propagation
   ├─ Logging correlation
   └─ Metrics collection

2. END-TO-END TESTING (1.5 hrs)
   ├─ Create E2E test scenarios:
   │  ├─ TC-E2E-001: P1 incident → WhatsApp + SMS delivered < 10 sec
   │  ├─ TC-E2E-002: SLA warning → escalation after 5 min no-ack
   │  ├─ TC-E2E-003: Priority change → new alert sent
   │  ├─ TC-E2E-004: Duplicate detection → only one alert
   │  └─ TC-E2E-005: GenAI timeout → fallback template sent
   ├─ Setup test data (mock ServiceNow data)
   ├─ Execute test scenarios
   └─ Document results

3. LOAD TESTING (1 hr)
   ├─ Test sustained throughput:
   │  ├─ 200 events/min ingestion
   │  ├─ 1000 messages/min output
   │  ├─ Latency percentiles (P50, P95, P99)
   │  └─ Resource utilization
   ├─ Identify bottlenecks
   ├─ Optimization opportunities
   └─ Capacity planning data

DELIVERABLE: Fully integrated, tested system
OWNER: QA Engineer + Backend Team
TESTING: 50+ E2E and load tests
```

---

## 6. Sprint 2B: Orchestration & Testing (Days 10-12)

### 6.1 Day 10: Policy Engine & Routing

#### Tasks:

```
1. POLICY ENGINE IMPLEMENTATION (2 hrs)
   ├─ Rule evaluator:
   │  ├─ Parse policy rules (DSL or JSON)
   │  ├─ Evaluate conditions:
   │  │  ├─ priority == "P1"
   │  │  ├─ sla_remaining_minutes < 15
   │  │  ├─ assignment_group matches "NOC-*"
   │  │  ├─ Time-based (business hours, weekends)
   │  │  └─ Custom conditions
   │  ├─ Execute actions:
   │  │  ├─ Route to recipients
   │  │  ├─ Select channels (WhatsApp, SMS)
   │  │  ├─ Set escalation policy
   │  │  └─ Enable/disable notifications
   │  └─ Order of evaluation (priority)
   │
   ├─ Example policies:
   │  {
   │    "id": "policy-001",
   │    "name": "P1 Dual Channel",
   │    "conditions": {
   │      "priority": "P1"
   │    },
   │    "actions": {
   │      "channels": ["whatsapp", "sms"],
   │      "recipients": ["assigned_engineer", "on_call"],
   │      "escalation_after_no_ack_minutes": 5
   │    }
   │  }
   └─ Versioning: Store policy history

2. RECIPIENT RESOLVER (1 hr)
   ├─ Resolve recipients based on:
   │  ├─ Assigned engineer
   │  ├─ On-call engineer
   │  ├─ Assignment group members
   │  ├─ Manager escalation
   │  ├─ Region-specific routing
   │  └─ Custom escalation paths
   ├─ Fetch contact info (phone, WhatsApp)
   ├─ Handle opt-in/opt-out preferences
   └─ DND (Do Not Disturb) checking

3. POLICY MANAGEMENT API (1 hr)
   ├─ CRUD endpoints:
   │  ├─ GET /api/v1/policies (list all)
   │  ├─ GET /api/v1/policies/{id} (get one)
   │  ├─ POST /api/v1/policies (create)
   │  ├─ PUT /api/v1/policies/{id} (update)
   │  └─ DELETE /api/v1/policies/{id} (soft delete)
   ├─ Approval workflow: Draft → Review → Approved → Active
   ├─ Version management
   └─ Audit trail for all changes

DELIVERABLE: Policy-driven routing engine
OWNER: Backend Engineer #1
TESTING: Unit tests for rule evaluation
```

### 6.2 Day 11: Dedup, Throttle, Escalation

#### Tasks:

```
1. DEDUPLICATION LOGIC (1 hr)
   ├─ Detect duplicate events:
   │  ├─ Exact duplicate: Same event_id → Skip
   │  ├─ Similar events: Same ticket_id + type within 5 min window → Count
   │  ├─ Burst suppression: >5 alerts in 1 min → Hold & batch
   │  ├─ Store in Redis with TTL
   │  └─ Metrics: Track dedup rate
   ├─ Configurable by:
   │  ├─ Time window (5, 10, 15 min)
   │  ├─ Event type
   │  └─ Priority level
   └─ Test: Verify dedup works correctly

2. THROTTLING LOGIC (1 hr)
   ├─ Prevent alert fatigue:
   │  ├─ Per-ticket: Max 1 alert per hour (configurable)
   │  ├─ Per-user: Max 10 alerts per hour (configurable)
   │  ├─ Global: Max 1000 alerts per hour (configurable)
   │  ├─ Queue suppressed alerts in holding queue
   │  ├─ Release when time window expires
   │  └─ Metrics: Track throttle rate
   ├─ Exception: P1 events always sent (no throttle)
   └─ Configuration via policy engine

3. ESCALATION SCHEDULER (1 hr)
   ├─ Schedule escalations on no-ack:
   │  ├─ Level 1: Original recipient (L1)
   │  ├─ Level 2: After N minutes → L2/Manager
   │  ├─ Level 3: After N more minutes → Exec/On-call
   │  ├─ Max levels: 3
   │  └─ Repeat alert vs new escalation alert
   ├─ Track acknowledgements via:
   │  ├─ User clicks link in message
   │  ├─ User replies to SMS
   │  ├─ Manual ack in ServiceNow
   │  └─ Ticket state change (resolved)
   ├─ Store escalation history
   └─ Metrics: Track escalation rate

DELIVERABLE: Smart alert management
OWNER: Backend Engineer #2
TESTING: Unit tests for logic
```

### 6.3 Day 12: Security Hardening & Deployment

#### Tasks:

```
1. SECURITY HARDENING (1.5 hrs)
   ├─ Input validation: All API endpoints
   ├─ CORS: Restrict to ServiceNow domains
   ├─ Rate limiting: Implemented and tested
   ├─ Secrets: All in AWS Secrets Manager
   ├─ RBAC: Admin operations protected
   ├─ PII/PHI: Scanning and masking
   ├─ Dependency scanning: No critical vulns
   ├─ Container image scanning: No critical vulns
   └─ SAST/DAST: Code scanning clean

2. DOCUMENTATION & DEPLOYMENT (1.5 hrs)
   ├─ API documentation (Swagger/OpenAPI):
   │  ├─ All endpoints documented
   │  ├─ Example requests/responses
   │  ├─ Error codes explained
   │  └─ Auto-generated, always in sync
   ├─ Architecture documentation:
   │  ├─ Diagrams (data flow, service interaction)
   │  ├─ Component descriptions
   │  ├─ Deployment options
   │  └─ Scaling considerations
   ├─ Deployment guide:
   │  ├─ Docker build/run instructions
   │  ├─ Kubernetes manifests for 3-replica deployment
   │  ├─ Database migrations
   │  ├─ Configuration steps
   │  └─ Verification checklist
   ├─ Troubleshooting guide:
   │  ├─ Common issues and solutions
   │  ├─ Log analysis tips
   │  ├─ Metrics interpretation
   │  └─ Escalation procedures

3. DEPLOYMENT AUTOMATION (1 hr)
   ├─ Build Docker image
   ├─ Push to registry (AWS ECR)
   ├─ Deploy to staging Kubernetes cluster
   ├─ Run smoke tests
   ├─ Verify all services healthy
   └─ Runbook accessible to ops team

DELIVERABLE: Production-ready, documented system
OWNER: DevOps + Full Team
TESTING: Deployment tests, verification
```

---

## 7. Phase 1 Testing Strategy

### 7.1 Test Matrix

```
TEST LEVEL          COVERAGE       AUTOMATED    OWNER         TIMELINE
────────────────────────────────────────────────────────────────────────
Unit Tests          80%+           Yes          Backend       Days 1-12
Integration Tests   60%+           Yes          Backend/QA    Days 4-12
E2E Tests           Critical flows Yes          QA            Days 9-12
Load Tests          Throughput     Semi         QA            Day 12
Security Tests      OWASP Top 10   Semi         Security      Days 10-12
Performance Tests   Latency        Semi         Backend       Days 10-12
```

### 7.2 Test Scenarios (Key Tests)

```
TC-001: Valid P1 Event Processing
└─ Webhook receives valid P1 event
└─ Event processed within 5 seconds
└─ Enrichment completes successfully
└─ AI message generated
└─ Messages sent via WhatsApp + SMS
└─ Status: PASS (< 10 sec total)

TC-002: Invalid Event Rejection
└─ Missing required field
└─ Webhook returns 400 Bad Request
└─ Event not queued
└─ Error logged with correlation_id
└─ Status: PASS

TC-003: LLM Timeout Fallback
└─ LLM call times out after 2.5 sec
└─ Fallback template used
└─ Message still delivered
└─ Fallback flag logged
└─ Status: PASS

TC-004: Duplicate Suppression
└─ Same event_id received twice
└─ First: Processed and sent
└─ Second: Deduplicated, not sent
└─ Status: PASS

TC-005: Escalation After No-Ack
└─ P1 event sent to L1
└─ No acknowledgement after 5 minutes
└─ Manager receives escalation alert
└─ Escalation logged
└─ Status: PASS

TC-006: Load Test (200 events/min)
└─ Send 200 events/min for 10 minutes
└─ P95 latency ≤ 15 seconds
└─ No errors or timeouts
└─ Queue depth manageable
└─ Status: PASS
```

---

## 8. Phase 1 Deliverables

```
CODE & ARTIFACTS:
✅ FastAPI application (all services)
✅ Celery worker setup
✅ Docker image and Dockerfile
✅ Kubernetes manifests
✅ Database schema and migrations
✅ Test suite (80%+ coverage)
✅ CI/CD pipeline configured

DOCUMENTATION:
✅ API documentation (Swagger)
✅ Architecture documentation
✅ Deployment guide
✅ Troubleshooting guide
✅ Code comments and docstrings
✅ README with quick start

MONITORING & OPERATIONS:
✅ Prometheus metrics exported
✅ Grafana dashboards (draft)
✅ Alert rules defined (not firing)
✅ Logging configuration
✅ Health check endpoints

QUALITY ARTIFACTS:
✅ Test results report
✅ Code coverage report
✅ Security scan report
✅ Performance benchmarks
✅ Known issues list (with workarounds)
```

---

## 9. Phase 1 Success Metrics

### 9.1 Technical Metrics

| Metric | Target | Actual |
|--------|--------|--------|
| Code Coverage | ≥ 80% | TBD |
| Test Pass Rate | 100% | TBD |
| Critical Vulns | 0 | TBD |
| High Vulns | ≤ 2 | TBD |
| P1 Latency (P99) | ≤ 15 sec | TBD |
| GenAI Fallback Rate | < 5% | TBD |
| Message Delivery Success | ≥ 99% | TBD |

### 9.2 Exit Criteria

```
MUST PASS (Blocking):
✅ All critical functional tests pass
✅ No critical/high security vulnerabilities
✅ P1 latency ≤ 10 seconds confirmed
✅ 80%+ test coverage achieved
✅ Documentation complete and clear
✅ Deployment to staging successful

SHOULD PASS (Important):
✅ Load test 200 events/min sustained
✅ Zero known P1 bugs
✅ All team members familiar with codebase
✅ Runbooks prepared and tested

NICE TO HAVE:
✅ Performance optimizations identified
✅ Scaling plan documented
✅ Future roadmap items brainstormed
```

---

## 10. Risk Mitigation for Phase 1

| Risk | Probability | Impact | Mitigation |
|------|-----------|--------|-----------|
| LLM API unavailability | Low | High | Fallback templates, retry logic, monitoring |
| ServiceNow API rate limits | Medium | Medium | Caching, throttling, queue batching |
| Team member unavailable | Low | Medium | Cross-training, documentation, pair programming |
| Database performance issues | Low | High | Query optimization, indexing, load testing |
| Integration test failures | Medium | Medium | Mock-based testing, clear test data setup |
| Deployment issues | Low | High | Thorough testing, rollback plan, runbooks |

---

**Phase 1 Document Status:** APPROVED FOR BUILD  
**Phase 1 Start Date:** [To be set after Phase 0 approval]  
**Phase 1 End Date:** +12 working days  
**Next Phase:** Phase 2 - Pilot & Hardening  

---
