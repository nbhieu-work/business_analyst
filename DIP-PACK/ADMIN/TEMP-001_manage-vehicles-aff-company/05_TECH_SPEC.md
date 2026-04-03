Feature: TEMP-001 manage-vehicles-aff-company
Version: v2.0
Last updated: 2026-03-04
Change request: CR-01

# Technical Specification

## Overview
This tech spec outlines the data structural change to move Affiliate Company association from individual Vehicle records to the User profile level.

## Motivation
Current state -> User registers vehicles, each vehicle maps to an affiliate company.
Problems -> Requires redundant UI selection, complicates billing aggregation per user, risk of user having vehicles with conflicting affiliate companies.
Desired state -> User is universally associated with one Affiliate Company. All owned vehicles fall under this association.

## Research Findings
- Using a background migration script resolving conflicts by checking the most recent vehicle registration is the safest approach for legacy data handling.

## Design Decisions
1. **Drop `company_id` from Vehicle table:** True normalization, avoids data anomalies.
2. **Add `affiliated_company_id` to User table:** Centralizes billing and logic.
3. **DTO Backward Compatibility Layer:** Older mobile app versions parse `company` on the `GET /vehicles` endpoints. The Backend BFF will intercept `GET /vehicles` and append the user's `affiliated_company_id` to each vehicle object.

## API Contract draft
- `GET /users/me` 
  - **NEW Response Field:** `"affiliated_company_id": "uuid"`
- `GET /vehicles`
  - **REMOVED Field (V2 API):** `"company_id"`
  - **V1 Proxy (Legacy App):** Continues to return `"company_id"` by pulling `user.affiliated_company_id`.
- `PUT /users/{id}/company`
  - **NEW Endpoint:** For Admins to assign a company to a user.

## Data Model outline
- **User Entity:** `id` (UUID, PK), `affiliated_company_id` (UUID, FK to AffiliateCompany, nullable).
- **Vehicle Entity:** `id` (UUID, PK), `user_id` (UUID, FK to User), ~~`company_id`~~ (Removed).

## AuthN/AuthZ mapping
- Admin Role `CAN_UPDATE_USER_COMPANY`.

## Implementation Plan
1. **Phase 1 (DB Migration):** Add `affiliated_company_id` to Users. Create script to populate this based on existing vehicle data. Drop `company_id` from Vehicles.
2. **Phase 2 (API V1 Proxy):** Ensure BFF handles V1 backward compatibility for `GET /vehicles`.
3. **Phase 3 (API V2 & UI):** Expose `company_id` on User object. Update frontend "Manage Vehicles" list to remove the column. Add company dropdown to Admin Edit User page.

## Edge Cases
- **EC-02:** Migration conflicts handled by logging anomalies and accepting the latest vehicle's company.

## Success Criteria
- [ ] DB Migration completes with zero data loss.
- [ ] V1 Mobile App continues to function without crashing (verified by proxy data).
- [ ] V2 Web App shows vehicles list cleanly without company column.

## Traceability
- Data Model -> BR-01, BR-03
- API Proxy -> NFR-02
