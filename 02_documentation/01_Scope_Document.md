# Harborview Bank: Scope Document

## 1. Problem statement

**Situation.** Harborview Bank's leadership disagrees about 2025. The Head of Retail believes 2025 was the bank's best year for fee income, driven by the checking-fee price change in July 2024. The CFO believes overall results got worse in 2025, with a shrinking customer base and rising credit losses. Neither claim had been checked against the data.

**Core question.** Did the 2024 price change raise fee income at the cost of customers and overall results? And, separately, what is driving the change in credit losses?

**Decision it supports.** Leadership will use the findings to decide whether to keep, reverse or adjust the fee in 2026, and where to act on customer retention and credit risk.

**Done means** an interactive Power BI dashboard and a short recommendations memo that show:

- how the customer base changed from 2023 to 2025, who left, and when
- how fee income changed, and whether the price change helped or hurt the bank overall
- where credit losses are coming from
- 3 prioritised recommendations for 2026

Chargeback refunds are included as a loss line in KPI 12. A fraud investigation and the channel-mix analysis are out of scope for version 1 (section 7).

## 2. Stakeholders

### Head of Retail

| | |
|---|---|
| **Role** | Manages and grows the retail business. Judged on retail revenue and customer growth. |
| **Believes** | 2025 was the best year for fee income, and the July 2024 price change is the reason (a claim about cause). |
| **Cares about** | Revenue growth first; customer numbers matter less to him than income. |
| **Metric he trusts** | Fee income, year over year. |
| **What would change his mind** | Evidence that the higher fee made customers leave, and that the income lost from them outweighs the extra fee income. |
| **What he needs from me** | A neutral view of fee income before and after July 2024, plus what the change cost in lost customers, so he can judge whether it was a net gain. |

### CFO

| | |
|---|---|
| **Role** | Responsible for the bank's overall financial health, not one department. |
| **Believes** | In 2025 net results got worse, the customer base shrank and credit losses went up (claims about what happened). |
| **Cares about** | Stability and long-term health, including risk such as loan losses. |
| **Metric he trusts** | Net results (income minus losses), year over year. The data has no operating costs (section 6). |
| **What would change his mind** | Evidence that net results did not fall, that the customer base did not shrink in a meaningful way, or that credit losses did not rise or were a one-off. |
| **What he needs from me** | A neutral view of net results, the customer base and credit losses for 2023, 2024 and 2025, and where the credit losses come from. |

## 3. Issue tree

**How each branch was built**

1. Turn the claim into a neutral question (the branch title).
2. Ask what must be true for the claim to hold, and list 3 or 4 conditions.
3. Break key numbers into parts (count times size).
4. Write each condition as a question: what, sliced how, over which period.
5. Check that the questions are neutral, specific, do not overlap and leave no gaps.
6. Choose slices that lead to a decision: if this is true, who would do what differently?

| Branch | Tests | Type of claim |
|---|---|---|
| 1. Fee income | Head of Retail: fees went up because of the price | Cause |
| 2. Credit losses | CFO: losses went up | What happened |
| 3. Customer base | CFO: customers are leaving | What happened |
| 4. Net result | CFO: results got worse, plus the net effect of the price change | What happened and cause |

### Branch 1: Did fee income increase, and was the July 2024 price change the reason?

```
1.1 How did total fee income change, per month, Jan 2023 to Dec 2025?            (did it go up?)
1.2 Did the change begin in July 2024, or was a trend visible before it?          (timing)
1.3 What happened to each part of fee income?                                     (which part drove it)
    Fee income = fee-paying checking accounts x fee per account
    1.3.1 How did the fee per account change, per month?
    1.3.2 How did the number of fee-paying accounts change, per month?
1.4 Does all fee income come from the accounts the fee schedule says should pay,
    at the scheduled amounts?                                                     (anything else?)
```

| Question | Data needed | Likely visual |
|---|---|---|
| 1.1 | transactions (type Fee) | Line by month |
| 1.2 | same as 1.1 | Same line with a July 2024 marker |
| 1.3.1 | fee_schedule and fee transactions | Line of average fee per account by month |
| 1.3.2 | accounts and customers (segment) | Line of fee-paying accounts by month |
| 1.4 | fee transactions, accounts, segments | Table of fees by segment and amount |

