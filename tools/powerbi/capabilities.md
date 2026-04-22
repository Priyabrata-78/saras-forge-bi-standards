# Power BI — Capabilities

What Power BI can do in a Forge BI build. Reference when deciding if Power BI is the right tool for a Homefield / Hexclad / other-account dashboard, especially in Microsoft-first shops.

---

## 1. Chart types and visual vocabulary

Native visuals in Power BI Desktop:

- **Bar / Column** — clustered, stacked, 100% stacked, horizontal and vertical.
- **Line, Area, Ribbon** — multi-series, dual-axis, forecasting.
- **Combo (line + column)** — for dual-metric views like "revenue (bar) + margin (line)".
- **Pie, Donut** — with dynamic slice highlighting.
- **Scatter, Bubble** — with Play Axis animation on a date dimension.
- **Treemap, Funnel, Waterfall** — for hierarchical and sequence analysis.
- **Matrix / Table** — Power BI's crosstabs. Hierarchy collapsing, conditional formatting, in-cell data bars and colour scales.
- **Card, KPI, Multi-row card** — KPI tiles with status indicators (up / down / flat).
- **Gauge** — for goal-vs-actual.
- **Decomposition tree (AI visual)** — drill into a measure by any dimension.
- **Key Influencers (AI visual)** — auto-find what drives a metric.
- **Q&A visual** — natural-language question input.
- **Map (ArcGIS, Bing, filled, shape)** — geographic.
- **Slicer** — Power BI's filter widget. Types: list, dropdown, between (range), relative date, hierarchy.
- **Custom visuals from AppSource** — 300+ third-party visuals (Sankey, small multiples, bullet, chord, radar, etc.).
- **R / Python visual** — embed any ggplot2 / matplotlib / plotly chart.

---

## 2. Interactivity

- **Cross-filter / Cross-highlight** — clicking a data point on any visual filters or highlights all other visuals on the page. Behaviour configurable per visual pair.
- **Slicers** — filter widgets; sync slicers across pages.
- **Drill-through** — right-click a data point to navigate to a dedicated detail page with context carried over.
- **Drill-down in hierarchies** — year → quarter → month → day.
- **Bookmarks** — save a specific filter + view state and reapply with a click. Useful for "story" flows and toggling between scenarios.
- **Buttons, Actions, Page navigation** — custom button visuals with on-click actions (navigate to page, apply bookmark, set parameter, etc.).
- **Field parameters** — user-swap which measure or dimension a visual uses.
- **What-if parameters** — numeric sliders that feed measures for scenario analysis.
- **Tooltips (custom page tooltips)** — an entire Power BI page can be used as a tooltip for another visual.

---

## 3. Data modelling and calculations

- **Power Query (M)** — ETL layer. Transform columns, filter rows, join tables, pivot / unpivot. All transformations are visual and also editable as code.
- **Star / Snowflake model** — Power BI is optimised for dimensional models. Build fact tables with foreign keys to dimension tables.
- **DAX (Data Analysis Expressions)** — the calculation language. Handles aggregations, time intelligence, row-context vs filter-context, and complex measure logic.
- **Calculated columns** — row-level calculations at the model layer.
- **Measures** — aggregation expressions (e.g. `Rebill Rate % := DIVIDE(SUM(rebilled_at_least_once), SUM(total_subs))`). Computed at query time.
- **Time intelligence** — `SAMEPERIODLASTYEAR`, `DATEADD`, `DATESYTD` — native DAX functions for period-over-period analysis.
- **Calculation groups** — reusable sets of measure variants (e.g. one calc group for YTD / MTD / QTD across every measure).
- **Relationships** — single / bi-directional; many-to-one, one-to-one, many-to-many.
- **Aggregations (for large models)** — pre-built summary tables that Power BI transparently swaps in for sub-second performance.
- **Row-Level Security (RLS)** — role-based filters; `USERPRINCIPALNAME()` drives dynamic user-based scoping.

---

## 4. Embedding and distribution

- **Power BI Service (SaaS)** — the primary hosting target.
- **Power BI Embedded (Azure)** — embed dashboards into a customer-facing SaaS product with A-SKU capacity.
- **Power BI Report Server (on-prem)** — for fully on-prem installs.
- **Apps** — curated bundles of reports + dashboards distributed to groups of users.
- **Subscriptions** — scheduled email delivery (PDF, PPT, link).
- **Export to Excel / CSV / PPT / PDF** — from any visual or the whole report.
- **Mobile app (iOS, Android, Windows)** — device-specific responsive layouts.
- **Microsoft Teams integration** — embed reports in Teams tabs.

---

## 5. Styling and theming

- **JSON theme files** — declarative theme with palette, fonts, default visual formatting.
- **Per-visual formatting pane** — override anything.
- **Custom fonts** — any font installed on the publishing and viewing endpoint.
- **Backgrounds, images, shapes** — full control of the canvas.
- **Device-specific mobile layout** — drag the subset of visuals you want on mobile into a separate mobile canvas.

---

## 6. Real-time and freshness

- **Import mode** — data is loaded into the Power BI engine (VertiPaq). Refresh on schedule (up to 8 times/day on Pro; 48 times/day on Premium).
- **DirectQuery** — queries push down to the source in real time.
- **Composite models** — mix Import and DirectQuery tables in one dataset.
- **Streaming datasets** — real-time push API for live-tile dashboards.
- **Automatic page refresh** — DirectQuery pages can auto-refresh as frequently as every second (on Premium).
- **Dataflows** — shared Power Query layer for ETL reuse across reports.

---

## 7. Governance

- **Workspaces** — collaboration containers. Each workspace maps to a deployment environment (Dev / Test / Prod).
- **Deployment pipelines** — promote content between Dev → Test → Prod with parameter swapping.
- **Sensitivity labels (Microsoft Purview)** — classify reports for DLP enforcement.
- **Usage metrics** — built-in "who viewed what, when".
- **Lineage view** — see how a report depends on datasets and source systems.
- **Endorsements** — promote / certify datasets and reports.

---

## 8. What Power BI is especially good at (for Forge builds)

- **Microsoft-first shops** — seamless with Excel, Azure AD, Teams, SharePoint, Dynamics.
- **Time-intelligence heavy dashboards** — DAX's date functions make rolling-window, period-over-period, and YTD calculations easy.
- **Star-schema modelling with large data volumes** — VertiPaq compression is industry-leading; 1B-row fact tables are workable on Premium capacity.
- **Self-service by finance and ops teams** — Excel-muscle-memory users pick up Power BI faster than Tableau.
- **Enterprise deployment pipelines** — Power BI's Dev / Test / Prod workflow is the most mature of the three tools.
- **AI visuals** — Decomposition Tree and Key Influencers are genuinely useful "show me why this metric moved" tools.

---

## 9. Connectors supported

Hundreds of native connectors across databases (SQL Server, Oracle, Postgres, MySQL, BigQuery, Snowflake, Redshift, Synapse), SaaS apps (Salesforce, Dynamics, HubSpot, Marketo, QuickBooks), file formats (Excel, CSV, XML, JSON, PDF), big data (Spark, Hive, Hadoop, Azure Data Lake), and enterprise systems (SAP HANA, SAP BW, Teradata, IBM Db2). Plus ODBC / OData / Web generic connectors.

---

*See also: `tools/powerbi/limitations.md` for what Power BI cannot do, and `tools/powerbi/prompt_library.md` for the Rebill Tracker build prompt.*
