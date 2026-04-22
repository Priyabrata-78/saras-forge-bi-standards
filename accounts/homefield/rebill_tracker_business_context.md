# Rebill Tracker — Business Context (Homefield)

> Self-contained reference for anyone — even with zero BI or analytics background — to understand what the Rebill Tracker dashboard is, what it measures, and how to rebuild it for Homefield in any BI tool.

Source of truth: adapted from the Instant Hydration AIDE implementation (`clients/instant_hydration/` in `frido-saras/AIDE`), April 2026 snapshot.

---

## 1. What is the Rebill Tracker?

A subscription retention dashboard for DTC / subscription-commerce brands that sell products on a recurring billing cycle (e.g. a 30-day replenishment subscription). Its job is to answer three business questions:

1. **Are our subscribers rebilling on schedule?** That is, when a subscription reaches its next billing cycle, does the charge go through, or does the customer cancel / skip / pause first?
2. **Why are subscribers cancelling?** Price? Accidental sign-up? Product preference? Oversupply?
3. **Does a reminder / save intervention move the needle?** The dashboard is designed to compare multiple cohorts side-by-side (e.g. a test group that received a pre-rebill reminder email vs a control group that did not), so the business can quantify whether the intervention helped retention, hurt it, or was neutral.

The dashboard was originally built at Instant Hydration (IH) to evaluate a "Rebill Reminder Test" — a 6-arm experiment (Rebill 1 / Rebill 2 × Control / Test-email / Bottle-incentive) running across 18 static cohorts, rebill dates March 9 – April 30, 2026.

Homefield is adopting the same framework for its own subscription base. The dashboard structure, KPIs, filters, and data model below are designed to translate directly once Homefield's subscription events are wired in.

---

## 2. Core business concepts

**Subscription.** A customer commitment to receive a product on a recurring cadence (weekly / monthly / 60-day / 90-day). Each subscription has a unique `subscription_id`, a `customer_id`, a billing interval, and a lifecycle (`ACTIVE` → `PAUSED` / `FAILED` / `CANCELLED`).

**Billing cycle / rebill.** The moment the platform attempts to charge the saved card for the next shipment. A subscription "rebills" when that charge succeeds. `cyclescompleted = 0` means the original checkout only; `cyclescompleted = 1` means one rebill succeeded; `cyclescompleted ≥ 2` means the subscription has rebilled at least once after creation and is considered "retained past first rebill".

**Rebill rate.** The headline KPI. For a cohort of customers acquired in month M, what % are still active (rebilled) in month M+1, M+2, … M+N? IH splits this by acquisition type:
- SNS (Subscribe & Save) M1 ≈ 69.4%, M2 ≈ 29.5%, M3 ≈ 23.5%
- OTP (One-Time Purchase) M1 ≈ 5.6%, M2 ≈ 7.4% (low volume, noisy)

**Critical rule: never blend SNS and OTP rebill rates.** A blended ~35% number is meaningless because SNS and OTP have fundamentally different retention shapes. Always split by `acquisition_order_type`. (Source: `rule_rebill_split` in AIDE business rules.)

**Day 27 reminder window.** For a 30-day subscription, the rebill reminder fires on Day 27 of the subscriber journey — 3 days before the scheduled rebill. The Day 27–30 window is the "decision window" where the subscriber chooses to let the rebill happen, cancel, skip, pause, change frequency, or change next-order date.

**Event buckets (status values seen in the source system, SKIO via Daton Shopify):**
- `ACTIVE` → Rebill or reactivation succeeded
- `FAILED` → Billing attempt failed (card declined, etc.)
- `PAUSED` → Subscriber paused
- `CANCELLED` / `CANCELLED_BY_MERCHANT` / `CANCELLED_BY_CALIFORNIA` / `CANCELLED_NO_TREATMENT_SHOWN`
- `SAVED_BY_SKIP` → Skipped a cycle (deferred, did not cancel)
- `SAVED_BY_PAUSE` → Paused as a save action
- `SAVED_BY_DISCOUNT` / `SAVED_BY_SECONDARY_DISCOUNT` → Offered a discount and stayed
- `SAVED_BY_EDIT_FREQUENCY` → Changed cadence
- `SAVED_BY_SWAP_PRODUCT` → Swapped product
- `SAVED_BY_CHANGE_NEXT_ORDER_DATE` → Rescheduled next order

