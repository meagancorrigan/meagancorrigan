# Prompt log
## 2026-10-02
- **Asked:** Draft AGENTS.md, CLAUDE.md, prompt-log.md, and .gitignore for my portfolio repo from the course setup prompt and my resume.
- **Produced:** The four root files. AGENTS.md was tailored to my field and the client and publication data I must never paste into a model.
- **What was wrong and how it was caught:** Re-read it and found nothing wrong.


## 2026-11-06
- **Asked:** Draft the Stage 2 model spec (spec.md) from my Stage 1 brief and my scenario, then review the draft and find gaps.
- **Produced:** A spec with all eight sections. The review found four gaps, and I accepted all four.
- **What was wrong and how it was caught:** The draft said only the Inputs tab holds typed numbers, but the checks had typed numbers in them (a 0.5% flag threshold and a USD 1 tolerance), and so did the sensitivity range. Before: "FLAG if the gap is over 0.5% of S0_in." After: "FLAG if the gap is over GAP_FLAG × S0_in," with GAP_FLAG, CHECK_TOL, SENS_STEP and SENS_RANGE added to the Inputs tab. The review also found that OUT_BREAKEVEN had no formula, the Checks tab said INFO where rule 3 said FLAG, and number formats were missing. I added a formula, fixed the label and added a formats line. I left chart styling for Stage 3.
