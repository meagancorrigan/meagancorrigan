# Validation: Scenario 20 (Lithium-Refining)

Date: 2026-11-13. Inputs are the Stage 4 live values from `data/2026-11-13-lithium-refining-market-data-memo.md`.

## 1. Independent LLM run

I gave a fresh Claude agent with no history exactly two documents, `capabilities/fx-hedging/spec.md` and the Stage 4 market-data memo, as they stood on main. I did not correct it or show it my workbook. The agent was run from inside my working session as a separate sub-agent, not in a new chat window.

Prompt, logged in `prompt-log.md`: "Using only those two documents, compute all the hedge outcomes the specification describes and recommend a strategy. Show your numbers, state any assumptions you had to make where the documents were unclear, and give a recommendation to the CFO."

The raw output is saved unedited in `analysis/2026-11-13-lithium-refining-llm-raw-output.md`.

## 2. Comparison: LLM vs workbook

The workbook figures come from recalculating the committed `model.xlsx` at the live inputs.

| Item | LLM | Workbook | Difference |
|---|---|---|---|
| Forward proceeds | 12,931,600 | 12,931,600 | 0 |
| MM step 1, euro loan | EUR 10,742,731 | EUR 10,742,731 | 0 |
| MM step 2, dollars today | 11,904,021 | 11,904,021 | 0 |
| MM step 3, proceeds | 12,313,405 | 12,313,405 | 0 |
| Implied forward | 1.1194 | 1.1194 | 0 |
| Gap in USD | 618,195 | 618,195 | 0 |
| Put breakeven vs MM | 1.1418 | 1.1418 | 0 |

At three settlement rates:

| S_T | Strategy | LLM | Workbook | Difference |
|---|---|---|---|---|
| 1.0527 | Unhedged | 11,579,645 | 11,579,645 | 0 |
| 1.0527 | Forward | 12,931,600 | 12,931,600 | 0 |
| 1.0527 | Money-market | 12,313,405 | 12,313,405 | 0 |
| 1.0527 | Put | 11,942,700 | 11,942,700 | 0 |
| 1.0527 | Short call | 11,993,245 | 11,993,245 | 0 |
| 1.1081 | Unhedged | 12,189,100 | 12,189,100 | 0 |
| 1.1081 | Put | 11,942,700 | 11,942,700 | 0 |
| 1.1081 | Short call | 12,602,700 | 12,602,700 | 0 |
| 1.1635 | Unhedged | 12,798,555 | 12,798,555 | 0 |
| 1.1635 | Put | 12,552,155 | 12,552,155 | 0 |
| 1.1635 | Short call | 12,602,700 | 12,602,700 | 0 |

Forward and money-market proceeds are the same at every S_T, as the spec says. The LLM also produced a long-call column (net payoff of -413,600 up to 195,855 across the grid). My workbook has the long call only as a premium cost, not as a column in the sensitivity table, so there is nothing to compare it against.

### Discrepancies and what caused them

1. **Spec check figures vs live results.** The Checks tab column "Matches spec figure?" shows No for the money-market proceeds, implied forward, gap, put floor, unhedged and breakeven rows. Cause: spec ambiguity, not a workbook or LLM error. Section 7 of the spec quotes figures computed at the Nov 5 placeholder inputs (for example 12,100,000 put floor and 1.13267 implied forward), and it never says they stop applying once Stage 4 loads live data. The LLM reached the same conclusion and relied on the parity checks, which hold at any inputs.
2. **Day-count basis of R_USD.** The LLM noticed that Treasury par yields are bond-equivalent yields (ACT/365 basis) and my memo used the interpolated figure in an ACT/360 formula. Converting by 360/365 gives 4.473% instead of 4.535% and lowers money-market proceeds by about USD 5,608. Cause: the memo's "approximation" note was honest, but the spec says all rates are ACT/360 and gives no conversion rule. It changes no ranking.
3. **Put-call parity of the given premiums.** The LLM pointed out that at live rates, put-call parity implies a call minus put premium of about 0.0109, while the scenario quotes give 0.0152. Cause: the scenario's premiums are teaching placeholders and don't fit live rates. The spec says to keep them but never says to check them for consistency.
4. **Memo wording.** The memo says both rates were "interpolated to 273 days," but R_FC is a midpoint of 6M and 12M Euribor. The value is right, the sentence isn't.
5. **Quote status.** The LLM assumed the 1.1756 forward is firm for the full EUR 11,000,000. Neither document says whether it is. This is a spec gap.

