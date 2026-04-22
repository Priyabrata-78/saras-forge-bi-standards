# Luzmo — Limitations

Known constraints and gotchas. Read this before scoping a Forge build in Luzmo.

---

## 1. Chart and visual limitations

- **No native Sankey with >2 node levels.** Luzmo's Sankey supports a two-level flow (source → target); multi-hop flows (e.g. acquired → active → cancelled → reactivated) require a custom Vega-Lite chart.
- **Limited annotation support on line charts.** Static reference lines and band annotations exist, but there is no native "callout" or "story point" annotation like Tableau offers.
- **No native small-multiples grid.** To show one chart per cohort or one chart per test arm, you either duplicate the widget manually or use a custom Vega-Lite chart.
- **Heatmap labels can overflow.** On dense cohort × month_diff heatmaps (e.g. 24 months × 12 cohorts), the numeric labels render small and sometimes get truncated. Mitigation: use a conditional color scale and show numbers only on hover.
- **Legend ordering is alphabetical by default.** To force a specific order (e.g. Control, Test, Bottle), you often need to prefix labels with `1_`, `2_`, `3_` or use a calculated column with an explicit sort key.
- **No scroll-inside-a-widget.** A table taller than the widget gets a scroll, but a column chart with 50 bars cannot be scrolled horizontally — it just compresses. Mitigation: paginate via a filter.

---

## 2. Filter and parameter limitations

- **Filters cascade strictly by definition order.** A child filter does not automatically reflect the parent's selected values unless explicitly configured to do so via a linked hierarchy. This is easy to miss during setup.
- **Multi-select filters have no "Select all except X" affordance.** The user can click Select All and then deselect individual items, but there is no inverse-select.
- **Date range filter granularity is dataset-defined.** If the underlying column is a DATETIME, you cannot filter to "only April 2026 mornings". Pre-aggregate to the desired grain in the data layer.
- **Parameters are global to a dashboard, not per-widget.** A parameter set on the Overview tab affects any widget on the Cancel Reasons tab that references the same parameter. Sometimes this is desired; often it isn't.
- **No cross-dashboard parameters.** If two dashboards share a filter (e.g. "selected cohort"), the selection does not persist across navigation by default — use drill-through with an explicit parameter pass.

---

## 3. Calculation and formula limitations

- **Calculated measures cannot reference other calculated measures in all contexts.** Some measure-of-a-measure expressions break at render time. Mitigation: flatten into a single expression or pre-calculate in the data layer.
- **Window functions (LAG, LEAD, rank-over-partition) are limited.** Luzmo supports `rank` and running totals, but arbitrary SQL window functions should be expressed at the SQL / view layer before the data hits Luzmo.
- **No native percentile (P50 / P95) in the formula editor.** Work around by pre-computing at the SQL layer.
- **String manipulation is basic.** `CONCAT`, `SUBSTRING`, `LOWER`, `UPPER`, `TRIM` exist; regex is not supported in calculated columns. For regex-based classification (e.g. cancel reason bucketing), do it in the source table.

---

## 4. Data volume and performance

- **Row limit per dataset.** Luzmo can host large datasets but the sweet spot is < 50M rows per dataset for sub-second render. Above that, enforce aggressive pre-aggregation or rely on a live connection with pushdown.
- **Live-query latency depends on the source.** A live BigQuery connection inherits BigQuery slot scheduling — typical 2–8s per render. For interactive dashboards, prefer Luzmo's cached mode with a short TTL.
- **Cross-dataset joins at query time can be slow.** When a widget mixes columns from two datasets, Luzmo joins in-memory. For large joins, pre-join at the source.
- **Export formats are capped.** CSV export is capped at ~100K rows per widget. For full extracts, point the user to the source table or build a dedicated export endpoint via the Luzmo API.

---

## 5. Embedding and auth

- **JWT embed tokens have a TTL.** Tokens expire (default 1 hour). The embedding application must refresh; there is no silent auto-refresh built into the SDK.
- **Row-level security parameters must be passed in the JWT.** Forgetting to pass them results in the dashboard showing all data, which is the wrong default for multi-tenant embeds. Always validate in staging.
- **Deep linking to a specific tab + filter state is limited.** You can pass parameters via the URL / SDK but tab-state is not part of the URL by default.

---

## 6. Collaboration and governance

- **Comments and annotations are limited.** There is no robust in-dashboard comment thread like Tableau Cloud's. Users typically comment in Slack / a ticketing system out-of-band.
- **No native approval workflow for dashboard changes.** Changes ship immediately. Implement version freeze via the folder structure (e.g. `/_published/` and `/_draft/`).
- **Dataset documentation is limited.** Column descriptions exist but there is no rich markdown description per dataset.

---

## 7. Specific Rebill-Tracker-context limitations

- **Daily snapshot history requires pre-computation.** The JSX implementation stores snapshot history in browser localStorage. In Luzmo, snapshot history must be materialised as a table where each row is tagged with a `snapshot_date`; then the "Trend Over Time" chart filters to the selected metric and plots one line per cohort across `snapshot_date`.
- **Save-event overlap needs a footnote.** Luzmo has no "this metric overlaps with these other metrics" annotation. Add the overlap warning as a widget-level tooltip or markdown card.
- **Cancel reason classification must happen upstream.** Luzmo does not support regex; the 10-bucket cancel-reason taxonomy must be applied in the SQL view that feeds the dashboard.
- **14-column cohort table will look crowded.** Luzmo tables render fine with 14 columns but require horizontal scroll on mobile. For embedded Homefield customer views, consider hiding 4–5 of the less-essential columns (e.g. Paused count when Pause % is already shown).

---

## 8. When Luzmo is the wrong choice

- **Pixel-perfect static reporting.** For PDF-first reports with precise typography and multi-page layouts, use a dedicated reporting tool (e.g. Google Slides / docx pipeline).
- **Heavy statistical workload.** For tasks like bootstrap confidence intervals or regression with many predictors, pre-compute in Python / R and feed the results into Luzmo as a reference table.
- **Very large result sets surfaced as row-level.** A 10M-row flat table served as-is will time out; always pre-aggregate.
- **Highly custom Excel-style pivots.** Luzmo's pivot table is good, but Excel power users sometimes want features (custom group collapse, VBA-style macros) that Luzmo does not have.

---

*See also: `tools/luzmo/capabilities.md` for what Luzmo can do, and `tools/luzmo/prompt_library.md` for the Rebill Tracker build prompt.*