**Cancel reasons (user-facing).** The Rebill Tracker classifies cancellations into 10 buckets:
1. Too Expensive (`cancel_reason_price`)
2. Oversupply (`cancel_reason_oversupply`)
3. Accidental Sub (`cancel_reason_accidental`)
4. Taste/Flavor (`cancel_reason_taste`)
5. Prefer One-Time (`cancel_reason_prefer_otp`)
6. No Longer Use (`cancel_reason_no_longer_use`)
7. Prefer Another Product (`cancel_reason_prefer_alt`)
8. Not Given (`cancel_reason_not_given`)
9. No Reason Found (`cancel_reason_no_reason`)
10. Other (`cancel_reason_other`)

**Critical rule: cap cancel timing at rebill.** If a rebill succeeded at T0 and the customer cancelled at T0 + 2 hours, the cancellation does NOT count against the "rebill cancel rate" — the rebill already happened. Cancellations after the rebill timestamp are counted in the post-rebill cancel bucket, not the reminder-window cancel bucket. (Source: `rule_rebill_reminder_test_methodology` in AIDE.)

**Critical rule: filter SkioOrders to cancelled_at IS NULL.** SkioOrders creates a row at billing ATTEMPT, not billing completion. Without the filter you count failed billing attempts as successful rebills, which overstates rebill counts and subscription revenue. This is the most common SkioOrders mistake.

---

## 3. Dashboard structure — the 4 tabs

The Rebill Tracker is organised into 4 tabs. Every tab must respond to the global filter controls described in Section 4.

### Tab 1 — Overview

The "headline" tab. A one-screen read of subscription health.

**Widgets, top to bottom:**

- **KPI strip (8 tiles)**
  - Total Subs — `COUNT(subscription_id)`
  - Active — count and % of total
  - Cancelled — count and % of total
  - Paused — count and % of total
  - Failed — count and % of total
  - Under Review — count and % of total
  - Rebilled ≥1x — count and % of total (`cyclescompleted >= 2`)
  - Avg Cycles — `AVG(cyclescompleted)` across the filtered set

- **Trend Over Time (line chart)** — appears only when ≥2 daily snapshots exist. Metric dropdown: Rebill Rate %, Active Rate %, Cancel Rate %, Pause Rate %. X-axis = snapshot date, one line per cohort.

- **Current Status by Cohort (100% stacked horizontal bar)** — per-cohort split of Active / Cancelled / Paused / Failed rates.

- **Rebill Performance by Cohort (grouped bar)** — two series: Rebill Rate % and Avg Cycles.

- **Subscription Events by Cohort (grouped bar)** — three series: Skips, Pauses, Saves. Footnote: "Save events overlap with Skip and Pause events. Counts are not additive."

- **Cohort Detail Table** — one row per cohort, 14 columns: Cohort, Total, Active, Active %, Cancelled, Cancel %, Paused, Pause %, Failed, Under Rev., Rebill %, Avg Cycles, Skips, Saves.

### Tab 2 — Cancel Reasons

**Widgets:**
- **Cohort picker (dropdown)** — "All Cohorts" or a single cohort.
- **Donut chart** — centered on total cancellations for the selected scope, 10 slices (the cancel reasons). Each slice labelled with reason name + %. Slices <5% are unlabeled but still shown in legend.
- **Reason distribution list** — vertical list, one row per reason: colored dot, reason name, absolute count, %.
- **By Cohort table** — one row per cohort, columns: Cohort, Total Cancelled, then one column per cancel reason.

### Tab 3 — Subscription Detail

A row-level drill-down.

**Controls (filter bar at top):**
- Cohort filter dropdown
- Status filter dropdown (populated dynamically from the distinct statuses in the data)
- Search box — matches on `subscription_id`, `customer_id`, or `cancellation_reason`
- Sortable column headers (toggle asc / desc)
- Pagination — 50 rows per page

