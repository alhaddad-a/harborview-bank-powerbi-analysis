# Harborview Bank: Analysis

Answers to every question in the scope document's issue tree, with the number that answers it and where it comes from. All figures come from the Power BI model, January 2023 to December 2025 unless stated. F numbers refer to the findings log and L numbers to the data-quality log.

---

## The answer in five points

1. **Fee income rose strongly**, from $450K (2023) to $860K (2024) and $1.16M (2025), **because of the price**: the fee went from $5 to $12 while the number of paying accounts fell.
2. **The price change very likely cost customers.** About 2,640 more Mass checking accounts closed than normal from July 2024 to December 2025, roughly 1 in 3. Only fee payers reacted, only on checking, from the week of the change, in every region. This is strong evidence, not proof (section 4.2).
3. **It still paid off so far:** a net gain of about $0.68M to $0.74M (extra fees of $1.03M minus $0.29M to $0.35M of lost revenue, depending on whether the other products leavers closed are counted). The monthly gain has almost halved and keeps shrinking.
4. **The bank's measurable result fell from +$2.56M (2023) to -$0.41M (2025)** because gross credit losses (charged-off balances, before any recoveries) rose from $0.5M to $4.6M, not because of the fee. Three quarters of 2025 losses came from East, where all five branches tripled their lending from Q1 2024 and gave about half of it to borrowers scoring under 650.
5. **The credit problem is not over.** At the end of 2025 East had 115 loans 30 or more days late with $4.65M outstanding (other regions $0.5M to $0.7M each), and it kept lending the same way through Q4 2025.

---

## Branch 1: Did fee income increase, and was the July 2024 price change the reason?

| Question | Answer | Evidence |
|---|---|---|
| 1.1 Fee income per month | Up every year: $450,165 (2023), $859,558 (2024), $1,158,228 (2025). Monthly: $34K (Jan 2023), $44K (Jun 2024), $104K (Jul 2024), $96K (Dec 2025) | KPI 1 (F1) |
| 1.2 Did the change start in July 2024? | Yes. Before, slow growth of about $0.5K a month as accounts grew; then a jump from $44,015 to $104,328 in one month | KPI 1 by month (F1, F2) |
| 1.3.1 Fee per account | $5.00 every month to June 2024, $12.00 every month from July 2024, and no other value | KPI 3 (F2) |
| 1.3.2 Fee-paying accounts | Grew to 8,803 (June 2024), fell to 7,888 (March 2025), and stayed around 8,000 (7,994 in December 2025) | KPI 4 (F3) |
| 1.4 Does all fee income come from the right accounts at the right price? | Yes. All 288,066 fee rows ($2,467,951) are on Mass checking at $5 or $12. Paying accounts match open Mass checking accounts within 0 to 10 a month; the gap is explained by L2 | F32 |

**Verdict.** The Head of Retail is right that 2025 was the best year for fee income and that the price is why (H1a supported, H1b rejected). The number of payers worked against fee income after July 2024.

---

## Branch 2: How did credit losses change, and what is driving it?

