# Harborview Bank: KPI Measure Plan

For each of the 14 KPIs: what it answers, the DAX, how it was tested, and where it appears in the report. Definitions are in `01_Scope_Document.md` section 4 and the model in `03_Data_Model.md`. All measures live in the `_Measures` table.

Code below uses the original source column names (for example `loans[credit_score_at_origination]`). In the model they now have display names (`Loans[Credit Score]`); the mapping is in `03_Data_Model.md` section 4.

---

## 0. How each KPI was tested

A measure can return a number that looks fine but is wrong: a missing filter, the wrong column, or a relationship that does not pass the filter. Every KPI was tested at least two ways before going on a page.

| Test | What it means | Example |
|---|---|---|
| 1. Known number | Calculate the same total outside Power BI and compare | All fee rows added up in the cleaned files give $2,467,951; the Fee Income card must show exactly that |
| 2. KPIs agree | Related KPIs must fit together | Fee income = paying accounts times average fee. July 2024: 8,694 accounts times $12 = $104,328 |
| 3. By hand | Check one month, customer or loan row by row | Filter transactions to March 2023 and type Fee; the sum must equal Fee Income for March 2023 |
| 4. Parts add up | The pieces of a split must add back to the total | Active customers in the five regions add up to all active customers |
| 5. Other level | Try the measure at a different level | Average fee for all of 2024 would mix $5 and $12 into $8.50, so the measure returns blank above month level |

The checks run on the **Data Checks** page of the report. Each returns 0 when the model is healthy.

### Known totals (for test 1)

| Measure | Expected with no filters |
|---|---|
| Fee Income | $2,467,951 |
| Loans Issued | 5,300 |
| Charged-Off Loans | 230 |
| Credit Losses | $7,016,926.20 |
| Chargeback Refunds | $372,952.11 (811 rows) |

The same check also showed that all 288,066 fee rows are Completed, labelled Account Fee and in USD, and that 493 customers closed all their accounts and later came back (section 4).

---

## 1. Helper columns

### Fee Group (customers)

To test whether the price change made customers leave, two groups are compared: Mass customers, who got the price rise, and Affluent and Private customers, who pay $0 before and after.

```
Fee Group =
IF ( customers[segment] = "Mass", "Fee-paying (Mass)", "No fee (Affluent + Private)" )
```

### Score Band and Score Band Order (loans)

KPI 7 compares default rates for groups of credit scores. A chart with 309 different scores is unreadable, so scores are grouped.

```
Score Band =
VAR s = loans[credit_score_at_origination]
RETURN
    SWITCH (
        TRUE (),
        ISBLANK ( s ), "Unknown",
        s < 600, "Under 600",
        s < 650, "600 to 649",
        s < 700, "650 to 699",
        s < 750, "700 to 749",
        "750 and above"
    )

Score Band Order =
VAR s = loans[credit_score_at_origination]
RETURN
    SWITCH ( TRUE (), ISBLANK ( s ), 6, s < 600, 1, s < 650, 2, s < 700, 3, s < 750, 4, 5 )
```

`SWITCH ( TRUE (), ... )` checks the conditions from top to bottom and stops at the first true one. The order column is calculated from the score itself, not from Score Band, because a sort column must not depend on the column it sorts. Score Band is sorted by Score Band Order, which is hidden.

### Other helper columns

- `Date[Year Quarter]`: for example "2024 Q3"
- `Date[Price Period]` and `Date[Price Period Order]`: Before: $5 fee (Jan 2023 to Jun 2024), First 9 months at $12 (Jul 2024 to Mar 2025), Apr to Dec 2025
- `branches[Region Group]`: "East" or "Other regions"
- `accounts[Monthly Revenue]`: the account's average monthly revenue from `product_revenue`, by product and the customer's segment

---

## 2. Fee income (page: Fee Income)

### KPI 1: Fee Income

Answers 1.1 (did it go up?), 1.2 (did the change start in July 2024?) and 4.4.1.

```
Fee Income =
CALCULATE (
    SUM ( transactions[amount] ),
    transactions[txn_type] = "Fee",
    transactions[status] = "Completed"
)
```

