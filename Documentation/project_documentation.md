# UrbanCart Marketing Campaign Performance & Customer Acquisition Analytics

## Project Documentation

**Company:** UrbanCart (fictional Australian e-commerce company)\
**Project period:** January 2024 -- December 2025\
**Expected source dataset:** approximately 50,000--100,000 rows\
**Primary tool:** Microsoft Power BI Desktop / Power Query / DAX

------------------------------------------------------------------------

# 1. Executive Summary

UrbanCart is an Australian e-commerce company investing in multiple
marketing channels, including Google, Meta, Email and other digital
channels.

The purpose of this project is to build a reliable Power BI analytics
solution that answers:

> Which campaigns are generating customers and revenue efficiently, and
> how should marketing performance be evaluated when deciding where to
> investigate changes in budget?

The project covers the complete analytics workflow:

1.  Raw data inspection
2.  Data-quality assessment
3.  Data cleaning
4.  Missing-value treatment
5.  Duplicate investigation
6.  Business-rule validation
7.  Fact and dimension table creation
8.  Data-model design
9.  DAX KPI development
10. Dashboard development
11. Business interpretation

The solution is designed to preserve source information, make
data-quality issues visible, and use cleaned fields for analytical
calculations.

------------------------------------------------------------------------

# 2. Business Problem

The Marketing Manager has asked:

> "We are spending heavily across Google, Meta, Email and other
> channels. Which campaigns are actually generating profitable
> customers, and where should we increase or reduce our marketing
> budget?"

The analytical challenge is not simply to identify campaigns with the
highest revenue.

Campaigns must be evaluated using multiple dimensions, including:

-   Spend
-   Revenue
-   ROAS
-   ROI
-   CPA
-   CPC
-   CTR
-   Conversion Rate
-   Conversions
-   New Customers
-   Marketing funnel performance
-   Channel
-   Campaign type
-   Objective
-   Geography
-   Customer segment
-   Device
-   Time period

Because some source values contain missing or invalid data, data quality
must be established before financial and acquisition KPIs are
interpreted.

------------------------------------------------------------------------

# 3. Project Objectives

## Primary objectives

-   Build a reliable marketing performance dataset.
-   Identify and document data-quality issues.
-   Standardise inconsistent source values.
-   Preserve source data for auditability.
-   Create cleaned analytical fields.
-   Build a star-schema-style Power BI model.
-   Develop reusable DAX measures.
-   Create a campaign performance dashboard.
-   Create a marketing funnel dashboard.
-   Provide a framework for comparing marketing efficiency without
    relying on a single KPI.

## Secondary objectives

-   Make data-quality decisions transparent.
-   Separate source values from analytical values.
-   Ensure missing financial information is not silently converted to
    zero.
-   Make the Power BI model maintainable.
-   Provide a clear structure for future campaign and channel analysis.

------------------------------------------------------------------------

# 4. Dataset Scope

The proposed dataset contains approximately 50,000--100,000 rows
covering:

**January 2024 through December 2025**

The dataset contains campaign, customer, channel, geography, marketing
funnel and financial information.

------------------------------------------------------------------------

# 5. Source Data Dictionary

  Column             Description                      Expected Type
  ------------------ -------------------------------- ---------------
  Row_ID             Unique source row identifier     Text
  Campaign_ID        Campaign identifier              Text
  Campaign_Name      Campaign name                    Text
  Campaign_Type      Campaign format/type             Text
  Objective          Campaign objective               Text
  Campaign_Date      Campaign activity date           Date
  Channel            Source marketing channel         Text
  Platform           Advertising/email platform       Text
  Channel_ID         Channel identifier               Text
  Channel_Category   Higher-level channel grouping    Text
  State              Australian state/territory       Text
  City               City                             Text
  Customer_ID        Customer identifier              Text
  Customer_Segment   Customer classification          Text
  Age_Group          Customer age group               Text
  Device             Customer/device category         Text
  Impressions        Marketing impressions            Whole number
  Clicks             Marketing clicks                 Whole number
  Leads              Leads generated                  Whole number
  Conversions        Conversions generated            Whole number
  Spend              Marketing expenditure            Decimal
  Revenue            Revenue attributed to activity   Decimal
  New_Customers      Newly acquired customers         Whole number
  Email_Sent         Emails sent                      Whole number
  Email_Opens        Email opens                      Whole number
  Email_Clicks       Email clicks                     Whole number

