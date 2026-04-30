# Enterprise Data Governance — Power BI Report

# **Introduction**
This project presents an Enterprise Data Governance Analysis Dashboard built in Power BI to evaluate data quality, governance maturity, compliance risk, and financial exposure across a multi-domain organisation. The objective is to move beyond descriptive analytics and enable data trust and governance-driven decision-making. It analyses 5,500 fields, 6 business domains, 23 source systems,12 EU countries with €2.1 billion in regulatory exposure.

# **Key Features**
* Dynamic KPI selection using Field Parameters
* Data Quality framework (Accuracy, Completeness, Consistency, Duplicate Rate)
* Governance metrics (Audit findings, compliance status, open issues)
* Financial risk modelling (Exposure, remediation cost, fines)
* Data Trust classification (High Trust / Use With Caution / Low Trust)
* GDPR exposure and access control analysis
* Multi-page reporting:
  * Executive Overview
  * Data Quality
  * Risk & Compliance

# **Project Objective** 
To assess how effectively data is governed across the enterprise and identify high-risk data assets, Governance gaps, Financial exposure from non-compliance and Areas requiring immediate remediation.

# **Data Overview**
5,500 Data records, 6 business domains (Finance, HR, IT, Marketing, Operations and Sales) and 23 source systems.
Attributes include:
* Data Quality Scores
* Compliance Risk Scores
* Audit Metrics
* GDPR Relevance
* Access Levels
* Financial Exposure

# **Methodology**
1. **Data Preparation**
* Cleaned and structured governance dataset
* Created calculated columns and measures in DAX
2. **Data Modelling**
* Designed relationships for governance, risk, and quality metrics
* Implemented field parameters for dynamic KPI switching
3. **Analysis**
* Evaluated governance maturity across domains
* Identified high-risk tables and records
* Analysed audit cycles and compliance gaps
4. **Visualisation**
* Built executive dashboards using Power BI
* Applied conditional formatting, Tooltips and dynamic visuals

# **Key Insights**
1. The compliance crisis is structural, not tactical. At 88.8% non-compliant or at risk, this is a governance program maturity problem but a data quality problem. Tactical fixes will not move this number. The organisation needs foundational governance capability built from the ground up.
2. HR is the immediate regulatory priority: HR has the highest estimated fine exposure (€491M), the most non-compliant fields (542, 56.3%), the most GDPR-relevant fields (544), and the most open issues (4,698). A regulator's first port of call.
3. 771 fields are an Article 25 violation right before any breach. GDPR Article 25 (Data Protection by Design and Default) requires access to personal data be restricted to what is strictly necessary. 771 GDPR-relevant fields with Admin or Write access and no documented justification represent active violations independent of whether a breach has occurred.
4. 54 Critical Data Elements have Governance Maturity = 1. The worst combination in the dataset: fields marked as critical for business decisions sitting at the lowest possible governance level. 50 of those 54 are in Marketing; Customer_Master fields including Email, Phone, Address, and Consent_Status.
5. Stewardship concentration is a hidden risk: 1,836 fields (33.4% of the estate) are assigned to a generic "Data Gov Team" rather than a named individual. Of those, 568 are Non-Compliant. Collective ownership means zero individual accountability.
6. 372 days average audit delay highlights weak governance monitoring and the €1.78B fine figure needs a health warning: Regulators fine per incident, not per database column. This figure is a relative risk proxy but useful for prioritisation, not as a financial forecast. Always caveat before presenting to a board.

# **Key Recommendations**
1. Strengthen access controls for sensitive data
2. Reduce audit lag and enforce governance cycles
3. Improve data ownership and stewardship accountability
4. Prioritise remediation for high-risk tables
5. Introduce a Data Trust framework for decision-making

# Frameworks referenced
* DAMA-DMBOK v2 — Data Management Body of Knowledge
* DCAM (EDM Council) — Data Capability Assessment Model
* ISO 8000 — Data Quality (Completeness, Accuracy, Consistency, Uniqueness, Timeliness, Validity)
* EU GDPR — Articles 5 (storage limitation), 13 (transparency), 25 (privacy by design), 32 (security)
* NIS2 Directive — cybersecurity governance
* EU AI Act — Article 10, high-risk AI data governance
* BCBS 239 — risk data aggregation and reporting
