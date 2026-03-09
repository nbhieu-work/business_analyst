Feature: PAY-SETTLE-001 pg-settlement-reconciliation-reporting
Version: v1.0
Last updated: 2026-03-05
Change request: N/A

# Extraction

## A. Business Rules
- **BR-01:** Operators cannot approve or modify a PG settlement record; they can only run reconciliations and view mis-matches against internal DB records.
- **BR-02:** Auditors only possess read permissions for the dashboard and the generated Excel reports.
- **BR-03:** Every event triggered by the Operator or Auditor (running a reconciliation job, downloading an Excel sheet) must be logged in the Audit database.
- **BR-04:** The system reconciles the external PG `transaction_id` with the internal `Transaction.pg_tid`.
- **BR-05 (Corporate Deposit):** Deposits must be manually created by Operators identifying the sender, amount, and date.
- **BR-06 (Deposit Mapping):** A Corporate Deposit can be linked to exactly one or zero Billing Orders / Plans. An operator must "Verify" the received amount before applying.

## B. Edge Cases
- **EC-01:** Transaction exists in PG settlement list but is missing from the Internal DB (Status: "Missing Internal Data").
- **EC-02:** Transaction exists in Internal DB but is missing from the PG settlement list for the given period (Status: "Pending PG Settlement").
- **EC-03:** Amounts differ between PG Settlement and Internal DB for the same Transaction ID (Status: "Amount Mismatch").
- **EC-04:** Excel export request times out due to massive data size. (System exports asynchronously and notifies user).
- **EC-05:** Corporate deposit amount does NOT match the linked plan/invoice exactly. (System must throw a warning requiring explicit override by Admin to mark as Applied).

## C. Data Elements
| Name | Type | Validation | Source of Truth | Nullable? | Default | Notes |
|---|---|---|---|---|---|---|
| SettlementReport.id | UUID | Setup by System | Internal DB | No | UIID() | PK |
| SettlementReport.pg_tid | String | Must match PG output | PG System | No | None | Extracted from PG File |
| SettlementReport.internal_tid | UUID | Internal Trans ID | Internal DB | Yes | None | Only present if reconciled successfully |
| SettlementReport.reconcile_status | Enum | [MATCHED, MISSING_INTERNAL, PENDING_PG_SETTLEMENT, AMOUNT_MISMATCH] | Internal Logic | No | PENDING | Defines the status after reconciliation run |
| SettlementReport.amount_pg | Decimal | Standard Number | PG System | No | 0 | The actual collected amount from PG |
| SettlementReport.amount_internal | Decimal | Standard Number | Internal DB | Yes | None | Our expected amount |
| AuditLog.user_id | UUID | Must exist in Users list | Auth System | No | None | Who executed the action |
| AuditLog.action_type | String | Enum list of allowed actions | Internal Logic | No | None | e.g. "EXPORT_EXCEL", "RUN_RECON" |
| CorporateDeposit.id | UUID | Generated per record | Internal DB | No | UUID() | PK |
| CorporateDeposit.depositor_name | String | Min 2 chars | Input by Operator | No | None | Name of the sending bank account |
| CorporateDeposit.amount | Decimal | > 0 | Input by Operator | No | None | Deposited sum |
| CorporateDeposit.deposit_date | Date | YYYY-MM-DD | Input by Operator | No | None | The physical bank transfer date |
| CorporateDeposit.status | Enum | [RECEIVED, VERIFIED, APPLIED] | Internal Logic | No | RECEIVED | Lifecycle state |
| CorporateDeposit.linked_invoice_id| UUID | Must exist | Internal DB | Yes | None | The Plan/Invoice to clear |

## D. State Model
- **Reconciliation Statuses:**
  - `PENDING` -> Job submitted, not yet processed.
  - `PROCESSING` -> Background job parsing SFTP/API records and matching.
  - `COMPLETED` -> Full list matched and flags marked.
  - `FAILED` -> Processing failed due to malformed PG data or timeout.
- **Corporate Deposit Statuses:**
  - `RECEIVED` -> Payment recorded in system by Operator.
  - `VERIFIED` -> Payment is matched against a known Invoice/Plan conceptually.
  - `APPLIED` -> Payment is fully resolved and the internal invoice is marked as Paid.

## E. Permissions Matrix
| Role | Execute Reconciliation | View Report Dashboard | Download Excel | Configure PG Setup | View Audit Logs | Manage Corp Deposits |
|---|---|---|---|---|---|---|
| Admin | X | X | X | X | X | X |
| Finance Operator | X | X | X |   | X | X (Cannot override mismatch EC-05) |
| Auditor |   | X | X |   | X | Read Only |

## F. Non-functional Requirements
- **NFR-01:** The PG SFTP file parsing process must load data via bulk inserts (e.g., using COPY or multi-insert SQL commands) to avoid database lock contention.
- **NFR-02:** The system must not time out; if an Excel download exceeds 10,000 rows, it must transition to asynchronous processing (Worker -> Notification -> Download Link).
- **NFR-03:** Financial numbers format as KRW locally without decimals.

## G. Traceability
- **BR-03, NFR-02, EC-04** -> Intake Constraints (Large datasets, Audit Trail)
- **Permissions Matrix** -> Intake Scope In (RBAC requirements)