------------------------------------------------------------------------

# 6. Analytical Grain

The intended analytical grain is approximately:

**Campaign + Campaign Date + Channel + Geography + Customer**

The working uniqueness definition used during duplicate investigation
is:

`Campaign_ID + Campaign_Date + Channel_ID + State + City + Customer_ID`

The fact table may contain repeated foreign-key values because multiple
marketing records can belong to the same campaign, channel, customer or
geography.

------------------------------------------------------------------------

# 7. Data-Quality Strategy

The project follows a controlled data-quality process:

1.  Profile the raw dataset.
2.  Identify missing values.
3.  Identify duplicate records.
4.  Identify inconsistent text.
5.  Validate numeric business rules.
6.  Validate date range.
7.  Investigate ambiguous nulls.
8.  Create cleaned analytical fields.
9.  Preserve original/source values.
10. Recheck the cleaned dataset.
11. Document all material assumptions.

## Core principle

> Never silently change ambiguous business data.

For example, a missing Spend value does not necessarily mean zero Spend.
It may mean that the advertising platform failed to provide the cost.

------------------------------------------------------------------------

# 8. Power Query Workflow

## Step 1 --- Import

In Power BI Desktop:

**Home → Get Data → Excel workbook**

Select:

`Marketing_Campaign_Performance_Raw.xlsx`

Select:

`Raw_Data`

Choose:

**Transform Data**

------------------------------------------------------------------------

# 9. Raw Data Inspection

Create a duplicate query named:

`Data_check`

The source query should remain available for reference.

Review the Applied Steps:

`Source → Navigation → Changed Type`

Check:

-   Number of rows
-   Column Quality
-   Column Distribution
-   Column Profile
-   Data types
-   Missing values
-   Errors
-   Distinct categorical values

------------------------------------------------------------------------

# 10. Data-Quality Findings

## 10.1 Missing values

Initial profiling identifies missing values in areas including:

-   Campaign_Name
-   State
-   Customer_Segment
-   Spend
-   Revenue

Later detailed reconciliation reports:

-   **Spend:** 220 missing values
-   **Revenue:** 150 missing values

Because the supplied project notes contain different counts at different
investigation stages, the final report should use the reconciled counts
from the final cleaned query and document the earlier profiling counts
as intermediate observations where necessary.

------------------------------------------------------------------------

# 11. Duplicate Records

Reported duplicate records:

**150**

Duplicates should not be removed immediately.

First identify the business grain and group by:

-   Campaign_ID
-   Campaign_Date
-   Channel_ID
-   State
-   City
-   Customer_ID

Create:

`Record_count`

Filter:

`Record_count > 1`

Compare duplicate records in the original data.

Important fields to compare:

-   Impressions
-   Clicks
-   Leads
-   Conversions
-   Spend
-   Revenue
-   New_Customers
-   Email_Sent
-   Email_Opens
-   Email_Clicks

If the duplicated records are identical and represent the same business
event, remove them.

------------------------------------------------------------------------

# 12. Text Standardisation

## Channel

Expected inconsistent values include:

-   Google Ads
-   GoogleAds
-   Google Ads + spaces
-   Meta Ads
-   Facebook
-   Email
-   email
-   TikTok Ads
-   TikTokAds

Treatment:

1.  Trim
2.  Lowercase
3.  Merge with `channel_mapping_table`
4.  Expand `standard_channel`
5.  Remove the obsolete source field if appropriate

## Customer Segment

Apply:

`Trim → Lower`

Merge with:

`Segment_mapping_table`

Expand:

`standard_Customer_Segment`

