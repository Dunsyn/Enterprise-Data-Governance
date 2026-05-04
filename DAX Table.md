# DAX Table

Power Query prep (minimum required transformations) - Do these in Transform data:
* Ensure numeric types: Completeness Percent, Accuracy Percent, Data Quality Score, Compliance Risk Score, Estimated Fine EUR, Remediation Cost EUR, Open Issues Count, Audit Findings Count, Days Since Last Audit
* Convert flags to consistent values (Yes/No): GDPR Relevant, Masking Required, Critical Data Element, Duplicate Flag, Consistency Flag
* Trim/clean text columns (owners, stewards, systems)
* Create calculated columns & measures: Financial Exposure EUR = Estimated Fine EUR + Remediation Cost EUR

# Data Overview

Total Data Records = DISTINCTCOUNT ( Fact_DataGovernance[RecordID] )

Critical Data Elements = CALCULATE (
    [Total Data Records],
    Fact_DataGovernance[Critical Data Element] = "Yes")

GDPR Relevant Records =
CALCULATE(
    [Total Data Records],
    Fact_DataGovernance[GDPR Relevant] = "Yes"
)

Estimated Fine EUR =
SUM(Fact_DataGovernance[Estimated Fine EUR])

Remediation Cost EUR =
SUM(Fact_DataGovernance[Remediation Cost EUR])

Financial Exposure EUR =
SUM ( Fact_DataGovernance[Estimated Fine EUR] ) + SUM ( Fact_DataGovernance[Remediation Cost EUR] )

# Data Quality

Avg Data Quality Score =
AVERAGE ( Fact_DataGovernance[Data Quality Score] )

Avg Completeness % =
AVERAGE ( Fact_DataGovernance[Completeness Percent] )

Avg Accuracy % =
AVERAGE ( Fact_DataGovernance[Accuracy Percent] )

Consistency Rate =
DIVIDE(
    CALCULATE(
        COUNTROWS(Fact_DataGovernance),
        Fact_DataGovernance[Consistency Flag] = "Yes"
    ),
    [Total Data Records],
    0
)

Flagged Quality Assets =
CALCULATE (
    [Total Data Records],
    Fact_DataGovernance[Duplicate Flag] = "Yes"
        || Fact_DataGovernance[Consistency Flag] = "No")

[Duplicate Records] =
CALCULATE(
    COUNTROWS(Fact_DataGovernance),
    Fact_DataGovernance[Duplicate Flag] = "Yes"
)

[Duplicate Rate %] =
DIVIDE(
    CALCULATE(
        COUNTROWS(Fact_DataGovernance),
        Fact_DataGovernance[Duplicate Flag] = "Yes"
    ),
    COUNTROWS(Fact_DataGovernance),
    0
)

Avg Governance Maturity =
  AVERAGE(Fact_DataGovernance[Governance Maturity Score])


# Risk and Compliance

Open Issues = SUM ( Fact_DataGovernance[Open Issues Count] )

High Risk Records =
CALCULATE (
    [Total Data Records],
    Fact_DataGovernance[Compliance Risk Score] >= 70)

Low Risk Records =
CALCULATE (
    [Total Assets],
    Fact_DataGovernance[Compliance Risk Score] < 40)

Risk Tier =
SWITCH (
    TRUE(),
    Fact_DataGovernance[Compliance Risk Score] >= 70, "High",
    Fact_DataGovernance[Compliance Risk Score] >= 40, "Medium",
    "Low")

GDPR Relevant Records=
CALCULATE ( [Total Data Records], Fact_DataGovernance[GDPR Relevant] = "Yes" )

Governance Maturity Score=
DIVIDE(
    [Avg Data Quality Score] + (100 - [Avg Compliance Risk Score]),
    2)

Compliance rate = DIVIDE([Compliant Fields], [Total Data Records], 0)

Compliant Records =
CALCULATE(
    COUNTROWS(Fact_DataGovernance),
    Fact_DataGovernanceCompliance Status] = "Compliant"
)

Non-Compliant Records = 
CALCULATE(
    COUNTROWS(Fact_DataGovernance),
    Fact_DataGovernance[Compliance Status] = "Non-Compliant"
)

