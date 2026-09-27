# UrbanCart — Data Quality Summary

## 1. Purpose

This document summarises the key data-quality checks, cleaning actions and outstanding issues identified during the UrbanCart marketing campaign analysis.

**Business:** UrbanCart — fictional Australian e-commerce company  
**Reporting period:** 1 January 2024 – 31 December 2025  
**Dataset size:** Approximately 50,000–100,000 records  
**Currency:** Australian dollars (AUD)

The objective was to ensure that marketing performance KPIs such as **Spend, Revenue, ROAS, ROI, CPA, CPC, CTR and Conversion Rate** were calculated using data that had been appropriately validated and cleaned.

---

## 2. Executive Summary

The source data contained several quality issues, primarily involving:

- Duplicate records
- Inconsistent channel and customer-segment labels
- Missing financial data
- Missing campaign and geographic information
- Invalid funnel values
- Negative Spend values

The data was cleaned using a controlled approach that preserved the original source values and created separate analytical fields for validated data.

### Key Findings

| Issue | Finding | Treatment |
|---|---:|---|
| Duplicate records | 150 | Validated and removed |
| Missing Campaign Name | 20 | Recovered using Campaign ID where possible |
| Missing Customer Segment | 100 | Standardised; unresolved values classified as `Unknown` |
| Missing State | 15 | Derived from City where reliably supported |
| Missing Spend | 220 | Retained as null and flagged |
| Missing Revenue | 150 | Retained as null and flagged |
| Invalid Clicks | 35 | Clicks > Impressions; excluded from cleaned KPI calculations |
| Invalid Conversions | 20 | Conversions > Clicks; excluded from cleaned KPI calculations |
| Negative Spend | 20 | Flagged and excluded from cleaned Spend |
| Email validation | No material issues identified | Retained |

---

## 3. Data Quality Rules Applied

The following business rules were used to validate the marketing funnel and financial data:

- Clicks <= Impressions
- Leads <= Clicks
- Conversions <= Clicks
- Spend >= 0
- Revenue treatment validated separately
- Email Opens <= Email Sent
- Email Clicks <= Email Opens
- Campaign dates must fall between 1 January 2024 and 31 December 2025

Records failing these rules were flagged rather than silently corrected.

---

## 4. Key Data Cleaning Actions

### Duplicate Records

A duplicate investigation was performed using the intended record grain:

**Campaign + Date + Channel + Geography + Customer**

150 records were confirmed as duplicates and removed from the analytical dataset.

The original source data was retained for audit purposes.

### Channel Standardisation

Inconsistent values such as:

- `Google Ads`
- `GoogleAds`
- `Meta Ads`
- `Facebook`
- `Email`
- `email`
- `TikTok Ads`
- `TikTokAds`

were standardised using a controlled channel mapping.

Reporting uses the standardised channel classification while retaining the original source values.

### Customer Segment Standardisation

Customer-segment variations were standardised using a mapping table.

Where the source did not provide sufficient information to determine the correct segment, the value was classified as:

`Unknown`

Customer segments were not inferred without supporting source data.

### Geography

Australian State/Territory values were standardised using consistent codes:

- ACT
- NSW
- NT
- QLD
- SA
- TAS
- VIC
- WA

Missing State values were derived from City only where the City-to-State relationship was unambiguous.

Conflicting geography values were flagged for investigation.

---

## 5. Financial Data Quality

Financial fields received additional scrutiny because incorrect treatment could materially affect marketing KPIs.

### Spend

**220 records contained missing Spend.**

Missing Spend was not automatically converted to `$0`.

A blank Spend value may represent:

- No spend
- Missing platform cost data
- An incomplete source record

Without confirmation from the source system, these values remain `NULL`.

**20 negative Spend records** were identified and flagged as invalid.

Negative values were not converted to positive values.

### Revenue

**150 records contained missing Revenue.**

Of these, **142 records had recorded conversions**, indicating that the missing Revenue cannot safely be interpreted as zero.

Revenue therefore remains `NULL` where the source does not provide sufficient evidence of the actual value.

This prevents the dashboard from understating or overstating marketing performance.

---

## 6. Funnel Data Quality

The following exceptions were identified.

### Clicks

35 records contained:

`Clicks > Impressions`

