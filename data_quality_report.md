# Data Quality Report

## What this report covers

I checked all 7 data files for problems before starting any analysis. Here's what I found and what I did about it.

---

## The datasets

There are 7 CSV files about 2,400 customers from a personal-care brand:

- **customers.csv** — who the customers are (age, city, how they signed up). 2,400 rows.
- **orders.csv** — what they bought and when. 10,009 rows (~4 orders per customer on average).
- **support_tickets.csv** — complaints and queries. 1,921 rows. Only 1,247 customers have tickets — the rest never contacted support.
- **web_events_snapshot.csv** — website activity in the last 30 days. 2,400 rows.
- **churn_labels.csv** — whether each customer churned or not. 2,400 rows.
- **rfm_modeling_snapshot.csv** — a ready-made feature table. 2,400 rows.
- **intervention_history.csv** — what campaigns were sent to each customer. 2,400 rows.

All files connect through `customer_id`.

---

## Problem 1: Missing values

Three columns have missing data:

| Column | How many missing | Why it's missing |
|--------|-----------------|------------------|
| `loyalty_tier` (customers) | 1,386 out of 2,400 (57.8%) | These customers never joined the loyalty program. It's not a mistake — the program is optional. |
| `skin_type` (customers) | 401 out of 2,400 (16.7%) | An optional field during signup. Many people just skip it. |
| `rating` (orders) | 80 out of 10,009 (0.8%) | Some customers don't bother rating their orders. |

**What I did:** Left them as-is. They're not errors — they represent real situations (customer didn't enroll, didn't fill the field, didn't rate). When calculating averages, pandas automatically skips these missing values.

---

## Problem 2: Duplicate orders

Found **12 orders** with `_DUP` at the end of their order ID (like `ORD008249_DUP`). I checked each one against its original — every single column matched exactly. Same customer, same date, same amount, same everything.

**What I did:** Removed all 12. They're exact copies and would mess up the counts (making it look like some customers ordered more than they actually did). 12 out of 10,009 is tiny — removing them doesn't lose any real information.

After cleanup: 9,997 orders remain.

---

## Problem 3: Unusually high order amounts

Most orders are between ₹300 and ₹900. But a few are much higher:

- Highest: ₹24,789 (Skin Care, 3 items)
- Second: ₹22,719 (Fragrance, 2 items)
- 6 orders total above ₹8,000

The average order is ₹752, but the middle order (median) is only ₹598. The gap tells us those big orders are pulling the average up.

**What I did:** Kept them. They look like real purchases — expensive perfumes and skincare bundles. They're not typos or system errors. But I'll keep this in mind when calculating averages later.

---

## Problem 4: Future data that could cause leakage

This is the most important one. The dataset has orders from both BEFORE and AFTER the snapshot date (September 30, 2025):

- **8,128 orders** on or before Sep 30 → safe to use for building features
- **1,869 orders** after Sep 30 → these happened in the FUTURE. I can't use them as inputs because the model wouldn't know about them at prediction time.

The post-snapshot orders span October 1 to November 29, 2025 — exactly the 60-day window used to decide if someone churned.

**What I did:** Split orders into "before" and "after" groups. Only the "before" group will be used for features in Parts 2 and 3.

---

## Problem 5: Data types

- `order_date` loaded as text instead of a date. I converted it using `pd.to_datetime()` so Python can compare dates properly.
- `rating` shows as decimal (like 4.0 instead of 4) because pandas uses decimals when a column has missing values. This is normal — not a problem.

---

## Summary

| What I found | What I did |
|-------------|-----------|
| 12 duplicate orders | Removed them |
| Missing loyalty_tier (57.8%) | Left as-is — means "not enrolled" |
| Missing skin_type (16.7%) | Left as-is — optional field |
| Missing rating (0.8%) | Left as-is — customer didn't rate |
| 6 very high order amounts | Kept — they're real purchases |
| 1,869 future orders | Flagged — won't use as features |
| order_date as text | Converted to datetime |