| Question | Answer | Evidence |
|---|---|---|
| 2.1 Losses per quarter | 2023 quarters $55K to $248K. 2024 rose every quarter: $87K, $236K, $467K, $1.01M. 2025: $1.44M, $1.73M (peak), $1.09M, $0.38M. By year: $519K, $1.80M, $4.65M | KPI 5 (F6) |
| 2.2.1 More lending? | Yes: 801, 1,063, 1,707 and 1,729 loans (2022 to 2025). The 2024 jump is almost all East (207 to 673 loans) | KPI 6 (F5) |
| 2.2.2 Riskier lending at the same age? | Yes, and this is the larger driver. 12-month default rate 2.1% (2022), 2.0% (2023), 5.4% (2024). At the 2023 rate, 2024 loans would have produced about 34 defaults instead of 93 | KPI 7 (F8) |
| 2.2.3 Bigger losses per default? | Not within each loan type (median about $10K to $13K every year). The average rose from $20K to $35K because of mix: mortgage defaults went 1, 3, 10 | KPI 8 (F7) |
| 2.3.1 Where? | East: 12-month default rate 1.9% (2023) to 10.5% (2024), about 71 of 93 defaults, and 75% of 2025 losses. All five East branches went from 31 to 50 loans (2023) to 128 to 138 (2024), with 44% to 57% under 650 in every branch. It is a region-wide change, not one branch | F9, F33 |
| 2.3.2 Who, and did the approved mix change? | Score predicts risk strongly: under 600 27.3%, 600 to 649 10.5%, 650 to 699 1.6%, 700 to 749 0.6%, 750 and above 0.2%. East's share of loans under 650 jumped from about 17% (2023) to about 53% from Q1 2024 and stayed at 48% to 54% through Q4 2025. Other regions stayed at 12% to 16%. Within each band East performs about like the others | F10 to F12, F33 |

**Early warning.** At the end of 2025 East has 115 loans 30 or more days late with $4.65M outstanding, against 11 to 20 loans and $0.47M to $0.69M in each other region. 77 of East's late loans were issued in 2025. Losses fell in Q4 2025 because the 2024 loans have largely defaulted already, but the 2025 East loans are next (F34).

**Verdict.** The CFO is right that losses rose, ninefold from 2023 to 2025. H5a (riskier lending) is supported and is the main driver; H5b (more lending) is partly supported; H6 is supported (East, scores under 650).

---

## Branch 3: How did the customer base change, who left, and when?

| Question | Answer | Evidence |
|---|---|---|
| 3.1 Active customers per month | Grew every year: 11,010 (Dec 2022), 14,789 (Dec 2024), 15,773 (Dec 2025). Growth slowed: October 2023 to October 2024 +1,594 (12.1%); October 2024 to October 2025 +1,252 (8.5%). Mass alone fell from 9,548 (June 2024) to 9,198 (February 2025), then recovered | KPI 2 (F13 to F15) |
| 3.2.1 New customers | Steady at about 206 to 276 a month before and after the change. November and December 2025 (7 and 3) are a data gap, not a business event | KPI 9 (F22, D1) |
| 3.2.2 Customers who fully left | 35 to 64 a month before; 192 to 258 a month from July 2024 to March 2025; 93 to 125 a month from April 2025, still about twice normal. 3,775 in total from 2023 to 2025 | KPI 10 (F17) |
| 3.3 Partial churn | Customers who closed something but stayed: 0.84% to 1.07% a month before, 1.42% to 1.77% from July 2024 to March 2025. The account they dropped was checking: Mass checking 6 to 16 a month before, 62 to 85 after; savings and cards flat | KPI 11 (F24, F25) |
| 3.4.1 Who? | Only Mass. Monthly churn rate: Mass 0.32% before, 2.25% in the first 9 months at $12, 0.99% from April to December 2025. Affluent 0.30%, 0.28%, 0.28%; Private 0.32%, 0.32%, 0.31% | F18 |
| 3.4.2 Where? | Everywhere alike. Mass checking closure rate before and in the first 9 months at $12: Central 0.35% to 2.70%, East 0.45% to 2.95%, North 0.41% to 3.32%, South 0.38% to 3.09%, West 0.36% to 3.12% | F31 |

Returning customers also rose after the change, almost all Mass: 41 Mass reactivations in the 18 months before, then 100 and 83 in the two 9-month periods after. Affluent and Private stayed low (F37).

**Verdict.** The CFO is wrong that the base shrank (H2 not supported), but partly right: the fee-paying base shrank for about 8 months and total growth slowed. H3a supported, H3b rejected.

---

## Branch 4: Was the bank better or worse off, and did the price change pay?

### 4.1 Measurable net result (not profit)