| Test | Result |
|---|---|
| Known number | $2,467,951 |
| KPIs agree | Paying accounts times average fee equals fee income in every month (Data Checks) |
| Parts add up | Affluent and Private are $0 |

### KPI 4: Fee-Paying Accounts

Answers 1.3.2 and 1.4; input to KPI 13.

```
Fee-Paying Accounts =
CALCULATE (
    DISTINCTCOUNT ( transactions[account_id] ),
    transactions[txn_type] = "Fee",
    transactions[status] = "Completed"
)
```

Cross-check against Mass checking accounts open at month end: paying accounts are 0 to 10 higher each month, explained by data issue L2.

### KPI 3: Average Fee per Account

Answers 1.3.1.

```
Average Fee per Account =
IF (
    HASONEVALUE ( 'Date'[Year Month] ),
    DIVIDE ( [Fee Income], [Fee-Paying Accounts] )
)
```

`HASONEVALUE` returns blank above month level, so nobody sees a blended $8.50. Result: $5.00 every month to June 2024 and $12.00 every month from July 2024, matching `fee_schedule`.

---

## 3. Credit risk (page: Credit Risk)

### KPI 6: Loans Issued

```
Loans Issued = DISTINCTCOUNT ( loans[loan_id] )

Amount Lent = SUM ( loans[loan_amount] )
```

Uses the active relationship on the issue date. Known number: 5,300. The five regions add up to the total.

### KPI 5: Credit Losses

```
Credit Losses =
CALCULATE (
    SUM ( loans[outstanding_balance] ),
    loans[loan_status] = "Charged Off",
    USERELATIONSHIP ( 'Date'[Date], loans[default_date] )
)

Charged-Off Loans =
CALCULATE (
    COUNTROWS ( loans ),
    loans[loan_status] = "Charged Off",
    USERELATIONSHIP ( 'Date'[Date], loans[default_date] )
)
```

Known numbers: $7,016,926.20 and 230. A loan issued in one year and charged off in a later year appears in the year of default. Loans Issued and Credit Losses can sit in the same visual by year, each using its own date.

### KPI 7: Default Rate (12 months)

```
Defaults Within 12 Months =
COUNTROWS (
    FILTER (
        loans,
        loans[loan_status] = "Charged Off"
            && NOT ISBLANK ( loans[default_date] )
            && loans[default_date] <= EDATE ( loans[origination_date], 12 )
    )
)

Default Rate (12m) =
VAR LastMatureDate = DATE ( 2024, 12, 31 )   -- loans issued after this have less than 12 months of history
RETURN
    IF (
        MAX ( 'Date'[Date] ) <= LastMatureDate,
        DIVIDE ( [Defaults Within 12 Months], [Loans Issued] )
    )
```

Grouped by year of issue. 2025 returns blank (too young). The Unknown band holds the 39 loans without a valid score. Groups under about 50 loans are noisy, so analysis starts with regions and then branches.

### KPI 8: Average Loss per Default

```
Average Loss per Default = DIVIDE ( [Credit Losses], [Charged-Off Loans] )

Median Loss per Default =
CALCULATE (
    MEDIAN ( loans[outstanding_balance] ),
    loans[loan_status] = "Charged Off",
    USERELATIONSHIP ( 'Date'[Date], loans[default_date] )
)
```

Charged-off loans times the average equals credit losses. An average far above the median means a few large losses (mortgages) drive it.

### Supporting measures

```
Late 30+ Balance =
CALCULATE ( SUM ( loans[outstanding_balance] ), loans[loan_status] = "Late 30+" )

Share of Loans Under 650 =
DIVIDE ( CALCULATE ( [Loans Issued], loans[credit_score_at_origination] < 650 ), [Loans Issued] )
```

---

## 4. Customers (page: Customers)

### Definition update found while planning

493 customers closed all their accounts and later opened a new one. With the first definitions, the check "end = start + new - churned" would not balance, so all customer flows use month-end snapshots and a fourth flow, Reactivated:

