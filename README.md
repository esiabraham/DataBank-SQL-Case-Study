# DataBank-SQL-Case-Study

![Status](https://img.shields.io/badge/Status-Complete-brightgreen)
![Tool](https://img.shields.io/badge/Tool-SQL%20Server-CC2927?logo=microsoftsqlserver&logoColor=white)
![Domain](https://img.shields.io/badge/Domain-FinTech%20%2F%20Neo--Banking-6C3FBF)
![Challenge](https://img.shields.io/badge/Source-8%20Week%20SQL%20Challenge-orange)

> **Modelling storage demand for a digital bank where cloud allocation is directly tied to customer account balances.**

---

## Table of Contents
- [Business Background](#business-background)
- [Problem Statement](#problem-statement)
- [Dataset](#dataset)
- [Objectives](#objectives)
- [Methodology](#methodology)
- [Key Assumptions](#key-assumptions)
- [Business Logic](#business-logic)
- [Analysis Sections](#analysis-sections)
- [Conclusions](#conclusions)
- [Recommendations](#recommendations)

---

## Business Background

Data Bank is a next-generation Neo-Bank operating entirely in the digital space with no physical branches. Unlike traditional banks, it has built its product around two interlinked services: a standard banking platform and a highly secure distributed cloud data storage system.

The core differentiator is that **each customer's cloud storage allocation is directly tied to their account balance** — making the banking and storage experiences inseparable.

This creates a unique analytical challenge: as balances grow or shrink with every transaction, storage demand fluctuates in real time. Over-forecasting wastes infrastructure budget; under-forecasting results in storage failures and poor customer experience. Both outcomes are costly.

---

## Problem Statement

Data Bank lacks visibility into how customers are distributed and reallocated across its global node network, creating risks for infrastructure planning and security. Transaction behaviour including deposits, withdrawals and spending is not being tracked in a way that supports customer health assessment or financial modelling. Since cloud storage is tied to account balances, the organisation cannot accurately forecast data provisioning needs without modelling allocation under end of month, rolling average and real time strategies.

---

## Dataset

Three tables are available for analysis:

| Table | Key Columns | Description |
|-------|-------------|-------------|
| `customer_nodes` | customer_id, region_id, node_id, start_date, end_date | Maps each customer to a node and region over time. Assignments rotate frequently for security. |
| `customer_transactions` | customer_id, txn_date, txn_type, txn_amount | Records all financial activity — deposits, withdrawals and purchases — per customer. |
| `regions` | region_id, region_name | Reference table mapping region IDs to geographic names (Africa, America, Asia, Europe, Oceania). |

---

## Objectives

The primary objective is to **accurately model and forecast storage demand** across the customer base in order to:

- Prevent **over-provisioning** — which wastes infrastructure resources and inflates cost
- Prevent **under-provisioning** — which results in insufficient storage and degrades customer experience
- Support infrastructure investment decisions across five global regions

---

## Methodology

The analysis is structured across four sequential sections, each building on the previous:

1. **Customer Nodes Exploration** — Understanding node structure, regional distribution and how frequently customers are reallocated across the network.
2. **Customer Transactions Analysis** — Analysing transaction behaviour including deposits, withdrawals, closing balances and monthly activity patterns.
3. **Data Allocation Challenge** — Modelling and quantifying storage requirements under three different provisioning strategies.
4. **Daily Interest Challenge** — Extending the provisioning model with a daily interest calculation to simulate a savings-incentive storage reward.

After each section, insights are drawn and recommendations are made to support accurate storage demand forecasting.

---

## Key Assumptions

- Records where `end_date = '9999-12-31'` represent currently active node assignments and are **excluded** from reallocation duration calculations to avoid inflating day counts.
- Reallocation days are calculated as `end_date - start_date` on closed records only.
- For all provisioning models, **negative balances are floored at zero** — customers in overdraft receive no storage allocation.

---

## Business Logic

### Node Allocation
Each customer is assigned to exactly one node within a region at any given time. The system deliberately rotates these assignments as a security measure to prevent targeted attacks. A customer may be reassigned to a different node within the same region or moved to an entirely new region.

### Transaction Types

| Transaction | Balance Impact | Meaning |
|-------------|---------------|---------|
| Deposit | Positive (+) | Customer adds money — increases balance and storage allocation |
| Withdrawal | Negative (−) | Customer removes money — decreases balance and storage allocation |
| Purchase | Negative (−) | Debit card spending — decreases balance and storage allocation |

### Data Provisioning Options

| Option | Strategy | Lag | Operational Cost |
|--------|----------|-----|-----------------|
| Option 1 | End-of-month closing balance | 1 month | Low |
| Option 2 | 30-day rolling average balance | ~15 days | Medium |
| Option 3 | Real-time per-transaction tracking | None | High |

### Interest Calculation (Section D)
A 6% annual interest rate is applied daily on positive balances using simple (non-compounding) interest:

```
Daily Interest = (Balance × 0.06) / 365
```

---

## Analysis Sections

### Section A — Customer Nodes Exploration
- Total unique nodes across the system
- Node count per region
- Customer distribution per region
- Average reallocation days per customer
- Percentile analysis of reallocation days by region (median, P80, P95)

### Section B — Customer Transactions
- Transaction count and total amount by type
- Average deposit frequency and amount per customer
- Monthly active customers (1+ deposit AND 1+ outflow in the same month)
- Closing balance per customer at the end of each month
- Percentage of customers whose balance grew by more than 5%

### Section C — Data Allocation Challenge
- **Option 1:** Storage based on prior month's closing balance
- **Option 2:** Storage based on 30-day rolling average balance
- **Option 3:** Real-time storage tracking with every transaction

### Section D — Interest-Based Allocation
- Daily interest accrual on positive balances at 6% per annum
- Monthly aggregation of balance plus accrued interest for total storage demand modelling

---

## Conclusions

**Node Management**
- The network operates across 5 nodes and 5 regions — a well-diversified architecture.
- Average reallocation frequency of approximately 14 to 15 days reflects an aggressive but operationally sound security posture.
- America and Oceania carry the highest customer loads and will need priority capacity expansion as growth continues.

**Transaction Behaviour**
- Deposits are the most frequent transaction type, indicating customers primarily use Data Bank as a savings vehicle — a healthy sign for balance stability.
- A portion of customers carry negative closing balances, representing a potential churn risk if not addressed proactively.

**Data Provisioning**
- Option 1 produces the lowest storage estimate but lags reality by a full month — risky during high-inflow periods.
- Option 2 strikes the best balance between accuracy and cost and is recommended as the primary strategy.
- Option 3 is the most accurate but operationally the most complex and expensive to maintain at scale.

---

## Recommendations

| # | Area | Recommendation |
|---|------|----------------|
| R1 | Infrastructure | Monitor node reallocation times by region and alert when average exceeds 20 days |
| R2 | Infrastructure | Expand node capacity in America and Oceania ahead of projected customer growth |
| R3 | Infrastructure | Flag customers on the same node for more than 28 days (95th percentile) for manual review |
| R4 | Engagement | Target customers with negative or near-zero balances with a re-engagement campaign |
| R5 | Engagement | Build a monthly dashboard tracking customers with both deposits and outflows as a retention KPI |
| R6 | Engagement | Introduce tiered storage bonuses at balance milestones to drive higher average balances |
| R7 | Provisioning | Adopt Option 2 (30-day rolling average) as the primary provisioning strategy |
| R8 | Provisioning | Run Option 3 in parallel for 6 months post-launch to quantify the accuracy trade-off |
| R9 | Provisioning | Implement interest-based storage (Option D) as a loyalty feature for premium customers only |
| R10 | Data Quality | Validate end_date values — investigate records where end_date < start_date or overlapping active assignments exist |

---

## Tools Used

![SQL Server](https://img.shields.io/badge/SQL%20Server-CC2927?style=flat&logo=microsoftsqlserver&logoColor=white)
![GitHub](https://img.shields.io/badge/GitHub-181717?style=flat&logo=github&logoColor=white)

---

*Based on the 8 Week SQL Challenge by Danny Ma · [8weeksqlchallenge.com](https://8weeksqlchallenge.com)*
