# FX Hedging Model Specification

Scenario 20: lithium-refining. Draft of November 6, 2026.

Formulas below use named ranges only, no cell addresses. Someone who hasn't seen my brief should be able to build the workbook from this.

## 1. Problem statement

A U.S. lithium refiner has invoiced a German battery maker EUR 11,000,000, due in 273 days. The euro amount is fixed but the dollar amount isn't. If the euro weakens before payment arrives, the refiner gets fewer dollars than it planned on. Every 1 cent move in EURUSD changes proceeds by USD 110,000.

The workbook compares three ways to lock in or protect the dollar amount: a forward, a money-market hedge, and a put option. It shows what each one pays across a range of settlement rates.

## 2. Inputs: the named-range contract

All rates are ACT/360. Every value is a placeholder, "indicative, replaced with live market data at Stage 4," unless the scenario fixes it.

| Name | Value | Unit | Where it came from, and the Stage 4 source |
|---|---|---|---|
| FC_AMT | 11,000,000 | EUR | Fixed by the scenario. |
| T_DAYS | 273 | Days | Fixed by the scenario. |
| S0_in | 1.1224 | USD per EUR | ECB reference rate for Nov 5, 2026, since Nov 6 wasn't final. Stage 4: market close on the day I populate, with date and source. |
| F0_in | 1.1756 | USD per EUR | The scenario's indicative forward. Stage 4: a live forward or the parity-implied forward, and a note on the gap. |
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

- **Cover:** title, scenario, build date, and a line saying the numbers are placeholders until Stage 4.
- **Legend/Key:** what the colors mean. Blue text is an input, black is a formula, gray fill is an output, red is a failed check.
- **Inputs:** the ten names above, with units, sources and a placeholder flag.
- **Forward:** the forward hedge.
- **MoneyMarket:** the three-step money-market hedge and the implied forward.
- **Option:** the put payoff and the call comparison.
- **Sensitivity:** proceeds for each strategy across settlement rates, with one chart.
- **Checks:** each rule from section 7, marked PASS, FAIL or FLAG.
- **Notes & Assumptions:** section 4 of this spec and the dates of every source.

## 4. Assumptions and constraints

- Interest is simple, ACT/360, in both currencies.
- No transaction costs, bid-ask spreads, margin or credit charges. With more time I'd test whether these matter.
- One rate per currency, used for both borrowing and investing.
- The put premium is paid in USD on day one and isn't earned or financed to settlement. This flatters the put a little.
- The euro amount and the date are certain.
- If the market inputs are consistent, the forward and the money-market hedge should give about the same dollars. The scenario's forward may not be consistent with live rates, so section 7 treats that gap as something to report, not an error.

## 5. Calculation flow

**Forward.** USD proceeds are FC_AMT times F0_in.

**Money-market hedge.** Three steps, each on its own line:
1. Euro loan: divide FC_AMT by (1 + R_FC × T_DAYS/360). This sizes the loan so the receivable pays it off exactly.
2. Dollars today: multiply the loan by S0_in.
3. Dollars at settlement: multiply that by (1 + R_USD × T_DAYS/360).

**Implied forward.** S0_in × (1 + R_USD × T_DAYS/360) divided by (1 + R_FC × T_DAYS/360). Also show the gap between F0_in and this number, per euro and in USD on FC_AMT.

**Put.** For a settlement rate S_T, proceeds are FC_AMT times the larger of S_T and K_PUT, minus FC_AMT times PREM_PUT. The floor per euro is K_PUT minus PREM_PUT.

**Call.** A long call doesn't protect someone selling euros, so it's here for comparison. The version that fits a receivable is a short call: FC_AMT × S_T, minus FC_AMT times the larger of (S_T − K_CALL) and zero, plus FC_AMT × PREM_CALL. It brings in the premium and gives up gains above K_CALL.

**Unhedged.** FC_AMT times S_T.

**Put breakeven.** The settlement rate above which the put beats the money-market hedge is money-market proceeds divided by FC_AMT, plus PREM_PUT.

## 6. Sensitivity plan

S_T runs from (1 − SENS_RANGE) × S0_in to (1 + SENS_RANGE) × S0_in in steps of SENS_STEP, which at the current settings is 0.95 to 1.05 times S0_in in 1% steps, or 11 rows. For each row, show proceeds when unhedged, with the forward, with the money-market hedge, with the put, and with the short call. One line chart shows all five against S_T.

The chart should let the CFO see that the forward and money-market lines are flat, that the put is flat below the strike and rises above it but stays under by the premium, and that the unhedged line is the risk the others remove.

## 7. Validation rules

At the placeholder inputs, the finished workbook should show these figures, to the nearest dollar.

1. The euro loan × (1 + R_FC × T_DAYS/360) equals FC_AMT. PASS.
2. Money-market proceeds equal FC_AMT × the implied forward, within CHECK_TOL. The implied forward should be about 1.13267 and proceeds about 12,459,327. PASS.
3. Report F0_in minus the implied forward, in USD per EUR and in USD. Mark it FLAG if the gap is over GAP_FLAG × S0_in. At the placeholders this will show FLAG, about 0.0429 per euro or USD 472,000, because the teaching forward of 1.1756 doesn't match current rates. That's expected now and has to be explained or cleared at Stage 4.
4. Forward proceeds at the placeholder are 12,931,600.
5. When S_T equals S0_in, put proceeds are FC_AMT × (K_PUT − PREM_PUT) = 12,100,000. They never go below that in any row.
6. When S_T equals S0_in, unhedged proceeds are 12,346,400.
7. The sensitivity table has exactly 11 rows, the middle row has S_T equal to S0_in, and forward and money-market proceeds are the same in every row.
8. No error cells, every output is a formula, and the only typed numbers are on the Inputs tab.

## 8. Outputs

Each of these is a gray cell with this exact name:

- OUT_FWD_PROCEEDS: forward USD proceeds
- OUT_MM_BORROW, OUT_MM_USD_NOW, OUT_MM_PROCEEDS: the three money-market steps
- OUT_F_IMPLIED: the implied forward
- OUT_FWD_GAP_USD: the USD value of the gap between F0_in and the implied forward
- OUT_PUT_FLOOR_PER_EUR: K_PUT minus PREM_PUT
- OUT_PUT_PREM_USD: FC_AMT × PREM_PUT, which is 246,400
- OUT_BREAKEVEN: the settlement rate above which the put beats the money-market hedge, about 1.1551 at the placeholders
- The sensitivity table and its chart

Formats: dollar amounts in whole dollars with thousands commas, exchange rates to 4 decimals, interest rates as a percent to 3 decimals.

The Checks tab shows the status of every rule in section 7.
