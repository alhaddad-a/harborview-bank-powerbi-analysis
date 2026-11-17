# Harborview Bank: Project Brief

> Harborview Bank is fictional and the dataset is synthetic.

## 1. The business case

You are a newly hired data analyst at Harborview Bank, a retail bank with 25 branches in 5 regions.

At the 2025 year-end review, two executives disagree:

- **Head of Retail:** "2025 was our best year ever for fee income. The July 2024 pricing change worked."
- **CFO:** "Our net results are worse, our customer base is shrinking, and credit losses are up. Something is wrong."

Your job is to find out who is right (possibly both), explain why, and recommend what the bank should do in 2026.

### Questions leadership wants answered

1. How has the customer base changed from 2023 to 2025? Who is leaving, and when did it start?
2. Did the July 2024 checking-fee change help or hurt the bank overall?
3. How is the loan book performing? Are some regions, branches, products or credit-score bands riskier than others?
4. Is there anything unusual in the transaction data that the bank should investigate?
5. How are customers' banking habits (channels) changing, and what does that mean for the branch network?

## 2. The data

The analysis period is 1 January 2023 to 31 December 2025. Customers and accounts go back to 2019. All amounts are in USD unless the `currency` column says otherwise.

| File | Rows (approx.) | One row is |
|---|---|---|
| `customers.csv` | 20,100 | one customer |
| `accounts.csv` | 36,600 | one account |
| `transactions.csv` | 2,330,000 | one transaction |
| `loans.csv` | 5,320 | one loan |
| `branches.csv` | 25 | one branch |
| `products.csv` | 6 | one product |
| `fee_schedule.csv` | 4 | one fee rule |
| `fx_rates.csv` | 72 | one currency in one month |
| `product_revenue.csv` | 9 | one product and segment |

**Extract date:** all tables were extracted on 31 December 2025. Anything dated after that is a data error.

### How the bank operates (rules reflected in the data)

- Customers are adults (18 or older) when they join, and every customer holds at least one product.
- Salaries arrive once a month by direct deposit on the customer's payday (the 1st, 15th or 25th, moved to the previous Friday when it falls on a weekend). Amounts are steady, with a small yearly raise.
- Branches open Monday to Friday 9:00 to 17:00 and Saturday 9:00 to 13:00, and are closed on Sunday. ATMs, online and mobile banking work all the time.
- Activity is higher on Fridays and Saturdays and lower on Sundays. Spending peaks in November and December, and travel in summer.
- Foreign currency (EUR) appears only on travel and card spending abroad, never on salaries, fees or transfers.
- Loans are approved on business days, only to existing customers aged 21 or older who hold an open account. Some borrowers repay early (status Paid Off before the term ends).
- Transaction volumes per account are lower than a real bank's, to keep the files a workable size for Power BI Desktop.

Rows that break these rules are data-quality problems for you to find.

### Data dictionary

**customers**

| Column | Description |
|---|---|
| customer_id | Unique customer key (C00001 onwards) |
| first_name, last_name, gender, birth_date, email | Demographics (fictional) |
| region | Customer's home region |
| home_branch_id | The branch the customer belongs to (links to `branches`) |
| segment | Mass, Affluent or Private |
| income_band | Low, Medium, High or Very High |
| signup_date | Date the customer joined the bank |

**accounts**

| Column | Description |
|---|---|
| account_id | Unique account key |
| customer_id | Links to `customers` |
| product_id | P01 Checking, P02 Savings, P03 Credit Card (links to `products`) |
| open_date, close_date | close_date is empty if the account is still open |
| status | Open or Closed |

**transactions**

| Column | Description |
|---|---|
| txn_id | Unique transaction key |
| account_id | Links to `accounts` |
| txn_datetime | Date and time of the transaction |
| txn_type | Debit (money out), Credit (money in) or Fee (charged by the bank) |
| merchant_category | Groceries, Travel, Salary, Account Fee, Chargeback and others |
| amount | Amount in the original currency (should always be positive) |
| currency | USD or EUR. Convert EUR to USD with `fx_rates` (amount times rate_to_usd for that month) |
| channel | POS, Online, Mobile, Branch, ATM, Direct Deposit or System |
| status | Completed, Failed or Reversed. Only Completed transactions move money |

**loans**

| Column | Description |
|---|---|
| loan_id | Unique loan key |
| customer_id | Links to `customers` |
| branch_id | Originating branch (links to `branches`) |
| product_id | L01 Personal, L02 Auto, L03 Mortgage |
| origination_date | Date the loan was issued |
| loan_amount, interest_rate, term_months, monthly_payment | Loan terms. `interest_rate` is annual, as a decimal (0.075 means 7.5%) |
| credit_score_at_origination | Borrower's score when approved (valid range 300 to 850) |
| loan_status | Current, Late 30+, Paid Off or Charged Off (defaulted and written off) |
| default_date | Date of charge-off, if any |
| outstanding_balance | Balance at 31 Dec 2025, or at the default date if charged off. For Charged Off loans, this is the bank's loss |

**fee_schedule:** monthly checking-account fee by segment, with effective dates.

**fx_rates:** monthly conversion rate to USD for each currency.

**product_revenue:** the finance team's estimate of the average revenue the bank earns per month from one open account, by product and segment. It covers income that is not in the transactions table (deposit margin, card interest, interchange). It excludes the monthly checking fee, which is already in `transactions`, so never count the two together for the same thing. These are averages, not exact per-customer figures, so say so when you use them.

| Column | Description |
|---|---|
| product_id | P01 Checking, P02 Savings, P03 Credit Card (links to `products`) |
| segment | Mass, Affluent or Private |
| avg_monthly_revenue_usd | Average monthly revenue per open account, in USD |
| revenue_source | Where the revenue comes from |
| source | Who provided the estimate |
| effective_from | Date the estimate applies from |

## 3. Warning: the data is dirty

Like real company data, these files contain quality problems. There are at least 15 kinds, spread across customers, accounts, transactions and loans. Types to look for:

- Duplicates
- Inconsistent text (case, spelling, spaces)
- Mixed date formats
- Missing values
- Impossible or out-of-range values
- Records that break business rules
- Keys that don't match another table
- Values in the wrong unit or sign

Finding and documenting these is part of the project. Keep a data-quality log listing each issue, how many rows it affects, and what you decided to do about it.
