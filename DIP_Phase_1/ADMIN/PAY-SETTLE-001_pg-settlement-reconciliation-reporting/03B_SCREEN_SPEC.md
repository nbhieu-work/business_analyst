# Screen Spec — PAY-SETTLE-001 — v1.0
**Last updated:** 2026-03-05  
**Related:** US-01, US-02, US-03, US-04, BR-01, BR-02, BR-03, DE (SettlementReport, AuditLog), NFR-02

## 1) Screen Inventory
| Screen ID | Screen Name | Type | Primary Actor | Entry Points | Exit Points | Related US |
|---|---|---|---|---|---|---|
| SCR-01 | Settlement Dashboard | Dashboard/List | Finance Operator | Admin Menu > Settlements | Excel Download (Async), Recon Action | US-01, US-02, US-03 |
| SCR-02 | Audit Log Viewer | List | Auditor | Admin Menu > Audit Logs | None | US-04 |
| SCR-03 | Settlement Record Detail | Detail | Finance Operator | SCR-01 (click row) | SCR-05 (view case) | US-02 |
| SCR-04 | Payout Detail | Detail | Finance Operator | SCR-03 (view payout) | - | US-02 |
| SCR-05 | Reconciliation Case Detail | Detail/Compare | Finance Operator | SCR-01, SCR-03 | - | US-02 |
| SCR-06 | Collection Run Detail | Detail | Admin, Operator | SCR-01 (job history) | Retry action | US-01 |
| SCR-07 | Corporate Deposit Management | List/Action | Finance Operator | Admin Menu > Plan | Apply to Invoice | US-05, US-06, US-07 |

---

## 2) Screen Details

### SCR-01 — Settlement Dashboard
**Purpose:** Display the aggregate financial health and line-item details of PG reconciliation. Allow Operators to run reconciliations and export reports.  
**Primary Actor:** Finance Operator, Auditor  
**Related Stories:** US-01, US-02, US-03  
**Permissions:** 
- `ALLOWED`: Admin, Finance Operator (Full), Auditor (Read-only view and Export)
- `DENIED`: Regular Users, Drivers  
**Entry Conditions:** User must have the required RBAC role.  
**Exit Conditions:** Actioning an export queues a background job and triggers a notification. Actioning a reconciliation triggers a background job.  

#### A. Layout / Sections (functional)
- **Header:** Page Title ("PG Settlement & Reconciliation") + Date Range Picker + "Run Reconciliation" button + "Export to Excel" button.
- **Aggregate Summary Cards:** Three horizontal cards showing total sums based on the selected date range.
- **Filter/Toolbar:** Search by TID, filter by Status (All, Matched, Mismatch, Pending, Missing), Pagination controls.
- **Content Area:** Data table displaying the paginated line items of `SettlementReport`.

#### B. Components & Data Binding
| Component | Type | Data Source (DE / API) | Rules/Validation | Visibility/Role | Notes |
|---|---|---|---|---|---|
| Date Range Picker | Input (Date) | Selected state | Max range 31 days | All allowed roles | Defaults to last 7 days |
| "Run Reconciliation" | Button | `POST /reconcile` | Dates must be selected | Admin, Operator | Hidden/Disabled for Auditor |
| "Export to Excel" | Button | `POST /export` | Dates must be selected | Admin, Operator, Auditor | |
| Aggregate: Expected | Card | API (Internal Sum) | Sum of `amount_internal` | All allowed roles | Formatted as KRW (no decimals) |
| Aggregate: Received | Card | API (PG Sum) | Sum of `amount_pg` | All allowed roles | Formatted as KRW |
| Aggregate: Discrepancy | Card | API (Delta) | Expected - Received | All allowed roles | Highlighting Red if > 0 |
| Data Table | Table | `SettlementReport` list | Paginated (50/page) | All allowed roles | |
| ↳ Col: PG TID | Text | `pg_tid` | - | - | |
| ↳ Col: Internal ID | Text | `internal_tid` | - | - | |
| ↳ Col: PG Amount | Currency | `amount_pg` | - | - | |
| ↳ Col: Internal Amount| Currency | `amount_internal` | - | - | |
| ↳ Col: Status | Badge | `reconcile_status` | Color coded by enum | - | MATCHED(Green), MISMATCH(Red)... |