### Branch 2: How did credit losses change from 2023 to 2025, and what is driving the change?

```
2.1 How did total credit losses change, per quarter, compared with the previous year?
2.2 What happened to each part of credit losses?
    Credit losses = loans issued x default rate x average loss per default
    2.2.1 How did the number of new loans change, per year, 2022 to 2025?          (more lending?)
    2.2.2 How did the default rate differ between loans issued in different years,
          compared at the same age (within 12 months)?                            (worse lending?)
    2.2.3 How did the average loss per defaulted loan change, per year?            (bigger losses?)
2.3 Where are the losses coming from?
    2.3.1 How did default rate and losses differ by region, per year of issue,
          and within the regions that stand out, by branch?                       (where)
    2.3.2 How did the default rate differ by credit score band, per year of issue,
          and did the mix of approved scores change?                              (who)
```

Key choices: quarters in 2.1, because there are few charge-offs per month. 2.2.1 starts in 2022, because those loans can default in 2023. 2.2.2 compares loans at the same age. 2.3.1 goes from region to branch to avoid noise. 2.3.2 separates "riskier borrowers" from "the bank approved riskier borrowers".

| Question | Data needed | Likely visual |
|---|---|---|
| 2.1 | loans (Charged Off, default_date, outstanding_balance) | Columns by quarter, with prior-year comparison |
| 2.2.1 | loans (origination_date, loan_amount) | Columns of loans issued per year |
| 2.2.2 | loans (origination_date, default_date, loan_status) | Default rate by issue year at the same age |
| 2.2.3 | loans (Charged Off, outstanding_balance) | Columns of average loss per default per year |
| 2.3.1 | loans and branches (region) | Matrix of region by year, drill-through to branch |
| 2.3.2 | loans (credit score) | Score-band mix and default rate by band |

### Branch 3: How did the customer base change from 2023 to 2025, who left, and when did it start?

```
3.1 How did the number of active customers change, per month?
3.2 What happened to each part of the customer count?
    Customers at end = customers at start + new customers - customers who left
    3.2.1 How many new customers joined, per month?                               (fewer joiners?)
    3.2.2 How many customers fully left, and what was the monthly churn rate?      (more leavers?)
3.3 How did partial churn change: how many customers closed some products but
    kept others, and which products?                                              (early warning)
3.4 Who left, and when did it start?
    3.4.1 How did the churn rate differ by segment, per month?                     (who)
    3.4.2 How did the churn rate differ by region, per month?                      (where)
```

Full churn means all accounts closed; partial churn means some accounts closed; an active customer has at least one open account (KPI 2). The cause of leaving (the price change) belongs to Branch 4, not here.

| Question | Data needed | Likely visual |
|---|---|---|
| 3.1 | accounts (open and close dates), customers | Line of active customers by month |
| 3.2.1 | first account open date (signup_date as a check) | Columns of new customers by month |
| 3.2.2 | accounts (last close date per customer) | Line of churn rate by month |
| 3.3 | accounts by product, per customer | Partial closures by product |
| 3.4.1 | accounts and customers (segment) | Churn rate by segment |
| 3.4.2 | accounts and customers (region) | Churn rate by region |

### Branch 4: Was the bank better or worse off in 2025, and did the July 2024 price change produce a net gain or a net loss?

```
4.1 How did the bank's measurable net result (income minus losses in the data)
    change, per year and per quarter?                                             (CFO's third claim)
4.2 Did the price change cause customers to leave?
    4.2.1 Did the closure rate of fee-paying checking accounts change after
          July 2024, compared with segments that pay no fee?                      (control-group test)
    4.2.2 Did the effect fade, stay level or grow in the following months?
4.3 What did the customers who left after July 2024 take with them?
    4.3.1 What other products did they close, and how soon after checking?
    4.3.2 How active were they before they left?                                  (proxy for value)
4.4 What is the net effect of the price change?
    4.4.1 How much extra fee income did the higher price bring, per month?
    4.4.2 How much income was lost from customers who left because of it, and is
          the gap growing or shrinking by the end of 2025?
```

The treatment group is Mass checking accounts (the only ones that pay the fee). The control group is Affluent and Private checking accounts (fee $0). Each group's change before and after July 2024 is compared. Extra fee income is only the part caused by the higher price. Lost income uses `product_revenue` (an estimate).

