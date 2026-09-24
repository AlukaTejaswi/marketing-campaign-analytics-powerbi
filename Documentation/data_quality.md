# UrbanCart --- Marketing Campaign Performance & Customer Acquisition Analytics

## Data Quality, Cleaning, Data Model, DAX & Dashboard Guide

**Company:** UrbanCart (fictional Australian e-commerce company)\
**Period:** January 2024 -- December 2025\
**Expected dataset size:** approximately 50,000--100,000 rows

## Business problem

> "We are spending heavily across Google, Meta, Email and other
> channels. Which campaigns are actually generating profitable
> customers, and where should we increase or reduce our marketing
> budget?"

The analysis should evaluate campaign and channel performance using
revenue, spend, ROAS, ROI, CPA, CPC, CTR, conversion rate and customer
acquisition metrics. Data-quality issues must be identified, documented
and treated conservatively before KPI analysis.

------------------------------------------------------------------------

# 1. Raw dataset

### Main columns

  Column             Example
  ------------------ -------------
  Campaign_ID        CMP001
  Campaign_Name      Summer Sale
  Campaign_Type      Promotional
  Campaign_Date      15/01/2025
  Channel            Google Ads
  Platform           Google
  Region             VIC
  Customer_Segment   Returning
  Age_Group          25--34
  Device             Mobile
  Impressions        125,430
  Clicks             4,215
  Leads              823
  Conversions        187
  Spend              \$3,245
  Revenue            \$18,760
  New_Customers      142
  Email_Sent         0
  Email_Opens        0
  Email_Clicks       0

Additional fields used during the data-modeling process include
`Row_ID`, `Channel_ID`, `Customer_ID`, `Channel_Category`, `State`, and
`City`.

------------------------------------------------------------------------

# 2. Power Query --- inspect the raw data

Open **Power BI Desktop → Home → Get Data → Excel workbook**, select
`Marketing_Campaign_Performance_Raw.xlsx`, select `Raw_Data`, and choose
**Transform Data**.

Create a duplicate query named `Data_check`. Do not delete anything from
the source while investigating.

## 2.1 Applied Steps

Review:

`Source → Navigation → Changed Type`

Confirm that headers and data types were interpreted correctly.

## 2.2--2.5 Profiling

Check:

-   Number of rows
-   Column Quality
-   Column Distribution
-   Column Profile

Use these features to understand nulls, distinct values, errors,
distributions and basic statistics.

## 2.6 Categorical columns

These should be Text:

-   Campaign_Name
-   Campaign_Type
-   Objective
-   Channel
-   Platform
-   Channel_Category
-   State
-   City
-   Customer_Segment
-   Age_Group
-   Device

## 2.7 ID columns

These should be Text:

-   Row_ID
-   Campaign_ID
-   Channel_ID
-   Customer_ID

## 2.8 Numeric columns

These should be numeric:

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

## 2.9 Date

`Campaign_Date` should be Date.

## 2.10 Missing values

  Column             Missing?   Action
  ------------------ ---------- -------------
  Campaign_Name      Yes        Investigate
  State              Yes        Investigate
  Customer_Segment   Yes        Investigate
  Spend              Yes        Investigate
  Revenue            Yes        Investigate

## 2.11 Duplicate records

Create a duplicate query. Do not delete anything from the source.

Use the following fields to define a complete record for an initial
duplicate check:

-   Campaign_ID
-   Campaign_Date
-   Channel_ID
-   Customer_ID
-   Impressions
-   Clicks
-   Leads
-   Conversions
-   Spend
-   Revenue

Use **Home → Remove Rows → Remove Duplicates** on the duplicate query.

Reported duplicate records: **150**.

## 2.12 Inconsistent text

Inspect text columns with Column Distribution and filters.

Expected issues:

  Column             Issue
  ------------------ ----------------------------------------------
  Channel            Google Ads / GoogleAds / Google Ads + spaces
  Channel            Meta Ads / Facebook
  Channel            Email / email
  Channel            TikTok Ads / TikTokAds
  State              Upper/lower-case variations
  Customer_Segment   Customer/Cust variations

Record the issue rather than silently overwriting the source.

------------------------------------------------------------------------

# 3. Data-quality validation

## 3.1 Clicks cannot exceed impressions

Create `DQ_Clicks_Check`:

``` powerquery
if [Impressions] = null then "Missing"
else if [Clicks] = null then "Missing"
else if [Clicks] > [Impressions] then "Invalid"
else "Valid"
```

