Feature: TEMP-001 manage-vehicles-aff-company
Version: v2.0
Last updated: 2026-03-04
Change request: CR-01

# Dev Handoff Pack

## A. TL;DR
Refactor the data model to move the Affiliate Company association from individual Vehicles to the underlying User profile. Update the UI to match this logical transition.

## B. What changed since last version (CR-01)
- See `03A_FEATURE_SPEC_PATCH_CR-01_v2.0.md` and `05A_TECH_SPEC_PATCH_CR-01_v2.0.md` for specific deltas.
- **Key takeaway:** `company_id` is removed from `vehicles` table and `affiliated_company_id` is added to `users` table.

## C. In-scope / Out-of-scope
- **In-scope:** DB migration script, BFF proxy layer for V1 mobile backward compatibility, web UI updates for "Manage your vehicles" list and Admin User edit screens.
- **Out-scope:** Any changes to how Plans are assigned to vehicles.

## D. Key assumptions + decisions
- Migration script handles legacy users with conflicting vehicle companies by picking the most recently registered vehicle's company and assigning it to the User profile. Anomalies will be manually fixed by Admins later.

## E. Must-have User Stories list
- **US-01:** Driver views vehicles list (no company column).
- **US-02:** Admin assigns an Affiliate Company to a User profile.

## F. Endpoints summary
- `GET /vehicles` (V2 removes company object; V1 BFF proxy injects user's company).
- `GET /users/me` (Returns `affiliated_company_id`).
- `PUT /users/{id}/company` (New Admin endpoint).

## G. Data fields summary
- `User.affiliated_company_id` [UUID, Nullable]
- `Vehicle.company_id` [DELETED]

## H. State transitions summary
- N/A

## I. Permission matrix summary
- **Admin:** `CAN_UPDATE_USER_COMPANY` required for `PUT /users/{id}/company`.

## J. Test checklist
- [ ] AC: Driver sees vehicles and plans, NO company column.
- [ ] AC: Admin successfully updates User's affiliated company.
- [ ] Smoke: Legacy Mobile App `GET /vehicles` continues to work (receives fallback company data injected from User record).

## K. Open questions for sprint planning
- Who is responsible for writing the SQL migration script (Backend Eng vs Database Engineer)?
