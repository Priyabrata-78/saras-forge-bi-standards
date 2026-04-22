# Calculation Translation Protocol

BQ formula → Forge prompt → BI-layer formula → cross-validation. The canonical path every metric takes on its way from AIDE to a client-facing dashboard.

> **Stub — to be filled in.**

## The 4-step protocol

1. **BQ formula** — look up the formula in AIDE's `3_KPI_FORMULAS.md` for the metric you're building.
2. **Forge prompt** — find the validated prompt in the relevant tool's `PROMPT_LIBRARY.md`.
3. **BI-layer formula** — generate the tool-specific formula using the prompt.
4. **Cross-validation** — run both the BQ formula and the BI-layer formula against the same data; confirm outputs match before publishing.
