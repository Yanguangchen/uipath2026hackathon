# Nisrag Agentic Coding Brief

## Project Name

AML KYC Understand Purpose Automation using UiPath Agents

## Purpose of This Document

This document gives Nisrag the full context and task scope for the agentic coding part of the hackathon project.

The goal is to keep the project simple, clear, and demo friendly.

This is not meant to be a full production AML system.

## One Sentence Summary

We are building a simple UiPath Agent workflow that helps understand a customer purpose during KYC onboarding, checks whether the purpose makes sense, asks follow up questions if needed, and escalates unclear or risky cases to a human reviewer.

## Project Context

In AML KYC onboarding, companies need to understand why a customer wants to use their service.

This is called the Understand Purpose step.

The customer may say they want to use the service for personal use, business payments, remittance, investment, trading, payroll, supplier payments, or other reasons.

The compliance team must check whether the stated purpose makes sense when compared with the customer profile.

Example:

A salaried employee saying they want an account for salary savings and local spending is usually reasonable.

A student saying the account is for personal use but expecting SGD 80000 monthly overseas transfers is unusual and should be escalated.

The purpose of this project is to use UiPath Agents to assist this reasoning process.

The Agent should not make final high risk approvals.

The Agent should only summarize, detect issues, ask follow up questions, and recommend next action.

## Hackathon Scope

This hackathon project only focuses on one small part of AML KYC.

The scope is:

1. Collect customer purpose information

2. Let the UiPath Robot extract structured fields

3. Send the extracted data to the UiPath Agent

4. Let the Agent assess whether the purpose makes sense

5. Let the Agent return a simple recommendation

6. Let the system either continue, ask for more information, or escalate to human review

The project should stay simple.

## Out of Scope

Do not build these features for the hackathon version:

1. Full sanctions screening

2. Full PEP screening

3. Full adverse media screening

4. Full beneficial ownership checks

5. Real bank integration

6. Real transaction monitoring

7. Complex AML risk scoring

8. Real regulatory filing

9. Production grade identity verification

10. Complex document fraud detection

11. Complicated case management system

The demo should only focus on the Understand Purpose step.

## Main Actors

### Customer

The customer fills in a purpose form.

### UiPath Robot

The Robot handles structured automation.

Responsibilities:

1. Read the submitted form

2. Extract fields

3. Check for missing required fields

4. Send data to the UiPath Agent

5. Receive the Agent output

6. Save the result

7. Create a human review task if required

### UiPath Agent

The Agent handles reasoning.

Responsibilities:

1. Read the customer purpose

2. Summarize the purpose

3. Compare the purpose with the customer profile

4. Detect missing information

5. Detect mismatches

6. Generate follow up questions

7. Recommend a status

8. Explain the reason for the recommendation

### Compliance Officer

The human reviewer handles unclear or risky cases.

Responsibilities:

1. Review escalated cases

2. Approve the case

3. Reject the case

4. Request more information

## Simple Workflow

1. Customer submits purpose form

2. UiPath Robot extracts form data

3. Robot checks required fields

4. If fields are missing, Agent generates follow up questions

5. If fields are complete, Robot sends customer profile and purpose data to Agent

6. Agent assesses the purpose

7. Agent returns a structured response

8. Robot updates the case status

9. Clear cases continue KYC

10. Unclear cases ask the customer for more information

11. Risky cases go to human review

## Core Agent Task

Nisrag should build the agentic logic for the Understand Purpose assessment.

The Agent should take customer data as input and return a structured assessment.

The Agent should answer these questions:

1. What is the customer trying to do?

2. Is the purpose clear?

3. Is any key information missing?

4. Does the purpose match the customer profile?

5. Does the expected transaction amount make sense?

6. Does the expected transaction frequency make sense?

7. Do the countries involved make sense?

8. Is the source of funds explained?

9. Should the system continue, ask for more information, or escalate?

## Agent Input Fields

The Agent should expect data in this structure.

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

## Agent Output Fields

The Agent should return data in this structure.

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

## Allowed Recommendation Statuses

Use only these three main statuses for the hackathon.

### Clear

Use this when the purpose is complete and reasonable.

