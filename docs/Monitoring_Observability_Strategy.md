# MONITORING, OBSERVABILITY & OPERATIONS STRATEGY
**Applies to:** All Phases (0-3)  
**Owner:** DevOps Engineer + Architecture Lead  
**Status:** COMPREHENSIVE REFERENCE DOCUMENT  

## Project North Star

This document serves the same project motive as every other document in this folder: build a GenAI-powered ServiceNow alerting MVP that reduces SLA breaches and MTTA through intelligent WhatsApp and SMS notifications. This file only narrows that motive to monitoring, observability, alerting, and operations.

---

## 1. Monitoring Strategy Overview

### 1.1 Purpose

The monitoring strategy defines HOW we observe the system's health, detect problems early, and enable rapid incident response across all deployment phases.

```
Monitoring Layers:
├─ Infrastructure Layer (cloud resources)
├─ Platform Layer (services and APIs)
├─ Application Layer (business logic)
├─ User Experience Layer (user perception)
└─ Business Metrics Layer (business impact)
```

### 1.2 Key Objectives

```
1. EARLY PROBLEM DETECTION
   ├─ Detect issues before users notice
   ├─ Alert thresholds set to enable fast response
   └─ Predictive alerting (future: Phase 3+)

2. RAPID INCIDENT RESPONSE
   ├─ Clear alert descriptions
   ├─ Actionable runbooks
   ├─ Intelligent routing to right team
   └─ Response time SLAs enforced

3. PERFORMANCE OPTIMIZATION
   ├─ Identify bottlenecks
   ├─ Data-driven optimization
   ├─ Trend analysis for capacity planning
   └─ Cost optimization opportunities

4. COMPLIANCE & AUDIT
   ├─ Maintain audit trails
   ├─ Demonstrate SLO compliance
   ├─ Security event logging
   └─ Regulatory requirement evidence

5. OPERATIONAL EXCELLENCE
   ├─ Knowledge building (dashboards, runbooks)
   ├─ Team skill development
   ├─ Continuous improvement culture
   └─ Institutional memory preservation
```

---

## 2. Metrics Taxonomy

### 2.1 RED Metrics (Request-driven)

```
For every user request/event, measure:

RATE: How many requests per second?
├─ Unit: Events/sec or Messages/min
├─ Example: 200 events/min ingestion rate
├─ Alert: Rate drops below expected minimum
└─ Use: Capacity planning, volume tracking

ERRORS: How many fail?
├─ Unit: Errors per second or percentage
├─ Example: 0.5% error rate
├─ Alert: Error rate > 1% for 2 minutes
├─ Breakdown: By error type, service, endpoint
└─ Use: Quality tracking, incident detection

DURATION: How long do requests take?
├─ Unit: Milliseconds or seconds
├─ Example: P99 latency 15 seconds
├─ Metrics: P50, P95, P99, Max
├─ Alert: P99 > 20 seconds for 5 minutes
└─ Use: Performance monitoring, optimization
```

### 2.2 USE Metrics (Resource-driven)

```
For every system resource (CPU, memory, disk, network):

UTILIZATION: What % of resource is used?
├─ Unit: Percentage (0-100%)
├─ Example: CPU 45%, Memory 60%
├─ Alert: Utilization > 80% for 10 min
├─ Trend: Detect growth patterns
└─ Use: Capacity planning, auto-scaling

SATURATION: How much queuing/waiting?
├─ Unit: Queue depth or wait time
├─ Example: Database connection pool 80% full
├─ Alert: Saturation > 70% for 5 min
├─ Cascading: Monitor saturation at each layer
└─ Use: Performance degradation detection

ERRORS: What % fail?
├─ Unit: Errors or percentage
├─ Example: Failed queries 0.1%
├─ Alert: Error rate spike (sudden increase)
├─ Root cause: Insufficient resources?
└─ Use: Infrastructure troubleshooting
```

### 2.3 Business Metrics

