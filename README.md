# TrustFlow

TrustFlow is a hackathon project for AML KYC onboarding. It focuses only on the Understand Purpose step: checking why a customer wants to use a service, whether the explanation makes sense, whether more information is needed, and whether risky or unclear cases should go to a human compliance reviewer.

Slogan: Trust made simple. Compliance made faster.

Design principle: Robot handles process. Agent handles reasoning. Human handles final risky decisions.

## Important notice

This repository is a system design and demo guide only. It is not legal advice and it is not a production AML program. Any real implementation should be reviewed by compliance, legal, security, and data protection teams.

## Documentation map

1. [Process and diagrams](docs/process.md)
2. [Implementation brief](docs/implementation-brief.md)
3. [Agent specifications](docs/agents/)

## Scope

TrustFlow handles only the Understand Purpose stage of AML KYC onboarding.

In scope:

1. Collect customer purpose information
2. Extract structured fields
3. Compare purpose against customer profile
4. Detect missing, vague, inconsistent, or risky explanations
5. Ask follow up questions when information is incomplete
6. Escalate risky or unclear cases to a compliance officer
7. Store a clear audit trail

Out of scope:

1. Sanctions screening
2. PEP screening
3. Adverse media checks
4. Beneficial ownership checks
5. Source of wealth checks
6. Real bank integration
7. Transaction monitoring
8. Regulatory filing
9. Production identity verification

## Main workflow

1. Customer submits purpose information.
2. UiPath Robot extracts and validates structured fields.
3. Customer Intake Agent cleans the case.
4. Purpose Understanding Agent explains the purpose.
5. Profile Consistency Agent compares the purpose with the profile.
6. Risk Signal Agent recommends `Clear`, `Need More Information`, or `Escalate`.
7. Maestro routes the case to audit logging, follow up questions, or human review.

## Agents

| Order | Agent | Responsibility |
| --- | --- | --- |
| 00 | [Maestro Orchestrator Agent](docs/agents/00_Maestro_Orchestrator_Agent.md) | Routes the case through the workflow |
| 01 | [Customer Intake Agent](docs/agents/01_Customer_Intake_Agent.md) | Cleans customer form data and detects missing fields |
| 02 | [Purpose Understanding Agent](docs/agents/02_Purpose_Understanding_Agent.md) | Summarizes and classifies the stated purpose |
| 03 | [Profile Consistency Agent](docs/agents/03_Profile_Consistency_Agent.md) | Checks whether purpose matches the customer profile |
| 04 | [Risk Signal Agent](docs/agents/04_Risk_Signal_Agent.md) | Finds simple risk signals and recommends a route |
| 05 | [Follow Up Question Agent](docs/agents/05_Follow_Up_Question_Agent.md) | Generates customer questions for incomplete cases |
| 06 | [Compliance Escalation Agent](docs/agents/06_Compliance_Escalation_Agent.md) | Prepares the human review pack |
| 07 | [Audit Summary Agent](docs/agents/07_Audit_Summary_Agent.md) | Creates the final audit summary |
| 08 | [Demo Case Generator Agent](docs/agents/08_Demo_Case_Generator_Agent.md) | Creates fictional demo cases |

## Data fields

The customer form should capture:

1. `customer_type`
2. `occupation_or_business_activity`
3. `stated_purpose`
4. `expected_monthly_volume_sgd`
5. `expected_transaction_frequency`
6. `countries_involved`
7. `source_of_funds_summary`
8. `supporting_documents`
9. `customer_profile_notes`

## Recommendation statuses

`Clear`: The purpose is specific, complete, and consistent with the customer profile.

`Need More Information`: The purpose may be reasonable, but required information is missing or unclear.

`Escalate`: The purpose is unusual, inconsistent, risky, unsupported, or needs human judgment.

## Demo cases

Use the three cases in [Implementation brief](docs/implementation-brief.md#demo-cases):

1. Clear salaried employee case
2. Need More Information company profile case
3. Escalate high volume student case
