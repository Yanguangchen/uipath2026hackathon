from pathlib import Path

readme = """# AML KYC Understand Purpose Automation

## Project Overview

This project automates the Understand Purpose step inside an AML KYC onboarding workflow using UiPath Agents, UiPath Robots, and human review.

The goal is to help the compliance team understand why a customer wants to use the service, whether the stated purpose makes sense, and whether the case should continue, request more information, or be escalated.

The UiPath Agent is used for reasoning and explanation. The UiPath Robot is used for structured automation, system actions, form extraction, validation, queue handling, and record updates. The compliance officer remains responsible for unclear, inconsistent, or high risk cases.

## Important Notice

This documentation is a system design guide only. It is not legal advice. AML rules differ by jurisdiction. The final implementation should be reviewed by compliance, legal, and data protection teams before production use.

## Objectives

1. Collect the customer stated purpose.

2. Extract key purpose details from forms or documents.

3. Compare the stated purpose against the customer profile.

4. Identify missing information, unclear explanations, and possible mismatches.

5. Ask follow up questions when information is incomplete.

6. Escalate risky or unclear cases to a compliance officer.

7. Store a clear audit trail of every decision and action.

## Scope

This README covers the Understand Purpose stage only.

The full AML KYC process may include identity verification, sanctions screening, PEP screening, adverse media checks, beneficial ownership checks, source of funds checks, source of wealth checks, transaction monitoring, periodic review, and suspicious activity escalation.

## Main Users

1. Customer

The customer submits the purpose form and provides supporting information when requested.

2. UiPath Robot

The Robot handles repeatable tasks such as reading forms, extracting structured data, validating required fields, updating systems, sending notifications, and creating human review tasks.

3. UiPath Agent

The Agent reads the customer purpose, compares it with known profile data, identifies inconsistencies, generates follow up questions, and recommends a purpose status.

4. Compliance Officer

The compliance officer reviews escalated cases, approves reasonable cases, rejects unsuitable cases, or requests more information.

5. KYC System

The KYC system stores the customer profile, purpose summary, risk status, review outcome, evidence, and audit logs.

## High Level AML KYC Checklist

1. Collect customer details.

2. Verify identity.

3. Understand purpose.

4. Check source of funds.

5. Screen customer.

6. Rate risk.

7. Approve, reject, or escalate.

8. Monitor activity.

## Understand Purpose Data Fields

The customer form should capture the following fields.

1. Customer type

Examples: Individual, company, sole proprietor, partnership, trust.

2. Stated purpose

Examples: Personal account, business payment, remittance, investment, trading, payroll, supplier payment, platform usage.

3. Expected transaction amount

Examples: Monthly expected volume, average transaction amount, maximum single transaction amount.

4. Expected frequency

Examples: Daily, weekly, monthly, occasional, one time.

5. Countries involved

Examples: Singapore only, regional payments, overseas suppliers, cross border remittance.

6. Source of funds summary

Examples: Salary, business revenue, savings, investment income, sale proceeds.

7. Customer profile reference

Examples: Occupation, business activity, industry, income range, company size, registered address.

8. Supporting documents

Examples: Invoice, contract, bank statement, payslip, business registration, supplier agreement.

## Purpose Status

The automation should classify the Understand Purpose stage into one of these statuses.

1. Clear

The purpose is understandable, complete, and consistent with the customer profile.

2. Need More Information

The purpose may be legitimate, but important information is missing or unclear.

3. Escalate

The purpose is inconsistent, risky, unusual, or cannot be reasonably explained by the available information.

4. Reject

The customer cannot provide a reasonable explanation, refuses required information, or fails internal policy checks.

## Simple Risk Examples

1. Low risk example

A salaried employee opens an account for personal savings and local card payments.

2. Medium risk example

A small business account expects overseas supplier payments and provides invoices and business documents.

3. High risk example

A student claims the account is for personal use but expects large international transfers from unrelated third parties.

4. Escalation example

A company has a vague business purpose, complex ownership, overseas counterparties, and incomplete supporting documents.

## UiPath Component Design

### UiPath Robot

The Robot performs predictable workflow actions.

Responsibilities:

1. Read submitted form data.

2. Extract customer purpose fields.

3. Validate required fields.

4. Pull customer profile from the KYC system or CRM.

5. Send case data to the UiPath Agent.

6. Save the Agent recommendation.

7. Create Action Center task when human review is needed.

8. Update the KYC system after final decision.

9. Store audit logs.

### UiPath Agent

The Agent performs reasoning tasks.

Responsibilities:

1. Interpret the customer purpose in plain language.

2. Compare the stated purpose against profile data.

3. Identify mismatch, missing detail, or unclear wording.

4. Generate follow up questions.

5. Recommend purpose status.

6. Explain the reason for the recommendation.

7. Escalate when confidence is low or policy rules are triggered.

Important rule:

The Agent can recommend. It should not be the final approver for high risk or unclear AML cases.

### Compliance Officer

The compliance officer performs judgment based review.

Responsibilities:

1. Review escalated cases.

2. Check the customer explanation and documents.

3. Approve, reject, or request more information.

4. Add review notes.

5. Confirm final purpose status.

## BPMN Style Mermaid Map

```mermaid
flowchart LR
    Start((Start)) ==> C1[Customer submits purpose form]

    subgraph Customer
        C1[Submit purpose form]
        C2[Answer follow up questions]
        C3[Upload supporting documents]
    end

    subgraph UiPath_Robot
        R1[Extract form data]
        R2[Check required fields]
        R3[Pull customer profile]
        R4[Save purpose summary]
        R5[Create Action Center task]
        R6[Update KYC system]
    end

    subgraph UiPath_Agent
        A1[Read customer purpose]
        A2[Compare purpose with profile]
        A3[Detect mismatch or unclear reason]
        A4[Generate follow up questions]
        A5[Recommend purpose status]
    end

    subgraph Compliance_Officer
        H1[Review escalated case]
        H2[Approve]
        H3[Reject]
        H4[Request more information]
    end

    subgraph KYC_System
        S1[Purpose marked clear]
        S2[Purpose marked unclear]
        S3[Continue KYC process]
        S4[Stop onboarding]
        S5[Audit log stored]
    end

    C1 ==> R1
    R1 ==> R2
    R2 ==> Q1{Missing information}

    Q1 == Yes ==> A4
    A4 ==> C2
    C2 ==> R1

    Q1 == No ==> R3
    R3 ==> A1
    A1 ==> A2
    A2 ==> A3

    A3 ==> Q2{Purpose makes sense}

    Q2 == Yes ==> A5
    A5 ==> R4
    R4 ==> S1
    S1 ==> S3

    Q2 == No_or_Risky ==> R5
    R5 ==> H1
    H1 ==> Q3{Human decision}

    Q3 == Approve ==> H2
    H2 ==> S1
    S1 ==> S3

    Q3 == Reject ==> H3
    H3 ==> S4

    Q3 == Need_more_info ==> H4
    H4 ==> C3
    C3 ==> R1

    R4 ==> S5
    R6 ==> S5
