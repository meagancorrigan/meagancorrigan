# FX Hedging Model Specification

Scenario 20: lithium-refining. Draft of November 6, 2026. Revised November 13, 2026 after review.

Formulas below use named ranges only, no cell addresses. Someone who hasn't seen my brief should be able to build the workbook from this.

## 1. Problem statement

A U.S. lithium refiner has invoiced a German battery maker EUR 11,000,000, due in 273 days. The euro amount is fixed but the dollar amount isn't. If the euro weakens before payment arrives, the refiner gets fewer dollars than it planned on. Every 1 cent move in EURUSD changes proceeds by USD 110,000.

The workbook compares three ways to lock in or protect the dollar amount: a forward, a money-market hedge, and a put option. It shows what each one pays across a range of settlement rates.

## 2. Inputs: the named-range contract

All rates are ACT/360. Every value is a placeholder, "indicative, replaced with live market data at Stage 4," unless the scenario fixes it. The forward is the one exception to replacement: it is the bank's quote and stays as given.

| Name | Value | Unit | Where it came from, and the Stage 4 source |
|---|---|---|---|
| FC_AMT | 11,000,000 | EUR | Fixed by the scenario. |
| T_DAYS | 273 | Days | Fixed by the scenario. |
| S0_in | 1.1224 | USD per EUR | ECB reference rate for Nov 5, 2026, since Nov 6 wasn't final. Stage 4: market close on the day I populate, with date and source. |
| F0_in | 1.1756 | USD per EUR | The bank's quote from the scenario. Kept as given at every stage. If I think it is mispriced, I ask the bank for a requote instead of substituting another forward. |
| R_USD | 4.39% | Annual, ACT/360 | 1-year U.S. Treasury yield, Nov 5, 2026. It was the closest maturity I found. Stage 4: the 9-month Treasury yield. |
| R_FC | 3.155% | Annual, ACT/360 | Midpoint of 6-month Euribor (3.008%) and 12-month Euribor (3.302%), Nov 4, 2026. Stage 4: 9-month Euribor. |
| K_PUT | S0_in (1.1224) | USD per EUR | Scenario says strike at spot, so it follows S0_in. |
| K_CALL | S0_in (1.1224) | USD per EUR | Same. |
| PREM_PUT | 0.0224 | USD | Fixed by the scenario and kept at Stage 4. |
| PREM_CALL | 0.0376 | USD | Fixed by the scenario and kept at Stage 4. |

Four helper settings also live on the Inputs tab, below the ten names above. They aren't market data, but the checks and the sensitivity table need them:

| Name | Value | Use |
|---|---|---|
| CHECK_TOL | USD 1 | Tolerance for rounding in the parity and repayment checks. |
| GAP_FLAG | 0.5% | A forward-versus-implied gap larger than this share of S0_in is flagged. |
| SENS_STEP | 1% | Spacing of the settlement rates in the sensitivity table. |
| SENS_RANGE | 5% | How far the sensitivity table runs either side of S0_in. |

## 3. Tab architecture

