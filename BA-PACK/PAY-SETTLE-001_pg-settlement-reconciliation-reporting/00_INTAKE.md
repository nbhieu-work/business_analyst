Feature: PAY-SETTLE-001 pg-settlement-reconciliation-reporting
Version: v1.0
Last updated: 2026-03-05
Change request: N/A

# Intake

## A. Problem Statement
The operations and administration teams need a dedicated Payment/Settlement (정산) module to ingest PG (Payment Gateway) settlement data, reconcile actual payouts against internal records, and generate actionable financial reports with excel export capabilities.

## B. Goals & Success Metrics
- Successfully collect/ingest PG settlement records.
- Accurately reconcile PG actual payout details against internal transaction/order data.
- Provide a dashboard/reporting view for settlement statuses.
- Support Excel downloads for offline analysis and auditing.
- Maintain an audit trail for all reconciliation actions and exports.

## C. Actors/Personas
- **Admin:** Manages system configurations, including PG connection settings.
- **Finance/Settlement Operator:** Actively runs reconciliations, reviews discrepancies, and exports data.
- **Auditor:** Read-only access to view settlement reports and audit trails.

## D. Scope In / Scope Out
- **Scope In:** PG data ingestion pipeline, Reconciliation logic, Settlement Dashboard, Excel Export function, Audit Logging for these actions, Role-based access control (Admin, Operator, Auditor).
- **Scope Out:** Processing actual refunds or capturing initial payments (this module only reads and reconciles completed transaction data). Adding new PG providers beyond the initial architecture support.

## E. Constraints
- Must support audit trail for every reconciliation action and excel export download.
- Must support large datasets efficiently (requires data pagination and asynchronous background processing for large exports).
- Roles must be strictly enforced (Operator vs Auditor vs Admin).

## F. Known Systems & Dependencies
- External PG Providers (TBD: TossPayments, KCP, Inicis, etc.)
- Internal Order/Transaction Database
- Internal Authentication & Authorization Service (for RBAC and Audit tracking)

## G. Risks & Assumptions
- **Assumption:** Currency and locale follow Korean accounting conventions (KRW, no decimal placements).
- **Risk:** Handling massive data files from PGs might cause system timeouts if not processed asynchronously.
- **Risk:** PG data formats vary significantly. We must build an abstraction layer for ingestion.

## H. Open Questions
1. Which specific PG provider(s) are we launching with first?
2. What is the integration method for the PG data (REST API, SFTP nightly drop, or manual CSV upload)?
3. What is the business settlement frequency (Daily (D+N), Weekly, Monthly)?
4. What is the primary reconciliation key (e.g., `order_id` vs `transaction_id` vs `payout_id`) matching our internal DB to the PG DB?

## I. Glossary
- **PG (Payment Gateway):** External payment processor.
- **Settlement (정산):** The process of verifying that the funds collected by the PG match the expected payout, minus fees, transferred to the company bank account.
- **Reconciliation (대사):** Matching internal order records with PG payout line items to find missing, overpaid, or underpaid transactions.

## J. Confidence
- Medium. The business logic is clear and standard for e-commerce/platforms, but the technical specifics (Open Questions 1-4) regarding the exact PG provider, data frequency, and connection method are currently unknown and require technical spikes.
