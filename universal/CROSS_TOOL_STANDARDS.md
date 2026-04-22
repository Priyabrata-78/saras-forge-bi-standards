# Cross-Tool Build Standards

Universal rules that apply regardless of which BI tool is being used.

> **Stub — to be filled in.**

## Rules (v1 draft)

- All metrics must match AIDE's `3_KPI_FORMULAS.md`; no BI-layer-only calculations.
- All data connections point to production BQ datasets; never raw or staging layers.
- Every dashboard includes a data freshness indicator visible to the end user.
- Calculation translation protocol: BQ formula → Forge prompt → BI-layer formula → cross-validation before publishing.
- **AIDE ↔ Forge dependency rule:** No dashboard metric exists in a BI tool without a prior AIDE entry. If a metric is missing from AIDE, add it there first, then build in the BI tool.