- **Cover:** title, scenario, author (Meagan Corrigan), build date, and a line saying the numbers are placeholders until Stage 4. It also has a data-source block listing, for each input, where the value came from and its date: S0_in (ECB reference rate), R_USD (U.S. Treasury yield), R_FC (Euribor), F0_in (the bank's quote), and the scenario-fixed values.
- **Legend/Key:** what the colors mean. Yellow fill is an input, blue fill is an assumption, green fill is a formula, and gray fill is an output. A failed check shows in red text. The ten named inputs are yellow and the four helper settings are blue.
- **Inputs:** the ten names above, with units, sources and a placeholder flag.
- **Forward:** the forward hedge.
- **MoneyMarket:** the three-step money-market hedge and the implied forward.
- **Option:** premium costs in USD, plus a table over the same S_T rows as the Sensitivity tab showing put proceeds, long call net payoff, and short call proceeds.
- **Sensitivity:** proceeds for each strategy across settlement rates, with one chart.
- **Checks:** each rule from section 7, marked PASS, FAIL or FLAG.
- **Notes & Assumptions:** section 4 of this spec and the dates of every source.

## 4. Assumptions and constraints

- Interest is simple, ACT/360, in both currencies.
- No transaction costs, bid-ask spreads, margin or credit charges. With more time I'd test whether these matter.
- One rate per currency, used for both borrowing and investing.
- The euro amount and the date are certain.
- If the market inputs are consistent, the forward and the money-market hedge should give about the same dollars. The bank's forward may not be consistent with live rates, so section 7 treats that gap as something to report, not an error. F0_in is never replaced.
- The put premium is paid in USD on day one, and the main figures take it at face value: OUT_PUT_PREM_USD. The next section puts a dollar number on what that leaves out.

### Premium timing

A dollar paid on day one could have earned R_USD until day T_DAYS, so the put costs more than its face premium by settlement. The input that says what a day-one dollar is worth on day 273 is R_USD, over T_DAYS on an ACT/360 basis.

- OUT_PUT_PREM_FV = OUT_PUT_PREM_USD × (1 + R_USD × T_DAYS/360)
- OUT_PUT_PREM_COST = OUT_PUT_PREM_FV − OUT_PUT_PREM_USD

At the placeholder inputs OUT_PUT_PREM_FV is about 254,603 and OUT_PUT_PREM_COST is about 8,203, so taking the premium at face value flatters the put by about USD 8,200 on EUR 11,000,000, or 0.00075 per euro. The workbook shows OUT_PUT_PREM_COST as an extra line and doesn't use it in the main put proceeds.

The financed breakeven against the money-market hedge is OUT_BREAKEVEN_FIN = OUT_MM_PROCEEDS/FC_AMT + OUT_PUT_PREM_FV/FC_AMT. At the placeholders it moves from 1.1551 (OUT_BREAKEVEN) to 1.1558. No row of the sensitivity table sits between those two numbers, so no row changes winner. Against the forward, the put needs S_T above F0_in + OUT_PUT_PREM_FV/FC_AMT, which is about 1.1987, above the top of the grid at the placeholders (about 1.1785), so the forward beats the put in every row either way.

## 5. Calculation flow

Every step below is a named formula. Names starting OUT_ are the gray output cells.

**Forward.**
- OUT_FWD_PROCEEDS = FC_AMT × F0_in

**Money-market hedge, three steps, each on its own line:**
1. OUT_MM_BORROW = FC_AMT / (1 + R_FC × T_DAYS/360). This sizes the euro loan so the receivable pays it off exactly.
2. OUT_MM_USD_NOW = OUT_MM_BORROW × S0_in
3. OUT_MM_PROCEEDS = OUT_MM_USD_NOW × (1 + R_USD × T_DAYS/360)

**Implied forward and gap.**
- OUT_F_IMPLIED = S0_in × (1 + R_USD × T_DAYS/360) / (1 + R_FC × T_DAYS/360)
- OUT_FWD_GAP_PER_EUR = F0_in − OUT_F_IMPLIED
- OUT_FWD_GAP_USD = FC_AMT × OUT_FWD_GAP_PER_EUR

**Put.** For each settlement rate S_T:
- OUT_PUT_PREM_USD = FC_AMT × PREM_PUT
- PUT_PROCEEDS(S_T) = FC_AMT × MAX(S_T, K_PUT) − OUT_PUT_PREM_USD
- OUT_PUT_FLOOR_PER_EUR = K_PUT − PREM_PUT

**Call.** A long call doesn't protect someone selling euros, so it's here for comparison.
- OUT_CALL_PREM_USD = FC_AMT × PREM_CALL
- LONG_CALL_NET(S_T) = FC_AMT × MAX(S_T − K_CALL, 0) − OUT_CALL_PREM_USD
- The version that fits a receivable is a short call: SHORT_CALL_PROCEEDS(S_T) = FC_AMT × S_T − FC_AMT × MAX(S_T − K_CALL, 0) + OUT_CALL_PREM_USD. It brings in the premium and gives up gains above K_CALL.

**Unhedged.**
- UNHEDGED(S_T) = FC_AMT × S_T

**Put breakeven.**
- OUT_BREAKEVEN = OUT_MM_PROCEEDS / FC_AMT + PREM_PUT. This is the settlement rate above which the put beats the money-market hedge.

## 6. Sensitivity plan

S_T runs from (1 − SENS_RANGE) × S0_in to (1 + SENS_RANGE) × S0_in in steps of SENS_STEP, which at the current settings is 0.95 to 1.05 times S0_in in 1% steps, or 11 rows. For each row, show proceeds when unhedged, with the forward, with the money-market hedge, with the put, and with the short call. One line chart shows all five against S_T.

The chart should let the CFO see that the forward and money-market lines are flat, that the put is flat below the strike and rises above it but stays under by the premium, and that the unhedged line is the risk the others remove.

## 7. Validation rules

At the placeholder inputs, the finished workbook should show these figures, to the nearest dollar.

1. OUT_MM_BORROW × (1 + R_FC × T_DAYS/360) equals FC_AMT. PASS.
2. OUT_MM_PROCEEDS equals FC_AMT × OUT_F_IMPLIED, within CHECK_TOL. OUT_F_IMPLIED should be about 1.13267 and OUT_MM_PROCEEDS about 12,459,327. PASS.
3. Report OUT_FWD_GAP_PER_EUR and OUT_FWD_GAP_USD. Mark it FLAG if the gap is over GAP_FLAG × S0_in. At the placeholders this will show FLAG, about 0.0429 per euro or USD 472,000, because the bank's forward of 1.1756 doesn't match the forward implied by current rates. F0_in stays as quoted. At Stage 4 the gap is explained, and if I think the quote is mispriced I ask the bank for a requote.
4. OUT_FWD_PROCEEDS at the placeholder is 12,931,600.
5. When S_T equals S0_in, PUT_PROCEEDS is FC_AMT × OUT_PUT_FLOOR_PER_EUR = 12,100,000. It never goes below that in any row.
6. When S_T equals S0_in, UNHEDGED is 12,346,400.
7. The sensitivity table has exactly 11 rows, and the middle row has S_T equal to S0_in. Forward proceeds are the same in every row, and money-market proceeds are the same in every row. The two are equal to each other only if F0_in matches OUT_F_IMPLIED, which rule 3 reports.
8. No error cells, every output is a formula, and the only typed numbers are on the Inputs tab.

## 8. Outputs

Each of these is a gray cell with this exact name:

- OUT_FWD_PROCEEDS: forward USD proceeds
- OUT_MM_BORROW, OUT_MM_USD_NOW, OUT_MM_PROCEEDS: the three money-market steps
- OUT_F_IMPLIED: the implied forward
- OUT_FWD_GAP_PER_EUR, OUT_FWD_GAP_USD: the gap between F0_in and the implied forward, per euro and in USD
- OUT_PUT_FLOOR_PER_EUR: K_PUT minus PREM_PUT
- OUT_PUT_PREM_USD: FC_AMT × PREM_PUT, which is 246,400
- OUT_CALL_PREM_USD: FC_AMT × PREM_CALL, which is 413,600
- OUT_PUT_PREM_FV and OUT_PUT_PREM_COST: the premium with a day-one dollar carried to settlement, and the difference, about 254,603 and 8,203 at the placeholders
- OUT_BREAKEVEN: the settlement rate above which the put beats the money-market hedge, about 1.1551 at the placeholders
- OUT_BREAKEVEN_FIN: the same, with the premium carried to settlement, about 1.1558 at the placeholders
- The sensitivity table and its chart

Formats: dollar amounts in whole dollars with thousands commas, exchange rates to 4 decimals, interest rates as a percent to 3 decimals.

The Checks tab shows the status of every rule in section 7.
- The sensitivity table and its chart

Formats: dollar amounts in whole dollars with thousands commas, exchange rates to 4 decimals, interest rates as a percent to 3 decimals.

The Checks tab shows the status of every rule in section 7.