**Active at end = active at start + new + reactivated - churned**

| Flow | Definition (per month) |
|---|---|
| Active | At least one open account at month end |
| Churned | Active at the start (previous month end), not active at the end |
| New | Not active at the start, active at the end, first-ever account opened in the month |
| Reactivated | Not active at the start, active at the end, had an account before |

A customer who opens and closes within one month is never active at a month end and appears in no flow.

### KPI 2: Active Customers

```
Active Customers =
VAR d = MAX ( 'Date'[Date] )
RETURN
    CALCULATE (
        DISTINCTCOUNT ( accounts[customer_id] ),
        accounts[open_date] <= d,
        OR ( ISBLANK ( accounts[close_date] ), accounts[close_date] > d )
    )

Active Customers (Start) =
VAR d = MIN ( 'Date'[Date] ) - 1
RETURN
    CALCULATE (
        DISTINCTCOUNT ( accounts[customer_id] ),
        accounts[open_date] <= d,
        OR ( ISBLANK ( accounts[close_date] ), accounts[close_date] > d )
    )
```

At month level `MAX` is the month end and at year level 31 December. `Start` is the day before the period begins.

### KPI 9: New Customers (and Reactivated)

```
New Customers =
SUMX (
    VALUES ( 'Date'[Year Month] ),
    VAR mS = CALCULATE ( MIN ( 'Date'[Date] ) ) - 1
    VAR mE = CALCULATE ( MAX ( 'Date'[Date] ) )
    RETURN
        COUNTROWS (
            FILTER (
                VALUES ( customers[customer_id] ),
                VAR FirstOpen = CALCULATE ( MIN ( accounts[open_date] ) )
                VAR ActiveStart = CALCULATE ( COUNTROWS ( accounts ), accounts[open_date] <= mS, OR ( ISBLANK ( accounts[close_date] ), accounts[close_date] > mS ) ) > 0
                VAR ActiveEnd = CALCULATE ( COUNTROWS ( accounts ), accounts[open_date] <= mE, OR ( ISBLANK ( accounts[close_date] ), accounts[close_date] > mE ) ) > 0
                RETURN NOT ActiveStart && ActiveEnd && FirstOpen > mS
            )
        )
)
```

`Reactivated Customers` is the same measure with the last line `NOT ActiveStart && ActiveEnd && FirstOpen <= mS`. The outer `SUMX` over months makes a yearly total equal to the sum of its months.

### KPI 10: Churned Customers and Churn Rate

```
Churned Customers =
SUMX (
    VALUES ( 'Date'[Year Month] ),
    VAR mS = CALCULATE ( MIN ( 'Date'[Date] ) ) - 1
    VAR mE = CALCULATE ( MAX ( 'Date'[Date] ) )
    RETURN
        COUNTROWS (
            FILTER (
                VALUES ( customers[customer_id] ),
                VAR ActiveStart = CALCULATE ( COUNTROWS ( accounts ), accounts[open_date] <= mS, OR ( ISBLANK ( accounts[close_date] ), accounts[close_date] > mS ) ) > 0
                VAR ActiveEnd = CALCULATE ( COUNTROWS ( accounts ), accounts[open_date] <= mE, OR ( ISBLANK ( accounts[close_date] ), accounts[close_date] > mE ) ) > 0
                RETURN ActiveStart && NOT ActiveEnd
            )
        )
)

Churn Rate =
IF ( HASONEVALUE ( 'Date'[Year Month] ), DIVIDE ( [Churned Customers], [Active Customers (Start)] ) )
```

Companion measures for checking accounts:

```
Checking Closures =
VAR s = MIN ( 'Date'[Date] )
VAR e = MAX ( 'Date'[Date] )
RETURN
    CALCULATE ( COUNTROWS ( accounts ), accounts[product_id] = "P01", accounts[close_date] >= s, accounts[close_date] <= e )

Checking Open (Start) =
VAR d = MIN ( 'Date'[Date] ) - 1
RETURN
    CALCULATE (
        COUNTROWS ( accounts ),
        accounts[product_id] = "P01",
        accounts[open_date] <= d,
        OR ( ISBLANK ( accounts[close_date] ), accounts[close_date] > d )
    )

Checking Closure Rate =
IF ( HASONEVALUE ( 'Date'[Year Month] ), DIVIDE ( [Checking Closures], [Checking Open (Start)] ) )

Checking Closure Rate (Period) =
DIVIDE (
    SUMX ( VALUES ( 'Date'[Year Month] ), [Checking Closures] ),
    SUMX ( VALUES ( 'Date'[Year Month] ), [Checking Open (Start)] )
)
```

The period version gives the average monthly rate over any period, such as the three price periods.

### KPI 11: Partially Churned Customers

```
Partially Churned Customers =
SUMX (
    VALUES ( 'Date'[Year Month] ),
    VAR s = CALCULATE ( MIN ( 'Date'[Date] ) )
    VAR e = CALCULATE ( MAX ( 'Date'[Date] ) )
    RETURN
        COUNTROWS (
            FILTER (
                VALUES ( customers[customer_id] ),
                VAR ClosedInMonth = CALCULATE ( COUNTROWS ( accounts ), accounts[close_date] >= s, accounts[close_date] <= e )
                VAR OpenAtEnd = CALCULATE ( COUNTROWS ( accounts ), accounts[open_date] <= e, OR ( ISBLANK ( accounts[close_date] ), accounts[close_date] > e ) )
                RETURN ClosedInMonth > 0 && OpenAtEnd > 0
            )
        )
)

Customers With 2+ Accounts (Start) =
-- a snapshot: blank on total rows, because a level on one day cannot be summed
VAR d = MIN ( 'Date'[Date] ) - 1
RETURN
IF ( HASONEVALUE ( 'Date'[Year Month] ),
    COUNTROWS (
        FILTER (
            VALUES ( customers[customer_id] ),
            CALCULATE ( COUNTROWS ( accounts ), accounts[open_date] <= d, OR ( ISBLANK ( accounts[close_date] ), accounts[close_date] > d ) ) >= 2
        )
    )
)

Partial Churn Rate =
IF ( HASONEVALUE ( 'Date'[Year Month] ),
     DIVIDE ( [Partially Churned Customers], [Customers With 2+ Accounts (Start)] ) )

Accounts Closed (Still Active) =
VAR s = MIN ( 'Date'[Date] )
VAR e = MAX ( 'Date'[Date] )
RETURN
    COUNTROWS (
        FILTER (
            accounts,
            accounts[close_date] >= s && accounts[close_date] <= e
                && CALCULATE ( COUNTROWS ( accounts ), ALLEXCEPT ( accounts, accounts[customer_id] ),
                       accounts[open_date] <= e, OR ( ISBLANK ( accounts[close_date] ), accounts[close_date] > e ) ) > 0
        )
    )
```

Known values: January 2024, 62 partially churned out of 7,358 customers with 2 or more accounts (0.84%); July 2024, 140 out of 7,919 (1.77%). A customer can't be churned and partially churned in the same month.

---

## 5. Net result and the price change (pages: Price Change, Overview)

### KPI 12: Measurable Net Result

```
Chargeback Refunds =
CALCULATE (
    SUM ( transactions[amount] ),
    transactions[merchant_category] = "Chargeback",
    transactions[status] = "Completed"
)

Estimated Account Revenue =
SUMX (
    VALUES ( 'Date'[Year Month] ),
    VAR d = CALCULATE ( MAX ( 'Date'[Date] ) )
    RETURN
        CALCULATE (
            SUM ( accounts[Monthly Revenue] ),
            accounts[open_date] <= d,
            OR ( ISBLANK ( accounts[close_date] ), accounts[close_date] > d )
        )
)

Measurable Net Result =
[Fee Income] + [Estimated Account Revenue] - [Credit Losses] - [Chargeback Refunds]
```

Known number: Chargeback Refunds $372,952.11. The result equals the sum of its four parts. Labelled "estimated" and "not profit" in the report.

### KPI 13: Extra Fee Income

