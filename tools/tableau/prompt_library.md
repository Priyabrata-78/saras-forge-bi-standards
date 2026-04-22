# Tableau — Prompt Library

Reusable, self-contained Claude prompts that produce interactive, responsive dashboard mock-ups for Tableau. Copy-paste the entire prompt block into Claude and you will get a working HTML + JS mock-up that previews the intended Tableau build. The mock-up is the visual / interaction reference; the production dashboard is then built directly in Tableau Desktop / Cloud, mirroring the mock-up layout, worksheet structure, calculations, and filter behaviour.

---

## Prompt 1 — Rebill Tracker (Tableau-styled interactive mock-up)

### When to use

Building a Rebill Tracker dashboard in Tableau for Homefield (or any Shopify + SKIO / subscription-commerce client). Produces a single-file HTML mock-up that matches Tableau's visual conventions (Tableau 10 palette, Tableau-like title banners, action-driven filter behaviour), demonstrates every filter / highlight / drill action the final workbook must support, and is self-contained so any stakeholder can open it in a browser.

### Copy this prompt into Claude

````
You are a BI prototyping assistant. Build me a fully interactive, single-file HTML + JavaScript mock-up of a "Rebill Tracker" dashboard styled to match Tableau. The mock-up will be used by consultants at Saras Analytics to preview a Tableau build for the Homefield subscription-commerce account before building it in Tableau Desktop.

DELIVERABLE
A single self-contained HTML file. Inline CSS and JS. Use a CDN for Chart.js or Recharts. No build step — save as rebill_tracker_tableau.html, open in a browser, it works.

BUSINESS CONTEXT (read carefully before building)
The Rebill Tracker tracks subscription retention and rebill rate for a DTC subscription brand. The dashboard compares static cohorts (Control / Test / Bottle × 1-box / 2-box / 4-box × 1st-rebill / subsequent-rebill → up to 18 cohorts) on key retention metrics.

Critical business rules to reflect:
- Never blend SNS and OTP rebill rates — they are separate metrics.
- `cyclescompleted = 0` means the original order only. `has_rebilled = TRUE` requires `cyclescompleted >= 2`.
- Save events overlap with skip + pause events. Counts are NOT additive. Display a footnote below the Subscription Events chart.
- Cancel timing is capped at rebill — cancellations after the rebill timestamp do not count against the cancel rate.

DASHBOARD STRUCTURE (mirror the final Tableau workbook)
Three dashboards, switchable via a navigation bar at the top:
1. "Overview"
2. "Cancel Reasons"
3. "Subscription Detail"

