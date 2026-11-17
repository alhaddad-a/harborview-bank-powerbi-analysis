# Harborview Bank: Data Model

Built in Power BI Desktop in Import mode. Auto date/time and automatic relationship detection are off; every relationship was defined by hand.

## 1. Tables

| Table | Rows | Role | Describes |
|---|---|---|---|
| Transactions | 2,319,645 | Fact | Payments, fees and transfers |
| Loans | 5,300 | Fact | Loans issued, with amount, rate, balance and status |
| Customers | 20,000 | Dimension | Who: segment, region, income band |
| Accounts | 36,605 | Dimension, between Customers and Transactions | Which account: product, open and close dates |
| Products | 6 | Dimension | What: checking, savings, card and three loan types |
| Branches | 25 | Dimension | Where: branch, city, region |
| Date | 2,557 | Dimension, marked as the date table | When: 2019-01-01 to 2025-12-31 |
| fee_schedule | 4 | Lookup, hidden, no relationship | Checking-fee price list |
| fx_rates | 72 | Lookup, hidden, no relationship | Monthly EUR to USD rate (not used, all reporting is in USD) |
| product_revenue | 9 | Lookup, hidden, no relationship | Finance estimate of monthly revenue per account |
| _Measures | | Measures only | All report measures, in numbered folders |

The model is a star, except that Customers reaches Transactions through Accounts.

## 2. Relationships

All one-to-many and single-direction, so filters flow from dimension to fact.

| # | One side | Many side | Key | State |
|---|---|---|---|---|
| 1 | Customers | Accounts | customer_id | Active |
| 2 | Accounts | Transactions | account_id | Active |
| 3 | Customers | Loans | customer_id | Active |
| 4 | Branches | Loans | branch_id | Active |
| 5 | Products | Accounts | product_id | Active |
| 6 | Products | Loans | product_id | Active |
| 7 | Date | Transactions | Date to txn_date | Active |
| 8 | Date | Loans | Date to origination_date | Active |
| 9 | Date | Loans | Date to default_date | Inactive, switched on with `USERELATIONSHIP` in the loss measures (KPIs 5 and 8) |

### Design decisions

- **No relationship from Branches to Customers.** It would create two paths to Loans (issuing branch and home branch). Customer geography uses Customers[Region], which was filled from the branch during cleaning; loan geography uses Branches.
- **Two dates on Loans, one active.** The issue date is active (loans issued, default rate by year of issue, score mix). The default date is inactive and switched on inside the loss measures.
- **No Date relationship on Accounts.** Active customers and churn need date ranges (opened on or before, not yet closed), which DAX measures handle.
- **Lookup tables stay disconnected.** fee_schedule and product_revenue are keyed by product and segment and are used inside measures, not as filters.
- **txn_date was added in Power Query** (date only) so Transactions matches the Date table's daily grain. Transaction Time keeps the full timestamp.

## 3. Date table (DAX)

```
Date =
ADDCOLUMNS (
    CALENDAR ( DATE ( 2019, 1, 1 ), DATE ( 2025, 12, 31 ) ),
    "Year", YEAR ( [Date] ),
    "Quarter", "Q" & QUARTER ( [Date] ),
    "Month Number", MONTH ( [Date] ),
    "Month", FORMAT ( [Date], "MMM" ),
    "Year Month", FORMAT ( [Date], "YYYY-MM" ),
    "Month Start", DATE ( YEAR ( [Date] ), MONTH ( [Date] ), 1 ),
    "Weekday Number", WEEKDAY ( [Date], 2 ),
    "Weekday", FORMAT ( [Date], "ddd" )
)
```

Calculated columns added later: Year Quarter (for example "2024 Q3"), Price Period ("Before: $5 fee", "First 9 months at $12", "Apr to Dec 2025") and its hidden sort column Price Period Order.

- Covers every date in the fact tables, in whole years.
- Month is sorted by Month Number, Weekday by Weekday Number, Price Period by Price Period Order.
- Marked as the date table.

## 4. Display names

Tables and the columns people see were renamed in Power BI Desktop, which also updated every measure and visual. Code in the other documents uses the original source names.

| Table | Source name | Name in the model |
|---|---|---|
| accounts | accounts | Accounts |
| | open_date, close_date, status | Open Date, Close Date, Status |
| branches | branches | Branches |
| | branch_name, city, region, opened_year | Branch, City, Region, Opened Year |
| customers | customers | Customers |
| | birth_date, gender, income_band, region, segment, signup_date | Birth Date, Gender, Income Band, Region, Segment, Signup Date |
| loans | loans | Loans |
| | credit_score_at_origination | Credit Score |
| | interest_rate, loan_amount, loan_status | Interest Rate, Loan Amount, Loan Status |
| | monthly_payment, outstanding_balance, term_months | Monthly Payment, Outstanding Balance, Term (Months) |
| products | products | Products |
| | product_name, product_type | Product, Product Type |
| transactions | transactions | Transactions |
| | amount, channel, currency, merchant_category, status, txn_datetime | Amount, Channel, Currency, Merchant Category, Status, Transaction Time |

## 5. Hidden from report view

- Foreign keys on the many side and the ID columns (customer, account, loan, transaction, branch and product IDs)
- Date keys used only by relationships (txn_date, origination_date, default_date)
- Personal fields (first name, last name, email) and home_branch_id
- Cleaning flags (close_date_corrected, loan_amount_calculated)
- Sort helpers (Month Number, Weekday Number, Score Band Order, Price Period Order)
- The three lookup tables

Default summarisation is off on columns that must never be summed (credit score, rate, term, years). Every number in the report comes from a measure.

## 6. Lessons

- Filters flow from the one side to the many side; a filter on a fact column stays in that fact.
- Two active paths between the same tables make the model ambiguous, so keep one active.
- An inactive relationship is a route that `USERELATIONSHIP` can switch on.
- Relationships match values, not display formats.
- The Date table's grain is one day, so timestamps need a date-only key.
- Clean keys (no duplicates, no orphans) are what make the relationships trustworthy.
