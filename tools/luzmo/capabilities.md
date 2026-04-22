# Luzmo — Capabilities

What Luzmo (formerly Cumul.io) can do in a Forge BI build. Use this as the positive reference when deciding whether Luzmo is the right tool for a given Homefield / Hexclad / other-account dashboard.

---

## 1. Chart types and visual vocabulary

Out-of-the-box chart types Luzmo supports:

- **KPI / Big Number** — single-value tiles with optional sparkline, delta vs prior period, and conditional color rules.
- **Bar / Column** — grouped, stacked, 100% stacked, horizontal and vertical.
- **Line / Area** — single and multi-series. Supports dual y-axes.
- **Pie / Donut** — including center-value annotation.
- **Heatmap / Matrix** — good for cohort retention grids (rows = acquisition month, columns = month_diff).
- **Scatter / Bubble** — with optional size and color encoding.
- **Table** — with sortable columns, conditional formatting, in-cell bars, and column-level drill.
- **Pivot table** — drag-and-drop rows / columns / measures.
- **Geo (choropleth, bubble map)** — world, country, and custom GeoJSON.
- **Gauge / funnel / sankey** — for conversion and flow analyses.
- **Custom chart (Vega-Lite)** — full escape hatch for any viz Luzmo doesn't ship natively.

---

## 2. Interactivity

- **Cross-filter** — any click on any chart filters all other charts on the same dashboard page. Configurable per-chart (can opt out).
- **Drill-down** — hierarchical drill (e.g. Year → Quarter → Month) defined on the dataset.
- **Drill-through** — navigate from one dashboard to another with the filter context carried over.
- **Global filter bar** — top-of-dashboard filters including dropdown, multi-select, date range, relative-date, numeric slider, text search.
- **Parameters** — user-set values (e.g. "cost per unit") that are referenced in calculated measures. Supports number, date, and string types.
- **Tabs / pages** — multi-page dashboards with independent content per tab.
- **Conditional visibility** — show / hide widgets based on parameter or filter values.
- **Tooltip customisation** — rich hover tooltips with multiple measures.

---

## 3. Data modelling and calculations

- **Dataset layer** — Luzmo ingests tables from the source (BigQuery, Snowflake, Redshift, Postgres, MySQL, S3, REST API, file upload, Google Sheets, etc.). Each dataset has columns typed as hierarchy (dimension) or numeric (measure).
- **Joins** — cross-dataset joins defined at modelling time; Luzmo uses them automatically when a widget mixes columns from two datasets.
- **Calculated columns** — row-level expressions using Luzmo's formula syntax (similar to SQL / Excel).
- **Calculated measures** — aggregate expressions (e.g. `sum(active) / sum(total_subs)`), including conditional aggregates and window-style expressions via the custom formula editor.
- **Hierarchies** — date hierarchy (Year → Quarter → Month → Day) and custom hierarchies.
- **Dimension flags** — mark columns as key, hierarchy, or measure.
- **Row-level security** — parameterised filters at the dataset level that scope what a viewer can see.

---

## 4. Embedding and distribution

- **White-label embedding** — Luzmo's primary use case. Dashboards embed in customer-facing apps via iframe or SDK.
- **SSO** — OIDC, SAML, JWT-based auth for embed contexts.
- **Scheduled delivery** — email the dashboard as PDF or PNG on a schedule.
- **Data export** — CSV / XLSX export from any chart (can be disabled per-widget).
- **Public / unlisted share links** — for internal demos.
- **Luzmo API** — create, update, delete datasets and dashboards programmatically; push data to datasets via API.

---

## 5. Styling and theming

- **Theme editor** — colors, fonts, spacing. Themes apply dashboard-wide.
- **Per-widget overrides** — color palettes, number formats, axis labels.
- **Responsive layout** — drag-to-resize grid with breakpoints for desktop, tablet, mobile.
- **Custom fonts** — upload brand fonts.

---

## 6. Real-time and freshness

- **Cache control per dataset** — pick a refresh cadence (5 min, 1 hr, 6 hr, daily, etc.) or "live" (no cache).
- **Incremental refresh** — for large datasets, define an incremental partition column.
- **Push data via API** — append rows without a full dataset rebuild.

---

## 7. Governance

- **Folders / collections** — organise dashboards by team or client.
- **User roles** — viewer, editor, owner.
- **Version history** — dashboard edits are versioned; revert to any prior version.
- **Audit log** — who changed what, when.

---

## 8. What Luzmo is especially good at (for Forge builds)

- Embedded, multi-tenant dashboards where the same dashboard is parameterised by `account_id` / `brand_id` and served to different customers.
- Self-service exploration within a curated dataset — parameters + filters + drill cover ~90% of ad-hoc question patterns without a new build.
- Fast builds of standard chart patterns (KPI strip + bar + table + filter bar). A Rebill Tracker-style dashboard can be stood up in a day if the Datasets 1 and 2 (see `accounts/<client>/rebill_tracker_business_context.md`) are already shaped.
- Dashboards that need to be white-label embedded in a Homefield or Hexclad customer portal.

---

## 9. Connectors supported

BigQuery, Snowflake, Redshift, Postgres, MySQL, MariaDB, SQL Server, Oracle, Athena, Databricks, MongoDB, Elasticsearch, Google Sheets, Google Analytics, Salesforce, HubSpot, Stripe, Shopify (via partner), S3, REST API, static file (CSV / XLSX / JSON). This list changes — check the Luzmo integrations page for the current set before promising a connector to a client.

---

*See also: `tools/luzmo/limitations.md` for what Luzmo cannot do, and `tools/luzmo/prompt_library.md` for the Rebill Tracker build prompt.*
