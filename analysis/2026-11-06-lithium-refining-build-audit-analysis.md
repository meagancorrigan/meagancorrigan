# FX Hedging: Build Audit (Scenario 20, Lithium-Refining)

Date: 2026-11-06
Workbook: capabilities/fx-hedging/model.xlsx, built from capabilities/fx-hedging/spec.md

## Findings

### 1. Named ranges (confirmed)
- **Checked:** Formulas > Name Manager, against the ten names in spec section 2.
- **Found:** All ten names exist and point to the correct cells on the Inputs tab (e.g. FC_AMT = Inputs!B5, S0_in = B7, PREM_CALL = B14). Extra helper names are also present (CHECK_TOL, DAY_BASIS, SENS_STEP, ST_TEST, REF_ figures) and the OUT_ outputs.
- **Did:** Nothing needed.

### 2. Formulas, not typed values (confirmed)
- **Checked:** The forward proceeds cell (OUT_FWD_PROCEEDS) on the Forward tab.
- **Found:** It holds the formula =FC_AMT*F0_in and returns 12,931,600, matching spec section 7.4. It is a formula built from named ranges, not a pasted number.
- **Did:** Nothing needed.

### 3. Sensitivity table recalculates (confirmed)
- **Checked:** Changed S0_in from 1.1224 to 1.20 on the Inputs tab.
- **Found:** The S_T column on the Sensitivity tab moved from about 1.0663 to 1.1785 to about 1.14 to 1.26. The table is formula-driven.
- **Did:** Restored S0_in to 1.1224 and did not save the change.

### 4. Spec conflict on colour scheme (spec defect)
- **Checked:** Compared spec section 3 with the course build contract.
- **Found:** The spec describes blue text for inputs, black for formulas, gray fill for outputs and red for failed checks. The course contract says Yellow inputs, Blue assumptions, Green formulas, Gray outputs. The workbook follows the course contract.
- **Did:** Next step is to update spec section 3 to match the contract, commit it, and regenerate.

### 5. Ambiguous wording in spec section 7.7 (spec defect)
- **Checked:** Spec 7.7 says forward and money-market proceeds are "the same in every row."
- **Found:** The forward ($12,931,600) and money-market ($12,459,327) proceeds differ from each other, because the scenario forward is not consistent with the parity-implied forward. The workbook reads the line as "each is constant across rows" (flat).
- **Did:** Next step is to reword 7.7 in the spec.

### 6. Forward gap flag (expected)
- **Checked:** Checks tab, row 3a.
- **Found:** The gap between F0_in and the implied forward is about 0.0429 per EUR, or $472,273, which is flagged. The spec says "about USD 472,000", which is a rounded figure. The FLAG is expected until Stage 4 replaces the placeholder inputs.
- **Did:** Left as FLAG; to be explained or cleared at Stage 4.

- ### 7. Check numbers stored as text (cosmetic)
- **Checked:** Scanned the Checks tab for formatting or error indicators.
- **Found:** Excel flags the check numbers in column A as numbers stored as text (green triangles). They are labels only and no formula uses them, so no result is affected.
- **Did:** Left as is; can be fixed by making the labels text like "1a" or numbers when the spec is regenerated.

*Reviewed with help from Claude (Anthropic, 2026); audit steps performed and findings checked by me.*
