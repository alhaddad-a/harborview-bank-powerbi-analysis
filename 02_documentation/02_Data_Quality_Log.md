# Harborview Bank: Data-Quality Log

Profiling was done on the entire data set, not the first 1,000 rows. Each problem lists how it was found, the rows affected and the fix applied in Power Query.

## Summary

| Table | Rows in | Rows out | Problems fixed |
|---|---|---|---|
| customers | 20,120 | 20,000 | 7 |
| accounts | 36,605 | 36,605 | 2 |
| loans | 5,320 | 5,300 | 4 |
| transactions | 2,329,114 | 2,319,645 | 6 |
| branches, products, fee_schedule, fx_rates, product_revenue | | | 0 |

## customers (20,120 rows, 20,000 after cleaning)

| # | Column | Problem | How I found it | Rows | Fix |
|---|---|---|---|---|---|
| 2 | customer_id | Duplicate customers (whole rows copied) | Count 20,120, distinct 20,000, unique 19,880 | 120 | Removed duplicates across all columns |
| 3 | gender | 8 versions of 2 values: case (male, FEMALE) and abbreviations (M, m, F, f) | Distinct count 8, expected 2 | 1,968 | Capitalised each word, then replaced M with Male and F with Female (whole cell) |
| 4 | birth_date | Placeholder date 1900-01-01 (age 126) | Minimum value and a temporary filter | 104 | Replaced with null; customers kept, age analysis skips them |
| 5 | first_name | No issues | 0% empty, 30 distinct | 0 | None |
| 6 | last_name | No issues | 0% empty, 30 distinct | 0 | None |
| 7 | email | Upper case and a trailing space on the same rows | Contains "@MAIL" 1,030; ends with a space 1,030 | 1,030 | Trimmed and lower-cased |
| 8 | region | Missing region stored as an empty string, not null | 2% empty; 6 distinct values (5 regions and blank) | 299 (298 after removing duplicates) | Filled from the home branch's region (20,000 of 20,000 matched) |
| 9 | home_branch_id | No issues | 25 distinct, matching the 25 branches | 0 | None |
| 10 | segment | No issues | 3 distinct (Mass, Affluent, Private) | 0 | None |
| 11 | income_band | 8 values instead of 4: case ("Very high", "low"), a typo ("Hgh") and a leading space (" Medium") | Distinct count 8; minimum " Medium" shows the space | 189 | Trimmed, capitalised each word, replaced Hgh with High |
| 12 | signup_date | Mixed formats: YYYY-MM-DD and DD/MM/YYYY (17/12/2022 proves day first) | Value list; filter on "/" | 1,423 | Custom column parsing each format separately, 0 errors. A US-locale type change would have failed on 874 rows and silently swapped day and month on 549 |

**Cleaning steps**

1. Remove duplicate customers
2. Standardise gender case, then replace M with Male and F with Female
3. Trim email, then lower-case email
4. Trim income_band, standardise its case, fix the typo Hgh to High
5. Parse signup_date (two formats), set its type, replace the old column
6. Set birth_date type, replace the placeholder with null
7. Merge branches on home_branch_id, fill blank region from the branch region, remove helper columns, restore column order

**Result:** 20,000 rows. gender has 2 values, income_band 4, region 5 with none blank, email 20,000 unique, birth_date 104 null, signup_date 0 errors. Every home_branch_id matches a branch.

## accounts (36,605 rows)

| # | Column | Problem | How I found it | Rows | Fix |
|---|---|---|---|---|---|
| 1 | status | 4 values instead of 2: "open" (1,302) and "CLOSED" (529) next to "Open" (24,669) and "Closed" (10,105) | Distinct count 4; the four counts add to 36,605 | 1,831 | Capitalised each word: Open 25,971, Closed 10,634 |
| 13 | account_id | No issues | Count, distinct and unique all 36,605 | 0 | None |
| 14 | customer_id | No issues. Repeats are expected (7,105 customers have 1 account, 9,185 have 2, 3,710 have 3) | 20,000 distinct, matching all customers | 0 | None |
| 15 | product_id | No issues: only P01, P02, P03 (loan products are in `loans`) | 3 distinct | 0 | None |
| 16 | open_date | No issues | 0 empty; 2019-01-01 to 2025-12-15 | 0 | None |
| 17 | close_date | No issues on its own. 71% empty is expected (open accounts); the latest date equals the extract date | Closed accounts never empty, open accounts always empty | 0 | None |
| 18 | close_date and open_date | 40 accounts closed before they were opened, every one exactly 30 days earlier. open_date is right: account IDs follow open-date order | True/false check column, 40 true | 40 | Set close_date to open_date + 30 and flagged the rows in `close_date_corrected`. See L2 below: this fix turned out to be wrong for most of the 40 |

**Cleaning steps:** standardise status case; set date types; flag close dates before open dates; correct close_date; tidy column names and order.

**Result:** 36,605 rows. Open 25,971 and Closed 10,634; every open account has an empty close_date; 40 corrected dates flagged.

## loans (5,320 rows, 5,300 after cleaning)

