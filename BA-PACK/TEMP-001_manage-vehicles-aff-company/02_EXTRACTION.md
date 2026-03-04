Feature: TEMP-001 manage-vehicles-aff-company
Version: v2.0
Last updated: 2026-03-04
Change request: CR-01

# Extraction

## A. Business Rules
- **BR-01:** A User belongs to exactly zero or one Affiliate Company.
- **BR-02:** A Vehicle belongs to exactly one User.
- **BR-03:** A Vehicle does not have a direct relationship with an Affiliate Company.
- **BR-04:** The "Manage your vehicles" list must display the user's vehicles and their associated plans, without displaying company information per vehicle.

## B. Edge Cases
- **EC-01:** User has vehicles but no assigned Affiliate Company. (System should display vehicles normally; company is null/empty at the user level).
- **EC-02:** Legacy data migration: User has multiple vehicles with conflicting company IDs. (System applies fallback logic: most recent vehicle's company wins, others are logged for manual review).

## C. Data Elements
| Name | Type | Validation | Source of Truth | Nullable? | Default | Notes |
|---|---|---|---|---|---|---|
| User.affiliated_company_id | UUID | Must exist in AffiliateCompany table | User DB | Yes | Null | Moved from Vehicle to User in v2.0 |
| Vehicle.company_id | UUID | N/A | N/A | N/A | N/A | **[DELETED in v2.0]** Column removed |

## D. State Model
- N/A (No changes to vehicle or user state lifecycle due to this CR)

## E. Permissions Matrix
- **Driver:** Can view and manage their own vehicles.
- **Admin:** Can update a User's `affiliated_company_id`.

## F. Non-functional Requirements
- **NFR-01:** Data migration script must run without locking the entire User table for an extended period (batching recommended).
- **NFR-02:** Backward compatibility: Mobile app older versions that expect `vehicle.company` in the API might need a backward-compatible proxy/DTO layer that injects the `user.affiliated_company_id` into the vehicle response temporarily.

## G. Traceability
- **BR-01, BR-03, DE (User.affiliated_company_id)** -> Intake Problem Statement
- **BR-04** -> Intake Goals
