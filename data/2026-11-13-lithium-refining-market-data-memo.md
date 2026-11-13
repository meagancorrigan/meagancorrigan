# Market-Data Memo: Scenario 20 (Lithium-Refining)

Date: 2026-11-13. Data as of market close 2026-11-12.

| Input | Value | Source | Retrieved | Proxy / computation |
|---|---|---|---|---|
| S0_in | 1.1081 USD/EUR | ECB euro reference rate, ecb.europa.eu | 2026-11-13 (rate dated 2026-11-12) | None. Nov 13 fix not yet available. |
| R_USD | 4.535% | U.S. Treasury daily par yield curve, home.treasury.gov | 2026-11-13 (rates dated 2026-11-12) | Linear interpolation of 6M (4.42%) and 1Y (4.65%) to 273 days, to match the receivable's tenor. Bond-equivalent yield used as an approximation of ACT/360. |
| R_FC | 3.158% | Euribor 6M (3.023%) and 12M (3.293%), Suomen Pankki, 2026-11-12 | 2026-11-13 | Midpoint of 6M and 12M, because no 9M fixing was found. |
| F0_in | 1.1756 | Scenario's indicative forward (bank quote) | n/a | Kept as quoted. |
| PREM_PUT, PREM_CALL | 0.0224, 0.0376 | Scenario-given | n/a | Kept as an assumption: retail FX option quotes are unreliable. |
| K_PUT, K_CALL | 1.1081 | Set to S0_in per scenario | n/a | Follow S0_in by formula. |
| FC_AMT, T_DAYS | EUR 11,000,000; 273 | Scenario | n/a | None. |

## Rate choices
- R_USD and R_FC are interpolated to 273 days so they match the receivable's tenor, as in the spec.

## CIP-implied forward vs the quote
- Implied forward = 1.1081 x (1 + 4.535% x 273/360) / (1 + 3.158% x 273/360) = 1.1194.
- Quote 1.1756 minus implied 1.1194 = 0.0562 per EUR, or USD 618,195 on EUR 11,000,000 (5.1% of spot). The Checks tab shows FLAG.
- Forward is kept as quoted. The gap widened from USD 472,273 at placeholder inputs because spot fell while the quoted forward did not move. I have not asked for a requote.

## Structural fixes
- No formulas broke when live data loaded. The "Matches spec figure?" column on the Checks tab compares against the spec's placeholder-era figures, so it now shows No by design. This is not an error.

## Parity check and sensitivity
- Money-market proceeds equal FC_AMT x implied forward within tolerance (PASS). Sensitivity table recalculates around S0_in = 1.1081.

## FX Lab cross-check
- I entered my live inputs (S0_in 1.1081, F0_in 1.1756, R_USD 4.535%, R_FC 3.158%, T 273 days, strikes 1.1081, premiums 0.0224 and 0.0376) in the FX Hedging Lab. Forward ($12,931,600), the three money-market steps (EUR 10,742,731; $11,904,021; $12,313,405), the implied forward (1.1194) and the sensitivity grid matched my workbook. The lab's red parity banner flags the $618,195 gap between the quoted forward and the implied forward. Because the lab and my workbook use the same formulas and agree, I attribute the gap to the scenario quote being inconsistent with live rates, not to a formula error. Stage 5 will address what it means.

*Data hunt assisted by Claude (Anthropic, 2026); values verified by me against the cited sources.*
