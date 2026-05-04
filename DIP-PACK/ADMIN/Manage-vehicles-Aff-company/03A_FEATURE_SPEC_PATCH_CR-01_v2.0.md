# Feature Spec Patch — TEMP-001 — CR-01 — v2.0
**Date:** 2026-03-04  
**Requested by:** Denny  
**Reason:** Data model change: affiliate company is mapped only to user, not vehicle.

## What changed (Summary)
- Removed Affiliate Company details from the Vehicle list view UI.
- Shifted the Affiliate Company assignment from the Vehicle domain to the User domain.

## Spec sections changed
- **Section:** User Stories
  - **Before:** As a Driver, I want to assign a company to my vehicle...
  - **After:** As an Admin, I want to assign an Affiliate Company directly to a User profile...
- **Section:** Acceptance Criteria (US-01)
  - **Before:** ...And I see the Affiliate Company attached to each vehicle.
  - **After:** ...And I DO NOT see any Affiliate Company name attached to individual vehicles.

## Impacted items
- User Stories: US-01, US-02
- Business Rules: BR-01, BR-03, BR-04
- Edge Cases: EC-02
- Data Elements: User.affiliated_company_id, Vehicle.company_id (deleted)
- NFR: NFR-02 (API Backward compatibility)

## Dev action checklist
- [ ] Update frontend vehicle list component to remove company column.
- [ ] Ensure mobile UI handles the removed field gracefully.
