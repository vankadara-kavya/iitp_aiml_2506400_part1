# Part 1: Data Audit & EDA

## What this is

I looked at 7 data files about 2,400 customers from a personal-care brand to find data quality problems, explore patterns, and understand why customers are leaving (churning).

## How to run this

**Step 1:** Set up Python environment
```bash
python3 -m venv venv
source venv/bin/activate
```

**Step 2:** Install the packages
```bash
pip install -r requirements.txt
```

**Step 3:** Put the data files in the `data/` folder

You need these 7 CSV files:
- customers.csv
- orders.csv
- support_tickets.csv
- web_events_snapshot.csv
- churn_labels.csv
- rfm_modeling_snapshot.csv
- intervention_history.csv

**Step 4:** Open the notebook
```bash
jupyter notebook eda_audit.ipynb
```

Run all cells. Charts will be saved in the `charts/` folder automatically.

## What's in this folder

```
Part1_Data_Audit_EDA/
├── data/                        ← Put the CSV files here
├── charts/                      ← Charts get saved here when you run the script
├── eda_audit.ipynb              ← The main analysis notebook
├── data_quality_report.md       ← What data problems I found and how I handled them
├── business_memo.md             ← Summary of findings for non-technical readers
├── requirements.txt             ← List of Python packages needed
└── README.md                    ← You're reading this
```

## What I found

- **47% of customers churned** (stopped buying in the 60 days after Sep 30, 2025)
- **Biggest churn signal:** How long since their last order (recency). Longer gap = more likely to leave.
- **Website activity drops before churn.** Churners visited the site 26.6 days ago on average, vs 9.8 days for stayers.
- **How they found us matters.** Organic customers are most loyal (39.8% churn). Google Search customers leave the most (50.4%).
- **Loyalty program helps.** Platinum members churn at 37.1% vs 48.3% for non-members.
- **Age and city don't matter much.** Only 2-3% difference between groups.
- **Support tickets don't cause churn.** Customers with and without tickets churn at almost the same rate.