Example:

A salaried employee wants to use the account for salary savings and local spending.

### Need More Information

Use this when the purpose may be reasonable, but important information is missing or unclear.

Example:

A business owner says the purpose is business payments but does not explain the business activity.

### Escalate

Use this when the purpose looks unusual, inconsistent, risky, or difficult to explain.

Example:

A student says the account is for personal use but expects SGD 80000 monthly overseas transfers.

## Simple Rule Logic

The Agent should use simple rules and reasoning.

### Missing Information Checks

Recommend Need More Information when:

1. Stated purpose is empty

2. Expected monthly volume is missing

3. Countries involved are missing

4. Source of funds summary is missing

5. Business customer has no business activity

6. Supporting document is needed but not provided

### Mismatch Checks

Recommend Escalate when:

1. Personal use has unusually high transaction volume

2. Customer profile does not match the stated purpose

3. Countries involved do not match the explanation

4. Source of funds is vague for a high amount

5. Customer answers are inconsistent

6. The Agent has low confidence

### Clear Checks

Recommend Clear when:

1. Purpose is specific

2. Expected amount is reasonable

3. Expected frequency is reasonable

4. Countries involved make sense

5. Source of funds is explained

6. No major mismatch is found

## Recommended Demo Cases

Build or prepare the Agent around these three simple demo cases.

### Demo Case One: Clear

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

### Demo Case Two: Need More Information

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

### Demo Case Three: Escalate

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

## Suggested Agent Prompt

```text
You are an AML KYC Understand Purpose assistant.

Your job is to assess whether the customer stated purpose makes sense.

You must read the customer profile, stated purpose, expected monthly volume, transaction frequency, countries involved, source of funds summary, and supporting documents.

You must return a structured JSON response.

You must not make final approval decisions for unclear or risky cases.

You can only recommend one of these statuses:

1. Clear
2. Need More Information
3. Escalate

Use Clear only when the purpose is specific, complete, and consistent with the customer profile.

Use Need More Information when important information is missing or unclear.

Use Escalate when the purpose appears unusual, inconsistent, risky, or when human review is needed.

Always provide:

1. Purpose summary
2. Missing information
3. Possible mismatch
4. Follow up questions
5. Recommended status
6. Reason for recommendation
7. Confidence level
8. Human review required
```

## Simple Pseudocode

```text
receive customer case data

check missing fields

if important fields are missing:
    return Need More Information

summarize stated purpose

compare stated purpose with customer profile

check expected monthly volume

check transaction frequency

check countries involved

check source of funds summary

if major mismatch is found:
    return Escalate

if no major issue is found:
    return Clear
```

## Expected Deliverables from Nisrag

1. Agent prompt

2. Agent input schema

3. Agent output schema

4. Simple assessment logic

5. Three working demo cases

6. JSON output for each case

7. Integration point for UiPath Robot

8. Basic error handling for invalid or incomplete inputs

## Integration Notes

The Robot should send the customer case data to the Agent.

The Agent should return structured JSON.

The Robot should read the field recommended_status.

If recommended_status is Clear:

Continue KYC.

If recommended_status is Need More Information:

Send follow up questions to the customer.

If recommended_status is Escalate:

Create a human review task.

## Error Handling

If the Agent output is not valid JSON:

1. Retry once

2. If still invalid, send to human review

If the input is missing required fields:

1. Return Need More Information

2. List missing fields

3. Generate follow up questions

If the Agent is unsure:

1. Set confidence_level to Low

2. Set human_review_required to true

3. Recommend Escalate

## What Success Looks Like

The hackathon demo is successful if we can show:

1. A customer submits purpose information

2. UiPath Robot extracts and sends data to the Agent

3. UiPath Agent understands the case

4. Agent returns a clear structured recommendation

5. The system asks follow up questions or escalates when needed

6. Human review is kept in the loop for risky cases

## Final Reminder

Keep the project simple.

Do not overbuild the AML logic.

The goal is to demonstrate agentic automation, not to build a complete compliance platform.

The best version of this hackathon project is a clean workflow that clearly shows:

Robot handles process.

Agent handles reasoning.

Human handles final risky decisions.
