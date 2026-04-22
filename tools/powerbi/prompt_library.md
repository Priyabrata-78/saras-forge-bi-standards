# Power BI — Prompt Library

Reusable, self-contained Claude prompts that produce interactive, responsive dashboard mock-ups for Power BI. Copy-paste the entire prompt block into Claude and you will get a working HTML + JS mock-up that previews the intended Power BI build. The mock-up is the visual / interaction reference; the production report is then built directly in Power BI Desktop, mirroring the mock-up's layout, visual types, DAX measures, and slicer behaviour.

---

## Prompt 1 — Rebill Tracker (Power BI-styled interactive mock-up)

### When to use

Building a Rebill Tracker report in Power BI for Homefield (or any Shopify + SKIO / subscription-commerce client). Produces a single-file HTML mock-up that matches Power BI's visual conventions (Power BI default palette, slicer styling, card / KPI tile styling, matrix visual), demonstrates the cross-filter / drill-through interactions the final report must support, and is self-contained so any stakeholder can open it in a browser.

### Copy this prompt into Claude

````
You are a BI prototyping assistant. Build me a fully interactive, single-file HTML + JavaScript mock-up of a "Rebill Tracker" report styled to match Microsoft Power BI. The mock-up will be used by consultants at Saras Analytics to preview a Power BI build for the Homefield subscription-commerce account before building it in Power BI Desktop.

DELIVERABLE
A single self-contained HTML file. Inline CSS and JS. Use a CDN for Chart.js or Recharts. No build step — save as rebill_tracker_powerbi.html and open in a browser.

BUSINESS CONTEXT (read carefully before building)
The Rebill Tracker tracks subscription retention and rebill rate for a DTC subscription brand. The report compares static cohorts (Control / Test / Bottle × 1-box / 2-box / 4-box × 1st-rebill / subsequent-rebill → up to 18 cohorts) on key retention metrics.

Critical business rules to reflect:
- Never blend SNS and OTP rebill rates — they are separate metrics.
- `cyclescompleted = 0` means the original order only. `has_rebilled = TRUE` requires `cyclescompleted >= 2`.
- Save events overlap with skip + pause events. Counts are NOT additive. Display a text-box caption under the Subscription Events visual.
- Cancel timing is capped at rebill — cancellations after the rebill timestamp do not count against the cancel rate.

REPORT STRUCTURE (mirror the final Power BI .pbix)
Four report pages, switchable via a Power BI-style page navigator (tab strip at the bottom of the canvas):
1. "Overview"
2. "Cancel Reasons"
3. "Subscription Detail"
4. "Snapshot History"

Plus a slicer rail on the left side of every page (Power BI slicers — list / dropdown / date-between style).

SLICER RAIL (left-side, visible on every page)
- Cohort (list slicer; multi-select; default "All")
- Test Arm (list slicer; Control / Test / Bottle)
- Acquisition Order Type (dropdown; All / SNS / OTP)
- Cadence (list; 30-day / 60-day / 90-day)
- Subscription Created Date (between-date slicer)

FIELD PARAMETER (Power BI "Field Parameters" simulation)
- "Trend Metric" — user-selectable from: Rebill Rate %, Active Rate %, Cancel Rate %, Pause Rate %. Simulated as a dropdown pinned above the Trend chart on the Overview page.

Every visual updates when slicer selections or the field parameter change.

PAGE 1 — OVERVIEW
- Power BI-style page header: page title "Overview" in bold, subtitle below in grey, logo placeholder on the right.
- An 8-card KPI row at the top. Each card styled as a Power BI "Card" or "Multi-row card" (thin border, light grey background, label on top in 9-10px uppercase, big value below in 28-32px with the Power BI typography). Cards:
  1. Total Subs
  2. Active (% of Total as secondary line)
  3. Cancelled (%)
  4. Paused (%)
  5. Failed (%)
  6. Under Review (%)
  7. Rebilled ≥1x (%)
  8. Avg Cycles
- A line chart "Trend Over Time" — x-axis = snapshot_date, y-axis = selected field-parameter metric, one line per cohort. Power BI default line styling (thin lines, thin grid, "Segoe UI" font family).
- A 100% stacked bar chart "Current Status by Cohort" (horizontal) with stacks Active / Cancelled / Paused / Failed.
- A clustered bar chart "Rebill Performance by Cohort" with two series: Rebill Rate % (primary axis), Avg Cycles (secondary axis). Use Power BI's combo chart style (columns + line).
- A clustered column chart "Subscription Events by Cohort" with three series: Skips, Pauses, Saves. Below the visual, a Power BI-style text box: "Save events overlap with Skip and Pause events (a 'saved' sub was saved FROM a skip or pause). These counts are not additive."
- A 14-column matrix visual "Cohort Detail": Cohort (row), then columns Total, Active, Active %, Cancelled, Cancel %, Paused, Pause %, Failed, Under Rev., Rebill %, Avg Cycles, Skips, Saves. Conditional data-bar formatting on the Rebill % column.