Reported invalid rows: **35**.

## 3.2 Conversions cannot exceed clicks

Create `DQ_Conversion_Check`:

``` powerquery
if [Clicks] = null then "Missing"
else if [Conversions] = null then "Missing"
else if [Conversions] > [Clicks] then "Invalid"
else "Valid"
```

Reported invalid rows: **20**.

## 3.3 Spend

Marketing spend cannot be negative.

``` powerquery
if [Spend] = null then "Missing"
else if [Spend] < 0 then "Invalid"
else "Valid"
```

Reported:

-   Negative/invalid Spend: **20**
-   Missing Spend: **220** in the later reconciliation

> Do not convert negative values to positive values. Flag them for
> investigation.

## 3.4 Date range

Expected date range:

-   Minimum: `01/01/2024`
-   Maximum: `31/12/2025`

Create `DQ_Date_Check`:

``` powerquery
if [Campaign_Date] < #date(2024,1,1)
or [Campaign_Date] > #date(2025,12,31)
then "Invalid"
else "Valid"
```

## 3.5 Business rules

Validate:

1.  Clicks \<= Impressions
2.  Conversions \<= Clicks
3.  Leads \<= Clicks
4.  Spend \>= 0
5.  Revenue \>= 0
6.  Email_Opens \<= Email_Sent
7.  Email_Clicks \<= Email_Opens

## Initial checklist

  --------------------------------------------------------------------------
  Data Quality Check      Status                  Finding
  ----------------------- ----------------------- --------------------------
  Missing values checked  ✅                      Missing values identified

  Duplicate records       ✅                      150 duplicates
  checked                                         

  Inconsistent text       ✅                      Channel/segment formatting
  checked                                         variations

  Invalid numerical       ✅                      Invalid
  values checked                                  clicks/conversions/spend
                                                  identified

  Date range checked      ✅                      2024--2025

  Business-rule           ✅                      Several validation
  violations checked                              exceptions
  --------------------------------------------------------------------------

------------------------------------------------------------------------

# 4. Clean the data in Power Query

Create a duplicate named `Fact_Marketing_Raw_Cleaning`.

Recommended controlled order:

1.  Remove/resolve duplicate records
2.  Standardise text
3.  Handle missing values
4.  Validate/fix invalid numerical values
5.  Validate dates
6.  Recheck business rules
7.  Set correct data types
8.  Rename/organise columns
9.  Create the final clean fact table

## 4.1 Duplicates

The intended grain is:

`Campaign_ID + Campaign_Date + Channel_ID + State + City + Customer_ID`

Use **Group By** on these fields and create:

`Record_count = Count`

Filter `Record_count > 1`.

Compare duplicate combinations back to `Data_check`, especially:

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

If the records are identical, remove duplicates using the fields that
define the record grain.

Reported duplicates removed after validation: **150**.

## 4.2 Standardise Channel

Apply:

`Trim → Lower`

Merge with a `channel_mapping_table`, expand `standard_channel`, then
remove the old channel field if appropriate.

## 4.3 Standardise Customer Segment

Apply:

`Trim → Lower`

Merge with `Segment_mapping_table`, expand `standard_Customer_Segment`.

Replace unresolved values with `Unknown`.

Do not guess whether a customer is New, Returning, Loyal or At Risk.

## 4.4 Standardise State

Apply:

`Upper → Trim`

Use a city master where a City uniquely maps to one State.

Treatment:

  Situation                                     Treatment
  --------------------------------------------- ------------------------
  State present and valid                       Keep State
  State missing + City uniquely maps to State   Derive State from City
  State missing + City does not map             `Unknown`
  State conflicts with City                     Flag for investigation
  City and State both missing                   `Unknown`

The stated implementation used a city master and resulted in no
remaining Unknown values after the merge.

## 4.5 Campaign name

Create/prepare `Campaign_Master_Table` from campaign-related fields and
use `Campaign_ID` to recover missing campaign names.

Reported missing Campaign_Name values: **20**.

------------------------------------------------------------------------

# 5. Numerical cleaning and quality columns

## DQ / quality fields

### Clicks

``` powerquery
if [Impressions] = null then "Missing"
else if [Clicks] = null then "Missing"
else if [Clicks] > [Impressions] then "Invalid"
else "Valid"
```

### Conversions

``` powerquery
if [Clicks] = null then "Missing"
else if [Conversions] = null then "Missing"
else if [Conversions] > [Clicks] then "Invalid"
else "Valid"
```

### Leads