```
SLA IMPACT:
├─ Metric: SLA breach rate (%)
├─ Formula: (Breached tickets) / (Total tickets)
├─ Target: Reduce by 10-15% vs baseline
├─ Tracking: Weekly and monthly
├─ Owner: Product Owner, Business Unit Lead
└─ Reporting: Executive dashboard

MEAN TIME TO ACKNOWLEDGE (MTTA):
├─ Metric: Average time to acknowledge alert
├─ Formula: (Sum of ack times) / (Total alerts)
├─ Target: Reduce by 40-50% vs baseline
├─ Tracking: Daily moving average
├─ Owner: L1 Support Lead
└─ Reporting: Pilot group dashboard

USER ADOPTION & SATISFACTION:
├─ Metric 1: Adoption rate (%)
│  ├─ Formula: (Active users) / (Pilot group size)
│  ├─ Target: ≥ 75%
│  └─ Tracking: Monthly
│
├─ Metric 2: Net Promoter Score (NPS)
│  ├─ Formula: % Promoters - % Detractors
│  ├─ Target: ≥ +40 (pilot), ≥ +50 (scale)
│  ├─ Tracking: Monthly survey
│  └─ Use: Team satisfaction, product-market fit
│
├─ Metric 3: Alert Quality Score
│  ├─ Formula: (Useful alerts) / (Total alerts)
│  ├─ Target: ≥ 90%
│  ├─ Tracking: User rating on each alert
│  └─ Use: GenAI prompt optimization
│
└─ Metric 4: Escalation Accuracy
   ├─ Formula: (Correct escalations) / (Total escalations)
   ├─ Target: ≥ 95%
   ├─ Tracking: Manual audit (weekly)
   └─ Use: Policy tuning

COST METRICS:
├─ Metric: Cost per alert
│  ├─ Formula: Total monthly cost / Total alerts
│  ├─ Target: < $0.10 MVP, < $0.08 at scale
│  ├─ Breakdown: By channel, by service
│  └─ Tracking: Daily
│
└─ Metric: Budget variance
   ├─ Formula: (Actual spend - Budget) / Budget
   ├─ Target: ≤ 5% variance
   ├─ Tracking: Monthly
   └─ Alert: If overspend > 10%
```

---

## 3. Monitoring Stack Components

### 3.1 Recommended Open-Source Stack (MVP)

```
METRICS COLLECTION & STORAGE:
├─ Prometheus
│  ├─ What: Time-series metrics database
│  ├─ How: Pull-based (scrape endpoints)
│  ├─ Where: Runs in Docker/K8s
│  ├─ Retention: 15 days local, archive to S3
│  └─ Querying: PromQL language
│
├─ Node Exporter (for system metrics)
│  ├─ CPU, memory, disk, network per host
│  └─ Auto-discovered in K8s

METRICS VISUALIZATION:
├─ Grafana
│  ├─ What: Metrics visualization and dashboards
│  ├─ Data source: Prometheus
│  ├─ Where: Runs in Docker/K8s
│  └─ Access: Web UI on port 3000
│
└─ Pre-built dashboards available
   └─ Node Exporter Full, Kubernetes cluster monitoring

CENTRALIZED LOGGING:
├─ Elasticsearch
│  ├─ What: Full-text searchable logs
│  ├─ Index: By date and service
│  ├─ Retention: 30 days hot, 1 year archive
│  └─ Query: Lucene query syntax
│
├─ Logstash
│  ├─ What: Log processing and forwarding
│  ├─ Input: Syslog, files, Kafka
│  ├─ Processing: Parsing, enrichment
│  └─ Output: Elasticsearch, S3
│
└─ Kibana
   ├─ What: Log visualization and exploration
   ├─ Data source: Elasticsearch
   └─ Features: Dashboards, alerting

DISTRIBUTED TRACING:
├─ Jaeger
│  ├─ What: Distributed trace visualization
│  ├─ How: OpenTelemetry instrumentation
│  ├─ Sampling: Head-based (first) or tail-based
│  ├─ Storage: In-memory or Elasticsearch
│  └─ UI: Port 16686 for trace exploration
│
└─ Use: Track request flow across services

ALERTING:
├─ Prometheus AlertManager
│  ├─ Rule engine: Evaluate PromQL expressions
│  ├─ Routing: Route alerts to teams
│  ├─ Deduplication: Avoid alert spam
│  ├─ Silencing: Suppress known issues
│  └─ Integration: Slack, PagerDuty, email
│
└─ Grafana Alerting (alternative/supplement)
   ├─ Dashboard: Check Prometheus metrics
   ├─ Query-based: Custom PromQL queries
   └─ Notification: Multiple channels

COST ESTIMATION (MVP Stack):
├─ Prometheus: Free (open-source)
├─ Grafana: Free (open-source) or Grafana Cloud $29/month
├─ Elasticsearch/Logstash: Free (open-source)
├─ Kibana: Free (open-source)
├─ Jaeger: Free (open-source)
├─ Infrastructure: ~$500/month (AWS t3 instances + storage)
├─ On-call/incident: PagerDuty $29/user/month × 5 = $145/month
└─ TOTAL: ~$700/month (very cost-effective)

CLOUD ALTERNATIVE (If locked to cloud):
├─ AWS:
│  ├─ CloudWatch: Metrics + Logs ($0.50/GB ingestion)
│  ├─ X-Ray: Distributed tracing ($5 per million traces)
│  └─ Total: $1-2K/month (depending on volume)
│
├─ GCP:
│  ├─ Cloud Monitoring: $0.26/month per metric
│  ├─ Cloud Logging: $0.50/GB
│  └─ Cloud Trace: $0.20 per million spans
│
└─ Azure:
   ├─ Application Insights: $0.04 per GB ingested
   └─ Total: $500-1500/month
```

### 3.2 Instrumentation Approach