Unmapped values should become:

`Unknown`

The project does not infer a customer segment when the source does not
support that classification.

## State

Apply:

`Upper → Trim`

Use a City-to-State master where a city uniquely maps to a state.

Treatment:

  Situation                             Treatment
  ------------------------------------- -----------
  State present and valid               Keep
  State missing + unique City mapping   Derive
  State missing + no unique mapping     Unknown
  State conflicts with City             Flag
  City and State missing                Unknown

------------------------------------------------------------------------

# 13. Campaign Name Recovery

Create:

`Campaign_Master_Table`

The table should contain campaign-related fields and a unique
Campaign_ID.

Use Campaign_ID to recover missing Campaign_Name values.

Reported missing Campaign_Name:

**20**

------------------------------------------------------------------------

# 14. Business-Rule Validation

The project validates the following rules.

## Rule 1

`Clicks <= Impressions`

Reported violations:

**35**

## Rule 2

`Conversions <= Clicks`

Reported violations:

**20**

## Rule 3

`Leads <= Clicks`

Reported violations:

**0**

## Rule 4

`Spend >= 0`

Reported negative Spend:

**20**

## Rule 5

`Revenue >= 0`

Negative revenue should be flagged if encountered.

## Rule 6

`Email_Opens <= Email_Sent`

Reported violations:

**0**

## Rule 7

`Email_Clicks <= Email_Opens`

Reported violations:

**0**

------------------------------------------------------------------------

# 15. Data-Quality Columns

## DQ_Clicks_Check

``` powerquery
if [Impressions] = null then "Missing"
else if [Clicks] = null then "Missing"
else if [Clicks] > [Impressions] then "Invalid"
else "Valid"
```

## DQ_Conversion_Check

``` powerquery
if [Clicks] = null then "Missing"
else if [Conversions] = null then "Missing"
else if [Conversions] > [Clicks] then "Invalid"
else "Valid"
```

## DQ_Leads_Clicks

``` powerquery
if [Clicks] = null then "Missing"
else if [Leads] = null then "Missing"
else if [Leads] > [Clicks] then "Invalid"
else "Valid"
```

## DQ_Spend_Check

``` powerquery
if [Spend] = null then "Missing"
else if [Spend] < 0 then "Invalid"
else "Valid"
```

## DQ_Revenue_Check

``` powerquery
if [Revenue] = null then "Missing"
else if [Revenue] < 0 then "Invalid"
else "Valid"
```

## Email_Quality

``` powerquery
if [Email_Sent] = null or [Email_Opens] = null or [Email_Clicks] = null then "Missing"
else if [Email_Opens] > [Email_Sent] then "Invalid"
else if [Email_Clicks] > [Email_Opens] then "Invalid"
else "Valid"
```

------------------------------------------------------------------------

# 16. Clean Analytical Fields

The source columns should be preserved for auditing.

Create analytical versions for invalid numerical values.

## Clean_Clicks

``` powerquery
if [Clicks] = null then null
else if [Clicks] > [Impressions] then null
else [Clicks]
```

## Clean_Conversions

``` powerquery
if [Conversions] = null then null
else if [Conversions] > [Clicks] then null
else [Conversions]
```

## Clean_Spend

``` powerquery
if [Spend] = null then null
else if [Spend] < 0 then null
else [Spend]
```

## Clean_Revenue

``` powerquery
if [Revenue] = null then null
else if [Revenue] < 0 then null
else [Revenue]
```

------------------------------------------------------------------------

# 17. Missing-Value Policy

Missing values must be interpreted in business context.

  Field              Possible meaning
  ------------------ ----------------------------------------
  Campaign_Name      Lookup/data-entry problem
  State              Geography unavailable
  Customer_Segment   Not classified
  Spend              Missing cost transaction
  Revenue            Missing revenue transaction
  Clicks             Tracking issue or zero activity
  Leads              No leads or missing tracking
  Conversions        No conversions or missing tracking
  Email_Opens        Not applicable/no email/tracking issue
  Email_Clicks       Not applicable/no email/tracking issue

