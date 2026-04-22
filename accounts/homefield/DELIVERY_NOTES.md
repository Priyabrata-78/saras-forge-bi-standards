# Delivery Notes — Homefield

Forge delivery notes for the Homefield subscription-commerce account. Primary build: Rebill Tracker in Luzmo, modelled after the Instant Hydration Rebill Tracker built at frido-saras/AIDE.

---

## Dashboard(s) Built

- **Name:** Rebill Tracker
- **BI tool:** Luzmo (primary)
- **Link:** _(to be added after production build — paste Luzmo dashboard URL here)_
- **Date:** _(to be filled)_

## Scope

Track subscription retention, rebill rate, cancel reasons, and cohort-level performance for Homefield's Shopify + SKIO subscription base. Designed to support a rebill-reminder style A/B/C experiment when Homefield's growth team is ready to launch one.

See `accounts/homefield/rebill_tracker_business_context.md` for the full business spec — that file is the source of truth for KPIs, filters, data model, and critical business rules.

## Prompts Used (from Forge Prompt Library)

- [x] **Prompt 1** — "Rebill Tracker (Luzmo-styled interactive mock-up)" from `tools/luzmo/prompt_library.md`. Used to produce the visual mock-up before the Luzmo build.
- [x] **Prompt 2** — "Rebill Tracker SQL generator for Luzmo datasets" from `tools/luzmo/prompt_library.md`. Used to generate the two BigQuery views (`vw_rebill_tracker_cohort_summary`, `vw_rebill_tracker_subscription_detail`).
- [ ] _(Add any additional prompts used here with name + version)_

## Luzmo-specific Implementation Notes

### Datasets created in Luzmo

1. **`homefield_rebill_cohort_summary`** — connected to `vw_rebill_tracker_cohort_summary`. Grain: one row per cohort_name. Refresh: hourly cached.
2. **`homefield_rebill_subscription_detail`** — connected to `vw_rebill_tracker_subscription_detail`. Grain: one row per subscription_id. Refresh: hourly cached.
3. **`homefield_rebill_snapshot_history`** — materialised table of daily snapshots (insert-only). Refresh: daily at 06:00 UTC via a scheduled BigQuery job.

### Dashboard pages (tabs)

1. **Overview** — 8-tile KPI strip, Trend Over Time line chart, Current Status by Cohort (100% stacked horizontal bar), Rebill Performance by Cohort (grouped bar), Subscription Events by Cohort (grouped bar with footnote), Cohort Detail Table (14 columns).
2. **Cancel Reasons** — cohort picker, donut chart of 10 cancel reasons, reason distribution list, by-cohort matrix table.
3. **Subscription Detail** — 13-column paginated table with cohort, status, and search filters.
4. **Snapshot History** — summary table of each daily snapshot.

### Global filters (bound on every tab)

- Cohort Name (multi-select)
- Test Arm (multi-select: Control / Test / Bottle)
- Acquisition Order Type (single: All / SNS / OTP)
- Cadence (multi-select: 30-day / 60-day / 90-day)
- Subscription Created Date Range

### Parameters

- `selected_trend_metric` (string; default `rebill_rate_pct`) — drives the Overview Trend Over Time chart's y-axis.
- `cancel_reasons_cohort` (string; default `__all__`) — drives the Cancel Reasons donut + list.

### Row-level security

Not enabled for internal Saras use. If the dashboard is later embedded in a Homefield customer-facing portal, scope to a single brand_id via a JWT parameter and add a dataset-level RLS rule.

### Theme

Luzmo default palette adjusted to match Homefield brand colours. Accent green for Active and CTAs, red for Cancelled, blue for Paused, orange for Failed.

## Limitations Encountered

- [ ] Already in register: `tools/luzmo/limitations.md` — "Save events overlap" footnote required.
- [ ] Already in register: `tools/luzmo/limitations.md` — cancel-reason bucketing must be done in the SQL view (no regex in Luzmo formulas).
- [ ] Already in register: `tools/luzmo/limitations.md` — snapshot history must be materialised as a dedicated table.
- [ ] New limitation added to register: _(to be filled if any Homefield-specific limitation is hit)_

## Cross-Validation

Before Go Green, validate each headline KPI against the source system (Shopify admin for subscription counts, SKIO dashboard for cancel reasons).

- Metric validated: **Total Subs** — BQ vs Luzmo rendering match within ±0. _(to be filled)_
- Metric validated: **Rebill Rate %** — BQ vs Luzmo rendering match within ±0.1pp. _(to be filled)_
- Metric validated: **Cancel Reason distribution** — BQ counts vs Luzmo donut counts match exactly. _(to be filled)_

## Tool Cross-Builds (reference only)

Homefield's primary deliverable is Luzmo. Parallel Tableau and Power BI builds (for internal training or future migration) can be produced with:

- `tools/tableau/prompt_library.md` — Prompt 1 (Tableau-styled Rebill Tracker mock-up)
- `tools/powerbi/prompt_library.md` — Prompt 1 (Power BI-styled Rebill Tracker mock-up)

Use the same business context at `accounts/homefield/rebill_tracker_business_context.md` for all three tools.

## Go Green

- **Pod Lead:** _(to be filled — Homefield pod lead)_
- **Date:** _(to be filled)_
- **Checklist followed:** Yes / No — see `universal/BUILD_CHECKLIST.md`

---

*Last updated: 2026-04-22.*