At Risk Records =
CALCULATE(
    [Total Data Records],
    Fact_DataGovernance[Compliance Status] = "At Risk"
)

Avg Compliance Risk Score =
AVERAGE ( Fact_DataGovernance[Compliance Risk Score] )


Avg Days Since Last Audit =
AVERAGE(Fact_DataGovernance[Days Since Last Audit])

Overdue Audits =
CALCULATE(
    [Total Data Records],
    Fact_DataGovernance[Days Since Last Audit] > 365
)
Poor Audit Records =
CALCULATE (
    [Total Assets],
    Fact_Governance[Audit Rating] IN { "Poor", "Fail", "High Concern" })

Overdue Audits (365d+) =
CALCULATE (
    [Total Assets],
    Fact_DataGovernance[Days Since Last Audit] >= 365)

Audit Recency Band =
SWITCH (
    TRUE(),
    Fact_DataGovernance[Days Since Last Audit] < 90, "0-89 days",
    Fact_DataGovernance[Days Since Last Audit] < 180, "90-179 days",
    Fact_DataGovernance[Days Since Last Audit] < 365, "180-364 days",
    "365+ days")

Trust Label =
VAR dq = Fact_DataGovernance[Data Quality Score]
VAR risk = Fact_DataGovernance[Compliance Risk Score]
VAR issues = Fact_DataGovernance[Open Issues Count]
RETURN
SWITCH(
    TRUE(),
    dq >= 95 && risk < 40 && issues = 0, "High Trust",
    dq >= 90 && risk < 70, "Use With Caution",
    "Low Trust")
    
[Overexposed Fields] =
CALCULATE(
    COUNTROWS(Fact_DataGovernance),
    Fact_DataGovernance[Access Risk Flag] = "OVER-PERMISSIONED"
)

Governance Action =
SWITCH(
  TRUE(),
  -- Action 1: GDPR access violation — highest priority
  Fact_DataGovernance[GDPR Relevant] = "Yes"
    && Fact_DataGovernance[Access_level] IN {"Admin", "Write"},
  "Escalate GDPR access violation",
  -- Action 2: Non-compliant + poor audit = no owner
  Fact_DataGovernance[Compliance Status] = "Non-Compliant"
    && Fact_DataGovernance[Audit Rating] IN {"D", "E"},
  "Assign data owner urgently",
  -- Action 3: Overdue audit on critical fields
  Fact_DataGovernance[Days Since Last Audit] > 365
    && Fact_DataGovernance[Critical Data Element] = "Yes",
  "Trigger immediate re-audit",
  -- Action 4: Masking required but at risk
  Fact_DataGovernance[Masking Required] = "Yes"
    && Fact_DataGovernance[Compliance Status] = "At Risk",
  "Enforce masking and retention",
  
  # Dynamic KPI Selector Measures
  
  Selected KPI Value =
SWITCH(
    SELECTEDVALUE('KPI Selector'[KPI]),
    "Accuracy", [Avg Accuracy],
    "Completeness", [Avg Completeness],
    "Consistency Rate", [Consistency Rate],
    "Duplicate Rate", [Duplicate %]
)

Selected KPI Title =
VAR kpi = SELECTEDVALUE('KPI Selector'[KPI], "Accuracy")
RETURN
"Data " & KPI & " Trend"

Title 1 =
VAR KPI = SELECTEDVALUE('KPI Selector'[KPI], "Accuracy")
RETURN
"Data Owner with Data " & KPI

Title 2 =
VAR KPI = SELECTEDVALUE('KPI Selector'[KPI], "Accuracy")
RETURN
"Data " & KPI & " by Classification"

# Calculated Columns

Retention Years =
SWITCH(
    TRUE(),
    ISBLANK(Fact_DataGovernance[Retention Policy Years]), "Unknown",
    Fact_DataGovernance[Retention Policy Years] <= 3, "1–3 yrs",
    Fact_DataGovernance[Retention Policy Years] <= 7, "4–7 yrs",
    "7+ yrs"
)