| # | Column | Problem | How I found it | Rows | Fix |
|---|---|---|---|---|---|
| 19 | loan_id | 20 loans listed twice as exact copies | Count 5,320, distinct 5,300, unique 5,280; Keep Duplicates on all columns returned 40 rows | 20 extra rows | Removed duplicates; 5,300 unique loans |
| 20 | customer_id | No issues; 4,417 borrowers, repeats expected | 0 empty | 0 | None |
| 21 | branch_id | No issues | 25 distinct | 0 | None |
| 22 | product_id | No issues: L01, L02, L03 | 3 distinct | 0 | None |
| 23 | origination_date | No issues: business days from 2022-01-03 to 2025-12-31 | 0 empty | 0 | None |
| 24 | loan_amount | Missing amount stored as an empty string | 15 empty strings | 15 | Flagged in `loan_amount_calculated`, then calculated from payment, rate and term with the standard loan formula, rounded to 100. Tested first on loans with a known amount |
| 25 | interest_rate | Rate stored as a percent (9.96) instead of a decimal (0.0996) | Sorted descending; filtered values not starting with "0." | 341 (339 after removing duplicates) | Divided by 100 when 1 or higher. Average fell from 71.5% to about 9% |
| 26 | term_months | No issues: 24 to 72 months, and 180 or 360 for mortgages only | 7 distinct | 0 | None |
| 27 | monthly_payment | No issues: 46.12 to 6,382.21, all positive | 0 empty | 0 | None |
| 28 | credit_score_at_origination | Placeholder scores outside 300 to 850: 0 (22 loans) and 999 (17 loans) | Minimum 0, maximum 999 | 39 | Replaced with null; loans kept and shown as "Unknown" in score bands |
| 29 | loan_status | No issues | 4 distinct | 0 | None |
| 30 | default_date | No issues: filled for every Charged Off loan and empty for all others | Rule checked both ways | 0 | None |
| 31 | outstanding_balance | No issues: Paid Off loans are always 0, other statuses never 0 | Rule checked by status | 0 | None |
| 32 | default_date and origination_date | No loan defaulted before it was issued | True/false check column, 0 true | 0 | None |
| 33 | Money columns | No issues after setting types: loan_amount 2,200 to 682,600 (the top 10 are mortgages) | Column statistics | 0 | None |

**Cleaning steps:** remove duplicate loans; set data types; convert percent rates to decimals; replace scores 0 and 999 with null; flag and calculate missing loan amounts.

**Result:** 5,300 loans. Interest rate 0.038 to 0.145, credit score 520 to 850 with 39 unknown, no empty loan amounts (15 calculated and flagged).

## transactions (2,329,114 rows, 2,319,645 after cleaning)

| # | Column | Problem | How I found it | Rows | Fix |
|---|---|---|---|---|---|
| 34 | txn_id | 9,279 transactions listed twice as exact copies | Count 2,329,114, distinct 2,319,835; Keep Duplicates on all columns returned 18,558 rows | 9,279 extra rows | Removed duplicates |
| 35 | account_id | Orphans: account IDs not in `accounts` (A9xxxxx, while real accounts end at A036605) | Filter on IDs not starting with "A0" | 150 | Inner merge with accounts excluded them (35 credits $95,720.53, 101 debits $24,646.13, 14 fees $112.00) |
| 36 | txn_datetime | Dates after the extract date: March and April 2026, all at midnight | Maximum 2026-04-29; filter on 2026 | 40 | Removed rows dated 2026 or later |
| 37 | txn_type | No issues: Debit, Credit, Fee | 3 distinct on the full table | 0 | None |
| 38 | merchant_category | 34 values instead of 17: lower case and trailing spaces on the same rows ("retail  ") | 34 distinct, exactly 17 doubled | 46,503 (46,320 after removing duplicates) | Trimmed and capitalised each word; 17 values |
| 39 | amount | Negative amounts on Debit rows, so the direction was stored twice | Filter on values starting with "-": all Debit | 17,403 (17,327 after removing duplicates) | Absolute value; direction stays in txn_type |
| 40 | currency | No issues: USD and EUR | 2 distinct | 0 | None |
| 41 | channel | Missing channel stored as an empty string on normal debits and credits | 7 channels plus blank; 12,231 blanks | 12,231 (12,183 after removing duplicates) | Replaced the blank with "Unknown" |
| 42 | status | No issues: Completed, Failed, Reversed | 3 distinct | 0 | None; only Completed counts in the KPIs |
| 43 | amount | Real range after the fix: 2.11 to 40,537.71. The most common values, 12 and 5, are monthly fees | Column statistics | 0 | None |

**Cleaning steps:** remove duplicates; trim and standardise merchant_category; label blank channels as Unknown; set data types; make amounts positive; remove rows after the extract date; keep only transactions with a known account.

**Result:** 2,319,645 rows. 17 merchant categories, 7 channels plus Unknown, all amounts positive, latest date 2025-12-31, no orphans.

Not an error: thousands of rows share month-end timestamps at 02:00:00. This is the nightly batch that charges the monthly account fee, with different transaction and account IDs, not duplicates.

