# Screen Spec — PAY-SETTLE-001 — v1.0
**Last updated:** 2026-03-05  
**Related:** US-01, US-02, US-03, US-04, BR-01, BR-02, BR-03, DE (SettlementReport, AuditLog), NFR-02

## 1) Screen Inventory
| Screen ID | Screen Name | Type | Primary Actor | Entry Points | Exit Points | Related US |
|---|---|---|---|---|---|---|
| SCR-01 | Settlement Dashboard | Dashboard/List | Finance Operator | Admin Menu > Settlements | Excel Download (Async), Recon Action | US-01, US-02, US-03 |
| SCR-02 | Audit Log Viewer | List | Auditor | Admin Menu > Audit Logs | None | US-04 |

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
