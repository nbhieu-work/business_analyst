# Impact Analysis — TEMP-001 — CR-01 — v2.0
**Date:** 2026-03-04  
**Impact level:** High

## Change summary
Moving the `company_id` association from the Vehicle table to the User table to reflect accurate business reality where a user solely belongs to one Affiliate Company across all their registered vehicles. 

## Impact areas
- **Business rules impacted:** BR-01, BR-03 (new constraints on cardinality).
- **Data impacted:** `User.affiliated_company_id` (added); `Vehicle.company_id` (dropped/deprecated).
- **State transitions impacted:** None.
- **Permissions impacted:** Admin user management profile needs to include Company assignment.
- **API/contract impacted:** APIs returning vehicle lists will no longer naturally include company info per vehicle.
- **UX/flow impacted:** "Manage your vehicles" screen drops the company column/details. Admin "Edit User" screen adds the company dropdown.

## Risks introduced & mitigations
- **Risk:** Existing legacy apps crash if `vehicle.company` is null or missing in JSON response.
- **Mitigation:** Introduce a backward-compatibility layer in the BFF (Backend for Frontend) or controller to inject the user's `affiliated_company_id` into each vehicle's JSON response temporarily until forced update.
- **Risk:** Data loss during migration of users with vehicles spanning conflicting companies.
- **Mitigation:** Migration Script uses the most recently active vehicle's company as the source of truth, creating logs for anomalies to be adjusted manually via the Admin dashboard.

## Regression scope (must test)
- "Manage your vehicles" list screen (Mobile App & Web Platform).
- Admin User Profile editing screen.
- E2E flow for driver registering a new vehicle (should no longer prompt for an Affiliate Company selection).
