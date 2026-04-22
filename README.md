# saras-forge-bi-standards

**Forge — BI Development Standardisation** repo for Saras Analytics Consulting. Tool profiles, prompt libraries, limitation registers, and cross-tool build standards.

> **Stub — to be filled in.** The folder structure is live; content for each file will follow.

## What Forge is

The canonical BI development operating system for Saras Consulting. Forge reads from AIDE's business logic layer and governs everything from there to the client-facing dashboard.

## Repository Structure

```
saras-forge-bi-standards/
├── README.md                       ← You are here
├── CHANGELOG.md                    ← Version history across all profiles and standards
├── CONTRIBUTING.md                 ← How to submit a prompt, limitation, or standard
│
├── universal/                      ← Rules that apply regardless of BI tool
│   ├── BUILD_CHECKLIST.md
│   ├── CROSS_TOOL_STANDARDS.md
│   └── CALCULATION_TRANSLATION.md
│
├── tools/                          ← One profile per BI tool in use at Saras
│   ├── tableau/
│   ├── powerbi/
│   ├── luzmo/
│   └── _template/                  ← Copy when adding a new BI tool
│       ├── PROFILE.md
│       ├── PROMPT_LIBRARY.md
│       ├── LIMITATIONS.md
│       └── CHANGELOG.md
│
└── accounts/                       ← Per-account Forge delivery notes
    ├── _template/
    │   └── DELIVERY_NOTES.md
    └── hexclad/
        └── DELIVERY_NOTES.md
```

## Versioning

| Trigger | Version bump |
| --- | --- |
| New validated prompt added | Minor |
| New limitation documented | Minor |
| Build guideline updated | Minor |
| Cross-tool standard changed | Minor |
| New BI tool profile published | Major |
| Forge structure itself changes | Major |

Version history is never deleted.

## Forge Maintainer

Priyabrata (current maintainer).

## Related

- **AIDE** — the business logic layer Forge depends on.
- **Alfred** — the automated validation layer.
- Slack: `#forge-bi-standards` (TBC)

---

_Version 1.0 — April 2026 — Saras Analytics Consulting_
