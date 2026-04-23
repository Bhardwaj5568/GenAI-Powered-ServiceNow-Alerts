# GenAI Design and Prompting Specification

## 1. Purpose

Define how GenAI will convert raw incident context into compact, actionable, and policy-safe alerts.

## 2. GenAI Objectives

- Improve clarity vs raw incident fields.
- Highlight urgency and business impact.
- Recommend next best action with minimal cognitive load.
- Reduce noise through dedupe awareness and context compression.

## 3. Model Interaction Pattern

Input:
- Normalized incident context object.
- Event type and urgency metadata.
- Prompt template version and language preference.

Output:
- Structured concise message.
- Optional confidence or quality metadata.

## 4. Prompt Template Strategy

Template dimensions:
- Event category (new incident, SLA warning, escalation).
- Priority class (P1/P2).
- Recipient persona (L1, L2, Manager).

Baseline prompt instruction pattern:
- Summarize issue in up to 3 lines.
- Include priority, impact, SLA remaining time.
- Provide one actionable recommendation.
- Avoid speculative or unverified statements.
- Use plain operational language.

## 5. Output Schema Enforcement

Required fields in generated output object:
- title
- line_1_issue
- line_2_urgency
- line_3_action
- policy_flags (pii_detected, fallback_required)

Validation checks:
- Maximum character and line constraints per channel.
- Mandatory mention of ticket ID and remaining SLA.
- Prohibited terms and sensitive pattern checks.

## 6. Safety and Guardrails

Pre-generation controls:
- Redact known sensitive patterns from input context.
- Restrict input fields to allow-listed attributes.

Post-generation controls:
- Regex and policy checks for sensitive content.
- Hallucination risk check using strict extraction constraints.
- Fallback to deterministic template if validation fails.

## 7. Fallback Templates

Fallback must include:
- Alert type and priority.
- Incident ID.
- SLA remaining.
- Impact summary from deterministic fields.
- Prescriptive next step from routing policy.

Fallback trigger conditions:
- Model timeout.
- Response schema mismatch.
- Policy violation.
- Low confidence (optional threshold-based).

## 8. Quality Measurement

Offline evaluation metrics:
- Completeness (required fields present).
- Brevity compliance (line/character limits).
- Actionability score (human review rubric).
- Hallucination rate.

Online metrics:
- Ack rate uplift vs baseline static alerts.
- Mean time to acknowledge improvement.
- User feedback ratings on message usefulness.
- Fallback rate and root-cause breakdown.

## 9. Prompt and Model Governance

- Every prompt template versioned with approval record.
- Model version pinning for production stability.
- Controlled rollout via canary policy (for example 10% traffic).
- Rollback switch to previous prompt/model combo.

## 10. Example Generated Message (Target Format)

[Critical Alert - P1 | INC123456]
Payment API outage impacting checkout transactions.
SLA remaining: 18 minutes; breach risk: High.
Recommended action: Engage L2 API owner and initiate rollback check.

## 11. Future Enhancements

- Multilingual alert generation.
- Group-specific style customization.
- Retrieval-augmented context from known incident resolutions.
- Lightweight causal explanation for recommendation rationale.