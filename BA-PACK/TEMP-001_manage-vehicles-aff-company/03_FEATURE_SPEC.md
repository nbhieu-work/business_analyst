Feature: TEMP-001 manage-vehicles-aff-company
Version: v2.0
Last updated: 2026-03-04
Change request: CR-01

# Feature Specification

## Overview
This specification covers the "Manage your vehicles" feature under the new data model where the Affiliate Company is tied to the User rather than individual Vehicles.

## Proto-persona
- **Driver:** Wants to see all their registered cars and active plans quickly without redundant information.

## User Stories
- **US-01:** As a Driver, I want to view a list of my registered vehicles and their plans, so that I can manage them easily.
- **US-02:** As an Admin, I want to assign an Affiliate Company directly to a User profile, so that the billing and reporting apply universally to all their vehicles.

## Acceptance Criteria
**US-01 (View Vehicles List):**
- **Given** I am a logged-in Driver with registered vehicles
- **When** I navigate to the "Manage your vehicles" screen
- **Then** I see a list of my vehicles (make, model, license plate)
- **And** I see the plans attached to each vehicle
- **And** I DO NOT see any Affiliate Company name attached to individual vehicles.

**US-02 (Admin Assigns Company):**
- **Given** I am an Admin viewing a User's profile
- **When** I update the user's Affiliate Company
- **Then** the change is saved to the User record (`User.affiliated_company_id`)
- **And** the change implicitly applies to all vehicles owned by that user for billing purposes.

## Flows
- **Main Flow (View Vehicles):** User logs in -> Clicks "Manage your vehicles" -> List API is called -> UI renders list without company column.

## Traceability
| US | BR | EC | NFR |
|---|---|---|---|
| US-01 | BR-04 | EC-01 | NFR-02 |
| US-02 | BR-01, BR-02 | EC-02 | NFR-01 |