```
Old Mass Fee =
LOOKUPVALUE (
    fee_schedule[monthly_fee_usd],
    fee_schedule[segment], "Mass",
    fee_schedule[effective_to], DATE ( 2024, 6, 30 )
)

Extra Fee Income =
-- fee income minus what the same accounts would have paid at the old $5 fee
VAR ChangeDate = DATE ( 2024, 7, 1 )
RETURN
    SUMX (
        VALUES ( 'Date'[Year Month] ),
        VAR MonthStart = CALCULATE ( MIN ( 'Date'[Date] ) )
        RETURN
            IF ( MonthStart >= ChangeDate, [Fee Income] - [Fee-Paying Accounts] * [Old Mass Fee] )
    )

Extra Fee Income (Cumulative) =
CALCULATE ( [Extra Fee Income], FILTER ( ALL ( 'Date' ), 'Date'[Date] <= MAX ( 'Date'[Date] ) ) )
```

Results: Old Mass Fee $5; July 2024 8,694 accounts times $7 = $60,858; total July 2024 to December 2025 $1,027,621. Every month before July 2024 is blank.

### KPI 14: Estimated Revenue Lost and the net effect

Step 1, the normal closure rate before the change (January 2023 to June 2024):

```
Mass Baseline Closure Rate =
CALCULATE (
    DIVIDE (
        SUMX ( VALUES ( 'Date'[Year Month] ), [Checking Closures] ),
        SUMX ( VALUES ( 'Date'[Year Month] ), [Checking Open (Start)] )
    ),
    customers[Fee Group] = "Fee-paying (Mass)",
    DATESBETWEEN ( 'Date'[Date], DATE ( 2023, 1, 1 ), DATE ( 2024, 6, 30 ) )
)
```

`Control Baseline Closure Rate` is the same with `"No fee (Affluent + Private)"`. Results: Mass 0.39% (543 closures out of 139,009 account-months), no fee 0.42% (305 out of 72,791).

Step 2, expected and extra closures each month:

```
Expected Mass Closures =
SUMX (
    VALUES ( 'Date'[Year Month] ),
    CALCULATE ( [Checking Open (Start)], customers[Fee Group] = "Fee-paying (Mass)" ) * [Mass Baseline Closure Rate]
)

Extra Mass Closures =
SUMX (
    VALUES ( 'Date'[Year Month] ),
    VAR MonthStart = CALCULATE ( MIN ( 'Date'[Date] ) )
    RETURN
        IF (
            MonthStart >= DATE ( 2024, 7, 1 ),
            CALCULATE ( [Checking Closures], customers[Fee Group] = "Fee-paying (Mass)" ) - [Expected Mass Closures]
        )
)

Extra Mass Closures (Cumulative) =
CALCULATE ( [Extra Mass Closures], FILTER ( ALL ( 'Date' ), 'Date'[Date] <= MAX ( 'Date'[Date] ) ) )
```

The planned monthly adjustment by the control group was dropped. The control group's monthly rate is noisy (0.11% to 0.69% before the change), and its average did not move (0.42% before, 0.39% after), so it serves as a check that the baseline holds, not as a multiplier.

Step 3, the value of a lost account and the loss:

```
Value per Lost Account =
[Old Mass Fee]
    + LOOKUPVALUE ( product_revenue[avg_monthly_revenue_usd], product_revenue[product_id], "P01", product_revenue[segment], "Mass" )
-- $5 + $4 = $9 a month. A minimum: savings and cards closed later are not included

Estimated Revenue Lost =
SUMX ( VALUES ( 'Date'[Year Month] ), [Extra Mass Closures (Cumulative)] * [Value per Lost Account] )

Estimated Revenue Lost (Cumulative) =
CALCULATE ( [Estimated Revenue Lost], FILTER ( ALL ( 'Date' ), 'Date'[Date] <= MAX ( 'Date'[Date] ) ) )

Net Effect of Price Change = [Extra Fee Income] - [Estimated Revenue Lost]

Net Effect (Cumulative) = [Extra Fee Income (Cumulative)] - [Estimated Revenue Lost (Cumulative)]
```

