# Luzmo — Prompt Library

Reusable, self-contained Claude prompts that produce interactive, responsive dashboard mock-ups for Luzmo. Copy-paste the entire prompt block into Claude, attach the referenced data-context file if relevant, and you will get a working HTML + JS mock-up that demonstrates the intended Luzmo build. The mock-up is a visual / interaction reference — the production dashboard is then built directly in the Luzmo editor, mirroring the mock-up's layout, widgets, and filter behaviour.

---

## Prompt 1 — Rebill Tracker (Luzmo-styled interactive mock-up)

### When to use

Building a Rebill Tracker dashboard for Homefield (or any Shopify + SKIO / subscription-commerce client) in Luzmo. Produces a single-file HTML mock-up that matches Luzmo's visual conventions, demonstrates every filter and cross-filter interaction the final dashboard must support, and is self-contained (embedded sample data) so anyone can open it in a browser and click through.

### Copy this prompt into Claude

````
You are a BI prototyping assistant. Build me a fully interactive, single-file HTML + JavaScript mock-up of a "Rebill Tracker" dashboard styled to match Luzmo (cumul.io). The mock-up will be used by consultants at Saras Analytics to preview a Luzmo build for the Homefield subscription-commerce account before building it in the Luzmo editor.

DELIVERABLE
A single self-contained HTML file. Embed all CSS and JS inline. Use a CDN for Chart.js or Recharts. No build step required — I should be able to save the file as rebill_tracker_luzmo.html and open it in any modern browser.

BUSINESS CONTEXT (read carefully before building)
The Rebill Tracker tracks the health of a subscription portfolio for a DTC brand. The dashboard compares multiple static cohorts (e.g. Control, Test-email, Bottle-incentive × 1-box / 2-box / 4-box × 1st-rebill / subsequent-rebill → up to 18 cohorts) on key retention metrics.

Critical business rules to enforce in the mock-up:
- Never blend SNS and OTP rebill rates. They are separate metrics.
- `cyclescompleted = 0` means the original order only. `has_rebilled = TRUE` requires `cyclescompleted >= 2`.
- Save events overlap with skip + pause events. Counts are NOT additive. Show a footnote under the "Subscription Events by Cohort" chart saying so.
- Cancel timing is capped at rebill — cancellations after the rebill timestamp do not count against the "cancel rate" shown in the Overview tab.

DASHBOARD STRUCTURE
Four tabs, switchable via a tab bar at the top of the main content area (under the global filter bar):

1. "Overview"
2. "Cancel Reasons"
3. "Subscription Detail"
4. "Snapshot History"

GLOBAL FILTER BAR (always visible, filters every tab)
- Cohort (multi-select dropdown; default "All")
- Test Arm (multi-select; options: Control, Test, Bottle; default "All")
- Acquisition Order Type (single-select; options: All, SNS, OTP; default "All")
- Cadence (multi-select; options: 30-day, 60-day, 90-day; default "All")
- Subscription Created Date Range (date range picker)

Every widget, KPI tile, chart, and table must recompute the instant any filter is changed. No "Apply" button — this is a key Luzmo behaviour to demonstrate.

TAB 1 — OVERVIEW
- A horizontal KPI strip (responsive grid) with 8 tiles. Each tile shows a label, a big number, and optionally a sub-label (percentage or delta). Tiles:
  1. Total Subs
  2. Active (count + % of total)
  3. Cancelled (count + %)
  4. Paused (count + %)
  5. Failed (count + %)
  6. Under Review (count + %)
  7. Rebilled ≥1x (count + %)
  8. Avg Cycles (two decimals)
- A line chart "Trend Over Time" with a metric dropdown above it (options: Rebill Rate %, Active Rate %, Cancel Rate %, Pause Rate %). X-axis = snapshot_date. One line per selected cohort.
- A 2-column grid:
  - Left: 100% stacked horizontal bar chart, "Current Status by Cohort (%)", stacks: Active / Cancelled / Paused / Failed.
  - Right: grouped bar chart, "Rebill Performance by Cohort", two series: Rebill Rate %, Avg Cycles.
- A full-width grouped bar chart "Subscription Events by Cohort", three series: Skips, Pauses, Saves. Footnote under the chart: "Save events overlap with Skip and Pause events (a 'saved' sub was saved FROM a skip or pause). These counts are not additive."
- A sortable 14-column "Cohort Detail Table": Cohort, Total, Active, Active %, Cancelled, Cancel %, Paused, Pause %, Failed, Under Rev., Rebill %, Avg Cycles, Skips, Saves. Clicking a column header sorts the table. Numeric cells right-aligned, mono-font.

