# Tableau — Limitations

Known constraints and gotchas. Read before scoping a Forge build in Tableau.

---

## 1. Chart and visual limitations

- **No true responsive design.** Tableau dashboards are fixed-size by default. "Automatic" sizing scales proportionally but does not re-flow. For true responsiveness (tablet, phone), build separate device-specific layouts — one workbook, three layouts.
- **Custom marks beyond native types require workarounds.** Chart types like Sankey (before the native release), chord diagrams, or sunburst charts historically required dual-axis polygon tricks or an embed from an external tool.
- **Small-multiples are Trellis-style only.** True trellis (one chart per value of a dimension) works well, but mixing different chart types within the trellis is not natively supported.
- **Container layout can be fragile.** Nested horizontal / vertical containers are powerful but fiddly — dragging a single widget into the wrong container level breaks the layout. Name your containers before you populate them.
- **Labels on pie / donut slices truncate.** Long category labels on a donut chart get clipped. Mitigation: use a legend + center-label pattern.

---

## 2. Filter and parameter limitations

- **Parameters are single-value.** A parameter holds one value (number, string, date, etc.) at a time. There is no native multi-value parameter. Workaround: use a set or a dynamic parameter driven by a parameter action.
- **Filter cascading (dependent filters) requires explicit "Only Relevant Values" setting per filter.** Forgetting to set this leaves filters showing stale options.
- **Filter actions do not carry over to a new dashboard by default.** Use a URL action or parameter action to persist state across dashboards.
- **Wildcard filters on large text fields are slow.** A wildcard on a 10M-row `cancellation_reason` column can hit multi-second render. Pre-bucket into a classification column.

---

## 3. Calculation and formula limitations

- **LOD expressions can be slow on large datasets.** `{ FIXED ... }` forces a sub-query. On 100M+ rows, plan for careful partitioning.
- **Table Calculations are display-time.** They cannot be used in filters directly — wrap in a calculated field that references a parameter, or use a Table Calc filter which Tableau evaluates after everything else.
- **LODs + Table Calcs + filters interact in non-obvious ways.** The evaluation order (extract filter → data source filter → context filter → dimension filter → FIXED LOD → INCLUDE/EXCLUDE LOD → measure filter → table calc filter) trips up even experienced Tableau users.
- **Regex support is limited.** `REGEXP_REPLACE`, `REGEXP_MATCH`, `REGEXP_EXTRACT` exist but use PCRE syntax that can behave inconsistently across data source types (live BigQuery vs extract). Test on the production data source.
- **String functions on live BigQuery are pushed down** — which can cause differences vs extract mode for the same calc. Explicitly test calcs on the final data source.

---

## 4. Data volume and performance

- **Extract file size can be large.** A 100M-row extract with 80 columns easily exceeds 10GB. Manageable, but slow to refresh.
- **Live queries depend on the source.** A dashboard with 12 worksheets hitting a slow BigQuery slot pool can render in 10+ seconds. Mitigations: extract, aggregate, or pre-join.
- **Dashboard with too many worksheets renders slowly.** >20 worksheets on a single dashboard is a performance smell. Split into multiple dashboards.
- **Cross-database joins** happen in Tableau's engine (not the source) — pushing the join into the source is usually faster.

---

## 5. Embedding and auth

- **Trusted authentication setup** requires a whitelist of embedding-app IP addresses. Managed Cloud simplifies this; on-prem Server is more involved.
- **Row-level security** requires a `User Filters` calc that references `USERNAME()` or a join to an entitlements table. Entitlements table joins can slow the query.
- **Tableau Mobile** renders most but not all chart types perfectly — test before promising mobile support.

---

## 6. Collaboration and governance

- **Commenting is basic.** Tableau Cloud has per-view comments, but there is no robust threaded discussion.
- **No native approval workflow** — rely on project permissions (e.g. "Developer → Sandbox → Published").
- **Workbook diff is limited.** Version history retains the binary `.twb` / `.twbx`; diffing between versions requires an external tool.
- **Dashboard deep-linking** — passing filter state via URL works but the URL is long and brittle to copy-paste.

---

## 7. Specific Rebill-Tracker-context limitations

- **Snapshot history requires a dedicated table.** Tableau can compute running-trend metrics via Table Calculations on a single snapshot, but a genuine "snapshot per day" trend chart needs a materialised snapshot-history table. Same constraint as Luzmo and Power BI.
- **The 14-column cohort table is fine in Tableau but doesn't paginate natively.** For >50 rows, either enable scroll-in-place or use a parameter-driven pagination calc.
- **Cancel reason bucketing via REGEXP is possible but slow on live BigQuery.** Pre-compute the bucketing column in the source view.
- **Save-events overlap footnote.** Tableau supports rich tooltip annotations and caption text, so the footnote is straightforward — but easy to forget. Include it on the Overview dashboard below the events chart.

---

## 8. When Tableau is the wrong choice

- **Customer-facing embedded analytics for many tenants.** Tableau is licensable per-viewer or per-core; at scale for many external tenants, Luzmo or Power BI Embedded is cheaper.
- **Real-time dashboards (sub-minute refresh).** Tableau can do live queries but is not optimised for sub-minute render loops. For that use case, consider a bespoke solution.
- **Very bespoke pixel-perfect PDF reports.** Tableau's PDF export is good but not a replacement for a dedicated reporting tool when pixel perfection is required.

---

*See also: `tools/tableau/capabilities.md` for what Tableau can do, and `tools/tableau/prompt_library.md` for the Rebill Tracker build prompt.*
