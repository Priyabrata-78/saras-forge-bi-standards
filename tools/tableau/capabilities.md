# Tableau — Capabilities

What Tableau can do in a Forge BI build. Reference when deciding if Tableau is the right tool for a Homefield / Hexclad / other-account dashboard.

---

## 1. Chart types and visual vocabulary

Tableau is the most visual-forward of the three tools in the Forge stack. Native chart support includes:

- **Bar / Column** — single, stacked, dual, grouped, side-by-side. Dynamic reference bands.
- **Line / Area** — single and multi-series. Forecast (built-in) and trend-line fitting.
- **Pie / Donut** — including dual-axis donut with KPI in the centre.
- **Scatter / Bubble** — with trend line, density shading, and clustering.
- **Heatmap / Highlight Table** — excellent for cohort retention grids.
- **Treemap / Packed Bubbles** — hierarchical proportion.
- **Map (filled, symbol, point, polygon)** — world, US, custom territories, plus any Mapbox tiles.
- **Gantt** — for event-timeline views.
- **Sparklines / small multiples** — native "Trellis" / small-multiples grid.
- **Box-and-whisker, histograms, distribution plots**.
- **Waterfall, bullet, funnel** — via reference marks and dual-axis tricks.
- **Sankey** — native diagram with multi-level flows.
- **Radial / custom shapes** — via polygon mark types.
- **Custom marks / shape files** — upload bespoke glyphs.

Tableau's dual-axis and calculation-driven reference bands make it easier than Luzmo or Power BI to build highly customised retention and cohort visuals.

---

## 2. Interactivity

- **Actions** — the core interactivity primitive. Three flavours:
  - **Filter actions** — click a mark to filter downstream worksheets.
  - **Highlight actions** — click a mark to highlight corresponding marks on other worksheets (without filtering).
  - **URL / Go to Sheet actions** — navigate to another dashboard or external URL, passing parameters.
- **Parameters** — user-controllable values (number, date, string, boolean). Parameters drive calculated fields, reference lines, and titles.
- **Parameter actions** — a mark click sets a parameter value, enabling dynamic "click to select" interactions.
- **Set actions** — a mark click adds / removes from a set, enabling dynamic groupings.
- **Filters** — global, per-worksheet, per-data-source, context filters. Filter types: single / multi-select, date range, relative date, wildcard text, slider.
- **Tooltips** — rich "viz in tooltip" (embed a mini-chart inside a tooltip).
- **Dashboard navigation** — button-based navigation between dashboards in a workbook.
- **Containers (horizontal, vertical, floating)** — responsive layout primitives.

---

## 3. Data modelling and calculations

- **Data source layer** — connect to one or many sources. Tableau's "logical layer" (1 to many table relationships) supports multi-fact semantic models without pre-joining.
- **Level of Detail (LOD) expressions** — `{ FIXED / INCLUDE / EXCLUDE }` — Tableau's superpower for cohort analysis. Example: `{ FIXED [subscription_id] : MIN([created_at]) }` to pin per-subscription attributes regardless of filters.
- **Table Calculations** — running sums, moving averages, percent of total, rank, window functions. Calculated at render time, relative to the partition / addressing.
- **Calculated Fields** — standard expressions (arithmetic, conditional, string, date, aggregate).
- **Parameters in calcs** — plug parameters into any calculation to make KPIs user-controllable.
- **Data blending** — secondary data source linked on common dimensions (for tables that can't be joined).
- **Custom SQL / Initial SQL** — pre-process at the data source level.

---

## 4. Embedding and distribution

- **Tableau Server / Tableau Cloud** — the hosting / publishing target.
- **Embedded Analytics SDK** — iframe embed + JS API. Row-level security via `User Filters` or trusted authentication tickets.
- **Subscriptions** — scheduled PDF or image email delivery.
- **Alerts** — data-driven (e.g. "email me when rebill rate < 50%").
- **Mobile apps (iOS, Android)** — first-class responsive rendering.
- **Data-driven alerts** and **Metrics** (Pulse).

---

## 5. Styling and theming

- **Workbook-level formatting** — fonts, colours, lines, shading.
- **Dashboard layouts** — fixed-size, automatic, or responsive (via device-specific layouts).
- **Formatting panel per worksheet** — override any default.
- **Custom colour palettes** — sequential, diverging, categorical, regular colour.
- **Custom fonts** — system fonts on Server; the end-user must have the font installed for Desktop.

---

## 6. Real-time and freshness

- **Live connection** — queries run against the source on every render.
- **Extract** — `.hyper` file, Tableau's columnar cache. Refresh on schedule (hourly, daily, etc.).
- **Incremental extract refresh** — append rows without a full rebuild.
- **Hyper API** — push data into extracts programmatically.

---

## 7. Governance

- **Projects / sub-projects** — folder structure on Server / Cloud.
- **Permissions** — per-project, per-workbook, per-data-source.
- **Lineage** — see which workbooks depend on which data sources.
- **Version history** — workbook revisions retained for rollback.
- **Certified data sources** — tag authoritative sources to guide analysts.

---

## 8. What Tableau is especially good at (for Forge builds)

- **Cohort retention visualisations** — heatmaps with LODs, dynamic reference bands, layered dual-axis charts.
- **Ad-hoc exploration** — analysts can drag-and-drop new views over a certified data source.
- **Story points** — native narrative-flow feature where a single workbook walks the reader through a sequence of viz states.
- **Complex calculated fields** — LOD + Table Calc combined is the most powerful calculation environment in the three tools.
- **Print / PDF-ready layouts** — device-specific dashboard layouts let you build a "boardroom-ready" PDF alongside the interactive web view.

---

## 9. Connectors supported

Native connectors: BigQuery, Snowflake, Redshift, Postgres, MySQL, SQL Server, Oracle, Athena, Databricks, Teradata, Salesforce, Google Sheets, Google Analytics, HubSpot, Marketo, Workday, Amazon S3 (via Athena), SharePoint, OData, Web Data Connector (custom REST). Plus file-based: Excel, CSV, JSON, PDF (tables), statistical formats (SAS, SPSS, R). The Tableau Exchange hosts additional partner connectors.

---

*See also: `tools/tableau/limitations.md` for what Tableau cannot do, and `tools/tableau/prompt_library.md` for the Rebill Tracker build prompt.*
