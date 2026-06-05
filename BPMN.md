# BPMN Map

## AML KYC Understand Purpose BPMN Style Map

This file contains the BPMN style process map for the Understand Purpose stage using UiPath Robots, UiPath Agents, Compliance Officer review, and the KYC System.

## BPMN Mermaid Preview

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
```

## BPMN Process Explanation

1. The customer submits the purpose form.

2. UiPath Robot extracts the form data.

3. UiPath Robot checks whether required information is missing.

4. If information is missing, UiPath Agent generates follow up questions.

5. Customer answers the questions or uploads documents.

6. UiPath Robot pulls the customer profile.

7. UiPath Agent compares the stated purpose against the customer profile.

8. If the purpose is clear, the Robot saves the purpose summary and continues KYC.

9. If the purpose is unclear or risky, the Robot creates an Action Center task.

10. Compliance Officer reviews the case.

11. The case is approved, rejected, or sent back for more information.

12. The KYC System stores the final outcome and audit trail.
