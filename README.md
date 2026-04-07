# Google Ads Campaign EDA — Data Analytics Course

## Overview

Analysis of a simulated Google Ads campaign promoting a data analytics course, covering November 2024. The dataset was intentionally raw and uncleaned, closely mimicking real ad platform exports.

**Goal:** Analyse the Google Ads campaign performance and give business recommendations based on insights

### Dataset

2,600 rows of ad-level campaign data with the following columns (after cleaning):

| Column | Description |
|---|---|
| `ad_id` | Unique identifier per ad |
| `campaign_name` | Campaign name (contained typo variants) |
| `ad_date` | Date of ad activity (inconsistent formats) |
| `location` | City where the ad was served |
| `device` | Device type: desktop, mobile, tablet |
| `keyword` | Search keyword that triggered the ad |
| `impressions` | Number of times the ad was displayed |
| `clicks` | Number of clicks received |
| `leads` | Number of leads generated |
| `conversions` | Number of actual conversions |
| `conversion_rate` | Conversions ÷ clicks |
| `cost_amount` | Total ad spend ($) |
| `sale_amount` | Revenue generated ($) |

---

## What I Did

**Data cleaning** (`cleaning.ipynb`)
- Standardised categorical inconsistencies in `campaign_name`, `location`, and `device` using fuzzy matching (`thefuzz`)
- Parsed 3 inconsistent date formats (`YYYY-MM-DD`, `DD-MM-YYYY`, `YYYY/MM/DD`) into a unified datetime column
- Decomposed composite columns `cost` and `sale_amount` (e.g. `$231.88`) into numeric values
- Dropped the original `conversion_rate` column and recalculated it consistently as `conversions / clicks`
- Imputed missing values using KNN imputation across numeric columns, with a fallback logic prioritising back-calculation before imputation
- Dropped 3 rows where both `clicks` and `conversions` were missing

**EDA** (`eda.ipynb`)
- Computed campaign-level KPIs: CTR, CVR, CPA, ROAS, revenue per click
- Analysed funnel drop-off by device and keyword
- Time series analysis of sales, cost, and CVR over November 2024
- Seasonal decomposition of CVR (period=5) to detect weekly patterns

---

## Insights

- Cost-per-acquisition (CPA) in November 2024 is 32.96; Revenue_per_click is $10.76

<img width="1784" height="982" alt="image" src="https://github.com/user-attachments/assets/cad3bb55-44ad-4e78-a4f9-8226ac2bc61c" />

- **ROAS of 7.03x** ($1 spent on ads budget can generate $7.03 sales) — strong performance relative to the 4x industry benchmark
- **Nov 13–14 sales spike ($174k)** can be driven by a small increase in cost (the patterns are consistent in both sales and cost
- cost and sales are strongly linearly correlated (.97) --> as expected

<img width="1184" height="584" alt="image" src="https://github.com/user-attachments/assets/2ebb7f63-ccab-4840-a96f-1c0eaec6b7ce" />

- Monday and Sunday's conversion rates fall below the overall conversion rate (4.74% and 4.97%, respectively). Thus, we should put more effort into marketing on the days other than those two to increase the conversion rate.

<img width="1779" height="987" alt="image" src="https://github.com/user-attachments/assets/25c34ccf-0993-4d81-ab95-c1b1b9000cce" />

- **CTR is flat (~3.1–3.4%)** with no meaningful day-of-week variation, suggesting the marketing efforts are consistent but could be optimised
- **CVR is more volatile (4.5–5.5%)** — the bottleneck is at the top of the funnel (impressions → clicks), not the landing page
- **"Learn Data Analytics"** keyword has the highest CVR (5.14%) while **"Data Analitics Online"** has the lowest (4.86%) despite comparable impressions

<img width="1182" height="784" alt="image" src="https://github.com/user-attachments/assets/77b14ac3-e676-4f9d-af9b-acb17957937f" />

- Seasonal decomposition shows a weak 5-day pattern (±0.05%) — insufficient to justify bid schedule changes with only 30 days of data

---

## Recommendations & Next Steps

1. **Run A/B tests on ad copy** to improve CTR, which is the primary funnel bottleneck
2. **Investigate the Nov 13–14 demand spike** and a bounce back conversion_rate on Nov 18-19 — identify the external trigger to replicate conditions in future campaigns
3. **Build a Tableau dashboard** segmenting ROAS and CVR by device and keyword for ongoing monitoring
4. **Increase budget on high-CVR keywords** (`learn data analytics`, `data analytics course`) and reduce on underperformers
5. Avoid aggressive marketing efforts (e.g. increase budget) on Monday and Sunday
6. **Extend data collection to 8–12 weeks** before drawing conclusions from seasonal decomposition
