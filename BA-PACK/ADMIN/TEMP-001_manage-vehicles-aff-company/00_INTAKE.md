Feature: TEMP-001 manage-vehicles-aff-company
Version: v2.0
Last updated: 2026-03-04
Change request: CR-01

# Intake

## A. Problem Statement
The application previously linked Affiliate Companies directly to Vehicles. However, the business reality dictates that a User has exactly one Affiliate Company, which applies across all their vehicles. We need to refactor the data model and UI to reflect this user-level mapping and remove the vehicle-level mapping.

## B. Goals & Success Metrics
- Refactor the data relationship so AffiliateCompany belongs to the User, not the Vehicle.
- Update the "Manage your vehicles" UI to remove company information per vehicle.
- Ensure existing vehicle and plan listings continue to work seamlessly (backward compatibility).

## C. Actors/Personas
- Driver/User: Can manage multiple vehicles but belongs to exactly one Affiliate Company at a time.

## D. Scope In / Scope Out
- **Scope In:** Updating User data model, updating Vehicle data model, updating "Manage your vehicles" list UI, updating vehicle detail displays and forms.
- **Scope Out:** Changes to how Plans are managed or assigned. Changes to the Admin flow of Affiliate Company creation.

## E. Constraints
- Keep existing vehicles and plans list behavior unchanged except for removing the vehicle.company display.
- Must preserve backward compatibility/migration notes if old data exists.

## F. Known Systems & Dependencies
- User Service / Database
- Vehicle Service / Database
- Frontend "Manage your vehicles" Screens (Mobile/Web)

## G. Risks & Assumptions
- Risk: Old vehicle records might have company IDs that contradict the user's new single company ID. We must define a migration strategy.
- Assumption: The user interface will display the user's global Affiliate Company elsewhere (e.g., Profile or Dashboard header), so it doesn't need to be forced into the individual vehicle items list.

## H. Open Questions
1. How should we migrate existing (Vehicle -> Company) data to the User level? Does the user inherit the company from their first/oldest vehicle?
2. If a user previously had 2 vehicles assigned to 2 different companies, which one becomes the User's primary single company?

## I. Glossary
- **Affiliate Company:** The partner organization the user is associated with.
- **Vehicle:** A car registered and managed by the user.

## J. Confidence
- High. The data model change perfectly aligns with the UI simplification requirement. Only the migration of legacy data requires careful technical handling.
