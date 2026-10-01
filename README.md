# Enterprise Data Reconciliation & Revenue Analytics

An end-to-end data analytics project that integrates enterprise customer records, raw server logs, and daily EUR/USD exchange rates to produce a reconciled revenue dataset and management dashboard.

## Business Problem

An enterprise has customer status history in a database, transaction information embedded in raw server logs, and daily EUR/USD exchange rates in a separate file. The objective is to:

1. Identify each user's most recent status and retain only currently active users.
2. Extract valid transaction details from unstructured server logs and exclude error records.
3. Match transactions to daily EUR/USD rates, forward-filling missing market dates.
4. Calculate reconciled USD revenue for transactions belonging to active users.
5. Produce management-level views of monthly revenue and the top five customers by revenue.

## Tech Stack

- **SQL / SQLite** — window functions and latest-status extraction
- **Python** — pipeline orchestration
- **Pandas** — cleaning, transformation, joins and aggregation
- **Regex** — structured extraction from raw server logs
- **Matplotlib** — management dashboard
- **Jupyter Notebook** — reproducible workflow

## Project Architecture

```text
Enterprise Database (SQLite) ─────┐
                                  │
Raw Server Logs (TXT) ────────────┼──> Cleaning & Reconciliation ──> Final Revenue Dataset
                                  │                                      │
Daily EUR/USD Rates (CSV) ────────┘                                      └──> CFO Dashboard
```

## Key Results

| Metric | Result |
|---|---:|
| Raw server-log records | 100,000 |
| Error records removed | 5,098 |
| Valid transactions extracted | 94,902 |
| Final reconciled transactions | 53,899 |
| Active customers represented | 13,888 |
| Missing FX rates after forward-fill | 0 |
| Reconciled USD revenue | ~$148.27M |

## Analytical Workflow

### 1. Active-user extraction

The SQLite database contains historical user records. A SQL `ROW_NUMBER()` window function ranks records by `updated_at` for each `user_id`. Only the latest record is retained, and users whose latest status is `Active` are selected.

### 2. Server-log parsing

The raw log file is read as text. Rows containing `ERROR` are excluded, and vectorized `Series.str.extract()` with Regex captures:

- Date
- User ID
- Product ID
- Euro transaction value

No Pandas `for`/`iterrows()` loops are used.

### 3. Financial reconciliation

Transactions are matched to the EUR/USD exchange-rate table by date. Missing exchange-rate values are forward-filled using the most recent available rate. Transactions are then joined to the latest active-user table by `User_ID`.

The revenue calculation is:

```text
USD Revenue = Euro Value × EUR-to-USD Rate
```

### 4. Business reporting

The final dataset is aggregated into:

- Monthly USD revenue
- Top five customers by total USD revenue

## Repository Structure

```text
enterprise-data-reconciliation-revenue-analytics/
│
├── README.md
├── requirements.txt
├── .gitignore
│
├── notebooks/
│   └── Enterprise_Data_Reconciliation.ipynb
│
├── data/
│   ├── raw/
│   │   ├── enterprise_database.db
│   │   ├── server_logs.txt
│   │   └── daily_exchange_rates.csv
│   │
│   └── processed/
│       └── df_final.csv
│
└── dashboard/
    └── CFO_Financial_Audit_Dashboard.png
```

## How to Run

### 1. Clone the repository

```bash
git clone <YOUR_GITHUB_REPOSITORY_URL>
cd enterprise-data-reconciliation-revenue-analytics
```

### 2. Install dependencies

```bash
pip install -r requirements.txt
```

### 3. Open the notebook

```bash
jupyter notebook notebooks/Enterprise_Data_Reconciliation.ipynb
```

Run all cells from top to bottom. The notebook reads the raw files from `data/raw/` and writes the reconciled dataset to `data/processed/` and the dashboard to `dashboard/`.

## Performance

The workflow processes 100,000 raw log records using vectorized Pandas string operations and SQL window functions rather than row-wise Pandas loops. In the executed run, Regex extraction took approximately 0.23 seconds and the merge/calculation stage approximately 0.11 seconds.

## Dashboard Preview

The dashboard presents monthly USD revenue and the top five customers by reconciled revenue.

![CFO Financial Audit Dashboard](dashboard/CFO_Financial_Audit_Dashboard.png)

## Portfolio Highlights

This project demonstrates practical experience with:

- Advanced SQL and window functions
- Data cleaning and validation
- Regex-based unstructured-data extraction
- Data integration and reconciliation
- Time-based/temporal data handling
- Financial calculations and revenue analytics
- Vectorized Pandas processing
- Business KPI reporting and visualization

## Author

**Sneha**  
Data Analyst | SQL | Power BI | Excel | Python