------------------------------------------------------------------------

# 18. Spend Treatment

The project reports 220 missing Spend values.

Do not automatically replace these with zero.

Possible interpretations:

### Scenario A --- No money was spent

Spend can be treated as zero if the data owner confirms this definition.

### Scenario B --- Cost data is missing

Keep Spend as null.

### Scenario C --- Data is temporarily unavailable

Keep Spend as null and raise a source-data-quality issue.

Review:

-   Campaign_ID
-   Campaign_Name
-   Campaign_Date
-   Channel
-   Impressions
-   Clicks
-   Leads
-   Conversions
-   Revenue
-   New_Customers

A record with:

`Impressions = 0, Clicks = 0, Conversions = 0, Revenue = 0, Spend = NULL`

may plausibly represent no activity.

A record with:

`Impressions = 5,000, Clicks = 300, Conversions = 20, Revenue = 2,500, Spend = NULL`

contains marketing activity and should not have Spend assumed to be
zero.

------------------------------------------------------------------------

# 19. Revenue Treatment

The project reports 150 missing Revenue values.

Investigate:

-   Spend
-   Conversions
-   Leads
-   Clicks
-   Impressions

Interpretation:

### Revenue NULL + Conversions = 0 + Spend \> 0

Could represent zero revenue, but business confirmation is required.

### Revenue NULL + Conversions \> 0

Strong indication of missing revenue information.

### Revenue NULL + Spend = 0 + Conversions = 0

May represent zero revenue depending on the business definition.

### Revenue NULL + Spend NULL

Both financial measures are missing and should be investigated.

Reported finding:

> 150 records had missing Revenue. 142 had recorded conversions and the
> remaining records had marketing activity or missing spend. Because
> there was insufficient evidence that these represented zero revenue,
> Revenue was retained as null and flagged for investigation.

------------------------------------------------------------------------

# 20. Funnel Data Quality

  Metric        Issue                                    Rows
  ------------- ----------------------- ---------------------
  Impressions   No reported issue                         ---
  Clicks        Clicks \> Impressions                      35
  Leads         No issue                                    0
  Conversions   Conversions \> Clicks                      20
  Spend         Missing                                   220
  Spend         Negative                  20 reported invalid
  Revenue       Missing                                   150

------------------------------------------------------------------------

# 21. Email Data Quality

  Check                           Rows Treatment
  ----------------------------- ------ -----------
  Email_Sent = null                  0 No issue
  Email_Opens = null                 0 No issue
  Email_Clicks = null                0 No issue
  Email_Opens \> Email_Sent          0 No issue
  Email_Clicks \> Email_Opens        0 No issue

------------------------------------------------------------------------

# 22. Data-Type Standardisation

## Whole Number

-   Impressions
-   Clicks
-   Leads
-   Conversions
-   New_Customers
-   Email_Sent
-   Email_Opens
-   Email_Clicks

## Decimal Number

-   Spend
-   Revenue

## Text

-   Campaign_ID
-   Campaign_Name
-   Channel
-   Channel_ID
-   State
-   City
-   Customer_ID
-   Customer_Segment
-   Age_Group
-   Device

## Date

-   Campaign_Date

------------------------------------------------------------------------

# 23. Data Model

The model uses a star-schema-style structure.

## Fact table

`Fact_Marketing`

## Dimension tables

-   `Dim_Date`
-   `Dim_Campaign`
-   `Dim_Channel`
-   `Dim_Geography`

A separate `Dim_Customer` is not created because Customer_ID does not
provide a sufficiently unique customer dimension for the supplied
dataset.

------------------------------------------------------------------------

# 24. Fact_Marketing

Keep:

-   Campaign_ID
-   Campaign_Date
-   Channel_ID
-   Geography_Key
-   Customer_ID
-   Standard_Customer_Segment
-   Age_Group
-   Device
-   Clicks
-   Clean_Clicks
-   Impressions
-   Conversions
-   Clean_Conversions
-   Leads
-   Spend
-   Clean_Spend
-   Revenue
-   Clean_Revenue
-   New_Customers
-   Email_Sent
-   Email_Opens
-   Email_Clicks
-   DQ fields