| Question | Data needed | Likely visual |
|---|---|---|
| 4.1 | fees, account revenue estimate, chargebacks, charge-offs | Columns by year, line by quarter |
| 4.2.1 | accounts and customers (segment) | Closure rate of fee payers against non-payers, July 2024 marker |
| 4.2.2 | same as 4.2.1 | Same chart, months after the change |
| 4.3.1 | all products per leaver | Other products closed, and days after checking |
| 4.3.2 | transactions per leaver before closing | Activity of leavers against stayers |
| 4.4.1 | fee transactions and fee_schedule | Line of extra fee income by month |
| 4.4.2 | results of 4.2 and 4.3, product_revenue | Cumulative gain against loss |

## 4. KPI definitions

Version 1 was written from the issue tree and data dictionary before looking at the data values. Changes made later are listed in section 6.

Each bottom-level question needs a number, and that number is a KPI. Slices (by region, segment and so on) reuse the same KPI with a filter.

| # | KPI | Answers |
|---|---|---|
| 1 | Fee Income | 1.1, 1.2, 4.4.1 |
| 2 | Active Customers | 3.1, churn denominator |
| 3 | Average Fee per Account | 1.3.1 |
| 4 | Fee-Paying Accounts | 1.3.2 |
| 5 | Credit Losses | 2.1 |
| 6 | Loans Issued | 2.2.1 |
| 7 | Default Rate (12 months) | 2.2.2, 2.3 |
| 8 | Average Loss per Default | 2.2.3 |
| 9 | New Customers | 3.2.1 |
| 10 | Churned Customers and Churn Rate | 3.2.2, 3.4, 4.2 |
| 11 | Partially Churned Customers | 3.3 |
| 12 | Measurable Net Result | 4.1 |
| 13 | Extra Fee Income | 4.4.1 |
| 14 | Estimated Revenue Lost | 4.4.2 |

### KPI 1: Fee Income

| Field | |
|---|---|
| Business meaning | Money the bank collected from monthly checking-account fees. |
| Formula | Sum of `amount` |
| Included | `txn_type` Fee, `status` Completed, dated 1 Jan 2023 to 31 Dec 2025 |
| Excluded | Failed or reversed rows, duplicates, future-dated rows. Negative amounts are data errors fixed in cleaning |
| Time rule | `txn_datetime` |
| Grain | Transaction, rolled up to day, month, quarter, year |
| Source | `transactions` (with `accounts` and `customers` for slicing) |
| Watch-outs | Compare full years only. Read together with the customer KPIs. |

### KPI 2: Active Customers

| Field | |
|---|---|
| Business meaning | Customers with at least one open account at month end. |
| Formula | Distinct count of `customer_id` with an account where `open_date` is on or before month end and `close_date` is empty or after month end |
| Included | All products (checking, savings, card) |
| Excluded | Duplicate customers; accounts with impossible dates (see cleaning log) |
| Time rule | Snapshot on the last day of each month, Dec 2022 to Dec 2025. Each month's churn divides by the previous month-end count, so Dec 2022 is needed for Jan 2023 |
| Grain | Customer and month end |
| Source | `accounts` and `customers` |
| Watch-outs | Active means "has an open account", not "uses it". Do not use the `status` column for history; it shows today only. |

### KPI 3: Average Fee per Account

| Field | |
|---|---|
| Business meaning | Average monthly fee paid by one fee-paying checking account. |
| Formula | Fee Income divided by the distinct count of accounts charged a fee that month |
| Time rule | Monthly only. A yearly 2024 average would blend $5 and $12 into a price that never existed |
| Expected | $5.00 from Jan 2023 to Jun 2024, $12.00 from Jul 2024 to Dec 2025 |
| Watch-outs | Divide by accounts actually charged, not all checking accounts. Check every month that KPI 4 times KPI 3 equals KPI 1. |

### KPI 4: Fee-Paying Accounts

| Field | |
|---|---|
| Business meaning | Checking accounts charged the monthly fee in a month. |
| Formula | Distinct count of `account_id` with a completed fee row that month |
| Check | Should match Mass checking accounts open at month end. KPI 4 times KPI 3 equals KPI 1 |
| Watch-outs | Counts accounts, not customers. Segment is assumed fixed for the whole period. |