#### C. User Interactions
- **Select Dates:** Updates the aggregate cards and table data automatically.
- **Click "Run Reconciliation":** Opens a confirmation modal ("Are you sure you want to run reconciliation for YYYY-MM-DD to YYYY-MM-DD?"). On confirm, triggers `POST /reconcile`, changes button state to "Processing...".
- **Click "Export to Excel":** Triggers `POST /export`, shows toast/snackbar message "Export queued. You will be notified when the file is ready."

#### D. States
- **Loading State:** 
  - Table: Skeleton rows.
  - Cards: Shimmering placeholders.
  - Buttons: Disabled with spinner.
- **Empty State:** 
  - Message: "No settlement records found for the selected dates." + Illustration.
  - Export Button: Disabled.
- **Error State:**
  - Message: "Failed to load settlement data. Please try again or contact support." + Retry button.
- **Permission Denied State:** Page shows 403 Forbidden full-screen illustration if accessed directly via URL without roles.

#### E. Validations & Error Messages
- **Date Range Check:** If start date > end date, show inline error "End date must be after start date."
- **Max Range Check:** If range > 31 days, show validation message "Please select a date range of 31 days or less to view data."

#### F. Analytics / Audit Events
| Event | Trigger | Properties |
|---|---|---|
| `ui.recon_run_clicked` | "Run Reconciliation" confirm | userId, dateFrom, dateTo |
| `ui.export_excel_clicked` | "Export to Excel" clicked | userId, dateFrom, dateTo, activeFilters |

#### G. Edge Cases to Handle
- **EC-04 (Large Export):** System handles large data cleanly via async queues. UI must correctly inform the user to check their notifications rather than locking the browser.
- **Running Job Concurrency:** If a reconciliation job is already running for the selected dates, the "Run Reconciliation" button should be disabled and say "Reconciliation in progress...".

#### H. Design Notes
- Status Badges: 
  - `MATCHED`: Green outline.
  - `AMOUNT_MISMATCH`: Solid Red background.
  - `MISSING_INTERNAL` / `PENDING_PG_SETTLEMENT`: Yellow/Warning styles.
- Numerics should be strictly right-aligned in the table to support easy visual scanning of KRW values.

---

### SCR-02 — Audit Log Viewer
**Purpose:** View a read-only list of sensitive actions performed by system users for compliance.  
**Primary Actor:** Auditor, Admin  
**Related Stories:** US-04  
**Permissions:** 
- `ALLOWED`: Admin, Auditor, Finance Operator
- `DENIED`: Regular Users, Drivers  
**Entry Conditions:** Same as SCR-01  
**Exit Conditions:** None (Read-only monitoring)

#### A. Layout / Sections (functional)
- **Header:** Title ("System Audit Logs").
- **Filter/Toolbar:** Date range picker, Action Type dropdown (e.g., Run Recon, Export Excel), User ID search.
- **Content Area:** Data table displaying `AuditLog` entries.

#### B. Components & Data Binding
| Component | Type | Data Source (DE / API) | Rules/Validation | Visibility/Role | Notes |
|---|---|---|---|---|---|
| Data Table | Table | `AuditLog` list | Paginated (50/page) | All allowed roles | Ordered by `created_at` DESC |
| ↳ Col: Timestamp | DateTime | `created_at` | Format: YYYY-MM-DD HH:mm:ss | - | |
| ↳ Col: User ID | Text | `user_id` | - | - | |
| ↳ Col: Action | Badge | `action_type` | - | - | E.g., EXPORT_EXCEL |
| ↳ Col: Target Params | JSON View | `target_params` | Show as tooltip or inline code block | - | Shows dates exported, etc. |
| ↳ Col: IP Address | Text | `ip_address` | - | - | |