PAGE 2 — CANCEL REASONS
- Page header: "Cancel Reasons".
- A donut chart of 10 cancel reasons. Center label = total cancellations.
- A clustered bar chart "Reason Distribution" — y-axis reason, x-axis count, sorted descending.
- A matrix "By Cohort" — rows = cohort, columns = cancel reasons, values = count. Conditional heat-map on cell values.
- A slicer at the top of the page: "Cancel Reasons Cohort" (single-select: All Cohorts / individual cohort names) — narrows the donut and the "Reason Distribution" chart.

PAGE 3 — SUBSCRIPTION DETAIL
- Page header: "Subscription Detail".
- A slicer row at the top: Status (multi-select), Cadence (multi-select), a Power BI "Text Filter" simulation (free-text search on subscription_id / customer_id / cancellation_reason).
- A single large Power BI table visual (13 columns): Cohort, Sub ID, Status (coloured by status with a Power BI "Conditional formatting" icon rule), Cadence, Cycles, Rebilled? (✓ / —), Days Active, Days to Bill, Skips, Pauses, Saves, Cancel Reason, Last Action. Sortable column headers.
- A KPI tile above the table: "N of M subscriptions shown" — updates with filters.
- Drill-through placeholder: right-click a row, "See customer journey" (simulate with a floating button).

PAGE 4 — SNAPSHOT HISTORY
- Page header: "Snapshot History".
- A matrix: rows = snapshot_date (descending), columns = Cohorts, Total Subs, Active %, Cancel %, Rebill %. Conditional formatting on the rate columns.
- A line chart: same as the Overview trend, but for all metrics at once on a dual-axis layout.

VISUAL STYLE (match Power BI defaults)
- Canvas background: #FAFAFA
- Visual background: #FFFFFF with 1px #E1E1E1 border
- Power BI default palette (current 2025 palette):
  - #118DFF (blue — primary)
  - #12239E (deep blue)
  - #E66C37 (orange)
  - #6B007B (purple)
  - #E044A7 (pink)
  - #744EC2 (violet)
  - #D9B300 (yellow)
  - #D64550 (red)
  - #197278 (teal)
  - #1AAB40 (green)