```
APPLICATION-LEVEL METRICS:
├─ FastAPI middleware:
│  ├─ Track all HTTP requests
│  ├─ Latency, status code, endpoint
│  └─ Expose at /metrics endpoint (Prometheus format)
│
├─ Celery task metrics:
│  ├─ Task start/completion times
│  ├─ Success/failure counts
│  ├─ Queue depth
│  └─ Worker availability
│
├─ Business logic metrics:
│  ├─ Events processed (counter)
│  ├─ Messages sent by channel (counter)
│  ├─ Acknowledgements received (counter)
│  ├─ Escalations triggered (counter)
│  └─ GenAI fallbacks (counter)
│
└─ Database metrics:
   ├─ Query execution time
   ├─ Connection pool status
   ├─ Slow query alerts
   └─ Connection failures

STRUCTURED LOGGING:
├─ JSON format (one JSON object per log line)
├─ Required fields:
│  ├─ @timestamp: ISO 8601 timestamp
│  ├─ level: debug, info, warning, error, critical
│  ├─ logger: Module/component name
│  ├─ message: Log message
│  ├─ correlation_id: Trace requests across services
│  ├─ user_id: For audit trail
│  ├─ service: Service name
│  └─ environment: dev, staging, prod
│
└─ Optional fields (context-dependent):
   ├─ duration_ms: Execution time
   ├─ error: Error message (if error)
   ├─ stack_trace: Stack trace (if error)
   └─ custom_fields: Application-specific data

TRACING WITH OPENTELEMETRY:
├─ Instrument all services:
│  ├─ FastAPI app (auto-instrumentation)
│  ├─ Database queries (SQLAlchemy)
│  ├─ HTTP client calls (requests/aiohttp)
│  ├─ Redis operations
│  └─ External API calls (OpenAI, WhatsApp, SMS)
│
├─ Key attributes:
│  ├─ trace_id: Unique per request
│  ├─ span_id: Unique per operation
│  ├─ service.name: Name of service
│  ├─ http.method: GET, POST, etc.
│  ├─ http.status_code: 200, 400, 500, etc.
│  ├─ duration: Latency in milliseconds
│  └─ error.type: Exception type (if error)
│
└─ Sampling strategy:
   ├─ MVP: Sample 100% (all traces)
   ├─ At scale: Head-based sampling (1-10%)
   ├─ Tail-based: Sample traces with errors (100%)
   └─ Budget: Limit total trace ingestion volume
```

---

## 4. Alert Rules & Escalation

### 4.1 Alert Rules by Severity

```
CRITICAL ALERTS (PagerDuty Immediate)
Rule 1: Platform Unavailable
├─ Condition: Availability < 95% for 5 minutes
├─ Impact: Users cannot receive alerts
├─ Action: On-call engineer responds immediately
├─ Runbook: docs/runbooks/platform_unavailable.md
└─ Resolution: Restart services, check logs

Rule 2: High Error Rate
├─ Condition: Error rate > 5% for 2 minutes
├─ Impact: Many alerts not being delivered
├─ Action: On-call engineer responds
├─ Runbook: docs/runbooks/high_error_rate.md
└─ Resolution: Check service logs, database, LLM API

Rule 3: Message Delivery Failure
├─ Condition: WhatsApp/SMS success rate < 90% for 5 min
├─ Impact: Users not receiving alerts
├─ Action: Check channel API status
├─ Runbook: docs/runbooks/delivery_failure.md
└─ Resolution: Contact channel provider, use fallback

Rule 4: Database Down
├─ Condition: PostgreSQL connection failures
├─ Impact: Cannot process events or store data
├─ Action: On-call DBA responds
├─ Runbook: docs/runbooks/database_down.md
└─ Resolution: Check database, promote replica if needed

Rule 5: Out of Memory (OOM)
├─ Condition: Memory utilization > 95%
├─ Impact: Service may crash
├─ Action: Immediately alert on-call
├─ Runbook: docs/runbooks/oom_killer.md
└─ Resolution: Increase memory, find memory leak

HIGH ALERTS (PagerDuty, also Slack)
Rule 6: Latency Degradation
├─ Condition: P99 latency > 20 seconds for 5 minutes
├─ Impact: Users waiting too long for alerts
├─ Action: On-call engineer investigates
├─ Runbook: docs/runbooks/latency_degradation.md
└─ Resolution: Database optimization, query caching

Rule 7: LLM API Timeout
├─ Condition: Timeout rate > 10% for 5 minutes
├─ Impact: Falling back to templates more often
├─ Action: Check OpenAI API status
├─ Runbook: docs/runbooks/llm_timeout.md
└─ Resolution: Reduce prompt size, use GPT-3.5

Rule 8: Queue Depth Growing
├─ Condition: Queue depth > 10,000 for 10 minutes
├─ Impact: Alerts delayed
├─ Action: Check for processing bottleneck
├─ Runbook: docs/runbooks/queue_depth_growing.md
└─ Resolution: Scale workers, check dependencies

Rule 9: Escalation Spike
├─ Condition: Escalations > 3x baseline for 10 minutes
├─ Impact: Too many false escalations
├─ Action: Review escalation policy
├─ Runbook: docs/runbooks/escalation_spike.md
└─ Resolution: Tune thresholds, investigate issues

Rule 10: Cost Overspend
├─ Condition: Daily cost > $500 (alert threshold)
├─ Impact: Budget overrun
├─ Action: Alert cost owner
├─ Runbook: docs/runbooks/cost_overspend.md
└─ Resolution: Investigate expensive service

MEDIUM ALERTS (Slack Only)
Rule 11: Slow Query Detected
├─ Condition: Query latency > 2 seconds
├─ Impact: Performance degradation
├─ Action: Add to backlog for optimization
└─ Runbook: docs/runbooks/slow_query.md

Rule 12: Fallback Template Usage High
├─ Condition: Fallback rate > 20% for 1 hour
├─ Impact: Message quality degraded
├─ Action: GenAI engineer reviews
└─ Resolution: Improve LLM timeout or prompts

Rule 13: Disk Usage Growing
├─ Condition: Disk usage > 80%
├─ Impact: May run out of space
├─ Action: Schedule cleanup/archival
└─ Resolution: Archive old logs, delete temp files

LOW ALERTS (Email Daily Digest)
Rule 14: Acknowledgement Rate Low
├─ Condition: Ack rate < 75% for 1 hour
├─ Impact: Users not engaging with alerts
├─ Action: Investigate alert quality
└─ Resolution: Improve message clarity

Rule 15: High Memory Usage (Not critical yet)
├─ Condition: Memory usage > 70%
├─ Impact: Monitor for leaks
└─ Resolution: Schedule performance review

Rule 16: Cloud API Rate Limit Warning
├─ Condition: Approaching 80% of rate limit
├─ Impact: May start getting rate-limited
└─ Action: Reduce request rate or increase quota
```

