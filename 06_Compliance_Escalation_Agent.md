# TrustFlow

# Compliance Escalation Agent

## Project context

TrustFlow is a hackathon project for AML KYC onboarding.

The project focuses only on the Understand Purpose step.

The system helps check why a customer wants to use a service, whether the explanation makes sense, whether more information is needed, and whether the case should be escalated to a human compliance reviewer.

This is not a full AML system. The goal is to demonstrate a simple Maestro workflow with multiple agents.

Slogan: Trust made simple. Compliance made faster.

Design principle: Robot handles process. Agent handles reasoning. Human handles final risky decisions.


## Agent role

Prepares a human review pack for the compliance officer.

## What this agent should do

1. Summarize the customer case
2. Summarize the reason for escalation
3. List key risk signals
4. List missing information
5. Suggest review points for the compliance officer
6. Prepare output for the human review screen

## What this agent should not do

1. Do not approve the case
2. Do not reject the case
3. Do not make legal conclusions
4. Do not create suspicious activity reports
5. Do not replace the compliance officer

## Technical outline

### Input schema example

```json
{
  "case_id": "KYC000003",
  "customer_name": "Example Student",
  "customer_type": "Individual",
  "occupation_or_business_activity": "Student",
  "stated_purpose": "Personal use",
  "expected_monthly_volume_sgd": 80000,
  "expected_transaction_frequency": "Weekly",
  "countries_involved": [
    "Singapore",
    "Vietnam",
    "China"
  ],
  "source_of_funds_summary": "Family support and online income",
  "supporting_documents": [],
  "risk_signals": [
    "High monthly volume",
    "Poor profile match",
    "No supporting documents"
  ],
  "recommended_status": "Escalate"
}
```

### Output schema example

```json
{
  "case_id": "KYC000003",
  "escalation_title": "Review required for high volume personal use case",
  "case_summary": "The customer is a student who states the service is for personal use but expects SGD 80000 monthly activity with weekly cross border transactions.",
  "reason_for_escalation": "The expected transaction activity does not clearly match the stated purpose or customer profile.",
  "key_review_points": [
    "High monthly volume",
    "Student profile",
    "Cross border activity",
    "No supporting documents"
  ],
  "suggested_human_actions": [
    "Request source of funds documents",
    "Ask for explanation of overseas transfers",
    "Decide whether to approve, reject, or request more information"
  ],
  "human_review_required": true
}
```

## Technical direction

Create a clean review pack for a demo human review screen. Keep it short and neutral.

## Agent prompt draft

```text
You are the Compliance Escalation Agent for TrustFlow.

Follow your role strictly.

Return valid JSON only.

Keep the workflow simple for a hackathon demo.

Do not make final high risk approvals.

Do not replace the compliance officer.
```

## Success criteria

1. Escalation reason is clear
2. Human review points are useful
3. Language is neutral
4. Output can be shown directly in the review screen
