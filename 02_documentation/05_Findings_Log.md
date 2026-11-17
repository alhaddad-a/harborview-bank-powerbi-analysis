# Harborview Bank: Findings Log

Notes on what each KPI showed while it was built and tested. They feed the analysis (`06_Analysis.md`), the hypothesis results in the scope document, and the memo. Each finding gives its evidence and what is still open.

---

## Branch 1: Fee income (KPIs 1, 3, 4)

| # | Finding | Evidence | Still open |
|---|---|---|---|
| F1 | Fee income rose strongly | $450,165 (2023), $859,558 (2024), $1,158,228 (2025); $2,467,951 in total | Whether the gain outweighs its cost (Branch 4) |
| F2 | The price went from $5 to $12 in July 2024 | Average fee $5.00 every month from Jan 2023 to Jun 2024, $12.00 every month from Jul 2024 to Dec 2025 | |
| F3 | Fee-paying accounts grew until June 2024, then fell | 6,847 (Jan 2023), 8,803 (Jun 2024, peak), 8,694 (Jul 2024), about 8,000 in 2025 (7,994 in Dec 2025) | Did they close because of the price? (KPI 10) |
| F4 | Fee income rose because of the price, despite fewer payers | F2 and F3; paying accounts times average fee equals fee income every month | |

Hypotheses: H1a (price) supported, H1b (more payers) rejected.

---

## Branch 2: Credit risk (KPIs 5 to 8)

| # | Finding | Evidence | Still open |
|---|---|---|---|
| F5 | Lending grew every year, with a jump in 2024 driven by East | Loans issued 801, 1,063, 1,707, 1,729 (2022 to 2025). East went from 207 to 673 loans in 2024 (3.3 times); other regions grew about 20%. About 85% of the extra money lent in 2024 went through East ($9.6M to $36.3M) | |
| F6 | Gross credit losses (charged-off balances, before recoveries) rose sharply, mostly in East | $0.52M (2023), $1.80M (2024), $4.65M (2025). East is 75% of 2025 losses (98 of 133 defaults) | |
| F7 | The loss per default is stable within each loan type; the average rose because of mix | Median about $10K to $13K every year; average $20K to $35K. Mortgage defaults 1, 3, 10; in 2025 the 10 mortgage defaults were about $3.0M (64% of losses), 7 of them in East | |
| F8 | Loans issued in 2024 were much riskier at the same age | 12-month default rate 2.1% (2022), 2.0% (2023), 5.4% (2024); 2025 too young. At the 2023 rate, 2024 loans would have produced about 34 defaults instead of 93, so most of the rise is riskier lending, not just more lending | |
| F9 | The 2024 risk is concentrated in East | East 1.9% (2023) to 10.5% (2024), about 71 of the 93 defaults. Other regions low or falling | |
| F10 | Credit score predicts risk strongly | 12-month default rate for loans issued 2022 to 2024: under 600 27.3%, 600 to 649 10.5%, 650 to 699 1.6%, 700 to 749 0.6%, 750 and above 0.2% | |
| F11 | East started approving low-score borrowers in 2024; other regions did not | East's share under 650 about 15% (2023) to about 51% (2024), and 750 or above 17.9% to 3.0%; it continued in 2025. Other regions stable | Who decided this, and why? |
| F12 | Within the same score band, East performs mostly like other regions; somewhat worse at 600 to 649 | Under 600: East 27.4%, others about 27%. 600 to 649: East 14.1%, others about 7%. 650 to 699: East 0.9%, lower than others. Most other-region cells have fewer than 10 defaults | Branch detail in East (question 2.3.1) |

**Small-numbers rule (used in F9 and F12).** Read every rate with its count, and compare rates only when they rest on enough defaults (about 10 or more defaults, or 50 or more loans). For example, North's under-600 rate is 42.9%, but that is 3 defaults out of about 7 loans: one loan more or less moves it by about 14 points. East's 27.4% rests on 34 defaults out of about 124 loans and moves less than 1 point.

**Conclusion.** Losses rose mainly because East lent much more in 2024, and half of it to borrowers scoring under 650, who default 10% to 27% of the time. Within each band East's loans behave mostly like other regions', so the main driver is who East approved, not how its loans performed afterwards.

Hypotheses: the CFO's "losses up" supported; H5b (more lending) partly supported; H5a (riskier lending) supported and larger; H6 (concentration) supported (East, scores under 650).

---

## Branch 3: Customers (KPIs 2, 9, 10, 11)