### 4.2 Alert Routing & Escalation

```
ROUTING DECISIONS:

CRITICAL (P1):
├─ Immediate PagerDuty alert to on-call
├─ Slack notification to #incidents channel
├─ Email to platform owner
├─ SMS to VP of Engineering (if action needed)
├─ Response SLA: < 5 minutes
└─ Escalation: Manager on-call if P1 > 30 min

HIGH (P2):
├─ PagerDuty alert to on-call (ack required)
├─ Slack notification to #platform-alerts
├─ Email to platform owner
├─ Response SLA: < 15 minutes
└─ Escalation: Engineering manager if > 1 hour

MEDIUM (P3):
├─ Slack notification to #platform-alerts
├─ Email (next business day)
├─ Response SLA: < 4 hours
├─ Handled during next sprint
└─ No escalation (track in backlog)

LOW (P4):
├─ Email daily digest (end of day)
├─ Response SLA: Next business day
├─ Handled during planning
└─ Document for future optimization

SUPPRESSION RULES:
├─ Business hours maintenance windows (planned)
├─ Known false positives (whitelist)
├─ Cascading alerts (only alert once at source)
├─ Bake alerts > 30 minutes before paging
└─ Avoid alert fatigue (max 10 alerts/hour to on-call)
```

---

## 5. Key Dashboards

### 5.1 Operations Dashboard (Real-time monitoring)

```
SECTION 1: System Health (Top of dashboard)
├─ Overall availability: Big number (target: 99.5%+)
├─ Error rate: Big number (target: < 1%)
├─ P99 latency: Big number (target: < 15 sec)
├─ Queue depth: Big number (target: normal range)
└─ Status lights: Green/Yellow/Red per service

SECTION 2: Traffic Patterns (Center-left)
├─ Events/min (line chart, last 24 hours)
├─ Messages sent by channel (stacked bar chart)
├─ Error rate over time (line chart, 2 colors)
├─ Acknowledgement rate over time (line chart)
└─ Latency percentiles (line chart: P50, P95, P99)

SECTION 3: Resource Utilization (Center-right)
├─ CPU usage by service (bar chart)
├─ Memory usage by service (bar chart)
├─ Database connections (gauge)
├─ Cache hit ratio (gauge)
└─ Disk usage (gauge)

SECTION 4: Alerts & Incidents (Bottom)
├─ Recent alerts (table: time, severity, message)
├─ Active incidents (if any)
├─ Alert volume by type (bar chart)
├─ Mean time to acknowledge (number)
└─ Escalation rate (number)

REFRESH RATE: 30 seconds
AUDIENCE: On-call engineer, platform owner
ACCESS: 24/7
```

### 5.2 Business Dashboard (Executive view)

```
SECTION 1: Impact Metrics (Top)
├─ SLA Breach Rate: X% (baseline vs current, trend)
├─ MTTA: Y minutes (baseline vs current)
├─ Cost per alert: $Z (target vs current)
├─ User adoption: A% (target vs current)
└─ User satisfaction (NPS): +B (trend)

SECTION 2: Trends (Center)
├─ SLA breach rate trend (line chart, last 30 days)
├─ MTTA trend (line chart, last 30 days)
├─ Cost per alert trend (line chart, last 30 days)
├─ Alert volume trend (bar chart, by team)
└─ Escalation rate (line chart)

SECTION 3: Breakdown (Bottom-left)
├─ Alert volume by team
├─ Alerts by priority (P1/P2/P3)
├─ Delivery success by channel
├─ Top incident types

SECTION 4: Cost (Bottom-right)
├─ Monthly cost breakdown (pie chart)
├─ Cost by channel (bar chart)
├─ Cost trend (line chart)
└─ Budget vs actual (gauge)

REFRESH RATE: 1 hour
AUDIENCE: Product Owner, Executives, Business Units
ACCESS: Business hours, on-demand
```

