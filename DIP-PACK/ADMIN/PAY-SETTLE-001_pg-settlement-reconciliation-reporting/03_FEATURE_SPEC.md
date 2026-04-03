Feature: PAY-SETTLE-001 pg-settlement-reconciliation-reporting
Version: v1.0
Last updated: 2026-03-05
Change request: N/A

# Feature Specification

## Overview
This specification details the initial version of the PG Settlement Reconciliation and Reporting module. The solution automates the matching process between PG settlement data and internal order records, highlights discrepancies, and exposes this data via a dashboard and secure Excel downloads.

## Proto-persona
- **Finance Operator:** Managing daily cash flow validation. They want automated mismatch flagging so they can focus strictly on addressing edge cases (e.g., failed payouts, missing internal hooks) rather than manual VLOOKUPs in Excel.

## User Stories
- **US-01 [Execute Recon]:** As a Finance Operator, I want to trigger a reconciliation job for a specific date range, so that the system automatically matches PG records with internal DB records.
- **US-02 [View Dashboard]:** As an Operator/Auditor, I want to view a dashboard summarizing the reconciliation results (Matched vs Missing vs Mismatch), so that I can quickly gauge the financial health for the period.
- **US-03 [Download Report]:** As an Operator/Auditor, I want to download the detailed reconciliation list into an Excel file, so that I can perform deeper offline accounting analysis.
- **US-04 [Audit Tracking]:** As an Admin/Auditor, I want all sensitive actions (Reconciliation Runs, Excel Downloads) logged, so that we comply with financial auditing standards.
- **US-05 [Register Deposit]:** As a Finance Operator, I want to manually register a Corporate Deposit received in our bank account, so that it enters the system for verification.
- **US-06 [Search Deposits]:** As an Operator/Admin, I want to search and filter Corporate Deposits by status, date, and depositor name, so that I can find unapplied payments easily.
- **US-07 [Verify & Apply Deposit]:** As an Operator, I want to link a `RECEIVED` Corporate Deposit to a specific Billing Order/Plan, so that the invoice is cleared and the deposit status transitions to `APPLIED`.

## Acceptance Criteria

**US-01 [Execute Recon]:**
- **Given** I am logged in as a Finance Operator
- **When** I define a Date Range and click "Run Reconciliation"
- **Then** a background job is triggered
- **And** the UI shows a "Processing..." state
- **And** the system logs my action in the `AuditLog` table.

**US-02 [View Dashboard]:**
- **Given** a reconciliation job has completed
- **When** I view the Settlement Dashboard
- **Then** I see aggregate sums of: Total Expected, Total Received, Total Discrepancy Amount
- **And** I see a paginated table of line items with columns for: PG TID, Internal Order ID, PG Amount, Internal Amount, Status.

**US-03 [Download Report]:**
- **Given** I am viewing the Settlement Dashboard
- **When** I click "Export to Excel"
- **Then** the system queues an asynchronous export job
- **And** logs my action in the `AuditLog` table
- **And** when the job finishes, my notification bell alerts me with a secure download URL to retrieve the `.xlsx` file.

**US-04 [Audit Tracking]:**
- **Given** an action like Export Excel is performed
- **When** the Auditor checks the Audit Menu
- **Then** they see a read-only list showing: User ID, Time, Action ("EXPORT_EXCEL"), target date parameters, and the user's IP Address.

**US-05 [Register Deposit]:**
- **Given** I am on the Corporate Deposit Management screen
- **When** I fill in Depositor Name, Amount, and Date, and click "Save"
- **Then** a new `CorporateDeposit` record is created with status `RECEIVED`.

**US-06 [Search Deposits]:**
- **Given** I am on the Corporate Deposit Management screen
- **When** I filter by Status = `RECEIVED`
- **Then** I see a list of all unapplied deposits.

**US-07 [Verify & Apply Deposit]:**
- **Given** I select a `RECEIVED` deposit
- **When** I search for and select an open Invoice ID
- **Then** the UI shows a comparison of the Invoice Amount vs Deposit Amount.
- **And** if they match, I can click "Apply to Invoice", transitioning the status to `APPLIED`.
- **And** if they do not match, the "Apply" button requires an Admin override (EC-05).

## Flows
- **Export Async Flow:** Dashboard -> Click Export -> UI shows toast "Export queued" -> Worker creates Excel -> Uploads to S3/Cloud Storage -> Saves URL to user's Notification box -> User clicks notification -> Download triggers.

## Traceability
| US | BR | EC | NFR |
|---|---|---|---|
| US-01 | BR-01, BR-04 | EC-01, EC-02, EC-03 | NFR-01 |
| US-02 | BR-02 | | NFR-03 |
| US-03 | BR-02, BR-03 | EC-04 | NFR-02 |
| US-04 | BR-03 | | |
| US-05 | BR-05 | | NFR-03 |
| US-06 | | | |
| US-07 | BR-06 | EC-05 | NFR-03 |