| # | Finding | Evidence | Still open |
|---|---|---|---|
| F13 | The total customer base kept growing; it did not shrink in 2025 | 11,010 (Dec 2022), 14,789 (Dec 2024), 15,773 (Dec 2025) | Growth can hide churn (KPIs 9 and 10) |
| F14 | Total growth almost stopped after the price change | January to June 2024: +919 customers; July to December 2024: +145 | |
| F15 | Mass customers fell after July 2024, then recovered in 2025 | 9,548 (Jun 2024, peak), 9,198 (Feb 2025, down 350 or about 4%), 9,547 (Dec 2025) | Did they leave because of the price? |
| F16 | The no-fee segments never dipped (the control group) | Affluent 3,951, 4,311, 4,842 and Private 1,145, 1,236, 1,384 (Jun 2024, Dec 2024, Dec 2025), steady through July 2024 | |
| F17 | Customers leaving quadrupled right after the price change | Churned 35 to 64 a month (Jan to Jun 2024, rate 0.26% to 0.45%), 226 in July 2024 (1.54%), 225 to 258 a month for the rest of 2024; 192 to 209 in Jan to Mar 2025; 93 to 125 from Apr to Nov 2025 (0.60% to 0.79%), still about twice the old level | |
| F18 | The jump is only among Mass customers; the control group did not move | Mass churn 0.23% to 0.43% (Jan to Jun 2024), then 2.19% to 2.57% (Jul to Dec 2024), about 6 times; about 1% to 2% through 2025. Affluent and Private unchanged. 209 of the 226 who left in July 2024 were Mass | |
| F19 | Account-level control test: only fee-paying checking accounts closed faster after July 2024 | Before: Mass 0.28% to 0.51% a month, no fee 0.11% to 0.69%, moving together. July to December 2024: Mass 2.96% to 3.48% (about 8 times), no fee 0.28% to 0.50%. Q1 2025: Mass 2.56% to 2.78%. April to August 2025: Mass 1.09% to 1.40% (about 3 times the old level) | |
| F20 | Some Mass customers dropped checking but stayed | July 2024: 288 Mass checking accounts closed, 209 Mass customers fully left, so about 79 kept another product | Which products? (KPI 11) |
| F21 | Customer flows reconcile every month | Start + new + reactivated - churned = end in every month. 2023 to 2025: 11,010 + 8,257 + 281 - 3,775 = 15,773 | |
| F22 | New customers were steady; the slower growth came from departures | New about 206 to 276 a month before and after July 2024; churned 25 to 64 a month before, 192 to 258 from July 2024 to March 2025, 93 to 125 after | |
| F23 | Returning customers rose after the price change | Reactivated 0 to 7 a month (Jan 2023 to Jul 2024), then 12 to 18 a month from August 2024 | See F37 |
| F24 | Customers who stayed but dropped an account also rose after the change, only among Mass | Partially churned 62 to 81 a month (Jan to Jun 2024, 0.84% to 1.07%), 140 in July 2024 (1.77%), 1.42% to 1.77% to March 2025, about 1.0% to 1.3% from April 2025. Mass 39 to 108 in July (about 2.7 times the rate); no-fee unchanged | |
| F25 | The account they dropped was checking, the product with the fee | Checking closures by customers who stayed: 10 to 24 a month (Jan to Jun 2024) to 70 to 90 (Jul to Dec 2024), about 5 times. Credit card and savings flat. 2024: 417 of 765 such Mass closures were checking | |
| D1 | Data question, not a finding: new customers almost stop in November and December 2025 | 7 (Nov) and 3 (Dec) against about 225 to 276 every other month. Account openings also drop (Oct 493, Nov 209, Dec 109; the last open date is 2025-12-15). The extract has no new customers created after 31 Oct 2025 | Decision: keep the data, record it as a known limitation, report customer counts to October 2025 and compare October 2024 with October 2025. Churn, closures, fees and losses are not affected |

Hypotheses: H2 (smaller base) not supported. H3a (closures rose only among fee payers) supported; H3b rejected. The CFO is partly right: the Mass base shrank for about 7 months after the change and total growth stalled in late 2024.

**Conclusion.** The total base kept growing, but after July 2024 Mass customers, and only them, left about 6 times faster than before, and those who stayed dropped checking about 5 times more often. Savings and card closures did not change. Timing (July 2024), group (Mass only, control flat) and product (checking only) all point to the price change. The effect eased from April 2025 but stayed above the old level.

**Chart note.** From a zero baseline the Mass dip is small (about 4%), so the dashboard shows the Mass line on its own scale, with a title that states the size of the drop.

---

## Branch 4: Net result and the price change (KPIs 12, 13, 14)