The result leaves out operating costs, interest paid on deposits, loan interest income and provisions for expected loan losses, none of which are in the data. Credit losses are gross charged-off balances; money recovered later is not in the data. Chargeback refunds are money paid back to card customers; they have not been confirmed as fraud.

| Year | Fee income | Account revenue (estimated) | Credit losses (gross) | Chargeback refunds | Net result |
|---|---|---|---|---|---|
| 2023 | $450,165 | $2,632,509 | $519,236 | $0 | $2,563,438 |
| 2024 | $859,558 | $3,103,307 | $1,800,887 | $0 | $2,161,978 |
| 2025 | $1,158,228 | $3,448,584 | $4,645,842 | $372,952 | -$411,982 |

From 2023 to 2025: fees +$708K, account revenue +$816K, credit losses -$4,127K, chargebacks -$373K, a total change of -$2,975K. Credit losses explain all of the fall. Without the one-off chargebacks, 2025 is still about -$39K (F30). H7 supported.

The chargebacks are one event: 811 refunds ($372,952) in July 2025 only, all on 35 credit cards, all processed by the System channel, with none in any other month (F29). The pattern looks like a card compromise, but without case records it cannot be confirmed as fraud.

### 4.2 Did the price change cause customers to leave?

| Question | Answer | Evidence |
|---|---|---|
| 4.2.1 Control-group test | Mass checking closure rate 0.39% a month before, 3.05% in the first 9 months at $12, 1.28% from April to December 2025. No-fee group 0.42%, 0.38%, 0.39% (flat). The two groups moved together before the change | F19, F31 |
| Timing | Mass checking closures 4 to 11 a week in June 2024, then 77 in the week of 1 to 7 July 2024 and 65 to 78 a week after. No-fee 1 to 7 a week throughout | F31 |
| Income band | Low income 0.40% to 3.09%, medium 0.37% to 3.01%. No difference, so this check neither supports nor contradicts the fee | F31 |
| 4.2.2 Did the effect fade? | It faded but did not disappear: extra closures of 175 to 268 a month (July 2024 to March 2025), then 56 to 89 a month (April to December 2025), still 2 to 3 times normal | KPI 14 (F27) |

**How sure are we?** Strong evidence, not proof. Timing to the week, the group (fee payers only), the product (checking only) and geography (every region) all point to the fee. Only a nationwide event aimed at Mass customers in the same week could explain it equally well. Exit reasons would confirm it.

### 4.3 What did the leavers take with them?

| Question | Answer | Evidence |
|---|---|---|
| 4.3.1 Other products closed, and how soon | Of the 3,217 Mass checking closures from July 2024 to December 2025, 1,533 customers still had another product. 1,083 of them (71%) closed another product within 90 days: 795 savings accounts and 471 cards in total. The share was the same before the change (192 of 261, 74%): the fee changed how many customers left, not how they left | F35 |
| Value of those products | On average $2.06 a month per checking closure (product_revenue rates) | F35 |
| 4.3.2 How active were the leavers? | As active as those who stayed. In January to June 2024, leavers averaged 2.94 transactions and $255 of spending a month; stayers 2.92 and $256. The bank lost typical customers, not dormant ones | F36 |

### 4.4 Net effect of the price change

| | Base case ($9 per lost account) | With other products ($11.06) |
|---|---|---|
| Extra fee income (July 2024 to December 2025) | $1,027,621 | $1,027,621 |
| Estimated revenue lost | $285,102 | about $350,300 |
| Net effect | +$742,519 | about +$677,300 |

- The $9 case is the main estimate. The $11.06 case adds savings and cards that leavers closed within 90 days, but some of those would have closed anyway, so it may overstate the loss.
- Extra closures attributed to the price: 2,640.7, about 30% of the 8,795 Mass checking accounts open in July 2024.
- Monthly net: about $58.6K in July 2024 and about $32K in December 2025. The gain stays near $56K a month while the loss grows every month.
- Projection (an assumption, not data): if extra closures continue at the 2025 pace of about 70 a month, the monthly loss grows by about $640 to $780 a month and the monthly net reaches zero in roughly 3 to 4 years. The gain already earned stays.