### 5.3 Performance Dashboard (Engineering)

```
SECTION 1: Service Latencies (Top row)
├─ API latency (histogram: P50, P95, P99)
├─ Event processor latency
├─ Enrichment service latency
├─ GenAI adapter latency
├─ Notification orchestrator latency
└─ Channel connector latencies

SECTION 2: Database Performance (Middle)
├─ Query latency (P50, P95, P99)
├─ Top slow queries (table)
├─ Connection pool usage
├─ Replication lag
├─ Cache hit ratio
└─ Index usage

SECTION 3: Throughput (Lower-middle)
├─ Events processed/sec (line chart)
├─ Messages sent/sec by channel
├─ Database writes/sec
├─ Cache reads/writes/sec
└─ External API calls/sec

SECTION 4: Errors (Bottom)
├─ Error rate by service (bar chart)
├─ Error types (pie chart)
├─ Top error sources (table)
├─ Retry success rate
└─ Fallback template usage rate

REFRESH RATE: 1 minute
AUDIENCE: Backend engineers, DevOps, QA
ACCESS: Development environment (always), Production (on-demand)
```

---

## 6. Operational Runbooks

### 6.1 Runbook Structure

```
Each runbook follows this structure:

1. ALERT DEFINITION
   ├─ Alert name and ID
   ├─ Severity level
   ├─ Condition that triggers alert
   ├─ Expected impact on users
   └─ Business impact

2. DIAGNOSIS (Questions to ask)
   ├─ Is the alert correct? (false positive check)
   ├─ When did it start?
   ├─ What changed recently?
   ├─ Related alerts?
   ├─ Recent deployments?
   └─ Resource constraints?

3. IMMEDIATE ACTIONS (First 5 minutes)
   ├─ Confirm alert is real (check dashboard)
   ├─ Check service health (logs, status page)
   ├─ Assess user impact
   ├─ Notify stakeholders (if needed)
   └─ Start timer for escalation

4. INVESTIGATION (5-30 minutes)
   ├─ Check recent changes/deployments
   ├─ Review logs for errors
   ├─ Check external service status
   ├─ Analyze metrics for correlation
   └─ Narrow down root cause

5. RESOLUTION (Depends on cause)
   ├─ Option A: Restart service
   ├─ Option B: Scale up resources
   ├─ Option C: Rollback recent change
   ├─ Option D: Clear cache/queue
   ├─ Option E: Failover to backup
   └─ Option F: Call vendor support

6. ESCALATION (If stuck)
   ├─ Who to call next
   ├─ Information to provide
   ├─ When to escalate (time-based)
   └─ After-hours contacts

7. POST-INCIDENT (After resolution)
   ├─ Root cause summary
   ├─ Why it wasn't caught earlier
   ├─ Improvement opportunity
   ├─ Process change or monitoring improvement
   └─ Lessons learned
```

### 6.2 Example Runbooks (High-Frequency)