**Columns:**
Cohort · Sub ID · Status (colored by status) · Cadence · Cycles · Rebilled? (✓) · Days Active · Days to Bill · Skips · Pauses · Saves · Cancel Reason (truncated, full on hover) · Last Action

### Tab 4 — Data Input (operational)

In the IH implementation this tab accepts pasted CSV / TSV output from BigQuery. For Homefield's BI-tool implementation (Luzmo / Tableau / Power BI), this tab is replaced by a direct data connection and can be omitted — the equivalent is the dashboard's data-source settings page.

---

## 4. Global filters (every tab responds)

- **Cohort name** (multi-select or single-select)
- **Test arm** (Control / Test / Bottle, if using the IH experiment structure)
- **Acquisition order type** (SNS / OTP)
- **Cadence** (30-day / 60-day / 90-day)
- **Box count** (1-box / 2-box / 4-box) — relevant when the brand ships multi-box bundles
- **Subscription created date range** — controls the population
- **Snapshot date** (for the trend view) — controls which saved snapshot is displayed

All widgets, KPI tiles, and tables must recompute when any filter is changed. The dashboard has no server-side cache that needs to be manually refreshed — changes propagate instantly.

---

## 5. Data model — two core datasets

The dashboard is powered by two logical datasets. Column names below are normalized (lowercase, underscore-separated); match them in whatever tool is consuming.

### Dataset 1 — Cohort Summary (one row per cohort)

Populates Tab 1 KPIs, all Tab 1 charts, and the Cancel Reasons tab.

**Required columns:** `cohort_name`, `total_subs`

**Full schema:**

| Column | Type | Meaning |
|---|---|---|
| `cohort_name` | string | Cohort label (e.g. "2-box Test Rebill 1") |
| `test_arm` | string | Control / Test / Bottle |
| `total_subs` | int | Total subscriptions in this cohort |
| `active` | int | Currently ACTIVE |
| `cancelled` | int | Currently CANCELLED (any subtype) |
| `paused` | int | Currently PAUSED |
| `failed` | int | Currently FAILED |
| `under_review` | int | Subscription in review / ambiguous state |
| `active_rate_pct` | number | `active / total_subs * 100` |
| `cancel_rate_pct` | number | `cancelled / total_subs * 100` |
| `pause_rate_pct` | number | `paused / total_subs * 100` |
| `failed_rate_pct` | number | `failed / total_subs * 100` |
| `rebill_rate_pct` | number | % of cohort with `cyclescompleted >= 2` |
| `rebilled_at_least_once` | int | Count with `cyclescompleted >= 2` |
| `avg_cycles` | number | `AVG(cyclescompleted)` |
| `total_skip_events` | int | Sum of skip events in the cohort |
| `total_pause_events` | int | Sum of pause events in the cohort |
| `total_save_events` | int | Sum of all SAVED_BY_* events (overlaps with skip + pause) |
| `cancel_reason_price` | int | Count cancelled for price |
| `cancel_reason_oversupply` | int | Count cancelled for oversupply |
| `cancel_reason_accidental` | int | Count cancelled "accidental sub" |
| `cancel_reason_taste` | int | Count cancelled for taste / flavor |
| `cancel_reason_prefer_otp` | int | Count cancelled preferring one-time |
| `cancel_reason_no_longer_use` | int | Count cancelled "no longer use" |
| `cancel_reason_prefer_alt` | int | Count cancelled preferring another product |
| `cancel_reason_not_given` | int | Count cancelled reason not given |
| `cancel_reason_no_reason` | int | Count cancelled with no reason found |
| `cancel_reason_other` | int | Count cancelled "other" |

### Dataset 2 — Subscription Detail (one row per subscription)

Populates Tab 3 and the Subscription Detail table.

**Required columns:** `subscription_id`, `cohort_name`

**Full schema:**

