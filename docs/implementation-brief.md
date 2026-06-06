# Implementation Brief

This brief keeps the build guidance, schemas, rule logic, and demo cases in one place.

## Objective

Build a simple UiPath Agent workflow that helps assess whether a customer's stated purpose makes sense during KYC onboarding. The Agent should summarize, detect issues, ask follow up questions, and recommend the next action. It should not make final approval decisions for unclear or risky cases.

## Agent input schema

```json
{
  "case_id": "KYC000001",
  "customer_name": "Example Customer",
  "customer_type": "Individual",
  "occupation_or_business_activity": "Student",
  "stated_purpose": "Personal use",
  "expected_monthly_volume_sgd": 80000,
  "expected_transaction_frequency": "Weekly",
  "countries_involved": ["Singapore", "Vietnam", "China"],
  "source_of_funds_summary": "Family support and online income",
  "supporting_documents": [],
  "customer_profile_notes": "Young student with no declared business activity"
}
```

## Agent output schema

```json
{
  "case_id": "KYC000001",
  "purpose_summary": "The customer says the account is for personal use, but expects high monthly cross border activity.",
  "missing_information": [
    "Reason for high monthly transaction volume",
    "Evidence of online income",
    "Reason for transfers involving Vietnam and China"
  ],
  "possible_mismatch": "The stated personal use purpose does not clearly match the expected transaction amount and frequency.",
  "follow_up_questions": [
    "Please explain why personal use requires SGD 80000 in monthly transactions.",
    "Please provide supporting documents for your online income.",
    "Please explain why transfers involving Vietnam and China are expected."
  ],
  "recommended_status": "Escalate",
  "reason_for_recommendation": "The transaction volume is high for the stated purpose and supporting documents are missing.",
  "confidence_level": "Medium",
  "human_review_required": true
}
```

## Recommendation logic

Use only these recommendation statuses:

`Clear`: Purpose is specific, complete, reasonable, and consistent with the customer profile.

`Need More Information`: Important information is missing or unclear, but the case may still be reasonable.

`Escalate`: Purpose is unusual, inconsistent, risky, unsupported, or the Agent has low confidence.

## Simple rules

Recommend `Need More Information` when:

1. Stated purpose is empty
2. Expected monthly volume is missing
3. Countries involved are missing
4. Source of funds summary is missing
5. Business customer has no business activity
6. Supporting document is needed but not provided

Recommend `Escalate` when:

1. Personal use has unusually high transaction volume
2. Customer profile does not match the stated purpose
3. Countries involved do not match the explanation
4. Source of funds is vague for a high amount
5. Customer answers are inconsistent
6. Agent confidence is low

Recommend `Clear` when:

1. Purpose is specific
2. Expected amount is reasonable
3. Expected frequency is reasonable
4. Countries involved make sense
5. Source of funds is explained
6. No major mismatch is found

## Demo cases

### Clear

Input:

```json
{
  "case_id": "KYC000001",
  "customer_name": "Tan Wei Ming",
  "customer_type": "Individual",
  "occupation_or_business_activity": "Software Engineer",
  "stated_purpose": "Salary savings and local spending",
  "expected_monthly_volume_sgd": 5000,
  "expected_transaction_frequency": "Monthly",
  "countries_involved": ["Singapore"],
  "source_of_funds_summary": "Monthly salary",
  "supporting_documents": ["Payslip"],
  "customer_profile_notes": "Full time employee"
}
```

Expected result:

```json
{
  "recommended_status": "Clear",
  "human_review_required": false
}
```

### Need More Information

Input:

```json
{
  "case_id": "KYC000002",
  "customer_name": "ABC Trading",
  "customer_type": "Company",
  "occupation_or_business_activity": "",
  "stated_purpose": "Business payments",
  "expected_monthly_volume_sgd": 20000,
  "expected_transaction_frequency": "Weekly",
  "countries_involved": ["Singapore", "Malaysia"],
  "source_of_funds_summary": "Business revenue",
  "supporting_documents": [],
  "customer_profile_notes": "Company profile is incomplete"
}
```

Expected result:

```json
{
  "recommended_status": "Need More Information",
  "human_review_required": false
}
```

### Escalate

Input:

```json
{
  "case_id": "KYC000003",
  "customer_name": "Example Student",
  "customer_type": "Individual",
  "occupation_or_business_activity": "Student",
  "stated_purpose": "Personal use",
  "expected_monthly_volume_sgd": 80000,
  "expected_transaction_frequency": "Weekly",
  "countries_involved": ["Singapore", "Vietnam", "China"],
  "source_of_funds_summary": "Family support and online income",
  "supporting_documents": [],
  "customer_profile_notes": "Student with no declared business activity"
}
```

Expected result:

```json
{
  "recommended_status": "Escalate",
  "human_review_required": true
}
```

## Prompt guardrails

```text
You are an AML KYC Understand Purpose assistant.

Return valid JSON only.

You may recommend Clear, Need More Information, or Escalate.

Do not make final approval decisions for unclear or risky cases.

Use Clear only when the purpose is specific, complete, and consistent with the customer profile.

Use Need More Information when important information is missing or unclear.

Use Escalate when the purpose appears unusual, inconsistent, risky, or when human review is needed.
```

## Robot integration

1. Robot sends customer case data to the Agent.
2. Agent returns structured JSON.
3. Robot reads `recommended_status`.
4. `Clear` continues KYC.
5. `Need More Information` routes to follow up questions.
6. `Escalate` creates a human review task.

## Error handling

If Agent output is not valid JSON:

1. Retry once.
2. If still invalid, send to human review.

If input is missing required fields:

1. Return `Need More Information`.
2. List missing fields.
3. Generate follow up questions.

If the Agent is unsure:

1. Set `confidence_level` to `Low`.
2. Set `human_review_required` to `true`.
3. Recommend `Escalate`.
