# Power BI — Limitations

Known constraints and gotchas. Read before scoping a Forge build in Power BI.

---

## 1. Chart and visual limitations

- **Limited native layout control.** Power BI's canvas is pixel-positioned; there's no CSS flex / grid. Responsive behaviour across screen sizes is weaker than Luzmo.
- **Custom visuals carry supply-chain risk.** AppSource visuals are third-party; some have poor security postures or get deprecated. Prefer Microsoft-certified visuals only.
- **Small-multiples are limited.** Native small-multiples (introduced in 2021) work but do not support mixed chart types. For rich small-multiples, use the R / Python visual.
- **Conditional formatting in matrix is flexible but UI-heavy to configure.** Setting up colour scales, data bars, and icons per-measure is clicky.
- **Legend and label positioning controls are less flexible than Tableau.** Fine adjustments often require hacks.
- **Visual load order is not controllable.** If you want certain visuals to render before others on a slow report, you can't prioritise.

---

## 2. Filter and slicer limitations

- **Slicer interactions are page-level by default.** Cross-page sync requires explicit "Sync Slicers" setup per slicer. Easy to forget.
- **Drill-through requires a dedicated destination page.** Unlike Tableau's action-driven navigation, Power BI's drill-through is a named landing page.
- **No multi-value parameters.** Field parameters allow swapping which field a visual uses, but not passing a user-selected multi-value set to a measure directly.
- **Top-N filters re-evaluate on every filter change.** Can be slow on large datasets.
- **The "between" slicer for dates defaults to showing a slider bar that is hard to drag precisely.** Users often prefer two date-pickers — swap via the slicer's visual settings.

---

## 3. DAX and calculation limitations

- **DAX has a steep learning curve.** Filter context, row context, and context transition are notoriously tricky. A minor formula mistake (e.g. wrapping `SUM` inside a `CALCULATE` without an explicit filter) can produce wrong numbers silently.
- **Regex is not native to DAX.** String manipulation uses `SEARCH`, `FIND`, `SUBSTITUTE`, `LEFT`, `RIGHT`, `LEN`. For regex-based classification (e.g. cancel reason bucketing), do it in Power Query (M) or upstream.
- **Nested CALCULATE and iterator functions (SUMX, AVERAGEX) can be slow.** Optimisation requires profiling via DAX Studio / Performance Analyzer.
- **No native percentile-per-group.** DAX has `PERCENTILE.INC` but computing P95 per cohort requires iterator functions that are slow at scale.
- **Semi-additive measures require explicit handling.** Measures like "active subscriptions at a point in time" don't sum across dates — use `LASTDATE` / `OPENINGBALANCEMONTH` patterns.

---

## 4. Data volume and performance

- **Import-mode dataset size is capped by SKU.** Pro caps at 1GB per dataset; Premium Per User caps at 100GB; Premium capacity raises further. Homefield-scale data (millions of subscriptions) is fine but a 100M+ event table may need composite-mode or aggregations.
- **DirectQuery performance depends entirely on the source.** A live BigQuery dashboard with 10 visuals hitting a slow slot pool is slow for users.
- **Scheduled refresh failures are common.** Gateways, credentials, row counts, query timeouts — many moving parts. Set up refresh monitoring / alerts.
- **Bi-directional relationships can cause performance issues and circular logic.** Use single-direction relationships wherever possible.

---

## 5. Embedding and auth

- **Power BI Embedded (A-SKU) is expensive at scale.** Per-capacity pricing; even the lowest tier can be $700+/month.
- **Embedding requires an Azure AD service principal** and token exchange. Not trivial to set up.
- **Row-Level Security via Azure AD groups** works well for internal users; embedded scenarios need explicit token-based identity.
- **Mobile rendering is layout-specific.** Visuals that look great on desktop may overflow on mobile — build a phone layout explicitly.

---

## 6. Collaboration and governance

- **Versioning is workspace-level, not report-level.** Dev / Test / Prod via deployment pipelines is the prescribed pattern. Ad-hoc versioning requires naming conventions.
- **Commenting is basic** compared to Tableau Cloud. Users often comment in Teams instead.
- **External sharing** requires Azure AD B2B guest user setup.
- **Dataset size and performance visibility** is buried in admin portals; individual authors don't see gateway refresh queues.

---

## 7. Specific Rebill-Tracker-context limitations

- **Snapshot history requires a dedicated table** (same as Luzmo and Tableau). Power BI Import mode can load it and build a trend line per cohort; DirectQuery mode queries the source view per render.
- **Cancel reason bucketing must be done in Power Query or upstream.** DAX doesn't have regex, so the 10-bucket taxonomy is best applied in the source view.
- **Save-events overlap footnote** — Power BI supports page-level captions and text boxes. Add the overlap warning below the Subscription Events visual; otherwise easy to forget.
- **Subscription Detail pagination.** Power BI tables / matrices don't paginate in the Tableau crosstab sense — they virtualise scroll. For 10K+ rows, users will scroll rather than page. For a 14-column, 300-row table this is fine; for larger volumes consider a drill-through to a "sub detail" page.

---

## 8. When Power BI is the wrong choice

- **Highly customised visuals.** Tableau's LOD + dual-axis tricks produce viz that Power BI cannot match without R / Python custom visuals.
- **Non-Microsoft-first organisations.** If the client has no Microsoft footprint (no Office 365, no Azure AD), Power BI's licensing and auth setup is a heavier lift than Luzmo.
- **Customer-facing multi-tenant embedded analytics at scale.** Possible via Power BI Embedded but often more expensive per viewer than Luzmo.
- **Rapid prototyping with non-technical stakeholders.** Power BI's DAX and data modelling require training; Luzmo's drag-and-drop is faster for ad-hoc exploration.

---

*See also: `tools/powerbi/capabilities.md` for what Power BI can do, and `tools/powerbi/prompt_library.md` for the Rebill Tracker build prompt.*