```
RUNBOOK 1: High Error Rate
Severity: CRITICAL
Trigger: Error rate > 5% for 2 minutes
Time to resolve: < 15 minutes

IMMEDIATE ACTIONS:
1. Check Grafana Error Rate Dashboard
   └─ Is it real? Check latest 10 errors in logs
2. Check service logs (CloudWatch / ELK)
   └─ Are all services throwing errors?
3. Check external dependencies
   └─ ServiceNow, OpenAI, WhatsApp, SMS provider - all up?
4. Check resource usage
   └─ CPU/Memory/Disk/Network - any near limit?

INVESTIGATION:
1. If recent deployment → ROLLBACK
   ├─ kubectl rollout undo deployment/servicenow-alerts
   └─ Verify error rate drops
   
2. If ServiceNow unavailable → CIRCUIT BREAKER
   ├─ System should fallback (already implemented)
   ├─ Manual: Check ServiceNow status page
   └─ If down, wait for ServiceNow to recover
   
3. If OpenAI timeout → REDUCE PROMPT SIZE or USE GPT-3.5
   ├─ Manual override in config
   ├─ Restart pods: kubectl rollout restart deployment/genai-adapter
   └─ Monitor error rate
   
4. If database slow → SCALE REPLICAS
   ├─ Add read replica: kubectl scale statefulset db-replica --replicas=3
   └─ Monitor latency

ESCALATION:
├─ After 10 min: Call engineering manager
├─ After 20 min: Call CTO
└─ If still unresolved: Execute DR plan

POST-INCIDENT:
├─ Why wasn't this caught? (Add monitoring?)
├─ Can we prevent recurrence? (Better tests?)
├─ Process change needed? (Deployment validation?)
└─ Document in lessons learned

---

RUNBOOK 2: Message Delivery Failure (WhatsApp)
Severity: CRITICAL
Trigger: WhatsApp delivery < 90% for 5 minutes
Time to resolve: < 10 minutes

IMMEDIATE ACTIONS:
1. Check WhatsApp API status page
   └─ https://status.api.whatsapp.com/
2. Check our WhatsApp connector logs
   ├─ Are we sending requests?
   ├─ What errors are we getting?
   └─ Response codes from WhatsApp?
3. Check failed messages (dead-letter queue)
   ├─ kubectl logs deployment/notification-worker | grep whatsapp
   └─ Look for error patterns

RESOLUTION PATHS:
1. If WhatsApp API is down
   ├─ Use SMS as fallback (P1 gets dual-channel)
   ├─ Notify users of channel issue
   └─ Monitor WhatsApp status for recovery
   
2. If we're rate-limited
   ├─ Reduce send rate (circuit breaker)
   ├─ Contact Meta/WhatsApp for quota increase
   └─ Batch messages differently
   
3. If authentication issue
   ├─ Verify API token in Secrets Manager
   ├─ Restart connector pod
   ├─ Re-authenticate if needed
   └─ Check token expiration

4. If message format issue
   ├─ Check recent template changes
   ├─ Validate message format
   ├─ Rollback template change if recent
   └─ Test with sample message

ESCALATION:
├─ After 5 min: Notify Product Owner
├─ After 10 min: Escalate to Meta support
└─ Manual SMS sending if WhatsApp down > 30 min

---

RUNBOOK 3: Database Connection Timeout
Severity: CRITICAL
Trigger: DB connection failures spike
Time to resolve: < 10 minutes

IMMEDIATE ACTIONS:
1. Check database status
   ├─ AWS RDS console or: psql connection test
   └─ Is database up and responding?
2. Check connection pool
   ├─ kubectl describe statefulset postgres
   └─ Are there resource warnings?
3. Check network connectivity
   ├─ Can pods reach database?
   └─ Security group allows traffic?

RESOLUTION:
1. If database down
   ├─ Failover to read replica
   ├─ Promote read replica to primary
   └─ Update connection strings
   
2. If connection pool exhausted
   ├─ Increase pool size: max_connections parameter
   ├─ Restart database: systemctl restart postgres
   ├─ Scale app servers (reduce connection demand)
   └─ Review for connection leaks
   
3. If network issue
   ├─ Check security groups (allow inbound 5432)
   ├─ Check VPC routing
   ├─ Restart affected pods
   └─ Test from pod: nc -zv dbhost 5432

ESCALATION:
├─ After 5 min: Call DBA
├─ After 10 min: Call database vendor support
└─ >= 15 min: Activate disaster recovery

POST-INCIDENT:
├─ Why wasn't this caught in testing?
├─ Connection pool settings adequate?
├─ Monitoring setup to prevent recurrence?
└─ Update runbook with findings
```

---

## 7. Metrics Export & Integration

### 7.1 Prometheus Metrics Format

```
All metrics exposed at: http://service:8000/metrics

EXAMPLE METRICS:

# Counter (always increasing)
events_received_total{event_type="incident.created",priority="P1"} 1234
events_received_total{event_type="sla.threshold_warning",priority="P2"} 5678

# Histogram (latency distribution)
event_processing_duration_seconds_bucket{le="0.1"} 100
event_processing_duration_seconds_bucket{le="1.0"} 450
event_processing_duration_seconds_bucket{le="5.0"} 499
event_processing_duration_seconds_bucket{le="+Inf"} 500
event_processing_duration_seconds_sum 1234.5
event_processing_duration_seconds_count 500

# Gauge (point-in-time value)
queue_depth{queue_name="events"} 42
active_workers{service="event_processor"} 3

# Derived Metrics (PromQL queries)
rate(events_received_total[5m]) = Events per second (last 5 min)
histogram_quantile(0.95, event_processing_duration_seconds) = P95 latency
```

### 7.2 Integration with Alerting Systems

```
PAGERDUTY INTEGRATION:
├─ AlertManager routes to PagerDuty
├─ Severity mapping: Critical→P1, High→P2, Medium→P3
├─ Automatic incident creation
├─ On-call assignment based on schedule
├─ Acknowledgment feeds back to Prometheus
└─ Integration config:
   global:
     pagerduty_url: https://events.pagerduty.com/v2/enqueue
   route:
     receiver: 'pagerduty'
   receivers:
   - name: 'pagerduty'
     pagerduty_configs:
     - service_key: ${PAGERDUTY_SERVICE_KEY}

SLACK INTEGRATION:
├─ AlertManager routes to Slack channels
├─ #incidents for P1 alerts
├─ #platform-alerts for P2/P3
├─ Rich formatting with links to dashboards
├─ Example message:
   [HIGH] High Error Rate
   Error rate: 6.2% (target: <1%)
   Duration: 3 minutes
   Runbook: https://wiki/runbooks/high-error-rate
   Dashboard: https://grafana/d/errors

EMAIL INTEGRATION:
├─ Low-priority alerts (daily digest)
├─ Executive reports (weekly)
├─ Compliance reports (monthly)
└─ Example recipients: ops@company.com, product@company.com

CUSTOM WEBHOOKS:
├─ AlertManager can POST to custom webhook
├─ Useful for: ServiceNow ticket creation, custom logging
├─ Payload includes: alert name, severity, labels, description
└─ Example: Create ServiceNow incident for P1 alerts
```

