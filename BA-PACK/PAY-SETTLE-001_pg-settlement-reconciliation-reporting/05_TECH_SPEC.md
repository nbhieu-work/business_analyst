Feature: PAY-SETTLE-001 pg-settlement-reconciliation-reporting
Version: v1.0
Last updated: 2026-03-05
Change request: N/A

# Technical Specification

## Overview
This document specifies the technical architecture for the new PG Settlement Reconciliation module. It introduces a background ingestion pipeline, an asynchronous reconciliation engine, and a scalable export mechanism to handle large financial datasets without impacting core order processing.

## Motivation
Current state -> No automated settlement tracking.
Problems -> Finance relies on manual Excel VLOOKUPs between PG admin panels and internal DBs; risk of human error; lack of audit trails for financial reviews.
Desired state -> System automatically matches PG payouts to internal orders based on Transaction ID (TID), highlighting mismatches and providing secure, async Excel exports.

## Research Findings
- Using a chunked, asynchronous worker approach (e.g., Celery/Sidekiq or Spring Batch) is necessary for Excel exports > 10,000 rows to prevent HTTP timeouts.
- PG integrations should use an Adapter Pattern to normalize various PG formats into a standard internal `SettlementRecord`.

## Design Decisions
1. **Adapter Pattern for Ingestion:** Standardize inbound PG data before reconciliation to decouple the core logic from specific PG vendors.
2. **Background Jobs for Reconciliation & Export:** Use a message broker/job queue for all heavy lifting.
3. **Dedicated Audit Table:** Strictly log actions rather than relying on standard application logging, to allow easy querying by Auditors in the Dashboard.

## Architecture (Draft)
```text
[PG Admin / API] 
       | (SFTP / REST)
       v
[PG Ingestion Adapter] -> Standardizes data
       |
       v
[Reconciliation Worker] <- Reads Internal DB (Orders)
       |
       v
[SettlementReport DB] <- Stores results (MATCHED, MISMATCH, etc.)
       |
       v
[Settlement Dashboard API] -> [Frontend SPA]
       |
       v (Export Requested)
[Export Worker] -> Generates .xlsx -> Uploads to S3 -> Creates Notification
```

## API Contract draft
- `POST /api/v1/settlements/reconcile`
  - Body: `{ "date_from": "YYYY-MM-DD", "date_to": "YYYY-MM-DD", "pg_provider": "TOSS" }`
  - Response: `202 Accepted` `{ "job_id": "uuid" }`
- `GET /api/v1/settlements`
  - Query: `?date_from=...&date_to=...&status=MISMATCH&page=1&size=50`
  - Response: `200 OK` (Paginated list of SettlementReport objects + Aggregate Summaries)
- `POST /api/v1/settlements/export`
  - Body: `{ "date_from": "YYYY-MM-DD", "date_to": "YYYY-MM-DD" }`
  - Response: `202 Accepted` `{ "job_id": "uuid" }`
- `GET /api/v1/audit-logs`
  - Query: `?module=SETTLEMENT`
  - Response: `200 OK` (List of audit actions)

## Data Model outline
- **SettlementReport**
  - `id` (UUID, PK)
  - `pg_tid` (String, Index)
  - `internal_tid` (UUID, FK to Orders/Transactions, Nullable)
  - `reconcile_status` (Enum: MATCHED, MISSING_INTERNAL, PENDING_PG_SETTLEMENT, AMOUNT_MISMATCH)
  - `amount_pg` (Decimal)
  - `amount_internal` (Decimal)
  - `settlement_date` (Date)
- **AuditLog**
  - `id` (UUID, PK)
  - `user_id` (UUID)
  - `action_type` (String)
  - `target_params` (JSONB)
  - `ip_address` (String)
  - `created_at` (Timestamp)

## AuthN/AuthZ mapping
- **Admin**: All Endpoints.
- **Finance Operator**: `POST /reconcile`, `GET /settlements`, `POST /export`, `GET /audit-logs`.
- **Auditor**: `GET /settlements`, `POST /export`, `GET /audit-logs` (No `POST /reconcile`).

## Implementation Plan
- **Phase 1:** DB Schema creation (SettlementReport, AuditLog) & RBAC setup.
- **Phase 2:** Build the generic `ReconciliationService` and the first `PgDataAdapter` (e.g., TossPayments).
- **Phase 3:** API development (GET/POST endpoints).
- **Phase 4:** Background Job implementations (Reconciliation Job + Excel Export Job).
- **Phase 5:** Frontend Dashboard UI.

## Edge Cases
- **EC-04:** Background job failures (e.g., S3 upload fails for Excel) must mark the job status as `FAILED` and notify the user to retry.

## Success Criteria
- [ ] System handles parsing 50k+ rows of PG data without HTTP timeout (via jobs).
- [ ] Reconciliation logic accurately flags amounts differing by even 1 KRW.
- [ ] Export file contains all rows and is accessible via a secure, expiring URL.
- [ ] Every execution and export is visible in the Audit Log.

## Traceability
- **Data Model:** Traceable to Extraction (Section C).
- **APIs:** Traceable to Feature Spec (US-01, US-02, US-03).
- **Architecture:** Traceable to Intake Constraints (Async processing).
