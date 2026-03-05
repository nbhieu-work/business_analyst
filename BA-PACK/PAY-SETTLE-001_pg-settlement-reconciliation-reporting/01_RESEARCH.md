Feature: PAY-SETTLE-001 pg-settlement-reconciliation-reporting
Version: v1.0
Last updated: 2026-03-05
Change request: N/A

# Deep Research

## Executive Summary
This research focuses on the architectural and business patterns required to build a scalable PG Settlement and Reconciliation system in the South Korean market, addressing the open questions from the Intake phase.

## Key Findings
- Most major Korean PGs (TossPayments, KCP, KG Inicis) offer both SFTP batch file drops (usually D+1 or D+2) and REST APIs for settlement data. 
- For large scale operations, SFTP batch ingestion combined with async processing is the industry standard to prevent API rate limiting.
- Reconciliation is almost universally keyed on the `transaction_id` (TID) provided by the PG, which must be stored in the internal DB during the initial checkout phase. 

## Detailed Analysis
### 1. PG Integration Methods
- **Hypothesis:** We should design a flexible "Adapter/Strategy" pattern for ingestion.
- **Reasoning:** Different PGs use different column names and formats. The core system should define a standard `NormalizedSettlementRecord` and each PG integration (e.g., `TossSettlementAdapter`) maps the raw data into this standard format before reconciliation.

### 2. Settlement Frequency & Reconciliation
- **Hypothesis:** The system must support Daily (D+N) reconciliation but aggregate it computationally for Monthly reporting.
- **Reasoning:** Korean accounting closes monthly, but operational cash flow requires daily tracking. Discrepancies (e.g., PG delayed payout due to risk blocks) must be flagged daily.

### 3. Large Dataset Handling (Exports & Ingestion)
- **Constraint Check:** The intake mandates support for "large datasets (pagination + async exports)."
- **Architecture Suggestion:** 
  - **Ingestion:** Use a message queue (e.g., Kafka / SQS) or chunked background jobs (e.g., Celery / Sidekiq / Spring Batch) to parse PG files.
  - **Export:** When a user requests an Excel download, trigger a background worker. Create an `ExportJob` table to track status (Pending -> Processing -> Completed). The user receives a notification or checks an "Export History" tab to download the compiled `.xlsx` file.

### 4. Audit Trail Implementation
- **Requirement:** Every reconciliation execution and excel export must be audited.
- **Suggestion:** Introduce an `AuditLog` table capturing `user_id`, `action_type` (e.g., `EXECUTE_RECONCILIATION`, `DOWNLOAD_SETTLEMENT_EXCEL`), `timestamp`, and `ip_address`. Auditors view this table.

## Areas of Consensus / Debate
- **Debate:** Should the system auto-reconcile upon data ingestion, or require a human Operator to click "Run Reconciliation"?
- **Recommendation:** Auto-reconcile nightly when data arrives, but provide an interface for the Operator to view anomalies and click "Force Re-run" if manual adjustments are made.

## Gaps and Further Research
- Need confirmation from the business/finance team on the specific PG provider(s) to sign contracts with first, as this dictates the exact API/SFTP specs to implement.
- Need confirmation on the exact fee structures (flat vs percentage) to implement the mathematical calculation checks during reconciliation.