---

## 8. SLO Definition & Tracking

### 8.1 Service Level Objectives (SLOs)

```
SLO 1: AVAILABILITY
Definition: System is responsive to requests
Metric: Availability = (successful requests) / (total requests) × 100
Target: ≥ 99.5% monthly
Error budget: 0.5% (3.6 hours/month)
Alert threshold: < 99% in 5-minute window

SLO 2: LATENCY
Definition: Event processing completes within time threshold
Metric: P99 latency for P1 events ≤ 15 seconds
Target: 99% of requests meet latency
Error budget: 1% of requests can exceed 15 seconds
Alert threshold: P99 > 20 seconds for 5 minutes

SLO 3: ERROR RATE
Definition: Events processed successfully without user-facing errors
Metric: Successful processing = (processed - failed) / total × 100
Target: ≥ 99%
Error budget: ≤ 1% failure rate
Alert threshold: > 2% for 2 minutes

SLO 4: MESSAGE DELIVERY
Definition: Messages successfully delivered to user
Metric: Delivery success rate = (delivered) / (sent) × 100
Target: ≥ 99%
Error budget: ≤ 1% failure rate
Alert threshold: < 95% for 5 minutes

SLO 5: ACKNOWLEDGEMENT
Definition: Users acknowledge alerts within expected timeframe
Metric: Mean Time to Acknowledge (MTTA)
Target: MTTA ≤ 5 minutes (improve by 40-50% from baseline)
Error budget: N/A (trend metric)
Alert threshold: Average MTTA > 10 minutes (indicates quality issue)
```

### 8.2 Error Budget Tracking

```
CONCEPT: Error budget is how much you can "fail" while still meeting SLO

Example: 99.5% availability SLO
├─ Target: 99.5% uptime
├─ Maximum allowed downtime: 0.5% = 3.6 hours/month
├─ Tracking:
│  ├─ Week 1: 2 hours downtime (used 28% of budget)
│  ├─ Week 2: 0.5 hours downtime (used 7% of budget)
│  ├─ Week 3: 1 hour downtime (used 14% of budget)
│  ├─ Week 4: 0.1 hours downtime (used 1% of budget)
│  └─ Total: 3.6 hours (100% of budget used = SLO MET)

BUDGET BURN RATE:
├─ Actual burn rate: How fast are we consuming budget?
├─ Acceptable burn rate: 1x (use budget at expected rate)
├─ Fast burn alert: > 2x (alert if burning too fast)
├─ Slow burn alert: < 0.1x (alert if underutilized budget - suggests low volume)

ACTION RULES:
├─ If 2x burn rate for > 15 min: ALERT (could miss SLO)
├─ If 1x burn rate: Continue normal operations
├─ If budget spent with week remaining: FREEZE DEPLOYMENTS (reduce risk)
├─ If budget still available after week: Normal risk-taking OK
```

---

## 9. Continuous Improvement Program

### 9.1 Metrics Review Cycle

```
DAILY (15 minutes at standup):
├─ Check alerts fired overnight
├─ Review error rate trends
├─ Confirm on-call handled issues
└─ Plan day's work

WEEKLY (Thursday, 1 hour):
├─ Service health review
│  ├─ Availability: On target?
│  ├─ Latency: Trends?
│  ├─ Error rate: Changes?
│  └─ Resource usage: Growing?
├─ Incident review (if any)
│  ├─ Root cause analysis
│  ├─ Prevention actions
│  ├─ Runbook updates
│  └─ Assign action items
├─ Alert review
│  ├─ False positives: Reduce
│  ├─ False negatives: Improve
│  └─ Threshold tuning
└─ Capacity planning
   ├─ Growth rate
   ├─ Scaling plan
   └─ Resource allocation

MONTHLY (First Friday, 2 hours):
├─ SLO performance summary
│  ├─ Did we meet SLOs?
│  ├─ Error budget status
│  ├─ Burn rate analysis
│  └─ Trends
├─ Business metrics review
│  ├─ SLA impact: Improving?
│  ├─ MTTA: Improving?
│  ├─ User satisfaction: Trend?
│  └─ Cost: Within budget?
├─ Team feedback
│  ├─ On-call experience
│  ├─ Monitoring effectiveness
│  ├─ Documentation quality
│  └─ Stress level
└─ Planning
   ├─ Next month priorities
   ├─ Infrastructure improvements
   ├─ Monitoring enhancements
   └─ Team training needs

QUARTERLY (End of quarter, 4 hours):
├─ Strategic review
│  ├─ Business impact achieved?
│  ├─ ROI analysis
│  ├─ Competitive positioning
│  └─ Customer satisfaction
├─ Architecture review
│  ├─ Scalability adequate?
│  ├─ Tech debt assessment
│  ├─ Security posture
│  └─ Compliance status
├─ Team capability
│  ├─ Skills assessment
│  ├─ Training needs
│  ├─ Hiring plan
│  └─ Retention/satisfaction
└─ 6-month planning
   ├─ Roadmap
   ├─ Investment needs
   ├─ Team growth
   └─ Strategy
```

