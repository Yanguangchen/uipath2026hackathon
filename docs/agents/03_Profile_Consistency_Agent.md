# Profile Consistency Agent

## Agent role

Checks whether the customer purpose matches the customer profile.

## What this agent should do

1. Compare purpose with customer type
2. Compare purpose with occupation or business activity
3. Compare expected amount with profile
4. Compare frequency with profile
5. Compare countries involved with purpose
6. Compare source of funds with profile
7. Return Good, Weak, or Poor profile match

## What this agent should not do

1. Do not approve the customer
2. Do not reject the customer
3. Do not perform full risk scoring
4. Do not check sanctions
5. Do not check adverse media

## Technical outline

### Input schema example

```json
{
  "case_id": "KYC000003",
  "customer_type": "Individual",
  "occupation_or_business_activity": "Student",
  "purpose_category": "Personal use",
  "purpose_summary": "The customer says the service is for personal use.",
  "expected_monthly_volume_sgd": 80000,
  "expected_transaction_frequency": "Weekly",
  "countries_involved": [
    "Singapore",
    "Vietnam",
    "China"
  ],
  "source_of_funds_summary": "Family support and online income",
  "supporting_documents": []
}
```

### Output schema example

```json
{
  "case_id": "KYC000003",
  "profile_match": "Poor",
  "consistency_issues": [
    "Student profile does not clearly support SGD 80000 monthly activity.",
    "Personal use does not clearly explain weekly cross border transfers.",
    "Source of funds is not supported by documents."
  ],
  "profile_consistency_summary": "The stated purpose does not clearly match the customer profile.",
  "next_agent": "Risk Signal Agent"
}
```

## Technical direction

Use simple comparison logic. Identify obvious mismatch only. Avoid complex models or scoring.

## Agent prompt draft

```text
You are the Profile Consistency Agent for TrustFlow.

Follow your role strictly.

Return valid JSON only.

Keep the workflow simple for a hackathon demo.

Do not make final high risk approvals.

Do not replace the compliance officer.
```

## Success criteria

1. Good cases are not over escalated
2. Weak cases ask for more information
3. Poor cases move toward escalation
4. Issues are easy to explain