| Column | Type | Meaning |
|---|---|---|
| `subscription_id` | string | Unique subscription identifier |
| `customer_id` | string | Customer identifier |
| `cohort_name` | string | Cohort label |
| `current_status` | string | ACTIVE / CANCELLED / CANCELLED_BY_MERCHANT / PAUSED / FAILED / ... |
| `cadence` | string | e.g. "30 DAY", "60 DAY" |
| `cyclescompleted` | int | Number of rebills completed (0 = checkout only) |
| `has_rebilled` | bool | TRUE if `cyclescompleted >= 2` |
| `days_since_created` | int | Tenure in days |
| `days_until_next_bill` | int | Days from today to `next_billing_date`; NULL if inactive |
| `skip_events` | int | Lifetime skip events |
| `pause_events` | int | Lifetime pause events |
| `save_events` | int | Lifetime save events |
| `cancellation_reason` | string | Raw reason string |
| `last_action_type` | string | Most recent event_type from the timeline |

---

## 6. How the data is built — source tables and transformations

### For a Shopify + SKIO subscription stack (IH and Homefield both fit this pattern):

**Source staging tables (via Daton):**
- `SkioSubscriptions` — one row per subscription (current state). Join key: `subscription_id`. Filter: `_daton_batch_runtime = MAX(_daton_batch_runtime)` to get latest snapshot.
- `SkioSubscriptionsStatus` — one row per subscription event (full timeline). Used to compute event counts and cancel reasons.

**Presentation tables (if using a dbt / modeled layer):**
- `cohorts_base` — 6.7M rows in IH, 71 columns, clustered on `platform_name`. Grain: customer × acquisition_month × order_month. Primary keys: `acquisition_month_start`, `platform_name`, `acquisition_order_type`, `month_diff`. `month_diff = 0` is acquisition month. This is the primary source for multi-month rebill-rate curves.
- `OrderLinesMasterV1` — line-level orders with `order_type` (SNS/OTP), `customer_type` (New/Returning), `net_sales`, `gross_sales`, `is_test_order`.

**Transformation pipeline — Dataset 1 (Cohort Summary):**

