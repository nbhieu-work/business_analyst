# TEMP-001 — Change Log

## Current Version
- **Version:** v2.0
- **As of:** 2026-03-04
- **Status:** Draft
- **Baseline source:** Intake requirements and user context

## Change Requests
### CR-01 — 2026-03-04 — Refactor Affiliate Company Mapping to User
- **Version:** v2.0
- **Requested by:** Denny
- **Reason:** Data model change: affiliate company is mapped only to user, not vehicle.
- **Summary of changes:**
  - Data relationship changed from (Vehicle → AffiliateCompany) to (User → AffiliateCompany).
  - UI list: remove company column/row from each vehicle item in "Manage your vehicles".
  - Any vehicle-detail fields/forms referencing company are removed.
- **Impacted files:**
  - 02_EXTRACTION.md
  - 03_FEATURE_SPEC.md
  - 05_TECH_SPEC.md
  - 06_DEV_HANDOFF_PACK.md
- **Artifacts:**
  - 03A_FEATURE_SPEC_PATCH_CR-01_v2.0.md
  - 04B_IMPACT_ANALYSIS_CR-01_v2.0.md
  - 05A_TECH_SPEC_PATCH_CR-01_v2.0.md
- **Git reference:** N/A
- **Notes/Decisions:** Breaking change in data contract (API & DB), so version bumps from v1.0 to v2.0.
