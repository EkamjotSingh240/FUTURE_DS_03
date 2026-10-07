# FUTURE_DS_03 — Marketing Funnel & Conversion Performance Analysis

## 📌 Task Objective
This project was completed as part of the **Data Science & Analytics** track internship (Task 3). The goal was to analyze marketing and web traffic data to understand how users move from **Visitor → Lead → Customer**, identify where they drop off, and recommend actions to improve conversion. Core business questions addressed:

- Where are users dropping off in the funnel?
- Which channels bring high-quality leads?
- How can conversion rates be improved?
- Which stages need optimization?

## 📊 Dataset
**Source:** [Google Analytics Customer Revenue Prediction (Kaggle)](https://www.kaggle.com/c/ga-customer-revenue-prediction)

This dataset contains session-level web analytics data from the Google Merchandise Store, covering **September 2016 to August 2017**. The original `train.csv` file (1.5 GB, ~903,653 sessions) was used rather than the larger `train_v2.csv`, since it contains the actual purchase outcomes needed to identify converting sessions, without the much heavier per-hit tracking data found in the v2 files.

**Key challenge:** Several fields (`device`, `geoNetwork`, `totals`, `trafficSource`) are stored as nested JSON strings within single columns rather than flat fields, requiring a dedicated flattening step before analysis.

## 🛠️ Tools Used
- **Jupyter Notebook (Python, pandas)** — JSON flattening, data cleaning, funnel definition, and exploratory analysis
- **Power BI Desktop** — dashboard building, DAX measures, and visualization

## 📓 Notebooks

### `01_data_preparation.ipynb`
- Loaded `train.csv` in chunks (100K rows at a time) to manage memory on limited hardware (8 GB RAM)
- Parsed and flattened the four nested JSON columns (`device`, `geoNetwork`, `totals`, `trafficSource`) into individual fields, keeping only the ~20 columns relevant to funnel analysis
- Converted `transactionRevenue` from its stored unit (millionths of a dollar) into real dollars (`Revenue`)
- Filled missing values appropriately: `bounces`, `newVisits`, and `Revenue` default to 0 when absent (their absence is meaningful, not an error)
- Relabeled placeholder values (`(not set)`, `(not provided)`, `not available in demo dataset`) as "Unknown," while preserving genuine values like `(none)` and `(direct)`
- Exported the cleaned, flattened dataset to `ga_sessions_flat.csv`

### `02_funnel_conversion_analysis.ipynb`
- Defined the three funnel stages:
  - **Visitor** — every session (baseline)
  - **Lead** — a session that did not bounce (`bounces = 0`), based on Google Analytics' own engagement signal
  - **Customer** — a session with `Revenue > 0`
  - Verified that every converting session also satisfies the Lead condition, so the funnel narrows consistently at each stage
- Calculated overall funnel conversion rates and the Lead → Customer drop-off
- Analyzed conversion and revenue by channel, device, country, and month
- Compared mean vs. median revenue per customer to identify order-value skew
- Documented all findings with supporting numbers, used directly in the dashboard's insights panel

## 📈 Dashboard Overview

### KPI Cards
- Total Sessions
- Total Customers
- Overall Conversion Rate
- Total Revenue
- Average Revenue per Customer
- Median Revenue

### Visuals
| Visual | Purpose |
|---|---|
| **Funnel (Visitor → Lead → Customer)** | Core conversion story and drop-off shape |
| **Monthly Trend (Sessions vs. Revenue)** | Shows traffic and revenue seasonality do not align |
| **Sessions / Revenue / Conversion Rate by Channel** | Three-way comparison of traffic volume, revenue, and conversion quality per channel |
| **Sessions by Device / Revenue by Device** | Shows device-level conversion gap |
| **Top 10 Countries by Sessions / by Revenue** | Shows geographic concentration of traffic vs. revenue |

### Design System
Visuals are color-coded by metric type for quick scanning:
- **Purple** — traffic/volume metrics (Sessions, Visitors)
- **Teal** — revenue metrics
- **Coral** — risk/conversion metrics (drop-off, conversion rate)

### Interactivity
- **Channel slicer**, **Device slicer**, **Date range slicer** — filter the entire dashboard

A PDF export of the dashboard is included in the `report/` folder, alongside a PNG screenshot in `screenshots/`.

## 💡 Key Insights & Recommendations

- **97.46% drop-off from Lead to Customer** — this is the critical bottleneck in the funnel. The vast majority of engaged sessions never convert, pointing to friction in the checkout/purchase step rather than in attracting initial interest.
- **Referral drives 42% of revenue despite being only the 4th largest traffic source** — referred visitors convert at a notably higher rate, suggesting referral partnerships and word-of-mouth channels deserve more marketing investment relative to their current traffic share.
- **Desktop generates 96% of revenue** — mobile sessions make up a meaningful share of traffic but rarely convert, indicating the mobile purchase experience likely needs improvement.
- **United States drives 94% of revenue**, but traffic is more globally spread — international markets bring visitors who aren't converting at comparable rates, a potential localization or trust/payment-method gap.
- **Session peak (November) and revenue peak (April) don't align** — visit and purchase seasonality differ, meaning marketing timed around traffic spikes alone may miss when customers are actually ready to buy.
- **Mean revenue ($133.74) is far above median ($49.45)** — a small number of large orders skew the average, so typical customer value is lower than the headline average suggests.

## 📁 Repository Structure
```
FUTURE_DS_03/
│
├── README.md
│
├── notebooks/
│   ├── 01_data_preparation.ipynb
│   └── 02_funnel_conversion_analysis.ipynb
│
├── powerbi/
│   └── funnel_conversion_dashboard.pbix
│
├── screenshots/
│   └── dashboard_overview.png
│
└── report/
    └── dashboard_overview.pdf
```

## 📝 Notes & Limitations
- The raw Kaggle dataset (`train.csv`, 1.5 GB), the cleaned/flattened output (`ga_sessions_flat.csv`, ~150 MB), and the Power BI file (`funnel_conversion_dashboard.pbix`, which embeds the full ~900K-row dataset) are not included in this repository, as all three exceed GitHub's file size limits. The raw file can be downloaded directly from the [Kaggle competition page](https://www.kaggle.com/c/ga-customer-revenue-prediction); running `01_data_preparation.ipynb` against it reproduces `ga_sessions_flat.csv`, which can then be loaded into Power BI to rebuild the dashboard following the structure described above. The PDF export and screenshot in this repository reflect the completed dashboard.
- Analysis is performed at the **session level**, not the unique-visitor level — a single visitor may contribute multiple sessions.
- "Lead" is a derived definition (non-bounced sessions), since the dataset does not include an explicit lead-stage label. This is documented so the funnel's middle stage is understood as an analytical proxy, not a ground-truth business event.
- August 2017 is a partial month in the source data and should be interpreted with that caveat when viewing the monthly trend.