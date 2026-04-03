Feature: TEMP-001 manage-vehicles-aff-company
Version: v2.0
Last updated: 2026-03-04
Change request: CR-01

# Deep Research

## Executive Summary
This is an internal data model and UI refactoring change requested by Denny. External market research is not applicable. The primary focus is defining the technical and business migration paths for the legacy data.

## Key Findings
- Internal technical review indicates that mapping Affiliate Company to the User entity drastically simplifies permission management, reporting, and billing aggregation across multiple vehicles.

## Detailed Analysis
### Open Question 1: Migration Strategy for existing Vehicle->Company mapping
- **Hypothesis / Proposed Approach:** We should run a background data migration script that takes the distinct `company_id` from the user's active vehicles. If a user has exactly one distinct `company_id` across vehicles, we assign it to the User profile table.

### Open Question 2: Conflict Resolution for users with multiple distinct companies
- **Hypothesis / Proposed Approach:** If a user has vehicles spanning multiple companies, we apply a fallback logic: the most recently registered vehicle's `company_id` becomes the user's primary company, and the edge cases are flagged for manual admin review.

## Gaps and Further Research
- Need the Data/Engineering team to run a quick SQL query to confirm the exact number of users with conflicting distinct vehicle companies to finalize the data migration runbook.