### 9.2 Improvements from Monitoring Data

```
OPTIMIZATION OPPORTUNITIES (Identify from data):

1. Reduce P99 latency (target: 12 sec from 15 sec)
   ├─ Identify: Which requests are slow?
   ├─ Analyze: Database queries? LLM calls?
   ├─ Optimize: Caching? Query rewrite? Batching?
   └─ Measure: Confirm improvement

2. Improve GenAI quality (target: < 3% fallback rate)
   ├─ Identify: When does fallback happen?
   ├─ Analyze: Timeout? Validation failure?
   ├─ Optimize: Better prompts? Longer timeout?
   └─ Measure: Confirm improvement

3. Reduce alert fatigue (target: 50% fewer alerts)
   ├─ Identify: Which alerts are most spammy?
   ├─ Analyze: False positives? Threshold too sensitive?
   ├─ Optimize: Better dedup? Smarter thresholds?
   └─ Measure: User feedback score

4. Lower cost per alert (target: $0.08 from $0.12)
   ├─ Identify: Which channel is most expensive?
   ├─ Analyze: SMS or WhatsApp? LLM calls?
   ├─ Optimize: Channel selection? Model choice?
   └─ Measure: Cost trends

5. Improve MTTA (target: 3 min from 5 min)
   ├─ Identify: When are users fastest/slowest to ack?
   ├─ Analyze: Message clarity? Channel preference?
   ├─ Optimize: Better messages? Better channel?
   └─ Measure: MTTA trend
```

---

## 10. Monitoring Deployment by Phase

### 10.1 Phase 0: Planning Only
```
✅ Define all metrics (above)
✅ Design dashboard layouts
✅ Select and license tools
✅ Estimate costs
❌ No infrastructure deployed
❌ No data collected yet
```

### 10.2 Phase 1: MVP Monitoring Setup
```
✅ Deploy Prometheus + Grafana (Docker)
✅ Instrument FastAPI app with metrics
✅ Instrument Celery tasks
✅ Configure basic alerts (not firing yet)
✅ Set up ELK stack for logging
✅ Create operational dashboards
✅ Implement structured logging
✅ Test all metrics collection
```

### 10.3 Phase 2: Production Monitoring Activation
```
✅ Activate all alerts in production
✅ Integrate with PagerDuty/Slack
✅ Verify alert routing working
✅ Test incident response (drills)
✅ Create all runbooks
✅ Train on-call team
✅ Monitor real production traffic
✅ Tune alert thresholds from real data
✅ Implement SLO tracking
✅ Create business dashboards
```

### 10.4 Phase 3: Advanced Monitoring & Optimization
```
✅ Implement advanced anomaly detection
✅ Add predictive alerting (future enhancement)
✅ Multi-region monitoring
✅ Advanced cost tracking
✅ User experience monitoring
✅ Continuous improvement program established
✅ Knowledge base documentation complete
✅ Team training on monitoring mastery
```

---

## 11. Monitoring Troubleshooting

### 11.1 "Metrics not showing up"
```
STEPS TO DEBUG:
1. Service exposing metrics?
   ├─ curl http://service:8000/metrics
   └─ Should see Prometheus format output
2. Prometheus scraping?
   ├─ Check Prometheus targets (http://prometheus:9090/targets)
   └─ Should show "UP" status
3. Metrics in TSDB?
   ├─ PromQL query: {job="service_name"}
   └─ Should return data
4. Grafana connected?
   ├─ Check Grafana data sources
   └─ Test connection to Prometheus
```

### 11.2 "Logs not appearing in ELK"
```
STEPS TO DEBUG:
1. Application writing logs?
   ├─ Check app logs: kubectl logs deployment/app
   └─ Should see JSON format
2. Logstash receiving?
   ├─ Check Logstash logs: kubectl logs deployment/logstash
   └─ Should show "connected" messages
3. Elasticsearch indexing?
   ├─ Check ES indices: curl http://es:9200/_cat/indices
   └─ Should see logstash-* indices
4. Kibana seeing data?
   ├─ Create index pattern in Kibana
   ├─ Should find logstash-* pattern
   └─ Browse documents in Discover
```

---

**Document Status:** COMPLETE REFERENCE  
**Last Updated:** 2026-04-23  
**Applies to:** All project phases  
**Owner:** DevOps Engineer + Architecture Lead

---
