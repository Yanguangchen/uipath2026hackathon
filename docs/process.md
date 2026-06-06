# Process and Diagrams

This file is the canonical place for TrustFlow workflow diagrams.

## Simple workflow

```mermaid
flowchart TD
    A[Customer submits form] --> B[Customer Intake Agent]
    B --> C[Purpose Understanding Agent]
    C --> D[Profile Consistency Agent]
    D --> E[Risk Signal Agent]
    E --> F{Route}
    F --> G[Clear]
    F --> H[Need More Information]
    F --> I[Escalate]
    G --> J[Audit Summary Agent]
    H --> K[Follow Up Question Agent]
    I --> L[Compliance Escalation Agent]
    K --> J
    L --> J
```

## BPMN-style process map

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

    Q2 == No or risky ==> R5
    R5 ==> H1
    H1 ==> Q3{Human decision}

    Q3 == Approve ==> H2
    H2 ==> S1
    S1 ==> S3

    Q3 == Reject ==> H3
    H3 ==> S4

    Q3 == Need more info ==> H4
    H4 ==> C3
    C3 ==> R1

    R4 ==> S5
    R6 ==> S5
```

## Process explanation

1. Customer submits the purpose form.
2. UiPath Robot extracts form data and checks required fields.
3. If information is missing, the Agent generates follow up questions.
4. If fields are complete, the Robot pulls the customer profile.
5. The Agent compares stated purpose against the customer profile.
6. Clear cases continue KYC.
7. Incomplete cases ask the customer for more information.
8. Risky or unclear cases create a human review task.
9. The KYC system stores the final outcome and audit trail.

## Human review flow

```mermaid
flowchart TD
    A[Compliance officer receives task] ==> B[Review customer purpose]
    B ==> C[Review Agent recommendation]
    C ==> D[Review supporting documents]
    D ==> E{Decision}

    E == Approve ==> F[Mark purpose clear]
    E == Reject ==> G[Stop onboarding]
    E == Need more info ==> H[Request more information]
    E == Senior review ==> I[Escalate to senior compliance]
```

## Audit trail flow

```mermaid
flowchart TD
    A[Case created] ==> B[Log form submission]
    B ==> C[Log Robot extraction]
    C ==> D[Log Agent recommendation]
    D ==> E[Log business rule result]
    E ==> F{Human review needed}

    F == No ==> G[Log final automated status]
    F == Yes ==> H[Log human decision]

    G ==> I[Store audit record]
    H ==> I
```