These values were flagged and excluded from cleaned Click calculations.

### Conversions

20 records contained:

`Conversions > Clicks`

These values were flagged and excluded from cleaned Conversion calculations.

### Leads

No material Leads issue was identified, but the relationship:

`Leads <= Clicks`

was validated as part of the quality process.

### Email

No material violations were identified in:

- `Email Opens > Email Sent`
- `Email Clicks > Email Opens`

---

## 7. Analytical Treatment

The model retains both:

- **Original/source fields** — for audit and reconciliation
- **Cleaned fields** — for KPI calculations

Examples include:

| Original Field | Analytical Field |
|---|---|
| Clicks | `Clean_Clicks` |
| Leads | `Clean_Leads` |
| Conversions | `Clean_Conversions` |
| Spend | `Clean_Spend` |
| Revenue | `Clean_Revenue` |

Invalid values are not silently overwritten.

This allows management to trace reported KPIs back to the underlying source data.

---

## 8. Reporting Model

The Power BI model uses a simple star schema.

### Fact Table

`Fact_Marketing`

Contains:

- Campaign activity
- Impressions
- Clicks
- Leads
- Conversions
- Spend
- Revenue
- New Customers
- Email metrics
- Data-quality fields

### Dimension Tables

- `Dim_Date`
- `Dim_Campaign`
- `Dim_Channel`
- `Dim_Geography`

Relationships use a **one-to-many (1:*)** structure with single-direction filtering.

Dimension keys were validated for uniqueness before being used in relationships.

---

## 9. KPI Data Integrity

The following measures use cleaned analytical fields:

- Total Spend
- Total Revenue
- Total Impressions
- Total Clicks
- Total Leads
- Total Conversions
- New Customers
- CTR
- Conversion Rate
- CPC
- CPA
- ROAS
- ROI

### KPI Definitions

**CTR**

`Clicks ÷ Impressions`

**Conversion Rate**

`Conversions ÷ Clicks`

**CPC**

`Spend ÷ Clicks`

**CPA**

`Spend ÷ Conversions`

**ROAS**

`Revenue ÷ Spend`

**Marketing ROI**

`(Revenue − Spend) ÷ Spend`

ROAS and ROI should not be interpreted as measures of overall company profitability. The project ROI definition considers marketing spend only and does not include costs such as product costs, fulfilment, overheads, returns or taxes.

---

## 10. Outstanding Data Issues

The following issues should remain visible to management.

| Issue | Status | Business Impact |
|---|---|---|
| Missing Spend — 220 records | Open | Can affect Spend, ROAS, ROI, CPC and CPA |
| Missing Revenue — 150 records | Open | Can affect Revenue, ROAS and ROI |
| Negative Spend — 20 records | Under investigation | Can distort financial KPIs |
| Invalid Clicks — 35 records | Cleaned/flagged | Can affect CTR |
| Invalid Conversions — 20 records | Cleaned/flagged | Can affect Conversion Rate and CPA |
| Historical source inconsistencies | Controlled | Standardised through mapping tables |

The dashboard should not imply that unresolved financial data is complete.

---

## 11. Management Assessment

The dataset is suitable for exploratory and comparative marketing analysis after the documented cleaning process, provided the outstanding financial data issues are considered when interpreting results.

The primary limitation is the presence of missing Spend and Revenue values.

These fields directly affect:

- ROAS
- ROI
- CPC
- CPA
- Revenue
- Spend

Consequently, management should treat KPI comparisons involving affected records with appropriate caution until the underlying source data has been reconciled.

---

## 12. Data Governance Principle

The project follows four principles:

1. **Do not silently alter source data.**
2. **Do not assume NULL means zero.**
3. **Keep original values for auditability.**
4. **Use cleaned fields for analytical KPIs.**

Any unresolved business-data ambiguity should be confirmed with the relevant business or source-system owner before being converted into a definitive value.

---

## Final Status

| Area | Status |
|---|---|
| Data quality process | Completed |
| Analytical model | Implemented |
| Major data issues | Identified and documented |
| Cleaning rules | Applied |
| Outstanding financial issues | 220 missing Spend and 150 missing Revenue records |
| Management consideration | Financial KPI interpretation should account for unresolved source-data gaps |