No numeric difference between the LLM and the workbook is larger than USD 1.

## 3. Hand verification

Inputs: FC_AMT = 11,000,000, F0_in = 1.1756, S0_in = 1.1081, R_USD = 4.535%, R_FC = 3.158%, T_DAYS = 273, K_PUT = 1.1081, PREM_PUT = 0.0224. Calculator only, no Excel.

| # | Outcome | Arithmetic | Result | Workbook |
|---|---|---|---|---|
| 1 | Forward proceeds | 11,000,000 × 1.1756 | USD 12,931,600 | 12,931,600 |
| 2 | MM step 1, euro loan | T_DAYS/360 = 273/360 = 0.758333. R_FC × 0.758333 = 0.023948. 11,000,000 / 1.023948 | EUR 10,742,731 | 10,742,731 |
| 2 | MM step 2, dollars today | 10,742,731 × 1.1081 | USD 11,904,021 | 11,904,021 |
| 2 | MM step 3, proceeds | R_USD × 0.758333 = 0.034390. 11,904,021 × 1.034390 | USD 12,313,405 | 12,313,405 |
| 3 | Put at S_T = 1.1000 | S_T is below K_PUT, so use K_PUT. 11,000,000 × 1.1081 − 11,000,000 × 0.0224 = 12,189,100 − 246,400 | USD 11,942,700 | 11,942,700 (floor) |
| 4 | Put at S_T = 1.2000 | 11,000,000 × 1.2000 − 246,400 = 13,200,000 − 246,400 | USD 12,953,600 | n/a, outside the ±5% grid |
| 5 | Implied forward | 1.1081 × 1.034390 / 1.023948 | 1.11940 | 1.11940 |
| 6 | Gap in USD | (1.1756 − 1.11940) × 11,000,000 | USD 618,195 | 618,195 |

Row 4 checks something the grid can't show: the put beats the forward only above 1.1756 + 0.0224 = 1.1980 (1.1988 with the premium carried to settlement), and 1.2000 is just past that. The workbook's grid tops out at 1.1635, so the forward wins everywhere in it.

All hand results match the workbook to the dollar.

## 4. Spec retrospective

What the LLM guessed or had to work around, and what v2 of the spec would say:

1. **Stale check figures.** I wrote the section 7 figures at placeholder inputs and left them in after Stage 4. The LLM had to work out for itself which figures still applied. In v2 I would state the checks as relationships that hold at any inputs (money-market proceeds equal FC_AMT × implied forward) and label every typed dollar figure "placeholder inputs only."
2. **Day-count basis.** My spec says every rate is ACT/360 but my inputs table doesn't repeat the basis for how each rate is sourced, and the memo used bond-equivalent Treasury yields. The LLM caught a USD 5,608 effect I had written off as an approximation. In v2 each rate row would say what basis the source quotes in and how to convert it.
3. **The forward's status.** I never said whether 1.1756 is firm or indicative, or for what size. The LLM assumed firm and built its recommendation on a condition. In v2 §2 would say what a bank confirmation looks like.
4. **Option premium consistency.** The spec keeps the given premiums but doesn't test them. The LLM found they break put-call parity at live rates. In v2 I would add an informational check on the call minus put premium against the parity value.
5. **Rate tenor.** The spec asks for 9-month rates, and no 9-month fixings were available. The spec doesn't say how to proxy them, so the memo chose and the LLM had to read the memo to find out. In v2 the inputs table would give the proxy rule.
6. **What went well.** Every figure matched the workbook to the dollar. The named-range formulas, the three-step money-market hedge and the ±5% grid rules were enough for a reader with no context to rebuild the numbers without a single question about the formulas.