```sql
-- Pseudo-SQL. Replace the source table with the Homefield equivalent.
WITH latest_batch AS (
  SELECT subscription_id, MAX(_daton_batch_runtime) AS max_batch
  FROM `<project>.<staging>.SkioSubscriptions`
  GROUP BY subscription_id
),
sub_snapshot AS (
  SELECT
    s.subscription_id,
    s.customer_id,
    s.status,
    CAST(s.cyclescompleted AS INT64) AS cyclescompleted,
    s.cancellation_reason,
    s.billing_interval,
    CAST(s.billing_interval_count AS INT64) AS billing_interval_count,
    DATE(s.created_at) AS subscription_created_date
  FROM `<project>.<staging>.SkioSubscriptions` s
  INNER JOIN latest_batch lb
    ON s.subscription_id = lb.subscription_id
    AND s._daton_batch_runtime = lb.max_batch
),
events AS (
  SELECT
    subscription_id,
    status AS event_status,
    DATE(event_timestamp) AS event_date
  FROM `<project>.<staging>.SkioSubscriptionsStatus`
),
event_summary AS (
  SELECT
    subscription_id,
    COUNTIF(event_status LIKE 'SAVED_BY_SKIP%') AS skip_events,
    COUNTIF(event_status = 'PAUSED' OR event_status LIKE 'SAVED_BY_PAUSE%') AS pause_events,
    COUNTIF(event_status LIKE 'SAVED_BY%') AS save_events
  FROM events
  GROUP BY subscription_id
),
joined AS (
  SELECT
    cm.cohort_name,
    cm.test_arm,
    s.subscription_id,
    s.status,
    s.cyclescompleted,
    s.cancellation_reason,
    COALESCE(e.skip_events, 0) AS skip_events,
    COALESCE(e.pause_events, 0) AS pause_events,
    COALESCE(e.save_events, 0) AS save_events
  FROM `<project>.<cohort_mapping>` cm
  LEFT JOIN sub_snapshot s USING (subscription_id)
  LEFT JOIN event_summary e USING (subscription_id)
)
SELECT
  cohort_name,
  MAX(test_arm) AS test_arm,
  COUNT(*) AS total_subs,
  COUNTIF(status = 'ACTIVE') AS active,
  COUNTIF(status LIKE 'CANCELLED%') AS cancelled,
  COUNTIF(status = 'PAUSED') AS paused,
  COUNTIF(status = 'FAILED') AS failed,
  0 AS under_review, -- populate if applicable
  SAFE_DIVIDE(COUNTIF(status = 'ACTIVE'), COUNT(*)) * 100 AS active_rate_pct,
  SAFE_DIVIDE(COUNTIF(status LIKE 'CANCELLED%'), COUNT(*)) * 100 AS cancel_rate_pct,
  SAFE_DIVIDE(COUNTIF(status = 'PAUSED'), COUNT(*)) * 100 AS pause_rate_pct,
  SAFE_DIVIDE(COUNTIF(status = 'FAILED'), COUNT(*)) * 100 AS failed_rate_pct,
  SAFE_DIVIDE(COUNTIF(cyclescompleted >= 2), COUNT(*)) * 100 AS rebill_rate_pct,
  COUNTIF(cyclescompleted >= 2) AS rebilled_at_least_once,
  AVG(cyclescompleted) AS avg_cycles,
  SUM(skip_events) AS total_skip_events,
  SUM(pause_events) AS total_pause_events,
  SUM(save_events) AS total_save_events,
  COUNTIF(LOWER(cancellation_reason) LIKE '%price%' OR LOWER(cancellation_reason) LIKE '%expensive%') AS cancel_reason_price,
  COUNTIF(LOWER(cancellation_reason) LIKE '%oversupply%' OR LOWER(cancellation_reason) LIKE '%too much%') AS cancel_reason_oversupply,
  COUNTIF(LOWER(cancellation_reason) LIKE '%accident%') AS cancel_reason_accidental,
  COUNTIF(LOWER(cancellation_reason) LIKE '%taste%' OR LOWER(cancellation_reason) LIKE '%flavor%') AS cancel_reason_taste,
  COUNTIF(LOWER(cancellation_reason) LIKE '%one-time%' OR LOWER(cancellation_reason) LIKE '%otp%') AS cancel_reason_prefer_otp,
  COUNTIF(LOWER(cancellation_reason) LIKE '%no longer%') AS cancel_reason_no_longer_use,
  COUNTIF(LOWER(cancellation_reason) LIKE '%prefer%' OR LOWER(cancellation_reason) LIKE '%alternative%') AS cancel_reason_prefer_alt,
  COUNTIF(cancellation_reason IS NULL OR TRIM(cancellation_reason) = '') AS cancel_reason_not_given,
  COUNTIF(LOWER(cancellation_reason) = 'no reason') AS cancel_reason_no_reason,
  COUNTIF(cancellation_reason IS NOT NULL
    AND LOWER(cancellation_reason) NOT LIKE '%price%'
    AND LOWER(cancellation_reason) NOT LIKE '%oversupply%'
    AND LOWER(cancellation_reason) NOT LIKE '%accident%'
    AND LOWER(cancellation_reason) NOT LIKE '%taste%'
    AND LOWER(cancellation_reason) NOT LIKE '%flavor%'
    AND LOWER(cancellation_reason) NOT LIKE '%one-time%'
    AND LOWER(cancellation_reason) NOT LIKE '%otp%'
    AND LOWER(cancellation_reason) NOT LIKE '%no longer%'
    AND LOWER(cancellation_reason) NOT LIKE '%prefer%'
    AND LOWER(cancellation_reason) NOT LIKE '%alternative%'
    AND LOWER(cancellation_reason) != 'no reason') AS cancel_reason_other
FROM joined
GROUP BY cohort_name;
```

The exact string matching for cancel reasons should be replaced with Homefield's canonical reason taxonomy once that is confirmed.

**Transformation pipeline — Dataset 2 (Subscription Detail):**

The grain is one row per subscription. Take `sub_snapshot` and `event_summary` from above, add `cohort_name` from the cohort mapping, derive `has_rebilled`, `days_since_created`, `days_until_next_bill`, and carry `cancellation_reason` and `last_action_type` (the event_type of the most recent event per subscription).

---

## 7. Critical business rules (must be enforced in every implementation)