Plus a fourth section for parameter + filter controls (a "Filters" side panel always visible on the left — this simulates Tableau's left-panel filter shelf).

FILTER SIDE PANEL (mirrors Tableau's typical left-rail filter shelf)
- Cohort (multi-select dropdown; default "All")
- Test Arm (multi-select; Control / Test / Bottle; default "All")
- Acquisition Order Type (single-select; All / SNS / OTP)
- Cadence (multi-select; 30-day / 60-day / 90-day)
- Subscription Created Date Range (date range picker)
- Trend Metric (parameter; single-select; options: Rebill Rate %, Active Rate %, Cancel Rate %, Pause Rate %) — drives the Trend Over Time chart
- Cancel Reasons Cohort (parameter; single-select; "All" or a specific cohort) — drives the Cancel Reasons donut

Every widget must respond the moment any filter or parameter changes. No "Apply" button.

DASHBOARD 1 — OVERVIEW
- Title banner at the top: "Rebill Tracker — Overview" in Tableau Semibold. Subtitle below: "Homefield · Rebill Reminder Test · 18 cohorts · updated [date]".
- A horizontal 4-column × 2-row KPI grid (8 tiles total). Each tile styled as a Tableau "KPI" card (thin border, uppercase label, big number in Tableau Semibold). Tiles:
  1. Total Subs
  2. Active (count + %)
  3. Cancelled (count + %)
  4. Paused (count + %)
  5. Failed (count + %)
  6. Under Review (count + %)
  7. Rebilled ≥1x (count + %)
  8. Avg Cycles
- A line chart "Trend Over Time" — x-axis = snapshot_date, y-axis = selected trend metric, one line per cohort, Tableau 10 categorical palette.
- A 100% stacked horizontal bar chart "Current Status by Cohort" — stacks: Active, Cancelled, Paused, Failed. Tableau orange/blue/green style.
- A grouped bar chart "Rebill Performance by Cohort" — two series: Rebill Rate %, Avg Cycles (dual-axis).
- A grouped bar chart "Subscription Events by Cohort" — three series: Skips (Tableau orange), Pauses (Tableau blue), Saves (Tableau green). Footnote below: "Save events overlap with Skip and Pause events (a 'saved' sub was saved FROM a skip or pause). These counts are not additive."
- A sortable 14-column "Cohort Detail" table (Tableau crosstab style): Cohort, Total, Active, Active %, Cancelled, Cancel %, Paused, Pause %, Failed, Under Rev., Rebill %, Avg Cycles, Skips, Saves. Conditional colour scale on Rebill %.

DASHBOARD 2 — CANCEL REASONS
- Title: "Rebill Tracker — Cancel Reasons".
- Donut chart of 10 cancel reasons, center label = total cancellations for the selected scope. Tableau-style colour palette (warm palette for "negative" reasons, cool for "neutral"). Slices < 5% unlabeled.
- A reason distribution list (vertical bar chart): one bar per reason, x-axis = count, colour = reason. Sorted descending by count.
- A "By Cohort" crosstab: columns = cancel reasons, rows = cohorts, cell = count. Conditional heat-map shading.
- A "Cancel Reason Shift" mini-viz: stacked bar chart showing the cancel reason distribution broken out by Test Arm (Control vs Test vs Bottle). Shows whether the reminder changes WHY people cancel.

DASHBOARD 3 — SUBSCRIPTION DETAIL
- Title: "Rebill Tracker — Subscription Detail".
- A filter row at the top of this dashboard (in addition to the global left-panel filters):
  - Status filter dropdown (dynamically populated from distinct statuses in the data)
  - Cadence filter dropdown
  - Free-text search (matches subscription_id, customer_id, cancellation_reason)
- A Tableau-style crosstab with 13 sortable columns: Cohort, Sub ID, Status (coloured text), Cadence, Cycles, Rebilled? (✓ / —), Days Active, Days to Bill, Skips, Pauses, Saves, Cancel Reason (truncated, full in tooltip), Last Action.
- Pagination controls at the bottom (50 rows per page; previous / next; page X of N).
- A small KPI above the table: "N of M subscriptions shown" — updates with filters.

VISUAL STYLE (match Tableau defaults)
- Background: #FFFFFF (crosstabs) / #F7F7F7 (dashboard canvas)
- Title banner: solid #4E79A7 with white text (Tableau blue)
- Accent colours (Tableau 10 palette):
  - Blue: #4E79A7
  - Orange: #F28E2B
  - Red: #E15759
  - Teal: #76B7B2
  - Green: #59A14F
  - Yellow: #EDC948
  - Purple: #B07AA1
  - Pink: #FF9DA7
  - Brown: #9C755F
  - Grey: #BAB0AC
- Fonts: "Tableau Book", fallback "Benton Sans", then sans-serif. (Use Google Fonts "Inter" as a close substitute if Tableau fonts aren't available.)
- Thin 1px grid lines on charts (#E0E0E0).
- KPI cards: 1px solid #DDDDDD border, 4px radius, 12px internal padding.
- Crosstab headers: uppercase, 11px, grey (#595959). Alternating row shading in the crosstab.

SAMPLE DATA
Same synthetic data pattern as the Luzmo mock-up so the three mock-ups are comparable:
- 9 cohorts (1-box / 2-box / 4-box × Control / Test / Bottle for Rebill 1)
- total_subs between 800 and 5,000
- Cancel rate distributions where "Prefer One-Time" and "Too Expensive" are the largest buckets
- Snapshot history: 5 snapshots over the last 2 weeks. Control drifts up slightly; Test drifts down slightly.
- Subscription Detail: ~300 rows total.
- Reflect the expected directional finding: Control > Bottle > Test on rebill rate.

INTERACTIVITY CHECKLIST
- [ ] Every chart and table on every dashboard updates when any filter or parameter changes.
- [ ] Clicking a mark on one chart highlights the corresponding marks on other charts on the same dashboard (simulate Tableau's "Highlight" action).
- [ ] Clicking a cohort name in the Cohort Detail table acts like a "Filter Action" — filters all other worksheets on the current dashboard to that cohort.
- [ ] Clicking a cancel-reason bar in Dashboard 2 filters the "By Cohort" crosstab to that reason.
- [ ] The Trend Metric parameter switches the y-axis of the line chart without a reload.
- [ ] The Subscription Detail search box narrows the table in real time.
- [ ] The top nav bar switches between the three dashboards; filter state persists.
- [ ] Column headers in the crosstabs sort the table when clicked.
- [ ] Pagination works.

OUTPUT REQUIREMENTS
- Return the complete HTML file. Just the file, no explanations.
- Wrap in <html>...</html>.
- Chart.js 4.x from cdnjs.cloudflare.com, OR use pure SVG for charts — whichever is smaller.
- Do not use localStorage / sessionStorage. All state in JavaScript variables.
- Keep file under ~1,500 lines.

Thank you.
````

### Production-build steps after mock-up approval

1. Connect Tableau to the same BigQuery views used in Luzmo: `vw_rebill_tracker_cohort_summary` and `vw_rebill_tracker_subscription_detail`. Plus the `vw_rebill_tracker_snapshot_history` view for the trend chart.
2. Build the semantic model in the Tableau data source pane: cohort summary is the primary, subscription detail blended via `cohort_name`.
3. Create parameters for `p_trend_metric` (string, values: rebill_rate_pct / active_rate_pct / cancel_rate_pct / pause_rate_pct) and `p_cancel_reasons_cohort` (string).
4. Create a calculated field `[Selected Trend Metric]` that branches on `p_trend_metric` and returns the corresponding measure. Use on the Trend Over Time line chart's y-axis.
5. Build Worksheet-per-widget for each mock-up element (KPI tiles → one worksheet each, or one worksheet with many text marks). Standard Tableau pattern.
6. Assemble three dashboards (Overview / Cancel Reasons / Subscription Detail). Add a filter shelf to the left of each.
7. Add Filter Actions (click cohort → filter all worksheets). Add Highlight Actions (hover cohort → highlight corresponding bars). Add Set Actions where needed.
8. Add the "Save events overlap" caption below the Subscription Events worksheet on the Overview dashboard.
9. Apply conditional colour to the Rebill % column in the Cohort Detail crosstab.
10. Publish to Tableau Cloud / Server. Record the URL in `accounts/<client>/DELIVERY_NOTES.md`.
11. Walk through the interactivity checklist before handover.

---

## Prompt 2 — Tableau-specific calculated fields and LOD reference

### When to use

You have the two views (`vw_rebill_tracker_cohort_summary`, `vw_rebill_tracker_subscription_detail`) connected to Tableau and need the calculated-field boilerplate for the common Rebill Tracker calculations.

### Copy this prompt into Claude

````
I'm building a Rebill Tracker workbook in Tableau Desktop. The data source has two tables connected:
- `cohort_summary` (from vw_rebill_tracker_cohort_summary) — primary, one row per cohort_name
- `subscription_detail` (from vw_rebill_tracker_subscription_detail) — blended on cohort_name, one row per subscription_id

Generate:
1. A Tableau calculated field named `[Rebill Rate %]` that returns cohort-level rebill rate formatted as a percentage. Assume `total_subs` and `rebilled_at_least_once` are measures on cohort_summary.
2. A calculated field `[Selected Trend Metric]` that branches on the string parameter `[p_trend_metric]` and returns the matching rate column.
3. A calculated field `[Has Rebilled?]` at the subscription grain — TRUE when cyclescompleted >= 2.
4. A `{ FIXED [subscription_id] : MAX([event_date]) }` LOD expression to get the last-action date per subscription (assume subscription_detail has event-level fields denormalised; if not, explain what source change is needed).
5. A color-coding calc `[Status Color]` that returns "Active" / "Cancelled" / "Paused" / "Failed" / "Other" based on `[current_status]`, for use with a custom colour palette.
6. A "running total" Table Calc expression for the Trend Over Time chart showing cumulative new rebills per day per cohort. Specify the Addressing (Table across) and Partitioning (Cohort Name).

Return each as a Tableau formula block with a one-line description above it. No surrounding narrative.
````

---

*See also: `tools/tableau/capabilities.md`, `tools/tableau/limitations.md`, and `accounts/homefield/rebill_tracker_business_context.md`.*