### KPI 5: Credit Losses

| Field | |
|---|---|
| Business meaning | Money lost on loans the bank charged off. |
| Formula | Sum of `outstanding_balance` |
| Included | `loan_status` Charged Off, `default_date` 1 Jan 2023 to 31 Dec 2025 (any year of issue, including 2022) |
| Excluded | Current, Paid Off and Late 30+ loans (Late 30+ is shown separately as an early warning); fraud (KPI 12) |
| Time rule | `default_date`: the loss belongs to the period of the charge-off |
| Watch-outs | Use `outstanding_balance`, not `loan_amount`, because part was repaid. These are gross charge-offs: no recoveries are in the data. KPI 7 counts loans, so it is not a standard net charge-off rate. |

### KPI 6: Loans Issued

| Field | |
|---|---|
| Business meaning | Number and value of new loans in a period. |
| Formula | Count of `loan_id`; sum of `loan_amount` |
| Included | All loans, any status, issued 2022 to 2025 |
| Time rule | `origination_date` |
| Watch-outs | More lending is growth, not bad by itself. Show count and amount, because they can move differently. Never filter by current status. |

### KPI 7: Default Rate (12 months)

| Field | |
|---|---|
| Business meaning | Share of loans issued in a period that were charged off within 12 months. |
| Formula | Loans charged off with `default_date` within 12 months of `origination_date`, divided by all loans issued in that period |
| Included | Every loan in the year of issue (vintage), including early repayments |
| Excluded | Vintages younger than 12 months (2025) |
| Score bands | Under 600, 600 to 649, 650 to 699, 700 to 749, 750 and above, Unknown |
| Watch-outs | Same window for every vintage. Small groups are noisy: treat rates on fewer than about 50 loans with caution. |

### KPI 8: Average Loss per Default

| Field | |
|---|---|
| Business meaning | Average amount lost on each charged-off loan. |
| Formula | Credit Losses divided by the number of charged-off loans |
| Also show | Median loss, and a split by loan type (mix effect) |
| Watch-outs | A few large mortgage losses pull the average up. Shown per year, with the count of defaults. |

### KPI 9: New Customers

| Field | |
|---|---|
| Business meaning | Customers whose first-ever account opened in the month. |
| Formula | Distinct count of customers whose earliest `open_date` falls in the month |
| Excluded | Existing customers opening extra accounts |
| Time rule | Month of the first `open_date`; `signup_date` used only as a cross-check |
| Check | Active at end = active at start + new + reactivated - churned |

### KPI 10: Churned Customers and Churn Rate

| Field | |
|---|---|
| Business meaning | Customers who closed all their accounts in a month, and that number as a share of customers at the start of the month. |
| Formula | Churned: customers active at the start of the month and not at the end. Churn rate: churned divided by active customers at the start |
| Companion | Checking closure rate: checking accounts closed in the month divided by checking accounts open at the start, split into fee payers and non-payers for 4.2.1 |
| Watch-outs | Monthly rates are small (1% to 3% is a tripling). Partial churn is KPI 11. Never add monthly rates; yearly churn uses the start-of-year base. |

### KPI 11: Partially Churned Customers

| Field | |
|---|---|
| Business meaning | Customers who closed at least one account in a month but still had an open account at month end. |
| Formula | Count as described; rate = count divided by customers with 2 or more open accounts at the start of the month |
| Also show | Accounts closed by product among customers who stayed |
| Watch-outs | These customers are still active. Only customers with 2 or more accounts can partially churn. |

### KPI 12: Measurable Net Result

| Field | |
|---|---|
| Business meaning | Income the bank can measure in the data, minus the losses it can measure. Not profit. |
| Formula | Fee Income + Estimated Account Revenue - Credit Losses - Chargeback refunds |
| Estimated Account Revenue | Open accounts at each month end times `avg_monthly_revenue_usd`, by product and segment. Excludes the fee, so nothing is counted twice |
| Chargeback refunds | Sum of completed Chargeback rows: refunds paid to card customers. Not confirmed as fraud; recoveries unknown |
| Excluded | Operating costs, interest paid on deposits, loan interest income and provisions for expected loan losses (not in the data) |
| Watch-outs | Never call it profit. Always show the parts next to the total. |

