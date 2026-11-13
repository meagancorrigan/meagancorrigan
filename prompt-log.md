# Prompt log
## 2026-10-02
- **Asked:** Draft AGENTS.md, CLAUDE.md, prompt-log.md, and .gitignore for my portfolio repo from the course setup prompt and my resume.
- **Produced:** The four root files. AGENTS.md was tailored to my field and the client and publication data I must never paste into a model.
- **What was wrong and how it was caught:** Re-read it and found nothing wrong.


## 2026-11-06
- **Asked:** Draft the Stage 2 model spec (spec.md) from my Stage 1 brief and my scenario, then review the draft and find gaps.
- **Produced:** A spec with all eight sections. The review found four gaps, and I accepted all four.
- **What was wrong and how it was caught:** The draft said only the Inputs tab holds typed numbers, but the checks had typed numbers in them (a 0.5% flag threshold and a USD 1 tolerance), and so did the sensitivity range. Before: "FLAG if the gap is over 0.5% of S0_in." After: "FLAG if the gap is over GAP_FLAG × S0_in," with GAP_FLAG, CHECK_TOL, SENS_STEP and SENS_RANGE added to the Inputs tab. The review also found that OUT_BREAKEVEN had no formula, the Checks tab said INFO where rule 3 said FLAG, and number formats were missing. I added a formula, fixed the label and added a formats line. I left chart styling for Stage 3.


## 2026-11-06: Stage 3 build, FX hedging workbook

**Tool:** Claude (web chat, chat-only track)

**What I asked:** Pointed Claude at my committed Stage 2 spec (capabilities/fx-hedging/spec.md) and asked it to build the workbook the spec describes, with all ten named ranges, formulas only, cover page, Legend/Key, all three hedge families, a ±5% sensitivity table with chart, and live validation checks. It was told to ask questions if the spec was ambiguous.

**What it got wrong or left open:**
- My spec's colour scheme (§3) conflicts with the course build contract; the workbook follows the course contract.
- Spec §7.7 ("forward and money-market proceeds are the same in every row") is ambiguous; it was read as "each is flat across rows."
- It added named cells not in my spec (DAY_BASIS, ST_TEST, REF_ figures) so no constants are typed inside formulas.

**How I checked it:** Name Manager for all ten names; clicked calculated cells to confirm formulas; changed S0_in to 1.20 and saw the sensitivity table move; reviewed the Checks tab (all PASS except the expected FLAG on the forward gap).

**Result:** model.xlsx committed to capabilities/fx-hedging/; audit note in analysis/. Spec fixes to follow as separate commits.


## 2026-11-13: Stage 4 market data

**Tool:** Claude (web chat)

**What I asked:** Find live inputs for EURUSD spot, USD and EUR rates near my 273-day tenor, compute the CIP-implied forward, and help me populate the workbook and write the memo.

**What needed checking:** I re-opened the ECB, Treasury and Euribor pages to confirm every value. Rates were interpolated to 273 days, and Treasury yields are bond-equivalent, not ACT/360. The scenario forward (1.1756) is far from the implied forward (1.1194), a gap of $618,195.

**How I checked it:** Workbook Checks tab (all PASS except the expected FLAG on the forward gap) and the FX Hedging Lab, which matched on all outputs and the full sensitivity grid.

**Result:** Workbook repopulated, memo committed to data/.



## 2026-11-13: Stage 5 validation and decision memo

- **Asked:** Run a fresh Claude agent (no history) with only my spec and my Stage 4 market-data memo, no coaching: "Using only those two documents, compute all the hedge outcomes the specification describes and recommend a strategy. Show your numbers, state any assumptions you had to make where the documents were unclear, and give a recommendation to the CFO." Then compare its output with my workbook, verify by hand, and draft the validation doc and decision memo.
- **Produced:** The raw LLM output, a comparison table, a hand-verification table, a spec retrospective, and a decision memo.
- **What was wrong and how it was caught:** The LLM matched my workbook to the dollar. What it caught was in my documents: my spec's check figures were from placeholder inputs and went stale, my Treasury yield was bond-equivalent and not ACT/360 (about USD 5,608 on money-market proceeds), the spec never said whether the 1.1756 forward is firm, and the given premiums don't fit put-call parity at live rates. I recomputed the forward, the three money-market steps, two put outcomes, the implied forward and the gap by hand and they matched. The agent was run inside my working session, not in a separate chat window.

