Feature: PAY-SETTLE-001 pg-settlement-reconciliation-reporting
Version: v1.0
Last updated: 2026-03-05
Change request: N/A

# Dev Handoff Pack

## A. TL;DR
Build a new PG Settlement Reconciliation module. This system reads PG payout data, matches it against our internal order transactions (via `pg_tid`), flags discrepancies (missing records, amount mismatches), and provides a Dashboard with an asynchronous Excel export feature. Additionally, it provides a manual entry interface for tracking and applying Corporate Deposits offline to system invoices.

## B. In-scope / Out-of-scope
- **In-scope:** Async Reconciliation Engine, Settlement Dashboard APIs, Async Excel Export generation, Audit Logging table and APIs, Corporate Deposit CRUD endpoints, Invoice Linking logic.
- **Out-scope:** Automatically triggering or executing refunds based on the mismatches (this is purely a reporting/reconciliation tool).

## C. Key assumptions + decisions
- To prevent locking up the web server during heavy reconciliations or massive Excel exports, these tasks **must** use background workers (e.g., message queues / job workers).
- Korean accounting rules apply: amounts are integers (KRW).
- The PG integration must use an Adapter pattern so we can hot-plug different vendors later without rewriting the core reconciliation math.

## D. Must-have User Stories list
- **US-01:** Operator triggers a reconciliation job for a date range.
- **US-02:** Operator/Auditor views the paginated dashboard showing status sums and mismatch flags.
- **US-03:** Operator/Auditor exports the specific view to Excel asynchronously.
- **US-04:** Admin/Auditor views the Audit Log of who triggered what.
- **US-05/06/07:** Operator registers a manual Corporate Deposit, searches them, and safely links them to unpaid Invoices via the UI.

## E. Endpoints summary
- `POST /api/v1/settlements/reconcile` (Async 202)
- `GET /api/v1/settlements` (Paginated 200)
- `POST /api/v1/settlements/export` (Async 202)
- `GET /api/v1/audit-logs` (Paginated 200)
- `POST /api/v1/deposits` (Create Corp Deposit 201)
- `PUT /api/v1/deposits/{id}/status` (Apply to Invoice 200)

## F. Data fields summary (Top elements)
- `SettlementReport.reconcile_status`: The core output flag (`MATCHED`, `MISSING_INTERNAL`, `PENDING_PG_SETTLEMENT`, `AMOUNT_MISMATCH`).
- `SettlementReport.pg_tid`: The foreign key linking PG worlds to internal worlds.
- `CorporateDeposit.status`: Lifecycle tracking (`RECEIVED`, `VERIFIED`, `APPLIED`).

## G. State transitions summary
- Async Jobs: `PENDING` -> `PROCESSING` -> `COMPLETED` | `FAILED`

## H. Permission matrix summary
- **Admin**: Full access.
- **Operator**: Cannot modify system PG configs; can trigger recon and export.
- **Auditor**: Read-only (can only view dashboard, export Excel, and read audit logs).

## I. Test checklist
- [ ] Unit Test: Create an internal order for 10,000 KRW, mock PG data for 9,000 KRW -> Assert status `AMOUNT_MISMATCH`.
- [ ] Integration Test: Request Excel export -> Assert 202 Accepted -> Assert Worker creates file and saves URL.
- [ ] Integration Test: Operator attempts `PUT /deposits/{id}` to link a 5M KRW deposit to a 4M KRW invoice -> Assert `400 Bad Request` unless `force_override=true` by Admin.
- [ ] Security Test: Auditor attempts `POST /reconcile` -> Assert 403 Forbidden.

## J. Open questions for sprint planning
- Which job queue system does the current backend stack support natively (e.g., Redis/BullMQ, Sidekiq, Celery)?
- Do we have a generic "Notification" service to deliver the Excel download link, or should we build a localized polling mechanism on the frontend?