| # | Finding | Evidence | Still open |
|---|---|---|---|
| F26 | The $12 price brought about $57K to $61K of extra fee income a month, slowly shrinking | July 2024 $60,858 (8,694 accounts times $7); December 2024 $56,644. July to December 2024 $351,988; 2025 $675,633; total July 2024 to December 2025 $1,027,621, about 42% of all 2023 to 2025 fee income | Does it outweigh the revenue lost? |
| F27 | About 2,640 extra Mass checking closures are attributable to the price change (July 2024 to December 2025) | Baselines January 2023 to June 2024: Mass 0.39% a month, no fee 0.42%; no fee after the change 0.39% (flat). Expected Mass closures about 31 to 34 a month. Extra: 253.6 in July 2024; 175 to 268 a month to March 2025; 56 to 89 a month from April to December 2025. Total 2,640.7, about 30% of the 8,795 Mass checking accounts open in July 2024 | |
| F28 | The net effect so far is positive, about +$743K, but the monthly gain is shrinking | Value per lost account $9 ($5 old fee and $4 checking revenue). Revenue lost: $2,283 in July 2024, $12,978 in December 2024, about $23,800 in December 2025; $285,102 in total. Net $1,027,621 - $285,102 = $742,519. Monthly net about $58.6K (July 2024) to about $32K (December 2025) | If extra closures continue at the 2025 pace, the monthly net keeps falling |
| F29 | All chargeback refunds come from one event in July 2025; not confirmed as fraud | $372,952.11 from 811 refunds of $420 to $500 each. All in July 2025, all on the Rewards Credit Card, all processed by the System channel, on only 35 card accounts (about 23 refunds each), customers in every region and segment. No chargebacks in any other month from 2019 to 2025 | Was it a card breach? Why are there no other chargebacks at all? |
| F30 | The measurable result fell from +$2.56M (2023) to -$0.41M (2025); credit losses explain all of the fall | 2023 $2,563,438; 2024 $2,161,978; 2025 -$411,982. Change 2023 to 2025: fees +$708K, account revenue +$816K, credit losses -$4,127K, chargebacks -$373K. Without the chargebacks, 2025 is still about -$39K | The price change was a net gain in 2025 (about +$437K); the problem is credit risk |
| F31 | Further checks on whether the fee explains the closures | Mass checking closure rate: 0.39% before, 3.05% in the first 9 months at $12, 1.28% from April to December 2025; no fee 0.42%, 0.38%, 0.39%. Every region alike (Central 0.35% to 2.70%, East 0.45% to 2.95%, North 0.41% to 3.32%, South 0.38% to 3.09%, West 0.36% to 3.12%). Weekly Mass closures 4 to 11 in June 2024, then 77 in the week of 1 to 7 July 2024. No difference by income band | Strong evidence, not proof: timing to the week, every region, checking only, fee payers only. Only a Mass-targeted event in the same week could explain it equally. Exit reasons would confirm |
| F32 | Fee income is clean | All 288,066 completed fee rows ($2,467,951) are on Mass checking, at $5 or $12 only. Paying accounts exceed open Mass checking accounts by 0 to 10 a month, explained by L2 | |
| F33 | East's riskier lending was region-wide and started in Q1 2024 | All five East branches went from 31 to 50 loans (2023) to 128 to 138 (2024), with 44% to 57% under 650 in every branch (12-month default rates 5.1% to 15.6%). East's share under 650 by quarter of issue: about 12% to 19% (2023), 53% (Q1 2024), 48% to 54% through Q4 2025. Other regions about 12% to 16% throughout | Who changed East's approval policy in January 2024? |
| F34 | Losses peaked in Q2 2025, but more are coming from East | Quarterly losses: 2024 $87K, $236K, $467K, $1.01M; 2025 $1.44M, $1.73M (peak), $1.09M, $0.38M. At the end of 2025 East has 115 loans 30 or more days late with $4.65M outstanding (77 issued in 2025); other regions 11 to 20 loans and $0.47M to $0.69M each | 2026 losses are likely to rise again unless East's 2025 loans perform better |
| F35 | Leavers took other products too; value per lost account about $11 | Of 3,217 Mass checking closures (July 2024 to December 2025), 1,533 customers had another product; 1,083 of them (71%) closed another within 90 days (795 savings, 471 cards). The share was the same before the change (192 of 261, 74%). Those products are worth $2.06 a month per checking closure, giving $11.06 per lost account, about $350.3K of revenue lost and a net effect of about +$677.3K | Shown as a sensitivity beside the base case |
| F36 | The leavers were typical customers, not dormant ones | January to June 2024: accounts that closed in the second half of 2024 (1,392) averaged 2.94 transactions and $255 of spending a month; accounts still open at the end of 2025 (5,373) averaged 2.92 and $256 | Supports using the average revenue rate |
| F37 | Customers returning after the change are almost all Mass | Reactivated Mass customers: 41 in the 18 months before, 100 in the first 9 months at $12, 83 from April to December 2025. Affluent and Private: 29, 15, 13 | Win-back potential |

Decision D1: option A (keep the data, label it, report customer counts to October 2025).