**Verdict.** H8 supported (positive but shrinking). H4 supported (most leavers with other products closed them too).

---

## Hypothesis scorecard

| # | Hypothesis | Prediction | Result | Key evidence |
|---|---|---|---|---|
| H1a | Fee income rose mainly because of the higher price | Expected | Supported | $5 to $12 while paying accounts fell (F2 to F4) |
| H1b | Fee income rose mainly because more accounts paid | Not expected | Rejected | 8,803 to 7,994 paying accounts (F3) |
| H2 | The base at the end of 2025 is smaller than at the end of 2024 | No prediction | Not supported | 14,789 to 15,773; Mass dipped then recovered (F13 to F15) |
| H3a | Closures rose only among fee payers | Expected | Supported | Mass 0.39% to 3.05%, no fee flat (F19, F31) |
| H3b | Closures rose in all segments | Not expected | Rejected | Affluent and Private unchanged (F18) |
| H4 | Customers who closed checking also closed other products | No prediction | Supported | 71% closed another within 90 days (F35) |
| H5a | Losses rose because of riskier lending | Not expected | Supported, main driver | 2.0% to 5.4% default rate; about 34 against 93 defaults (F8) |
| H5b | Losses rose mainly because of more lending | Expected | Partly | More loans, but most of the rise is risk (F5, F8) |
| H6 | Defaults are concentrated in regions or score bands | No prediction | Supported | East (all 5 branches), scores under 650 (F9 to F12, F33) |
| H7 | The net result was lower in 2025 than in 2024 | No prediction | Supported | $2.16M to -$0.41M (F30) |
| H8 | The price change's net effect is positive but shrinking | No prediction | Supported | +$0.68M to +$0.74M; monthly net $58.6K to $32K (F28, F35) |

Two of my four predictions were wrong (H5a and H5b): I expected volume to explain the losses, and the data says risk.

---

## Recommendations

1. **Credit, highest priority: tighten East's approval rules now.** Find out who decided to lend to borrowers under 650 from Q1 2024, bring East's score mix back in line with other regions, review the $4.65M of late East loans and set aside provisions for 2026 losses.
2. **Fee: keep the $12 fee, but protect the Mass base.** It is still a net gain, but a shrinking one. Collect exit reasons, test a fee waiver for active customers (for example those with a salary paid into the account) on a random group first, and contact former customers with a return offer, since many already come back.
3. **Chargebacks and data.** Investigate the July 2025 chargebacks (whether they were fraud, how much was recovered, which card controls apply), confirm why no other chargebacks exist, and fix the data gaps (new customers in November and December 2025, the 40 corrected close dates).

The full version is in the CFO memo.

---

## Limitations

- **Not profit:** no operating costs, no interest paid on deposits, no loan interest income, no provisions for expected loan losses, no deposit balances.
- **Gross credit losses:** charged-off balances before recoveries. The 12-month default rate counts loans, not money, so it is not a standard net charge-off rate.
- **Chargebacks:** refunds paid, not confirmed fraud; recoveries unknown.
- **Estimates:** account revenue and lost revenue use finance's average rates by product and segment.
- **Cause:** strong evidence for the fee, but not proof (no experiment, no exit reasons).
- **Baseline assumption:** without the change, Mass customers would have kept closing checking at 0.39% a month (supported by the flat control group).
- **Data gaps:** new customers in November and December 2025 are missing (D1); 2025 loans are too young for a 12-month default rate; 40 accounts have corrected close dates, 27 of which show later activity (L2). Removing those customers changes churn by 0.3% and the net effect by 0.2%, so no conclusion depends on them.
- **Not covered in this version:** two questions from the brief, unusual transaction patterns beyond the July 2025 chargebacks, and the shift between branch, online and mobile banking.
- **The dataset is synthetic.**
