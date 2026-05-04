# Tech Spec Patch — TEMP-001 — CR-01 — v2.0
**Date:** 2026-03-04  

## What changed (Summary)
- Dropped `company_id` from the `vehicles` DB table.
- Added `affiliated_company_id` to the `users` DB table.
- Updated API contracts to reflect the DB change, adding a backward compatibility proxy for legacy mobile versions.

## Contract changes
- Endpoints impacted:
  - `GET /vehicles` — change: No longer returns native `company_id` in V2. BFF injects it for V1 legacy apps.
  - `GET /users/me` — change: Added `affiliated_company_id`.
  - `PUT /users/{id}/company` — change: New endpoint for Admin assignment.
- Schema changes:
  - `vehicles.company_id`: before [UUID] → after [DELETED]
  - `users.affiliated_company_id`: before [N/A] → after [UUID]

## Backward compatibility / migration
- Run migration script: `migrate_company_to_user.sql`
- Mobile team confirmed V1 app will not crash if `company_id` relies on the injected fallback logic.

## Dev/QA checklist
- [ ] Update backend migrations and ORM entities.
- [ ] Update frontend vehicle list and Admin user detail views.
- [ ] Regression tests on legacy mobile endpoint V1.
