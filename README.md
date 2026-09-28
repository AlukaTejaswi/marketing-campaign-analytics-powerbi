# UrbanCart Marketing Campaign Performance Analytics

> An end-to-end Power BI marketing analytics project analysing campaign performance, marketing funnel efficiency, channel effectiveness, customer segments, geography and return on marketing investment.

---

## Table of Contents

- [Overview](#overview)
- [Business Problem](#business-problem)
- [Business Objectives](#business-objectives)
- [Dataset](#dataset)
- [Tools & Technologies](#tools--technologies)
- [Project Structure](#project-structure)
- [Data Quality & Cleaning](#data-quality--cleaning)
- [Data Model](#data-model)
- [Key DAX Measures](#key-dax-measures)
- [Business Questions Answered](#business-questions-answered)
- [Key Insights](#key-insights)
- [Power BI Dashboard](#power-bi-dashboard)
- [Full Campaign Performance Results](#full-campaign-performance-results)
- [How to Run This Project](#how-to-run-this-project)
- [Final Recommendations](#final-recommendations)
- [Skills Demonstrated](#skills-demonstrated)
- [Disclaimer](#disclaimer)
- [Author](#author)

---

## Overview

UrbanCart is a fictional Australian retail business created to demonstrate an end-to-end Data Analyst and Business Intelligence workflow.

This project analyses marketing performance across campaigns, channels, customer segments, devices and geography using Power BI, with a focus on turning marketing data into actionable business insights.

---

## Business Problem

UrbanCart needs a consolidated view of marketing performance to understand:

- How much revenue is generated from marketing investment?
- How efficiently is marketing spend converted into revenue?
- How are customers progressing through the marketing funnel?
- Which campaigns generate stronger revenue, conversions and returns?
- Which channels and customer groups contribute to engagement and conversions?
- Where are the major funnel drop-offs?
- Which historical campaign patterns could inform future campaign testing?

---

## Business Objectives

1. Measure overall marketing investment and revenue performance.
2. Monitor ROAS, ROI, CPA, CPC, CTR and conversion performance.
3. Analyse the marketing funnel from impressions through conversions.
4. Compare campaign-level revenue, spend, conversions and efficiency.
5. Analyse channel, customer segment, device and geographic performance.
6. Identify funnel drop-offs and potential areas for optimisation.
7. Distinguish customer acquisition performance from retention and reactivation.
8. Provide an executive-friendly Power BI dashboard to support marketing decision-making.

---

## Dataset

The project uses a synthetic UrbanCart marketing dataset covering **2024–2025**.

Key fields include:

- Campaign
- Date
- Channel
- Customer
- Customer segment
- Age group
- Device
- State and City
- Impressions
- Clicks
- Leads
- Conversions
- Spend
- Revenue

The final Campaign Performance view contains **25 campaign names**.

---

## Tools & Technologies

- Power BI
- DAX
- Power Query
- Data modelling
- Data quality validation
- Business intelligence
- Dashboard design
- GitHub

---

## Project Structure

```text
UrbanCart-Marketing-Analytics/
│
├── README.md
├── .gitignore
├── UrbanCart Marketing Campaign Performance Report.pdf
│
├── data/                         # Raw and cleaned marketing datasets
│
├── dashboard/                      # Power BI dashboard file
│   └── UrbanCart Marketing Analytics.pbix
│
├── dax/                          # DAX measures and calculations
│
├── documentation/                # Data dictionary, business insights
│                                # and project documentation
│
└── screenshots/                  # Dashboard screenshots and previews
```

---

## Data Quality & Cleaning

Key data-quality checks included:

| Check | Action |
|---|---|
| Duplicate records | 150 duplicates identified and removed |
| State values | Unknown values investigated and corrected/derived using city information where appropriate |
| Missing Spend | Missing values identified and investigated |
| Missing Revenue | Missing values identified and investigated |
| Clicks > Impressions | Invalid records flagged for investigation |
| Conversions > Clicks | Invalid records flagged for investigation |
| Category consistency | Values standardised and mapping tables created |

The cleaning process was designed to preserve source information while providing reliable fields for analytical calculations.

---

## Data Model

The Power BI model uses a fact-and-dimension approach.

### Fact Table

**Fact_Marketing**

Includes:

- Campaign
- Date
- Channel
- Geography
- Customer
- Customer Segment
- Device
- Impressions
- Clicks
- Leads
- Conversions
- Spend
- Revenue

### Dimension Tables

- `Dim_Date`
- `Dim_Campaign`
- `Dim_Channel`
- `Dim_Geography`
- `Channel Mapping`
- `Segment Mapping`

---

## Key DAX Measures

### Total Spend
``` dax
Total Spend =
SUM(Fact\_Marketing\[Clean\_Spend])
```

### Total Revenue
``` dax
Total Revenue =
SUM(Fact\_Marketing\[Clean\_Revenue])
```

### Total Conversions
``` dax
Total Conversions =
SUM(Fact\_Marketing\[Clean\_Conversions])
```

### ROAS
``` dax
ROAS =
DIVIDE(\[Total Revenue], \[Total Spend], 0)
```

### Conversion Rate
``` dax
Conversion Rate % =
DIVIDE(\[Total Conversions], \[Total Clicks], 0)
```

### CTR
``` dax
CTR % =
DIVIDE(\[Total Clicks], \[Total Impressions], 0)
```

### CPC 
``` dax
CPC =
DIVIDE(\[Total Spend], \[Total Clicks], 0)
```

### CPA
``` dax
CPA =
DIVIDE(\[Total Spend], \[Total Conversions], 0)
```

### ROI
``` dax
ROI =
DIVIDE(\[Total Revenue] - \[Total Spend], \[Total Spend], 0)
```
---

## Business Questions Answered

### Overall Performance

- How much are we spending on marketing?
- How much revenue is being generated?
- What is the overall ROAS and ROI?
- How many leads and conversions are being generated?
- Is marketing efficiency improving or declining over time?

### Funnel

- How many users move from impressions to clicks?
- How many clicks become leads?
- How many leads become conversions?
- Where are the largest funnel drop-offs?
- How is conversion rate changing over time?

### Campaigns

- Which campaigns generate the most revenue?
- Which campaigns generate the highest conversion volume?
- Which campaigns have stronger ROAS?
- Which campaigns have lower CPA?
- Are high-volume campaigns also efficient?

### Channels

- Which channels generate stronger engagement?
- Which channels convert more effectively?
- Which channels contribute the most conversions?
- How do owned channels compare with paid media?

### Customers

- How much conversion volume comes from new versus existing customers?
- Which customer segments contribute the most conversions?
- Should acquisition and retention performance be evaluated separately?

### Geography & Devices

- Which states and cities contribute the most revenue and conversions?
- Does device type affect conversion performance?
- Where are potential geographic opportunities for further investigation?

---

## Key Insights

### 1. UrbanCart is scaling revenue, but efficiency is broadly flat

UrbanCart generated **$241.36M revenue from $130.97M marketing spend**, delivering **1.84x ROAS**. Revenue and spend have both almost doubled versus the previous period, while ROAS has moved only slightly from **1.86x to 1.84x**.

**Business impact:**  

UrbanCart is successfully scaling, but growth is currently requiring a broadly proportional increase in marketing investment.

**Management implication:**  

The next growth target should focus on improving incremental return from existing spend, rather than relying purely on additional budget.

### 2. Payday Promotion delivers scale, but scale alone doesn't justify more budget

Payday Promotion generated approximately **90.8K conversions** and **$10.17M revenue**, making it one of the largest campaigns by conversion and revenue. However, it required **$5.54M in spend**, producing **1.83x ROAS**, which is close to the overall portfolio ROAS of **1.84x**.

**Business impact:**  

Payday Promotion is an important volume driver, but its large conversion contribution does not necessarily mean it is generating superior efficiency.

**Management implication:**  

Before increasing Payday Promotion investment, evaluate:

- New-customer acquisition
- Average order value
- Gross margin
- Repeat purchase rate
- Customer lifetime value

The key question is whether the campaign is creating valuable customers or primarily driving short-term promotional volume.

### 3. UrbanCart's biggest visible funnel opportunity is after lead generation

UrbanCart generates **33.12M leads**, but only **2.17M conversions**. The Lead → Conversion rate is approximately **6.55%**, meaning **93.45% of leads do not currently progress to conversion**.

**Business impact:**  

The dashboard suggests there may be more value in improving the conversion of existing leads than simply generating additional traffic.

**Management implication:**  

Investigate:

- Lead quality
- Follow-up timing
- Offer relevance
- Landing-page experience
- Remarketing
- Checkout friction

### 4. Email is a major conversion engine — but attribution needs to be separated from incrementality

Email has the highest visible **CTR at 8.62%** and the highest channel **conversion rate at 1.81%**. It also contributes approximately **1.04M conversions**, nearly half of total reported conversions.

**Business impact:**  

UrbanCart's customer database appears to be a major commercial asset.

**Management implication:**  

The opportunity is not simply to send more emails. It is to determine how much incremental revenue Email generates versus purchases that would have occurred anyway.

For the Marketing Manager, the key question is:

> **"How much revenue is Email actually creating, rather than simply receiving attribution for?"**

This is particularly important before reallocating significant budget based on channel attribution alone.

### 5. Existing customers are a major source of UrbanCart's conversion volume

The customer analysis shows:

| Customer Type | Conversions | Share |
|---|---:|---:|
| New Customers | 823K | 38.26% |
| Returning Customers | 759K | 35.30% |
| Loyal Customers | 355K | 16.51% |
| At-Risk Customers | 214K | 9.93% |

Approximately **62% of conversions come from Returning, Loyal and At-Risk customers combined**.

**Business impact:**  

UrbanCart is not purely an acquisition-led business. A significant proportion of marketing performance is coming from customers who already have a relationship with the brand.

**Management implication:**  

Marketing performance reporting should separate:

- **New Customer Acquisition**
- **Retention / Reactivation**

because their economics are fundamentally different.

---

## Power BI Dashboard
### Open Power BI Dashboard:
'./dashboard/UrbanCart\_marketing\_campaign\_performance\_dashboard.pbix'

### 1. UrbanCart Marketing Performance Overview
**Business question:** How is UrbanCart's overall marketing performance?

#### KPIs

- Total Spend
- Total Revenue
- ROAS
- Total Leads
- Total Conversions
- Conversion Rate

#### Visuals

- Revenue & Spend Trend
- Overall ROAS Trend
- Marketing Investment → Business Outcome
- Performance vs Previous Period
- Monthly Conversion Rate
  
!\[UrbanCart Marketing Performance Overview](./screenshots/01-marketing-performance-overview.png)

### 2. Marketing Funnel
**Business question:** How effectively are users progressing through the marketing funnel?

#### Funnel

**Impressions → Clicks → Leads → Conversions**

#### Analysis

- Funnel volume
- CTR
- Lead Rate
- Lead-to-Conversion Rate
- Funnel drop-off
- Funnel volume trend
- Conversion and conversion-rate trend

  !\[UrbanCart Marketing Performance Overview](./screenshots/02-marketing-funnel.png)

### 3. Campaign Performance
**Business question:** How have individual campaigns performed financially and operationally?

#### KPIs

- Total Spend
- Total Revenue
- ROAS
- Total Conversions
- CPA

#### Visuals

- Revenue, Cost and ROI by Campaign
- ROAS by Campaign
- Campaign Performance table
- Conversions by Campaign

The campaign table contains Spend, Revenue, ROAS, ROI, Conversions and CPA.

!\[UrbanCart Marketing Performance Overview](./screenshots/03-campaign-performance.png)

### 4. Channel & Customer Analysis
**Business question:** Which channels, customer groups, devices and locations are driving engagement and conversions?

#### Analysis

- CTR by Channel
- Conversion Rate by Channel
- Leads & Conversions by Channel
- Conversions by Customer Segment
- Conversion Rate by Device
- Conversions by Geography\## Full Campaign Performance Results

!\[UrbanCart Marketing Performance Overview](./screenshots/04-channel-customer-performance.png)

---

## Full Campaign Performance Results

The full Campaign Performance view shows:

| Metric | Result |
|---|---:|
| Total Spend | **$130.97M** |
| Total Revenue | **$241.36M** |
| ROAS | **1.84x** |
| Total Conversions | **2,168,828** |
| CPA | **$60.39** |
| ROI | **84.29%** |

These represent the full Campaign Performance view and may differ from values displayed on filtered dashboard pages.

---

## How to Run This Project

- Clone the repository.
- Open the `.pbix` file in Power BI Desktop.
- Update the data source path if required.
- Refresh the dataset.
- Explore the dashboard.

---

## Final Recommendations

### 1. Investigate high-efficiency campaigns

New Product Launch and EOFY Deals showed strong historical ROAS and relatively low CPA. Their audience, offer, channel mix, timing and creative could be investigated to identify transferable practices.

### 2. Analyse high-volume campaigns separately

Payday Promotion, Tech Upgrade, Refer a Friend and Black Friday generated high conversion volumes. These campaigns can be evaluated for their ability to generate scale as well as efficiency.

### 3. Investigate lower-efficiency campaigns

VIP Customer Offer could be investigated further to understand whether audience, offer, channel mix, timing or acquisition cost contributed to its lower ROAS.

### 4. Use historical results as benchmarks

Historical ROAS, CPA and conversion performance can be used as benchmarks for future campaign tests. Historical performance should be treated as evidence for testing, not as a guarantee of future performance.

---

## Skills Demonstrated

- Data cleaning
- Data quality analysis
- Data transformation
- Data modelling
- Power Query
- DAX
- KPI development
- Marketing analytics
- Funnel analysis
- Campaign performance analysis
- Customer segmentation
- Dashboard design
- Business storytelling
- Insight generation
- Stakeholder-focused reporting

---

## Disclaimer

UrbanCart is a fictional company created for portfolio and learning purposes. The dataset is synthetic and does not represent real UrbanCart business performance.

The recommendations are analytical suggestions based on the available historical data and should be validated with additional business, financial and customer information before being used for real-world decisions.

---

## Author
**Tejaswi Aluka**
Data Analyst / BI Portfolio Project
Email: alukatejaswi@gmail.com
🔗 \[LinkedIn](linkedin.com/in/tejaswi-aluka-9240a3403)

**Skills:** SQL | Power BI | Excel | Python | DAX