Results (July 2024 to December 2025): extra closures 2,640.7 (253.65 in July 2024); revenue lost $285,102 ($2,283 in July 2024); net effect $742,519. With $11.06 per lost account (savings and cards added), revenue lost is about $350,300 and the net effect about $677,300. A Fee Group filter must not apply to this page, because revenue lost is fixed to Mass while extra fee income is not.

---

## 6. Data checks (folder 7 QA Checks, page Data Checks)

```
QA Loans by Default Date =
CALCULATE ( COUNTROWS ( loans ), USERELATIONSHIP ( 'Date'[Date], loans[default_date] ) )

QA Known Numbers Failed =
VAR FeeIncome  = CALCULATE ( [Fee Income], REMOVEFILTERS () )
VAR Loans      = CALCULATE ( [Loans Issued], REMOVEFILTERS () )
VAR ChargedOff = CALCULATE ( [Charged-Off Loans], REMOVEFILTERS () )
VAR CreditLoss = CALCULATE ( [Credit Losses], REMOVEFILTERS () )
VAR Fraud      = CALCULATE ( [Chargeback Refunds], REMOVEFILTERS () )
VAR ExtraFee   = CALCULATE ( [Extra Fee Income], REMOVEFILTERS () )
RETURN
    INT ( ROUND ( FeeIncome, 0 ) <> 2467951 ) + INT ( Loans <> 5300 ) + INT ( ChargedOff <> 230 )
  + INT ( ROUND ( CreditLoss, 2 ) <> 7016926.20 ) + INT ( ROUND ( Fraud, 2 ) <> 372952.11 )
  + INT ( ROUND ( ExtraFee, 0 ) <> 1027621 )

QA Fee Check =
SUMX ( VALUES ( 'Date'[Year Month] ), ABS ( [Fee Income] - [Fee-Paying Accounts] * [Average Fee per Account] ) )

QA Customer Flow Check =
SUMX ( VALUES ( 'Date'[Year Month] ),
    ABS ( [Active Customers (Start)] + [New Customers] + [Reactivated Customers] - [Churned Customers] - [Active Customers] ) )
```

| Check | Result |
|---|---|
| Known totals that differ | 0 |
| Fee check gap | $0 in every month |
| Customer flow gap | 0 in every month |
| Blank year in slicers | Not an error: the 5,070 loans that never defaulted have no default date |

---

## 7. Measure folders

| Folder | Contents |
|---|---|
| 1 Fee Income | KPIs 1, 3, 4 |
| 2 Credit Risk | KPIs 5 to 8, Late 30+ Balance, Share of Loans Under 650 |
| 3 Customers | KPIs 2, 9, 10, 11 and the checking-closure measures |
| 4 Price Change | KPIs 13 and 14, baselines, net effect |
| 5 Net Result | KPI 12 and its parts |
| 6 Card Labels | Text measures used by the cards; each measure's name is the card's label |
| 7 QA Checks | The data checks |

Every main measure has a description, shown when hovering over it in the field list.

## 8. Build order

1. KPI 1, then 4, then 3 (check: paying accounts times average fee equals fee income)
2. KPI 6, then 5 and Charged-Off Loans, then 8, then 7 with the Score Band column
3. KPI 2 and its start version, then 10, then 9 and Reactivated, then 11 (check: the customer flow identity), plus the checking-closure measures
4. Chargeback Refunds, then Estimated Account Revenue, then KPI 12
5. Old Mass Fee, then KPI 13, then the baselines, KPI 14 and the net effect

## 9. Notes

- **Currency:** fees and chargebacks are all in USD; EUR appears only on travel and card spending. No KPI needs conversion.
- **Performance:** the customer-flow measures (KPIs 9 to 11) loop over 20,000 customers each month. If visuals become slow, a customer-by-month snapshot table built in Power Query (about 740,000 rows) would be faster.
- **One number everywhere:** KPIs 12 and 13 reuse KPIs 1, 3, 4 and 5 instead of repeating their logic.