#### C. User Interactions
- **Filtering:** Changing filters fetches new data for the table.
- **View Params:** Hovering or clicking on Target Params expands to show the raw JSON payload of the action.

#### D. States
- **Loading State:** Skeleton table rows.
- **Empty State:** "No actions recorded."

#### E. Validations & Error Messages
- None specific to this read-only view.

#### F. Analytics / Audit Events
| Event | Trigger | Properties |
|---|---|---|
| `ui.audit_logs_viewed` | Page Load | userId |

#### G. Edge Cases to Handle
- Logs table could be massive. Infinity scroll or hard pagination is strictly required. No "Export All" button to prevent runaway DB queries.

#### H. Design Notes
- Keep it dense and utilitarian. Monospace fonts for UUIDs, IP addresses, and JSON blobs are recommended.

---

### SCR-03 — Settlement Record Detail
**Purpose:** View the detailed information of a specific internal settlement record, its matched status, and related transactions.  
**Primary Actor:** Finance Operator  
**Related Stories:** US-02  
**Permissions:** 
- `ALLOWED`: Admin, Finance Operator, Auditor
- `DENIED`: Regular Users, Drivers
**Entry Conditions:** User clicked a row on SCR-01  
**Exit Conditions:** User navigates back to Dashboard or clicks to view Case Detail (SCR-05)  

#### A. Layout / Sections (functional)
- **Header:** Back button, Title (Internal ID), Status Badge
- **Summary Section:** PG Amount, Internal Amount, Discrepancy Amount
- **Transaction Details:** Order details from internal DB
- **PG Match Details:** Link to view Payout Detail (SCR-04) or Case Detail (SCR-05)

#### B. Components & Data Binding
| Component | Type | Data Source (DE / API) | Rules/Validation | Visibility/Role | Notes |
|---|---|---|---|---|---|
| Status Badge | Badge | `reconcile_status` | - | All allowed roles | - |
| Amounts Summary | Key-Value | `amount_internal`, `amount_pg` | - | All allowed roles | - |
| PG Match Link | Link | `pg_tid` | - | All allowed roles | Navigates to SCR-04/05 |

#### C. User Interactions
- **Click "View Compare/Case":** Navigates to SCR-05 to compare internal vs PG data.
- **Click PG TID:** Navigates to SCR-04 to see raw PG payout details.

#### D. States
- **Loading State:** Skeleton loaders for summary and details.
- **Error State:** "Failed to load record details." + Retry button.

#### E. Validations & Error Messages
- N/A (Read-only detail view)

#### F. Analytics / Audit Events
| Event | Trigger | Properties |
|---|---|---|
| `ui.settlement_detail_viewed` | Page Load | userId, internalId |

#### G. Edge Cases to Handle
- **Missing PG Record:** If status is `PENDING_PG_SETTLEMENT`, the PG match link should be disabled/hidden.

#### H. Design Notes
- Use a 2-column layout for desktop to show Internal Details left, PG Match summary right.

---

### SCR-04 — Payout Detail
**Purpose:** Display the raw, detailed payload received from the PG provider for a specific payout ID / TID.  
**Primary Actor:** Finance Operator  
**Related Stories:** US-02  
**Permissions:** 
- `ALLOWED`: Admin, Finance Operator, Auditor
- `DENIED`: Regular Users, Drivers
**Entry Conditions:** User clicked a PG TID from SCR-01 or SCR-03  
**Exit Conditions:** User navigates back  

#### A. Layout / Sections (functional)
- **Header:** Back button, Title (PG TID context)
- **Raw Data Viewer:** Key-value list of all fields directly mapped from the PG integration adapter.

#### B. Components & Data Binding
| Component | Type | Data Source (DE / API) | Rules/Validation | Visibility/Role | Notes |
|---|---|---|---|---|---|
| Property List | Key-Value | PG Ingestion DB | - | All allowed roles | Show exactly what PG sent |