``` powerquery
if [Clicks] = null then "Missing"
else if [Leads] = null then "Missing"
else if [Leads] > [Clicks] then "Invalid"
else "Valid"
```

### Spend

``` powerquery
if [Spend] = null then "Missing"
else if [Spend] < 0 then "Invalid"
else "Valid"
```

### Revenue

``` powerquery
if [Revenue] = null then "Missing"
else if [Revenue] < 0 then "Invalid"
else "Valid"
```

### Email

``` powerquery
if [Email_Sent] = null or [Email_Opens] = null or [Email_Clicks] = null then "Missing"
else if [Email_Opens] > [Email_Sent] then "Invalid"
else if [Email_Clicks] > [Email_Opens] then "Invalid"
else "Valid"
```

## Clean numeric fields

### Clean_Clicks

``` powerquery
if [Clicks] = null then null
else if [Clicks] > [Impressions] then null
else [Clicks]
```

### Clean_Conversions

``` powerquery
if [Conversions] = null then null
else if [Conversions] > [Clicks] then null
else [Conversions]
```

### Clean_Spend

``` powerquery
if [Spend] = null then null
else if [Spend] < 0 then null
else [Spend]
```

### Clean_Revenue

``` powerquery
if [Revenue] = null then null
else if [Revenue] < 0 then null
else [Revenue]
```

Keep original/source values for auditability. Use the Clean\_\* fields
for analysis.

------------------------------------------------------------------------

# 6. Missing-value treatment

Do not automatically convert business-critical nulls to zero.

  Field              Null could mean
  ------------------ --------------------------------------------
  Campaign_Name      Data entry / lookup issue
  State              Geography unavailable
  Customer_Segment   Customer not classified
  Spend              Missing source transaction
  Revenue            Missing transaction/revenue data
  Clicks             Tracking issue OR zero activity
  Leads              No leads OR missing tracking
  Conversions        No conversions OR missing tracking
  Email_Opens        Not applicable / no email / tracking issue
  Email_Clicks       Not applicable / no email / tracking issue

### Recommended treatment

  Field                                  Null Count Recommendation                 Owner decision
  ------------------ ------------------------------ ------------------------------ ----------------
  Campaign_Name                                  20 Recover using Campaign_ID      Approved
  State                                          15 Investigate/derive from City   Pending
  Customer_Segment                              100 Classify as Unknown            Approved
  Spend                30 / later reconciled to 220 Investigate source             Pending
  Revenue                25 / later reported as 150 Investigate source             Pending
  Clicks                  10 in one profiling stage Investigate tracking           Pending
  Leads                    8 in one profiling stage Investigate tracking           Pending

The later detailed reconciliation reports **220 missing Spend values**
and **150 missing Revenue values**. Preserve these as null until the
business owner confirms their meaning.

## Spend

Ask:

> What does a blank Spend value mean?

Possible interpretations:

-   **Scenario A:** no money was spent → Spend = 0
-   **Scenario B:** platform failed to provide cost → keep NULL
-   **Scenario C:** temporarily unavailable → keep NULL and raise a DQ
    issue

Inspect Campaign_ID, Campaign_Name, Campaign_Date, Channel, Impressions,
Clicks, Leads, Conversions, Revenue and New_Customers together.

For example:

    Impressions   Clicks   Conversions   Revenue   Spend
  ------------- -------- ------------- --------- -------
              0        0             0         0    NULL
          5,000      300            20     2,500    NULL

The second pattern clearly contains activity, so NULL should not be
assumed to mean zero.

### Spend treatment

    Original Spend   Clean Spend Quality
  ---------------- ------------- --------------------------------
            250.50        250.50 Valid
                 0             0 Valid
              -150          null Negative Spend --- Investigate
              null          null Missing Spend

The stated project treatment retains missing Spend as null and flags
negative values.

## Revenue

For missing Revenue, examine Spend, Conversions, Leads, Clicks and
Impressions.

Interpretation patterns:

-   Revenue NULL + Conversions = 0 + Spend \> 0 → could mean zero
    revenue, but confirm
-   Revenue NULL + Conversions \> 0 → strongly suggests missing revenue
    data
-   Revenue NULL + Spend = 0 + Conversions = 0 → may be zero depending
    on business definition
-   Revenue NULL + Spend NULL → both are missing; investigate

Stated finding:

> 150 records had missing Revenue. 142 had recorded conversions and the
> remaining records had marketing activity or missing spend. Because the
> data did not provide sufficient evidence that these represented zero
> revenue, Revenue was retained as null and flagged for investigation.