### KPI 13: Extra Fee Income

| Field | |
|---|---|
| Business meaning | The part of fee income caused by the higher price: what paying accounts pay above the old $5 fee. |
| Formula | Fee-paying accounts times (fee minus $5), per month from July 2024 |
| Why not "after minus before" | That difference mixes the price effect (accounts that stayed pay $7 more) with the volume effect (accounts that left pay nothing). KPI 13 is the price effect only; lost income is KPI 14 |
| Watch-outs | It is only the gain, so always show it next to KPI 14. |

### KPI 14: Estimated Revenue Lost

| Field | |
|---|---|
| Business meaning | Estimated revenue the bank no longer earns each month from accounts that closed because of the price change. |
| Step 1, extra closures | Actual Mass checking closures minus expected closures. Expected = Mass checking accounts open at the start of the month times the Mass closure rate before the change (Jan 2023 to Jun 2024) |
| Step 2, value per lost account | $5 old fee plus $4 Mass checking revenue = $9 a month (base case). $11.06 if the savings and cards leavers closed later are added (sensitivity) |
| Formula | Extra closures so far times value per lost account, each month (a lost account keeps costing every month) |
| Net effect | KPI 13 minus KPI 14, monthly and cumulative |
| Watch-outs | An estimate: label it and explain the method on the dashboard. |

## 5. Hypotheses

Predictions were recorded before looking at the data values. Competing hypotheses (a and b) are tested against each other.

| # | Hypothesis | How tested | What would prove it wrong | Prediction | Result |
|---|---|---|---|---|---|
| H1a | Fee income rose mainly because of the higher price | KPIs 1, 3, 4 | Fee-paying accounts grew a lot | Expected | Supported: fee $5 to $12 while paying accounts fell (F2 to F4) |
| H1b | Fee income rose mainly because more accounts paid | KPI 4 | Paying accounts flat or falling | Not expected | Rejected: 8,803 to 7,994 paying accounts (F3) |
| H2 | The customer base at the end of 2025 is smaller than at the end of 2024 | KPI 2 | Dec 2025 at or above Dec 2024 | No prediction | Not supported: 14,789 to 15,773. Mass dipped from Jun 2024 to Feb 2025, then recovered (F13 to F15) |
| H3a | After July 2024, checking closures rose only among fee payers, so the price is the likely cause | Checking closure rate by group | Closures rose as much for Affluent and Private | Expected | Supported: Mass 0.39% to 3.05% a month, no-fee flat, same in every region, from the week of 1 Jul 2024 (F19, F31) |
| H3b | Closures rose in all segments, so an outside cause is likely | Same | Affluent and Private stayed flat | Not expected | Rejected (F18) |
| H4 | Customers who closed checking also closed other products | KPI 11, question 4.3.1 | Most kept their other products | No prediction | Supported: 71% of customers with another product closed one within 90 days (F35) |
| H5a | Credit losses rose because the bank lent to riskier borrowers | KPIs 5, 7 | Similar default rates across years | Not expected | Supported, and the main driver: 12-month default rate 2.0% to 5.4% (F8) |
| H5b | Credit losses rose mainly because the bank lent more | KPI 6 | Loans issued flat | Expected | Partly: more loans, but most of the rise is riskier lending (F5, F8) |
| H6 | Defaults are concentrated in some regions, branches or score bands | KPI 7 by region and band | Similar rates everywhere | No prediction | Supported: East (all 5 branches) and scores under 650 (F9 to F12, F33) |
| H7 | The measurable net result was lower in 2025 than in 2024 | KPI 12 | 2025 at or above 2024 | No prediction | Supported: $2.16M to -$0.41M, because of credit losses (F30) |
| H8 | The price change's net effect is positive but shrinking | KPI 13 minus KPI 14 | Negative from the start, or growing | No prediction | Supported: +$0.68M to +$0.74M so far; monthly net $58.6K to $32K (F28, F35) |

## 6. Assumptions and questions log