#### C. User Interactions
- **Copy to Clipboard:** Icon next to raw JSON or TID to easily copy for auditing.

#### D. States
- **Loading State:** Skeleton loader.
- **Error State:** "Unable to fetch raw PG payload."

#### E. Validations & Error Messages
- N/A

#### F. Analytics / Audit Events
- N/A (General payload view)

#### G. Edge Cases to Handle
- **Raw Payload Missing:** If data wasn't fully ingested, show "Raw payload unavailable."

#### H. Design Notes
- Format JSON payloads or long strings clearly.

---

### SCR-05 — Reconciliation Case Detail
**Purpose:** Provide a side-by-side comparison (diff view) of the Internal DB Order vs the PG Settlement Record to easily spot discrepancies (e.g., amount mismatch, fee difference).  
**Primary Actor:** Finance Operator  
**Related Stories:** US-02  
**Permissions:** 
- `ALLOWED`: Admin, Finance Operator, Auditor
- `DENIED`: Regular Users, Drivers
**Entry Conditions:** User clicks a mismatched row from SCR-01 or from SCR-03  
**Exit Conditions:** User resolves case (future scope) or navigates back  

#### A. Layout / Sections (functional)
- **Header:** Title ("Reconciliation Compare"), Status Badge
- **Split View:** Left Column (Internal Data), Right Column (PG Data)
- **Delta Summary:** Highlight exact fields that mismatch (e.g., Amount Expected vs Received)

#### B. Components & Data Binding
| Component | Type | Data Source (DE / API) | Rules/Validation | Visibility/Role | Notes |
|---|---|---|---|---|---|
| Split Comparison | Table/Grid | Internal DB + PG Adapter | Highlight diffs | All allowed roles | - |
| Delta Highlight | Text/Icon | Calculated difference | Only show if >0 | All allowed roles | - |

#### C. User Interactions
- **Navigations:** Links to original internal order.

#### D. States
- **Loading State:** Skeleton split view.
- **Empty State:** N/A

#### E. Validations & Error Messages
- N/A

#### F. Analytics / Audit Events
| Event | Trigger | Properties |
|---|---|---|
| `ui.recon_case_viewed` | Page Load | userId, pg_tid, internal_tid |

#### G. Edge Cases to Handle
- **Missing Side:** If record is missing in Internal DB, the left side shows "No matching internal record found."
- **Missing Side:** If missing in PG, right side shows "No PG record found."

#### H. Design Notes
- Use visual diffing patterns (e.g., red background for mis-matching values).

---

### SCR-06 — Collection Run Detail
**Purpose:** View the history, status, and logs of a specific PG data ingestion or reconciliation background job.  
**Primary Actor:** Admin, Finance Operator  
**Related Stories:** US-01  
**Permissions:** 
- `ALLOWED`: Admin, Finance Operator
- `DENIED`: Auditor, Regular Users, Drivers
**Entry Conditions:** User clicks a "View Jobs" or a specific job link from SCR-01  
**Exit Conditions:** User triggers a retry, navigates back  

#### A. Layout / Sections (functional)
- **Header:** Job ID, Date Range targeted, Job Status (COMPLETED, FAILED, PROCESSING)
- **Metrics:** Total records processed, Total Matched, Total Mismatched, Total Errors
- **Error Log Console:** List of system/parsing errors encountered during ingestion
- **Actions:** "Retry Job" button (if failed)

#### B. Components & Data Binding
| Component | Type | Data Source (DE / API) | Rules/Validation | Visibility/Role | Notes |
|---|---|---|---|---|---|
| "Retry Job" | Button | `POST /job/{id}/retry` | Only active if FAILED | Admin, Operator | - |
| Metrics Grid | Number Cards| Job Statistics DB | - | Admin, Operator | - |
| Error Logs | List/Text | Job Worker Logs | - | Admin, Operator | - |

