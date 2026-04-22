# Forge Universal Build Checklist

The universal checklist every BA/BI runs before a dashboard goes to Go Green.

> **Stub — to be filled in.** See the Forge GitHub Repository Structure doc in ClickUp for the v1 item list.

## Before You Build
- [ ] AIDE files complete for this account (Definition of Done met)
- [ ] BI tool profile exists in Forge GitHub
- [ ] You have identified ≥3 prompts from the tool's Forge prompt library to use

## During the Build
- [ ] All metrics traced back to AIDE's `3_KPI_FORMULAS.md` (no BI-layer-only calculations)
- [ ] All data connections point to production BQ datasets (not raw or staging)
- [ ] Dashboard includes a data freshness indicator visible to the end user
- [ ] Calculation translation protocol followed: BQ formula → Forge prompt → BI formula

## Before Go Green
- [ ] ≥1 metric cross-validated: BQ formula output = BI-layer formula output
- [ ] No limitations discovered during build that aren't already in the Limitations Register
- [ ] Delivery Notes written and stored in `accounts/[account-name]/DELIVERY_NOTES.md`

## At Go Green
- [ ] Pod Lead confirms Forge checklist was followed (not just the dashboard reviewed)