| # | Question | Who I'd ask | Assumption for now | Impact if wrong |
|---|---|---|---|---|
| 1 | Where is the operating cost data (salaries, rent, IT)? | Finance | Not available. Net result uses only the income and losses in the data (KPI 12) | Medium: the true result differs, but the direction year over year should hold |
| 2 | Balances and deposits are not in the data. How do we value a lost customer? | Finance | Use finance's average monthly revenue per account (`product_revenue`) and transaction activity as a sign of engagement | Medium |
| 3 | Are `product_revenue` figures exact? | Finance | No. They are averages, and every number using them is labelled "estimated" | Medium |
| 4 | Does `product_revenue` include the monthly fee? | Finance | No. Fees come from `transactions`, so nothing is counted twice | High if counted twice |
| 5 | Can a customer's segment change over time? | Data owner | Assume it is fixed for 2023 to 2025 | Medium |
| 6 | Does the bank recover money after a charge-off? | Collections | No recovery data; assume the full balance is lost | Medium: true losses may be somewhat lower |
| 7 | How much interest income do loans earn? | Finance | Left out of KPI 12 (the data has one balance per loan, not a monthly schedule) | Medium: the result looks lower than the real one |
| 8 | Without the price change, would Mass customers have kept closing checking at the old rate? | Retail product team | Yes, supported by the flat control group | High: this baseline drives the size of the estimated loss |

### Changes made during the analysis

| # | Change | Why |
|---|---|---|
| C1 | Expected closures in KPI 14 use the Mass baseline only, with no monthly control adjustment | The control group's monthly rate is noisy, and its period average did not move (0.42% before, 0.38% to 0.39% after). It is used as a check, not a multiplier |
| C2 | Value per lost account is $9 (base case) and $11.06 with the other products leavers closed within 90 days (sensitivity) | Question 4.3.1 measured an extra $2.06 a month per closure |
| C3 | Customer-base trends are reported to October 2025 | No new customers exist in the data after October 2025 (D1) |
| C4 | Chargebacks are treated as a one-off event in KPI 12, labelled as refunds, not confirmed fraud | All chargebacks are from July 2025, on 35 cards (F29) |
| C5 | Three price periods: Before ($5 fee, Jan 2023 to Jun 2024), first 9 months at $12 (Jul 2024 to Mar 2025), Apr to Dec 2025 | The effect has two phases (F17, F27) |

## 7. Scope and deliverables

### In scope

| Area | Scope |
|---|---|
| Period | Jan 2023 to Dec 2025 (loans from 2022, because they can default in 2023; customer snapshots from Dec 2022) |
| Products | Checking, savings, credit card, personal, auto and mortgage loans |
| Customers | All segments and regions |
| Questions | Branches 1 to 4 |
| Fraud | Chargebacks as a loss line in KPI 12 |

### Deferred from version 1

Questions 4 (unusual transactions) and 5 (channels) of the brief are covered only by the July 2025 chargeback finding. They are listed in the report and the memo as not covered.

| Item | Why |
|---|---|
| True profit | No operating costs in the data |
| Loan interest income | Needs monthly loan schedules |
| Deep fraud investigation | Not needed for the leadership decision; optional extension |
| Channel mix | Does not change the fee or credit decision; optional extension |
| Forecasting 2026 | Data stops at Dec 2025; trends are shown, not forecast |
| Why customers left | Not in the data |

### Deliverables

| # | Deliverable | Where |
|---|---|---|
| 1 | Scope document (this file) | `02_documentation` |
| 2 | Data-quality log | `02_documentation` |
| 3 | Cleaned Power Query steps, clearly named | Inside the .pbix |
| 4 | Star-schema model with a Date table | Inside the .pbix |
| 5 | DAX measures for the 14 KPIs in a `_Measures` table | Inside the .pbix |
| 6 | Interactive report, six pages | `03_powerbi/Harborview_Bank.pbix`, PDF copy in `04_outputs` |
| 7 | Recommendations memo | `04_outputs/CFO_Memo.pdf` |
| 8 | README with screenshots | Repository root |

**Dashboard pages as built:**

1. Overview: headline numbers and key findings
2. Fee Income (Branch 1): KPIs 1, 3, 4
3. Credit Risk (Branch 2): KPIs 5 to 8
4. Customers (Branch 3): KPIs 2, 9, 10, 11
5. Price Change (Branch 4): KPIs 13 and 14 and the net effect
6. Data Checks: reconciliation checks and known limitations