## Clicks

Reported original Clicks had no null values.

Invalid clicks where Clicks \> Impressions are converted to null in
`Clean_Clicks`.

Quality expression:

``` powerquery
if [Clicks] = null then "Missing Clicks - Investigate"
else if [Impressions] = null then "Cannot Validate"
else if [Clicks] > [Impressions] then "Invalid - Clicks > Impressions"
else "Valid"
```

Reported invalid rows: **35**.

Preserve original Clicks; use `Clean_Clicks` for analysis.

## Leads

Reported: no Leads data-quality issue.

## Conversions

Reported:

-   Conversions null: 0
-   Conversions \> Clicks: 20

Use `Clean_Conversions` for analysis.

## Impressions

No reported issues.

## Email metrics

  Email data-quality check      Rows Treatment
  --------------------------- ------ -----------
  Email_Sent = null                0 No issue
  Email_Opens = null               0 No issue
  Email_Clicks = null              0 No issue
  Email_Opens \> Email_Sent        0 No issue

------------------------------------------------------------------------

# 7. Final data-quality summary

  -----------------------------------------------------------------------
  Field                   Issue                   Treatment
  ----------------------- ----------------------- -----------------------
  Channel                 Inconsistent labels     Standardised

  Customer_Segment        Inconsistent labels     Standardised

  State                   Missing/inconsistent    Standardised / derived
                                                  where supported

  Campaign_Name           Missing                 Recovered using
                                                  Campaign_Master

  Duplicate records       150                     Removed after
                                                  validation

  Spend                   220 nulls               Keep null + flag

  Spend                   Negative values         Flag/investigate

  Revenue                 150 nulls               Keep null + flag

  Clicks                  35 \> Impressions       Flag/investigate

  Leads                   No issue                Keep

  Conversions             20 \> Clicks            Flag/investigate

  Email metrics           No identified issues    Keep
  -----------------------------------------------------------------------

------------------------------------------------------------------------

# 8. Final fact and dimension tables

## Fact_Marketing

Create a duplicate of `Fact_Marketing_Raw_Cleaning` and rename it
`Fact_Marketing`.

Keep:

  Column                      Purpose
  --------------------------- -----------------------------
  Campaign_ID                 Campaign foreign key
  Campaign_Date               Date foreign key
  Channel_ID                  Channel foreign key
  Geography_Key               Geography foreign key
  Customer_ID                 Customer identifier
  Standard_Customer_Segment   Customer analysis
  Age_Group                   Customer analysis
  Device                      Customer analysis
  Clicks                      Original/source value
  Clean_Clicks                Use for analysis
  Impressions                 Funnel metric
  Conversions                 Original/source value
  Clean_Conversions           Use for analysis
  Leads                       Funnel metric
  Spend                       Original/source value
  Clean_Spend                 Use for analysis
  Revenue                     Original/source value
  Clean_Revenue               Use for analysis
  New_Customers               Acquisition metric
  Email_Sent                  Email metric
  Email_Opens                 Email metric
  Email_Clicks                Email metric
  DQ columns                  Audit / quality information

No separate `Dim_Customer` is created because Customer_ID is not unique
enough to support a clean customer dimension in this dataset; the
combination of ID, segment, age group and device creates unique
combinations.

## Dim_Channel

Keep:

-   Channel_ID
-   Standard_Channel
-   Platform
-   Channel_Category

Remove duplicates only where the remaining combination is a unique
dimension record.

## Dim_Campaign

Keep:

-   Campaign_ID
-   Campaign_Name
-   Campaign_Type
-   Objective

Remove duplicates only where the combination is a unique dimension
record.

## Dim_Geography

Keep:

-   State
-   City

Add an Index column starting at 1 and rename it `Geography_Key`.

## Dim_Date

Create in Power Pivot / DAX:

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

# 9. Data model

Load only the fact and dimension tables to the model.

For investigation/helper queries, right-click and turn off **Enable
Load**.

Click **Close & Apply**.

## Relationships

Create:

-   `Dim_Date[Date]` → `Fact_Marketing[Campaign_Date]`
-   `Dim_Campaign[Campaign_ID]` → `Fact_Marketing[Campaign_ID]`
-   `Dim_Channel[Channel_ID]` → `Fact_Marketing[Channel_ID]`
-   `Dim_Geography[Geography_Key]` → `Fact_Marketing[Geography_Key]`

For each relationship:

-   Cardinality: \*\*One to many (1:\*)\*\*
-   Cross-filter direction: **Single**
-   Active: **Yes**

Do not create relationships between dimension tables.

## Validate dimension keys

Confirm that each dimension key is unique on the "1" side.

The fact table is allowed to contain repeated foreign-key values.

## Model test

Create a Table visual and test campaign/channel names against
`Clean_Revenue`.

If filters correctly affect fact-table values and the resulting totals
are sensible, the model is working as intended.

------------------------------------------------------------------------

# 10. DAX measures

## 1. Total Spend

``` dax
Total Spend =
SUM(Fact_Marketing[Clean_Spend])
```

## 2. Total Revenue

``` dax
Total Revenue =
SUM(Fact_Marketing[Clean_Revenue])
```

## 3. Total Impressions

``` dax
Total Impressions =
SUM(Fact_Marketing[Impressions])
```

## 4. Total Clicks

``` dax
Total Clicks =
SUM(Fact_Marketing[Clean_Clicks])
```

## 5. Total Leads

``` dax
Total Leads =
SUM(Fact_Marketing[Leads])
```

## 6. Total Conversions

``` dax
Total Conversions =
SUM(Fact_Marketing[Clean_Conversions])
```

## 7. New Customers

``` dax
New Customers =
SUM(Fact_Marketing[New_Customers])
```

## 8. CTR

``` dax
CTR =
DIVIDE([Total Clicks], [Total Impressions])
```

Format as Percentage.

CTR = Clicks ÷ Impressions.

Example: 4.25% means approximately 4.25 clicks per 100 impressions.

## 9. Conversion Rate

``` dax
Conversion Rate =
DIVIDE(
    SUM(Fact_Marketing[Clean_Conversions]),
    SUM(Fact_Marketing[Clean_Clicks]),
    0
)
```

Format as Percentage.

Conversion Rate = Conversions ÷ Clicks.

Example: 5M conversions ÷ 164M clicks ≈ 3.05%.

CTR answers whether impressions become clicks; Conversion Rate answers
whether clicks become conversions.

## 10. CPC

``` dax
CPC =
DIVIDE([Total Spend], [Total Clicks])
```

Format as currency.

CPC = Total Spend ÷ Total Clicks.

## 11. CPA

``` dax
CPA =
DIVIDE([Total Spend], [Total Conversions])
```

CPA = Total Spend ÷ Total Conversions.

It measures the marketing spend required to generate one conversion.

## 12. ROAS

``` dax
ROAS =
DIVIDE([Total Revenue], [Total Spend])
```

ROAS = Revenue ÷ Spend.

Example:

-   Revenue = \$500,000
-   Spend = \$100,000
-   ROAS = 5.0

This means \$5 of revenue was generated per \$1 of marketing spend.

Do not format ROAS as a percentage.

## 13. ROI

Project definition:

``` dax
ROI =
DIVIDE(
    [Total Revenue] - [Total Spend],
    [Total Spend]
)
```

Format as Percentage.

ROI = (Revenue − Spend) ÷ Spend.

Example:

-   Revenue = \$500,000
-   Spend = \$100,000
-   ROI = 400%

ROAS measures revenue generated relative to advertising spend; ROI
measures return after subtracting marketing spend.

> Note: This project's ROI is a marketing-return definition. It does not
> account for product costs, fulfilment, overhead, discounts, taxes or
> other business costs unless those are included elsewhere.

------------------------------------------------------------------------

# 11. Measure table

Create a dedicated measure table using **Enter data**.

Hide the dummy column.

Move measures to the measure table using:

**Select measure → Measure tools → Home table → Measures**

This keeps the model organised and separates calculations from source
fields.

------------------------------------------------------------------------

# 12. Dashboard

## Page 1 --- Campaign Performance

Purpose: campaign-level efficiency and financial performance.

### Recommended KPIs

  KPI             Business question
  --------------- -----------------------------------------------------
  Total Spend     How much are selected campaigns investing?
  Total Revenue   How much revenue are selected campaigns generating?
  ROAS            How much revenue is generated per \$1 spent?
  ROI             What return remains after marketing spend?
  Conversions     How many conversions are generated?
  CPA             How much does a conversion cost?
  CPC             How much does a click cost?

Suggested KPI cards:

**Spend \| Revenue \| ROAS \| Conversions \| CPA**

Keep ROI and CPC available in the campaign table.

## Visual 1 --- Revenue vs Spend by Campaign

Scatter chart:

-   X-axis → `[Total Spend]`
-   Y-axis → `[Total Revenue]`
-   Details → `Dim_Campaign[Campaign_Name]`
-   Tooltips → ROAS, ROI, Conversions, CPA

## Visual 2 --- ROAS by Campaign

Bar chart.

## Visual 3 --- Conversions by Campaign

Bar chart.

## Visual 4 --- Campaign Performance Table

  ----------------------------------------------------------------------------------------------
  Campaign     Spend   Revenue    ROAS     ROI   Conversions     CPA     CPC     CTR         New
                                                                                       Customers
  ---------- ------- --------- ------- ------- ------------- ------- ------- ------- -----------

  ----------------------------------------------------------------------------------------------

### Conditional formatting

Use metric-based visual cues for:

-   ROAS
-   CPA
-   Revenue

For ROAS, higher/lower values can be visually distinguished.

For CPA, lower/higher values can be visually distinguished.

For Revenue, higher/lower values can be visually distinguished.

These are visual cues for investigation, not an overall campaign
ranking.

## Slicers

  Slicer          Business question
  --------------- -------------------------------------------------------
  Date            How did campaigns perform during the selected period?
  Campaign        How is a specific campaign performing?
  Channel         Are results different by channel?
  Campaign Type   Does performance vary by campaign type?
  Objective       Are campaigns achieving their intended objective?

------------------------------------------------------------------------

# 13. Marketing Funnel page

## Recommended KPIs

  -----------------------------------------------------------------------
  KPI                     What it tells the       Measure
                          business                
  ----------------------- ----------------------- -----------------------
  Total Impressions       Marketing exposure      `[Total Impressions]`

  Total Clicks            Traffic generated       `[Total Clicks]`

  CTR                     Impressions converted   `[CTR]`
                          to clicks               

  Total Leads             Users reaching the lead `[Total Leads]`
                          stage                   

  Total Conversions       Completed target        `[Total Conversions]`
                          actions                 

  Conversion Rate         Clicks converted to     `[Conversion Rate]`
                          conversions             
  -----------------------------------------------------------------------

The funnel should help distinguish:

**Exposure → Click → Lead → Conversion**

and identify where performance changes between stages.

------------------------------------------------------------------------

# 14. Interview/business interpretation prompts

## How would you identify a high-performing marketing channel?

A defensible analytical response is:

> "I would not look at revenue alone. I would compare revenue with spend
> and evaluate ROAS, CPA and conversion rate. A channel generating high
> revenue but requiring disproportionately high spend may not be the
> most efficient channel."

## Why CPA matters

Consider:

  Campaign       Conversions    CPA
  ------------ ------------- ------
  Campaign A          10,000   \$25
  Campaign B          12,000   \$80

Conversion volume alone does not capture acquisition efficiency.

## ROAS vs ROI

**ROAS:** revenue generated relative to marketing spend.

**ROI:** return after subtracting marketing spend, using this project's
definition:

`(Revenue − Spend) ÷ Spend`

ROAS is useful for marketing efficiency; ROI is useful for evaluating
the return after marketing cost.

------------------------------------------------------------------------

# 15. Final quality-control checklist

Before using the dashboard for business decisions, verify:

-   [ ] Source data preserved
-   [ ] Raw profiling completed
-   [ ] Missing values documented
-   [ ] Duplicate records investigated and validated
-   [ ] Text values standardised
-   [ ] Campaign names recovered where possible
-   [ ] State values validated against City
-   [ ] Clicks \<= Impressions
-   [ ] Leads \<= Clicks
-   [ ] Conversions \<= Clicks
-   [ ] Spend \>= 0
-   [ ] Revenue \>= 0
-   [ ] Email funnel rules validated
-   [ ] Invalid values represented as null in Clean\_\* fields where
    appropriate
-   [ ] Financial nulls not automatically converted to zero
-   [ ] Date range validated
-   [ ] Correct data types applied
-   [ ] Fact/dimension grain documented
-   [ ] Dimension keys unique
-   [ ] Relationships are 1:\* and single direction
-   [ ] No dimension-to-dimension relationships
-   [ ] DAX measures use cleaned metrics
-   [ ] KPI formatting checked
-   [ ] Dashboard filters tested
-   [ ] Data-quality findings retained for auditability

## Key principle

**Never silently fix ambiguous business data.**

Preserve the original source values, create explicit quality flags and
cleaned analytical fields, document assumptions, and obtain
business-owner confirmation before turning an ambiguous null into a zero
or otherwise changing the meaning of a financial or customer metric.