The original fields provide auditability.

The Clean\_\* fields are used for analytical calculations.

------------------------------------------------------------------------

# 25. Dim_Channel

Columns:

-   Channel_ID
-   Standard_Channel
-   Platform
-   Channel_Category

The dimension key must be unique.

------------------------------------------------------------------------

# 26. Dim_Campaign

Columns:

-   Campaign_ID
-   Campaign_Name
-   Campaign_Type
-   Objective

The dimension key must be unique.

------------------------------------------------------------------------

# 27. Dim_Geography

Columns:

-   Geography_Key
-   State
-   City

Create an Index column starting from 1 and rename it:

`Geography_Key`

------------------------------------------------------------------------

# 28. Dim_Date

Use:

``` dax
Dim_Date =
VAR MinDate =
    MINX(
        Fact_Marketing,
        Fact_Marketing[Campaign_Date]
    )
VAR MaxDate =
    MAXX(
        Fact_Marketing,
        Fact_Marketing[Campaign_Date]
    )
RETURN
ADDCOLUMNS(
    CALENDAR(MinDate, MaxDate),
    "Year", YEAR([Date]),
    "Month Number", MONTH([Date]),
    "Month", FORMAT([Date], "MMM"),
    "Quarter", "Q" & FORMAT([Date], "Q"),
    "Year-Month", FORMAT([Date], "YYYY-MM")
)
```

------------------------------------------------------------------------

# 29. Relationships