#### C. User Interactions
- **Click "Retry Job":** Shows confirmation. If confirmed, triggers job restart.

#### D. States
- **Loading State:** Skeleton.
- **Processing State:** Auto-refreshing metrics (polling or WebSocket).

#### E. Validations & Error Messages
- N/A

#### F. Analytics / Audit Events
| Event | Trigger | Properties |
|---|---|---|
| `ui.collection_job_retry` | "Retry Job" confirmed | userId, jobId |

#### G. Edge Cases to Handle
- **Continuous Failure:** If retry fails repeatedly, surface clear system error message (e.g., "SFTP Auth Error") for admins.

#### H. Design Notes
- Code blocks or terminal-style UI for raw error logs.

---

### SCR-07 — Corporate Deposit Management
**Purpose:** Allow Finance Operators to manually register bank transfer deposits from corporate clients and link them to unpaid invoices/plans.  
**Primary Actor:** Finance Operator, Admin  
**Related Stories:** US-05, US-06, US-07  
**Permissions:** 
- `ALLOWED`: Admin, Finance Operator (Admin required for overrides)
- `DENIED`: Auditor (Read-only list), Regular Users
**Entry Conditions:** Admin Menu > Plan > Corporate Deposits  
**Exit Conditions:** -

#### A. Layout / Sections (functional)
- **Header:** Title ("Corporate Deposits") + "Register Deposit" button.
- **Filters:** Date Range, Status Dropdown (RECEIVED, VERIFIED, APPLIED), Depositor Name Search.
- **Content Area:** Data table displaying the list of `CorporateDeposit` records.
- **Sidebar/Modal:** "Apply to Plan" workflow panel (opens when clicking a row).

#### B. Components & Data Binding
| Component | Type | Data Source (DE / API) | Rules/Validation | Visibility/Role | Notes |
|---|---|---|---|---|---|
| "Register Deposit" | Button / Modal | `POST /deposits` | Form: Name, Amount, Date | Admin, Operator | - |
| Data Table | Table | `CorporateDeposit` list | Paginated | All | - |
| ↳ Col: Date | Date | `deposit_date` | - | All | - |
| ↳ Col: Depositor | Text | `depositor_name` | - | All | - |
| ↳ Col: Amount | Currency | `amount` | - | All | Format as KRW |
| ↳ Col: Status | Badge | `status` | Color coded by enum | All | RECEIVED(Warning), APPLIED(Success) |
| "Link to Invoice" | Button | `PUT /deposits/{id}` | Only if status != APPLIED | Admin, Operator | Opens linking panel |

#### C. User Interactions
- **Register Deposit:** Opens a modal. After submitting, row is added to the table with `RECEIVED` status.
- **Link to Invoice:** Select a record, search for a valid Invoice ID. System previews the Invoice Amount vs Deposit Amount.
- **Confirm Apply:** Click "Apply". If amounts match, transitions to `APPLIED`.

#### D. States
- **Loading State:** Skeleton table rows.
- **Error State:** N/A.

#### E. Validations & Error Messages
- **Amount mismatch (EC-05):** If Invoice Amount != Deposit Amount, show error: "Amounts do not match. Only Admins can force-apply partial or over-payments." Admin sees an "Override & Apply" (Red) button.

#### F. Analytics / Audit Events
| Event | Trigger | Properties |
|---|---|---|
| `ui.deposit_registered` | Modal Submit | userId, amount |
| `ui.deposit_applied` | Click Apply | userId, depositId, invoiceId, is_override |

#### G. Edge Cases to Handle
- **Duplicate Deposit Warning:** If registering a deposit with the exact same Name, Amount, and Date as an existing one, show a warning before saving: "A similar deposit already exists. Are you sure?"

#### H. Design Notes
- Link/Apply Workflow: Preferred as a right-side sliding drawer (side-sheet) so the user doesn't lose the context of the main list while searching for matching invoices.