- KPI card: solid white background, 1px light grey border, 4px radius, small label in uppercase grey (#605E5C), big value in 28px Segoe UI Semibold black.
- Matrix: thin rule lines, slightly smaller font (11px), hover row highlight #F3F2F1.
- Slicers on the rail: white background, rounded 2px, 12px label, list items with a left-border accent on selected.
- Fonts: "Segoe UI" primary, fallback "Inter", fallback sans-serif. Import Segoe UI substitute from Google Fonts if not available.
- Page navigator at the bottom: tab strip with 4 tabs, selected tab has an underline in the primary blue.

SAMPLE DATA
Same pattern as the Luzmo and Tableau mock-ups for easy comparison:
- 9 cohorts (1-box / 2-box / 4-box × Control / Test / Bottle for Rebill 1)
- total_subs 800–5,000 per cohort
- Expected directional pattern: Control > Bottle > Test on rebill rate
- Cancel reason distributions with "Prefer One-Time" and "Too Expensive" largest
- Snapshot History: 5 snapshots over 2 weeks
- Subscription Detail: ~300 rows across the 9 cohorts, realistic status distribution

INTERACTIVITY CHECKLIST
- [ ] All four pages re-render when any slicer changes.
- [ ] Cross-filter: clicking a bar on the "Rebill Performance" chart filters the other visuals on the Overview page.
- [ ] Cross-highlight: clicking a slice on the donut on Cancel Reasons page highlights corresponding cells in the "By Cohort" matrix.
- [ ] The Trend Metric field parameter dropdown swaps the Trend Over Time chart's y-axis.
- [ ] The Subscription Detail search box filters the table in real time.
- [ ] The Status and Cadence slicers on the Subscription Detail page filter the table.
- [ ] The page navigator at the bottom switches between the four pages.
- [ ] Column headers in the matrix and table visuals sort on click.
- [ ] Right-click on a Subscription Detail row shows the drill-through placeholder.

OUTPUT REQUIREMENTS
- Return the complete HTML file only — no narrative, no explanation.
- <html>...</html>.
- Chart.js 4.x from cdnjs.cloudflare.com or pure SVG — keep file small.
- Do NOT use localStorage or sessionStorage. State in JS variables.
- Keep file under ~1,500 lines.

Thank you.
````

### Production-build steps after mock-up approval

1. Import the two BigQuery views into Power BI Desktop: `vw_rebill_tracker_cohort_summary` (Import mode) and `vw_rebill_tracker_subscription_detail` (Import mode). Plus `vw_rebill_tracker_snapshot_history` (Import, with scheduled refresh).
2. Build the semantic model: mark the snapshot-history table's `snapshot_date` as a date column, create a dedicated Date dimension, relate `cohort_summary` to `snapshot_history` on `cohort_name + snapshot_date`.
3. Create a Field Parameter "Trend Metric" listing the four rate columns: rebill_rate_pct, active_rate_pct, cancel_rate_pct, pause_rate_pct. Assign it to the y-axis of the Trend Over Time visual.
4. Create DAX measures:
   - `Total Subs := SUM(cohort_summary[total_subs])`
   - `Active % := DIVIDE(SUM(cohort_summary[active]), [Total Subs])`
   - `Cancel % := DIVIDE(SUM(cohort_summary[cancelled]), [Total Subs])`
   - `Rebill % := DIVIDE(SUM(cohort_summary[rebilled_at_least_once]), [Total Subs])`
   - `Avg Cycles := AVERAGE(cohort_summary[avg_cycles])`
   - Sub-measures for Paused, Failed, Under Review.
5. Build the 4 report pages matching the mock-up. Add the slicer rail on each page; enable "Sync Slicers" across all 4 pages.
6. Configure cross-filter / cross-highlight per visual interaction rule.
7. Add the Save Events caption as a Text Box below the Subscription Events visual.
8. Apply conditional data bars on the Rebill % column of the Cohort Detail matrix.
9. Publish to Power BI Service → Homefield workspace. Record the URL in `accounts/homefield/DELIVERY_NOTES.md`.
10. Set up scheduled refresh (hourly or every-6-hours depending on freshness needs) via the Power BI gateway.
11. Walk through the interactivity checklist on the published report.

---

## Prompt 2 — Power BI DAX measure reference for Rebill Tracker

### When to use

You have the two views in Power BI Desktop and need the DAX boilerplate for all Rebill Tracker KPIs plus the field-parameter-driven trend metric.

### Copy this prompt into Claude

````
I'm building a Rebill Tracker report in Power BI Desktop. The data model has:
- `cohort_summary` (from vw_rebill_tracker_cohort_summary) — one row per cohort_name
- `subscription_detail` (from vw_rebill_tracker_subscription_detail) — one row per subscription_id
- `snapshot_history` (from vw_rebill_tracker_snapshot_history) — one row per cohort_name × snapshot_date

Generate DAX measures for each of the following, with a short comment above each explaining what it does:

1. `Total Subs`
2. `Active`
3. `Active %` (as a percentage, formatted "0.0%")
4. `Cancelled`
5. `Cancel %`
6. `Paused`, `Pause %`
7. `Failed`, `Failed %`
8. `Rebilled ≥1x`
9. `Rebill %`
10. `Avg Cycles`
11. `Total Skip Events`
12. `Total Pause Events`
13. `Total Save Events`
14. A calculated column on `subscription_detail` called `Has Rebilled?` returning TRUE / FALSE from `cyclescompleted >= 2`.
15. A measure `Subscriptions Shown` that counts rows in the filtered `subscription_detail` table (drives the "N of M" KPI tile).
16. A DAX expression for a Field Parameter that lists Rebill Rate %, Active Rate %, Cancel Rate %, Pause Rate % by name, so the user can select which measure drives the Trend Over Time chart.

Assume the VertiPaq column types match the business context (numeric columns are Int64 / Decimal, cohort_name is Text, cyclescompleted is Int64). Use SUM / DIVIDE / AVERAGE as appropriate — never raw division (to avoid divide-by-zero).

Return each measure as a DAX code block with the comment above it. No surrounding narrative.
````

---

*See also: `tools/powerbi/capabilities.md`, `tools/powerbi/limitations.md`, and `accounts/homefield/rebill_tracker_business_context.md`.*