TAB 2 — CANCEL REASONS
- A cohort picker dropdown above the charts ("All Cohorts" or a single cohort).
- A donut chart of cancellations with 10 slices (one per cancel reason): Too Expensive, Oversupply, Accidental Sub, Taste/Flavor, Prefer One-Time, No Longer Use, Prefer Another Product, Not Given, No Reason Found, Other. Center label = total cancellations. Slices < 5% unlabeled on the donut but present in the legend.
- A reason distribution list (next to the donut): colored dot, reason name, absolute count, %.
- A "By Cohort" table: columns = Cohort, Total Cancelled, then one column per cancel reason.

TAB 3 — SUBSCRIPTION DETAIL
- A sub-filter bar within the tab: Cohort filter, Status filter, free-text search box (matches sub_id, customer_id, or cancellation_reason substring).
- A paginated table (50 rows per page). Columns: Cohort, Sub ID, Status (colored badge), Cadence, Cycles, Rebilled? (✓ / —), Days Active, Days to Bill, Skips, Pauses, Saves, Cancel Reason (truncated at 150px, full on hover), Last Action.
- Status badge colors: ACTIVE = green, CANCELLED* = red, PAUSED = blue, FAILED = orange, else grey.
- Sortable column headers. Pagination controls at the bottom: Prev / Page X of N / Next.

TAB 4 — SNAPSHOT HISTORY
- A table showing each daily snapshot: Date, Cohorts, Total Subs, Active %, Cancel %, Rebill %.
- Rows sorted by date descending.

VISUAL STYLE (match Luzmo defaults)
- Background: #FAFAF7
- Surface (cards): #FFFFFF
- Border: #E8E5DF, 1px
- Primary accent: #2D6A4F (Luzmo-style green for Active and primary CTAs)
- Red / cancelled: #D62828
- Blue / paused: #457B9D
- Orange / failed: #E76F51
- Font: 'DM Sans' for body, 'DM Mono' for numeric cells. Import from Google Fonts.
- Cards use a 1px border, 8px radius, subtle shadow on hover.
- KPI tiles: 155px min width, 16–18px internal padding, uppercase 10px label in muted grey, 24px bold value in mono font.

SAMPLE DATA
Generate realistic sample data so the mock-up is immediately populated. Include:
- 9 cohorts: "1-box Control Rebill 1", "1-box Test Rebill 1", "1-box Bottle Rebill 1", "2-box Control Rebill 1", "2-box Test Rebill 1", "2-box Bottle Rebill 1", "4-box Control Rebill 1", "4-box Test Rebill 1", "4-box Bottle Rebill 1".
- Each cohort has total_subs between 800 and 5,000, active_rate_pct between 50–80, cancel_rate_pct between 10–30, pause_rate_pct between 2–8, failed_rate_pct between 4–8, rebill_rate_pct between 55–75, avg_cycles between 1.5 and 3.2.
- Reflect the expected directional finding (Control > Bottle > Test on rebill rate) — do NOT make Test the highest rebill-rate cohort.
- Cancel reason counts per cohort — generate plausible distributions with "Prefer One-Time" and "Too Expensive" being the largest buckets and "No Reason Found" being small.
- Subscription Detail: ~300 rows total, spread across the 9 cohorts, with realistic statuses.
- Snapshot History: 5 snapshots covering the last 2 weeks. Each snapshot's rebill_rate_pct should drift slightly upward across snapshots for Control, slightly downward for Test.

INTERACTIVITY CHECKLIST (verify before returning the HTML)
- [ ] Changing the Cohort filter re-renders every tab's charts, KPI tiles, and tables.
- [ ] Changing the Test Arm filter re-renders all four tabs.
- [ ] Changing the Date Range filter re-renders all four tabs.
- [ ] Clicking a tab switches content; filter selections persist.
- [ ] The Trend Over Time metric dropdown swaps the y-axis metric without a page reload.
- [ ] The Cancel Reasons cohort picker narrows the donut and list to a single cohort.
- [ ] The Subscription Detail search box filters rows in real time as the user types.
- [ ] Clicking a column header in the Cohort Detail Table and the Subscription Detail Table sorts the table.
- [ ] Pagination works on the Subscription Detail table.

OUTPUT REQUIREMENTS
- Return the complete HTML file. Do NOT return pseudo-code or explanations — just the file.
- Wrap everything in a <html> ... </html> so I can save and open directly.
- Use Chart.js 4.x from cdnjs.cloudflare.com for charts, or Recharts via ESM — whichever keeps the file smallest.
- Do not use localStorage or sessionStorage. All state lives in JavaScript variables.
- Keep the file under ~1,500 lines.

Thank you.
````

### What you get back

A single HTML file, ~1,000–1,500 lines, that opens in any browser and behaves like the real Luzmo dashboard minus the actual Luzmo back-end. Use it to:

1. Walk the Homefield stakeholder through the intended layout and filter flow.
2. Confirm the KPI strip, chart types, filter list, and colour scheme before a single widget is built in Luzmo.
3. Hand the confirmed mock-up to the Luzmo builder as the visual reference spec.

### Production-build steps after mock-up approval

1. Model the data in Luzmo: upload Datasets 1 (Cohort Summary) and 2 (Subscription Detail) per `accounts/homefield/rebill_tracker_business_context.md` Section 5. Mark dimensions vs measures. Add the date hierarchy on `subscription_created_date`.
2. Build the global filter bar: add filter widgets bound to `cohort_name`, `test_arm`, `acquisition_order_type`, `cadence`, `subscription_created_date`.
3. Build Tab 1 (Overview): drag 8 KPI widgets onto the grid, then the Trend chart, then the 2-column grid (stacked bar + grouped bar), then the events chart, then the table. Wire each to the right measure.
4. Build Tab 2 (Cancel Reasons): one pie widget, one table, one cohort filter.
5. Build Tab 3 (Subscription Detail): one large table widget, add pagination, add the cohort + status + search filters. The search box is a text-parameter that feeds a `contains` filter on 3 columns.
6. Build Tab 4 (Snapshot History): one table widget from the snapshot-history dataset.
7. Cross-filter: enable cross-filter on all charts.
8. Theme: load Homefield brand colours if provided, else default to the Luzmo-style palette from the mock-up.
9. Validate every item in the prompt's interactivity checklist.
10. Record the Luzmo dashboard URL in `accounts/homefield/DELIVERY_NOTES.md`.

---

## Prompt 2 — Rebill Tracker SQL generator for Luzmo datasets

### When to use

You have the source tables (SkioSubscriptions, SkioSubscriptionsStatus, a cohort mapping table) and need the two SQL views that feed Dataset 1 and Dataset 2 in Luzmo. Use this prompt to generate them against a specific source project / dataset.

### Copy this prompt into Claude

````
You are a SQL assistant. Generate two BigQuery views that will power a Luzmo Rebill Tracker dashboard.

SOURCE TABLES (replace placeholders with the actual project / dataset names when you reply):
- `<PROJECT>.<STAGING>.SkioSubscriptions` — one row per subscription (subscription_id, customer_id, status, cyclescompleted, cancellation_reason, created_at, cancelled_at, next_billing_date, billing_interval, billing_interval_count, updated_at, _daton_batch_runtime).
- `<PROJECT>.<STAGING>.SkioSubscriptionsStatus` — subscription event log (subscription_id, event_timestamp, status).
- `<PROJECT>.<DIM>.cohort_mapping` — cohort assignment (subscription_id, cohort_name, test_arm, cadence_bucket).

OUTPUT
Two CREATE OR REPLACE VIEW statements, both in `<PROJECT>.<MARTS>`:
1. `vw_rebill_tracker_cohort_summary` — one row per cohort_name. Schema matches Dataset 1 from the Rebill Tracker business context (30 columns starting with cohort_name, test_arm, total_subs … ending with cancel_reason_other).
2. `vw_rebill_tracker_subscription_detail` — one row per subscription_id. Schema matches Dataset 2 (14 columns: subscription_id, customer_id, cohort_name, current_status, cadence, cyclescompleted, has_rebilled, days_since_created, days_until_next_bill, skip_events, pause_events, save_events, cancellation_reason, last_action_type).

RULES
- Deduplicate SkioSubscriptions by selecting the latest `_daton_batch_runtime` per subscription_id.
- Derive `has_rebilled` as `cyclescompleted >= 2`.
- Derive `cadence` as one of "30-day", "60-day", "90-day" based on billing_interval and billing_interval_count.
- Skip events = count of event_status LIKE 'SAVED_BY_SKIP%'.
- Pause events = count of event_status = 'PAUSED' OR event_status LIKE 'SAVED_BY_PAUSE%'.
- Save events = count of event_status LIKE 'SAVED_BY%' (this overlaps with skip + pause by design — do NOT deduplicate).
- Last action type = the event_type bucket of the event with MAX(event_timestamp) per subscription_id.
- Cancel reason bucketing: use LOWER(cancellation_reason) LIKE patterns to map to the 10 Rebill Tracker categories.
- Views should ONLY include subscriptions whose subscription_id is present in the cohort_mapping table.

Return the two CREATE OR REPLACE VIEW statements only, no surrounding narrative.
````

---

*See also: `tools/luzmo/capabilities.md`, `tools/luzmo/limitations.md`, and `accounts/homefield/rebill_tracker_business_context.md`.*