Create:

  ------------------------------------------------------------------------------------------------------
  From                             To                                Cardinality       Direction
  -------------------------------- --------------------------------- ----------------- -----------------
  Dim_Date[Date](#date)            Fact_Marketing\[Campaign_Date\]   1:\*              Single

  Dim_Campaign\[Campaign_ID\]      Fact_Marketing\[Campaign_ID\]     1:\*              Single

  Dim_Channel\[Channel_ID\]        Fact_Marketing\[Channel_ID\]      1:\*              Single

  Dim_Geography\[Geography_Key\]   Fact_Marketing\[Geography_Key\]   1:\*              Single
  ------------------------------------------------------------------------------------------------------

All relationships should be active.

There should be no relationships directly connecting the dimensions to
one another.

------------------------------------------------------------------------

# 30. Model Validation

Validate:

1.  Every dimension key is unique.
2.  Fact foreign keys can repeat.
3.  Relationships are 1:\*.
4.  Cross-filter direction is Single.
5.  Relationships are active.
6.  Campaign filters affect Fact_Marketing.
7.  Channel filters affect Fact_Marketing.
8.  Geography filters affect Fact_Marketing.
9.  Date filters affect Fact_Marketing.
10. Clean_Revenue returns sensible results.

Create a simple Table visual using campaign/channel fields and
`Clean_Revenue`.

If dimension selections correctly filter the fact values, the model is
behaving as expected.

------------------------------------------------------------------------

# 31. DAX Measures

## Total Spend

``` dax
Total Spend =
SUM(Fact_Marketing[Clean_Spend])
```

## Total Revenue

``` dax
Total Revenue =
SUM(Fact_Marketing[Clean_Revenue])
```

## Total Impressions

``` dax
Total Impressions =
SUM(Fact_Marketing[Impressions])
```

## Total Clicks

``` dax
Total Clicks =
SUM(Fact_Marketing[Clean_Clicks])
```

## Total Leads

``` dax
Total Leads =
SUM(Fact_Marketing[Leads])
```

## Total Conversions

``` dax
Total Conversions =
SUM(Fact_Marketing[Clean_Conversions])
```

## New Customers

``` dax
New Customers =
SUM(Fact_Marketing[New_Customers])
```

## CTR

``` dax
CTR =
DIVIDE(
    [Total Clicks],
    [Total Impressions]
)
```

Format as Percentage.

**Definition:**

`CTR = Clicks ÷ Impressions`

## Conversion Rate

``` dax
Conversion Rate =
DIVIDE(
    SUM(Fact_Marketing[Clean_Conversions]),
    SUM(Fact_Marketing[Clean_Clicks]),
    0
)
```

Format as Percentage.

**Definition:**

`Conversion Rate = Conversions ÷ Clicks`

## CPC

``` dax
CPC =
DIVIDE(
    [Total Spend],
    [Total Clicks]
)
```

Format as Currency.

**Definition:**

`CPC = Spend ÷ Clicks`

## CPA

``` dax
CPA =
DIVIDE(
    [Total Spend],
    [Total Conversions]
)
```

Format as Currency.

**Definition:**

`CPA = Spend ÷ Conversions`

## ROAS

``` dax
ROAS =
DIVIDE(
    [Total Revenue],
    [Total Spend]
)
```

Do not format as a percentage.

**Definition:**

`ROAS = Revenue ÷ Spend`

A ROAS of 3.42 means \$3.42 of revenue was generated per \$1 of
marketing spend.

## ROI

``` dax
ROI =
DIVIDE(
    [Total Revenue] - [Total Spend],
    [Total Spend]
)
```

Format as Percentage.

**Project definition:**

`ROI = (Revenue − Spend) ÷ Spend`

------------------------------------------------------------------------

# 32. KPI Definitions

  KPI               Formula                     Purpose
  ----------------- --------------------------- ---------------------------------
  Spend             Sum of Clean_Spend          Marketing investment
  Revenue           Sum of Clean_Revenue        Attributed revenue
  CTR               Clicks / Impressions        Ad engagement
  Conversion Rate   Conversions / Clicks        Post-click effectiveness
  CPC               Spend / Clicks              Cost per click
  CPA               Spend / Conversions         Cost per acquisition/conversion
  ROAS              Revenue / Spend             Revenue efficiency
  ROI               (Revenue - Spend) / Spend   Return after marketing spend
  New Customers     Sum of New_Customers        Acquisition volume

------------------------------------------------------------------------

# 33. ROAS vs ROI

## ROAS

Answers:

> How much revenue did marketing generate for each dollar of marketing
> spend?

Example:

Revenue = \$500,000\
Spend = \$100,000

ROAS = 5.0

This means \$5 of revenue per \$1 spent.

## ROI

Answers:

> What return remains after subtracting marketing spend?

Using the project definition:

`ROI = (Revenue - Spend) / Spend`

Example:

Revenue = \$500,000\
Spend = \$100,000

ROI = 400%

The two measures answer different questions and should not be treated as
interchangeable.

------------------------------------------------------------------------

# 34. Measure Table

Create a dedicated measure table using **Enter data**.

Hide the dummy column.

Move measures into the measure table using:

**Measure → Measure tools → Home table → Measures**

This provides a cleaner model structure and keeps calculations separate
from source columns.

------------------------------------------------------------------------

# 35. Dashboard Architecture

The dashboard should contain at least two primary analytical pages:

1.  Campaign Performance
2.  Marketing Funnel

------------------------------------------------------------------------

# 36. Page 1 --- Campaign Performance

## Purpose

Evaluate campaign-level financial and acquisition efficiency.

## KPI cards

Recommended primary cards:

-   Total Spend
-   Total Revenue
-   ROAS
-   Total Conversions
-   CPA

Additional metrics:

-   ROI
-   CPC

## Scatter chart --- Revenue vs Spend by Campaign

Configuration:

-   X-axis: Total Spend
-   Y-axis: Total Revenue
-   Details: Campaign_Name
-   Tooltips: ROAS, ROI, Conversions, CPA

## Bar chart --- ROAS by Campaign

Use:

`Campaign_Name`

and:

`ROAS`

## Bar chart --- Conversions by Campaign

Use:

`Campaign_Name`

and:

`Total Conversions`

## Campaign Performance Table

Recommended columns:

  ----------------------------------------------------------------------------------------------
  Campaign     Spend   Revenue    ROAS     ROI   Conversions     CPA     CPC     CTR         New
                                                                                       Customers
  ---------- ------- --------- ------- ------- ------------- ------- ------- ------- -----------

  ----------------------------------------------------------------------------------------------

------------------------------------------------------------------------

# 37. Conditional Formatting

Use metric-level conditional formatting to make values easier to
inspect.

Recommended fields:

-   ROAS
-   CPA
-   Revenue

For example:

-   Higher/lower ROAS can receive different visual treatment.
-   Higher/lower CPA can receive different visual treatment.
-   Higher/lower Revenue can receive different visual treatment.

These visual cues should help users investigate metrics.

They should not be interpreted as a single overall campaign score.

------------------------------------------------------------------------

# 38. Campaign Performance Slicers

Recommended slicers:

  Slicer          Purpose
  --------------- ----------------------------------
  Date            Compare selected periods
  Campaign        Investigate selected campaigns
  Channel         Compare channel-specific results
  Campaign Type   Compare campaign formats
  Objective       Examine performance by objective

------------------------------------------------------------------------

# 39. Page 2 --- Marketing Funnel

The funnel page focuses on movement through:

**Impressions → Clicks → Leads → Conversions**

## Recommended KPIs

  KPI                 Business interpretation
  ------------------- --------------------------------
  Total Impressions   Marketing exposure
  Total Clicks        Traffic generated
  CTR                 Impression-to-click efficiency
  Total Leads         Lead generation volume
  Total Conversions   Completed target actions
  Conversion Rate     Click-to-conversion efficiency

The funnel should help identify where users drop off between marketing
stages.

------------------------------------------------------------------------

# 40. Business Interpretation Framework

Campaigns should not be evaluated using revenue alone.

A campaign with high revenue may also require substantially higher
spend.

A balanced analysis should examine:

-   Revenue
-   Spend
-   ROAS
-   ROI
-   CPA
-   CPC
-   Conversion Rate
-   New Customers
-   Conversion volume
-   Campaign objective
-   Time period
-   Data completeness

A high-performing campaign for one objective may not be directly
comparable to a campaign designed for a different objective.

------------------------------------------------------------------------

# 41. Example Business Interview Answer

## Question

"How would you identify a high-performing marketing channel?"

## Answer

> "I would not look at revenue alone. I would compare revenue with spend
> and evaluate ROAS, CPA and conversion rate. A channel generating high
> revenue but requiring disproportionately high spend may not be the
> most efficient channel."

This approach avoids relying on a single metric.

------------------------------------------------------------------------

# 42. Example CPA Interpretation

Consider:

  Campaign       Conversions    CPA
  ------------ ------------- ------
  Campaign A          10,000   \$25
  Campaign B          12,000   \$80

Campaign B has more conversions, but the CPA is substantially higher.

This demonstrates why conversion volume alone is insufficient for
evaluating acquisition efficiency.

------------------------------------------------------------------------

# 43. Data-Quality Summary

  Field               Issue                  Treatment
  ------------------- ---------------------- --------------------------------------
  Channel             Inconsistent labels    Standardised
  Customer_Segment    Inconsistent labels    Standardised
  State               Missing/inconsistent   Standardised/derived where supported
  Campaign_Name       Missing                Recovered using Campaign_Master
  Duplicate records   150                    Removed after validation
  Spend               220 nulls              Keep null + flag
  Spend               Negative values        Flag/investigate
  Revenue             150 nulls              Keep null + flag
  Clicks              35 \> Impressions      Clean field set to null + flag
  Leads               No issue               Keep
  Conversions         20 \> Clicks           Clean field set to null + flag
  Email metrics       No identified issues   Keep

------------------------------------------------------------------------

# 44. Data Governance Principles

## Preserve source data

Never overwrite the original source field when an analytical correction
is needed.

## Use explicit quality fields

Quality flags should explain why a value was considered invalid, missing
or unvalidated.

## Avoid unsupported imputation

Do not replace a financial null with zero unless the business definition
confirms that null means zero.

## Separate audit and analytical values

For example:

-   `Clicks` = original source value
-   `Clean_Clicks` = analytical value

This makes the model auditable.

## Document owner decisions

Where business interpretation is required, record the decision and the
responsible business owner.

------------------------------------------------------------------------

# 45. Final Validation Checklist

## Data quality

-   [ ] Source data preserved
-   [ ] Row count checked
-   [ ] Column profiling completed
-   [ ] Missing values documented
-   [ ] Duplicates investigated
-   [ ] Duplicate records validated before removal
-   [ ] Inconsistent text identified
-   [ ] Channel standardised
-   [ ] Customer segment standardised
-   [ ] State standardised
-   [ ] Campaign names recovered where possible
-   [ ] Numeric business rules validated
-   [ ] Date range validated
-   [ ] Email rules validated

## Data cleaning

-   [ ] Clean_Clicks created
-   [ ] Clean_Conversions created
-   [ ] Clean_Spend created
-   [ ] Clean_Revenue created
-   [ ] Invalid values flagged
-   [ ] Financial nulls retained where unresolved
-   [ ] Data types corrected

## Data model

-   [ ] Fact_Marketing created
-   [ ] Dim_Channel created
-   [ ] Dim_Campaign created
-   [ ] Dim_Geography created
-   [ ] Dim_Date created
-   [ ] Dimension keys unique
-   [ ] Relationships are 1:\*
-   [ ] Relationships are active
-   [ ] Cross-filter direction is Single
-   [ ] No dimension-to-dimension relationships

## DAX

-   [ ] Total Spend
-   [ ] Total Revenue
-   [ ] Total Impressions
-   [ ] Total Clicks
-   [ ] Total Leads
-   [ ] Total Conversions
-   [ ] New Customers
-   [ ] CTR
-   [ ] Conversion Rate
-   [ ] CPC
-   [ ] CPA
-   [ ] ROAS
-   [ ] ROI

## Dashboard

-   [ ] Campaign Performance page
-   [ ] Marketing Funnel page
-   [ ] KPI cards
-   [ ] Revenue vs Spend scatter chart
-   [ ] ROAS chart
-   [ ] Conversions chart
-   [ ] Campaign performance table
-   [ ] Conditional formatting
-   [ ] Date slicer
-   [ ] Campaign slicer
-   [ ] Channel slicer
-   [ ] Campaign Type slicer
-   [ ] Objective slicer
-   [ ] Funnel KPIs
-   [ ] Model/filter interactions tested

------------------------------------------------------------------------

# 46. Recommended Project Deliverables

The completed project should contain:

1.  `Marketing_Campaign_Performance_Raw.xlsx`
2.  Power Query transformation workflow
3.  Data-quality investigation queries
4.  Cleaned `Fact_Marketing`
5.  `Dim_Channel`
6.  `Dim_Campaign`
7.  `Dim_Geography`
8.  `Dim_Date`
9.  Dedicated measure table
10. Campaign Performance dashboard
11. Marketing Funnel dashboard
12. Data-quality documentation
13. Business interpretation notes

------------------------------------------------------------------------

# 47. Final Project Outcome

The completed UrbanCart solution should provide a structured and
auditable way to analyse marketing performance.

The final model should allow users to:

-   Examine campaign financial performance.
-   Compare marketing spend with attributed revenue.
-   Analyse ROAS and ROI.
-   Monitor acquisition efficiency through CPA and CPC.
-   Evaluate funnel performance through CTR and Conversion Rate.
-   Examine campaign results by channel.
-   Analyse performance over time.
-   Investigate campaign type and objective.
-   Explore geography and customer characteristics.
-   Identify data-quality issues before relying on KPIs.
-   Distinguish source values from cleaned analytical values.

The central analytical principle is:

> **Use multiple performance metrics, understand the data-quality
> limitations behind those metrics, and preserve enough source
> information to make the analysis auditable.**