1. **Never blend SNS and OTP rebill rates.** Always split by `acquisition_order_type`. A blended rate is meaningless.
2. **SkioOrders rows are billing attempts, not successes.** Filter `WHERE cancelled_at IS NULL` before counting rebills or subscription revenue.
3. **Cap cancel timing at rebill.** Cancellations after the rebill timestamp do not count against the rebill cancel rate.
4. **Static cohorts, not dynamic.** In a test/control experiment, the cohort is assigned at test launch and does not change — a customer who was in the Test cohort stays in Test even if they later cancel before their rebill.
5. **Save events overlap with skip + pause.** A "saved" subscription was saved FROM either a skip or a pause event. Do not sum skips + pauses + saves and expect a total that equals unique save actions.
6. **`cyclescompleted` is post-original.** `cyclescompleted = 0` means the original checkout only (no rebill has happened). `has_rebilled = TRUE` requires `cyclescompleted >= 2`.
7. **Subscription revenue pollutes the "Others" channel bucket.** When doing channel analysis, filter `sales_channel = 'web'` on the order-lines table; otherwise ~60K subscription rebill orders/month in IH get dumped into `Others` and distort the report.

---

## 8. How to adapt for Homefield

Homefield's subscription stack may differ from IH's (different platform / subscription engine, different event taxonomy). The dashboard structure, KPI list, filter list, and widget layout stay the same. The mapping work is:

1. **Identify Homefield's subscription source table** — the equivalent of `SkioSubscriptions`. Columns needed: subscription_id, customer_id, status, cyclescompleted (or equivalent), billing_interval, billing_interval_count, created_at, cancelled_at, cancellation_reason, next_billing_date.
2. **Identify Homefield's subscription event timeline** — the equivalent of `SkioSubscriptionsStatus`. Columns needed: subscription_id, event_timestamp, status.
3. **Confirm Homefield's cancel-reason taxonomy.** Either reuse the 10-bucket classification above, or remap to whatever categories Homefield already tracks.
4. **Get the cohort-mapping table from the Homefield experiment lead** (if running a test). This is `subscription_id → cohort_name → test_arm`. In IH this was owned by Sharon.
5. **Build Datasets 1 and 2** using the SQL pattern in Section 6 against Homefield's source tables.
6. **Build the dashboard** using the tool-specific prompt in the Forge `prompt_library.md` — one each for Luzmo (`tools/luzmo/prompt_library.md`), Tableau (`tools/tableau/prompt_library.md`), and Power BI (`tools/powerbi/prompt_library.md`).

---

## 9. File provenance from AIDE (IH)

For engineers who want to trace back to the originals:

| AIDE file | What was used |
|---|---|
| `clients/instant_hydration/aide/1_BUSINESS_RULES.md` | `rule_rebill_split`, `rule_subscription_detection`, `rule_rebill_reminder_test_methodology` |
| `clients/instant_hydration/aide/2_DATASET_REFERENCE.md` | `cohorts_base`, `SubscriptionMaster V1`, `OrderLinesMasterV1` schemas |
| `clients/instant_hydration/aide/3_KPI_FORMULAS.md` | `kpi_rebill_rate_sns`, `kpi_rebill_rate_otp`, `kpi_rebill_reminder_test_rate`, `kpi_rebill_pct`, `kpi_active_subscriptions` |
| `clients/instant_hydration/aide/4_SQL_TEMPLATES.md` | Time-Series Rebill Rate by Month Since Acquisition template |
| `clients/instant_hydration/dashboards/rebill-tracker.jsx` | 4-tab UI structure, KPI strip, filters, cancel-reason taxonomy, 14-column cohort table, 13-column subscription detail table |
| `clients/instant_hydration/deliverables/rebill_reminder_test_handover.md` | 5-view analysis framework, 18-cohort design, Day 27 reminder concept |
| `clients/instant_hydration/deliverables/rebill_reminder_test_datasets.sql` | DS1–DS5 SQL (snapshot, event timeline, event summary, customer revenue, customer LTV) |

---

*Last updated: 2026-04-22. Maintained by the Saras Forge BI Standardisation initiative.*
