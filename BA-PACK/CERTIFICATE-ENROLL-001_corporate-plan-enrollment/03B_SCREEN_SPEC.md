# Screen Spec — CERTIFICATE-ENROLL-001 — v1.0
**Last updated:** 2026-03-17  
**Related:** BA-Orchestrator, Bulk Mapping Flow

## 1) Screen Inventory
| Screen ID | Screen Name | Type | Primary Actor | Entry Points | Exit Points | Related US |
|---|---|---|---|---|---|---|
| SCR-01 | Securities Certificate Management | List | Administrator | Admin Menu > Certificates | -> SCR-02, SCR-03 | - |
| SCR-02 | Add Certificate | Modal | Administrator | SCR-01 ("Add Certificate" button) | -> SCR-01 | - |
| SCR-03 | Securities Certificate Details | Drawer/Side-panel | Administrator | SCR-01 (Click on a row) | -> SCR-01 | - |

---

## 2) Screen Details

### SCR-01 — Securities Certificate Management (List)
**Purpose:** Provide a high-level view of all issued certificates, distinguishing between Corporate and Individual member types.  
**Primary Actor:** Administrator  
**Entry Conditions:** Admin Menu > Certificates  

#### A. Layout / Sections (functional)
- **Header:** Title ("Securities Certificate Management") with total count badge (e.g., "24") + "+ Add Certificate" button + "Export to Excel" dropdown.
- **Tabs:** "ALL", "ACTIVE", "INACTIVE".
- **Content area:** Data table.
  - Columns: No, Certificate No, Plan name, Insurance Company, Member type (Display as Badges: `Corporate` (Orange), `Individual` (Blue/Purple)).

#### B. Components & Data Binding
| Component | Type | Notes |
|---|---|---|
| "+ Add Certificate" | Button | Opens SCR-02 |
| "Export to Excel" | Dropdown | Triggers download of current view |
| Data Table | Table | Paginated list (e.g., 1-10 of 23) |

#### C. User Interactions
- **Click "+ Add Certificate":** Opens the creation modal.
- **Click a table row:** Opens the right-side detail drawer.
- **Status Validation Rule:** A Certificate can only be marked as `Active` if:
  1. A valid Certificate PDF is uploaded.
  2. The current date falls within the valid Start Date / End Date range.

---

### SCR-02 — Add Certificate
**Purpose:** Allow admins to register a plan structure by mapping the plan, corporate entity, and uploading an Excel file to bulk-enroll users.  
**Primary Actor:** Administrator  

#### A. Layout / Sections (functional)
- **Header:** Title ("Add Certificate") + Close (x).
- **Form Fields (Left Side):**
  - **Plan Selection:** Dropdown (từ api get list plan) (e.g., "Chubb Safe Delivery", "General Liability").
  - **Certificate No:** Text Input.
  - **Insurance Company:** get from plan(trong plan có trường thông tin về insurance company nên có thể auto fill vào đây).
  - **Member Type:** Radio buttons (`Corporate`, `Individual`).
  - **Corporate Name:** Dropdown. (RULE: Hidden if Member Type == Individual).
  - **Certificate Status:** Radio buttons (`Active`, `Inactive`).
- **Form Fields (Right Side):**
  - **User List Upload:** Drag & Drop / Selection box. (Accepts `.xls`, `.xlsx`. Max 100MB). **RULE:** Only 1 file allowed, Hidden if Member Type == Individual.
  - **Download template:** Link text. (RULE: Hidden if Member Type == Individual).
  - **Certificate PDF Upload:** File upload box (Accepts `.pdf`). **RULE:** `Required` if Status == `Active`. `Optional` if Status == `Inactive`.
  - **Date Pickers:** Start Date, End Date (YYYY-MM-DD).
  - **Date-Time Pickers:** Enroll Start At, Enroll End At (YYYY-MM-DD HH:mm).
- **Footer:** "Cancel" button, "Create Certificate" button.

#### C. User Interactions (The Bulk Mapping Flow)
1. Admin fills in basic details and selects Corporate Name.
2. Admin uploads the User List Excel file.
3. System UI: Shows `Filename.xls` with a remove (x) icon.
4. Admin clicks **"Create Certificate"**.
5. **System Verification & Assignment Logic:**
   - **Check Corporate Entity:** Confirm corporate exists/register if new.
   - **Check User Existence (Excel):**
     - If User exists in system -> Use existing User ID.
     - If User does NOT exist -> Auto-create new User Account based on Excel data (Phone/Name).
   - **Check Plan Mapping:**
     - If User ID already has this Plan -> Skip assignment (avoid duplicates).
     - If User ID does NOT have this Plan -> Assign Plan. (Status: Pending Activation).
   - **Activate:** Link the uploaded PDF/Certificate to the corporate group. Activate the plan for these users.
6. **Success State:** Green toast notification "Certificate added!" appears at the bottom.

---

### SCR-03 — Securities Certificate Details
**Purpose:** View and modify existing certificate details, check the uploaded user list, and manage renewal history.  
**Primary Actor:** Administrator  
**Entry Conditions:** Clicking a row in SCR-01.  

#### A. Layout / Sections (functional)
- **Header:** Certificate No + Status Dropdown Badge ("Active"/"Inactive") + Close (x) and Nav (< >).
  - **Validation Note:** The dropdown allows changing the status to `Active` **only if** a Certificate PDF exists (uploaded via the Certificate File tab) and the current date is within the defined Start/End Date range. Otherwise, display an error toast.
- **Tabs:** "General information", "Certificate File", "Renewal History".
- **Tab: General Information (Read-only Top Box):** Plan Name, Plan Type, Total Covered Users (Count).
- **Tab: General Information (Editable Form):** Certificate No, Insurance Company, Member Type, Corporate Name, Certificate Status.
- **Tab: General Information (File List):** Uploaded User List file link (`Filename.xls` -> "Click to download").
- **Tab: General Information (Dates):** Start/End Dates, Enroll Dates, Created at/Updated at.
- **Tab: Certificate File (Dedicated Tab):** Area to view/upload/replace the official Certificate PDF file.
- **Tab: Renewal History:** List/Table tracking historical enrollments or policy updates over time.
- **Footer:** "Delete" (Red), "Reset", "Update" (Primary).

#### C. User Interactions
- **Download Excel:** Clicking the file link downloads the originally parsed Excel list.
- **Update:** Saves changes to the certificate fields.

---

## 3) Developer Notes
- **API Reference:** certificate related: /plan-certificates.
- **Data Consumption:** After the bulk mapping and activation process, the resulting enrollment data must be viewable on the **Plan Subscription Status** page (api: `/user-plan`).