## Lookup tables (all clean)

| # | Table | Rows | Result | How I checked |
|---|---|---|---|---|
| 44 | branches | 25 | No issues | 25 unique IDs, names and cities; 5 regions with 5 branches each; opening years 1985 to 2014 |
| 45 | products | 6 | No issues | P01 to P03 and L01 to L03; product types fit each product |
| 46 | fee_schedule | 4 | No issues, matches the brief | Checking only: Mass $5 (2019-01-01 to 2024-06-30), Mass $12 (from 2024-07-01), Affluent $0, Private $0; no gaps or overlaps |
| 47 | fx_rates | 72 | No issues | 36 months for each currency; USD always 1.0; EUR between about 1.05 and 1.17 |
| 48 | product_revenue | 9 | No issues | 3 products by 3 segments, all positive |

## Checks after typing and joining

| Check | Result |
|---|---|
| A. accounts: close date before open date | 40 found and corrected (#18) |
| B. loans: default date before issue date | 0 (#32) |
| C. Real minimum and maximum of numeric columns | Done (#33, #43) |
| D. Orphan keys | customers to branches 20,000 of 20,000 matched; transactions to accounts 150 excluded; accounts and loans to customers confirmed through model relationships |
| E. Fees only on Mass checking accounts | Confirmed in the model: all 288,066 fee rows ($2,467,951) are on Mass checking, at $5 or $12 |
| F. FX conversion | Not needed: fees and chargebacks are all in USD |

## Known limitations found during the analysis

| # | Tables | Problem | Impact | Handling |
|---|---|---|---|---|
| L1 | customers, accounts | Almost no new customers after 31 Oct 2025 (7 in November, 3 in December, against about 250 a month). Account openings also stop on 15 Dec 2025 | Active and new customers are understated for November and December 2025 | Kept the data. Customer counts are reported to October 2025, and this is stated on the dashboard, in the memo and in the README. Churn, closures, fees and losses are not affected |
| L2 | accounts, transactions | The fix for #18 is wrong for most of the 40 accounts: 27 have transactions after the new close date (1,436 rows, some years later), and 12 checking accounts kept being charged the fee (239 fees, $1,692) | Very small: 0.1% of accounts. It explains why fee-paying accounts exceed open Mass checking accounts by 0 to 10 a month. Tested by removing the 40 affected customers (see below) | Unresolved and still flagged. Recommended fix: set the close date to the end of the month of the last transaction, or treat the account as open if activity continues to December 2025 |

### Sensitivity test for L2

Key results recalculated without the 40 customers who own a corrected account:

| Result | All customers | Without the 40 | Change |
|---|---|---|---|
| Customers who left, 2023 to 2025 | 3,775 | 3,762 | -0.3% |
| Customers who left, Jul 2024 to Dec 2025 | 3,044 | 3,037 | -0.2% |
| Mass checking closure rate before the change | 0.391% a month | 0.389% a month | -0.002 points |
| Extra Mass closures, Jul 2024 to Dec 2025 | 2,640.7 | 2,639.8 | under 0.1% |
| Net effect of the price change | $742,519 | $741,381 | -0.2% |

No conclusion changes. The dates stay flagged until the source is corrected.

## Lessons

**Profiling**

- Profile the entire data set, and read the Count first every time: the setting can reset to 1,000 rows.
- 100% valid does not mean correct. Compare the distinct values with what you expect.
- Missing values can be empty strings, not null. Check both.
- Placeholder values (0, 999, 1900-01-01) are missing data that looks real.
- A clean column is a result too: record "no issues".
- Distinct is how many different values exist; unique is how many appear exactly once. Repeating IDs = distinct minus unique; extra rows = count minus distinct.
- Keep Duplicates on all columns tells exact copies apart from records that share an ID but differ.
- Numbers stored as text sort by their first character, so minimum and maximum are wrong until the type is set.
- The value dropdown reads only the first 1,000 or so rows. Use column statistics or text filters for large columns.

**Cleaning**

- Filters used only to look must be deleted afterwards; sorting adds a step too.
- One row can have several problems. Remove duplicates first, then count the rest.
- Test every text column you group, filter or join on for leading, trailing and double spaces.
- Before filling a missing value with a formula, test the formula on rows where the answer is known.
- Test a cleaning assumption against other tables before trusting it (L2).

**Judgement**

- Whether a repeat or an empty value is a problem depends on what the column means (a repeated customer_id is normal in accounts; an empty close_date means still open).
- Cross-column rules (filter one column, profile another) catch contradictions.
- The quality log is for data that is wrong. Patterns in correct data go on a separate list for the analysis.
- A sudden break in a flow that was steady for years is a data question before it is a business finding.

## Clues for the analysis (not data errors)

- Branches BR011 to BR015, the five East branches, issued clearly more loans than the others (F33).
- The lowest real credit score is 520, so the "Under 600" band means 520 to 599.
- The highest interest rates go with the lowest scores (pricing by risk).
- The month-end fee batch shows how many accounts were charged each month, which changes around July 2024.
