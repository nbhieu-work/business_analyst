# Affiliated Company Management - UI Specification & Workflow

Manage the registry of corporate partners and affiliated companies. This specification defines the UI components and workflows for administrators to view, create, edit, and toggle the active status of affiliated company records.

## Documentation Overview

This module allows administrators to:
- Maintain a centralized directory of all affiliated delivery companies.
- Register new companies with their official business registration numbers.
- Update company details to keep records accurate.
- Manage the availability of companies (Active vs. Inactive) to control user selection options without deleting historical data.

---

## 1. Affiliated Company List View

The central dashboard for managing corporate partnerships and affiliations. 

### Features
- **Tab Navigation**: Filter companies rapidly by their state: `ALL`, `ACTIVE`, and `INACTIVE`.
- **Top Bar Actions**: 
  - `+ Add Company` button (black primary button) to register a new corporate entity.
  - Search bar to find companies by name.
- **Data Table**:
  - **No**: Auto-incrementing index.
  - **Company Name**: The legal or registered name of the affiliate (shown as primary blue link; clickable).
  - **Status**: Visual indicator of the company's current state (`Active` [Green] or `Inactive` [Gray]). Clicking the badge opens a quick-change menu.
  - **Business registration no**: The official corporate registration number (format: `XXX-XX-XXXXX`).
  - **Create at**: Date the record was created (YYYY-MM-DD).
  - **Update at**: Date of the last modification (YYYY-MM-DD).
  - **Row Action**: The `Edit` button appears on the far right of the row.
- **Status Context Menu**: Clicking the Status badge triggers a small popover dropdown to quickly toggle between `Active` and `Inactive` states. Includes `Cancel` and `Apply` actions to confirm the state change.
- **Pagination**: Standard table controls (Rows per page: 30, First, Previous, Page Indicator, Next, Last).

---

## 2. Create Company Modal

The interface used to register a new affiliated company into the system.

### Form Fields
| Field Name | Type | Required | Description |
|------------|------|----------|-------------|
| **Company name** | Text Input | Yes | The official name of the company (Placeholder: "Enter company name"). |
| **Registration number** | Text Input | Yes | The business registration number (Placeholder mask: "000 - 00 - 00000"). |

### Elements
- **Header**: "Create company" with a generic close (X) button.
- **Actions**: `Cancel` (closes modal without saving) and `Create Company` (submits data to the backend). Both positioned at the bottom right.

---

## 3. Edit Company Modal

The interface used to modify an existing corporate record.

### Form Fields
| Field Name | Type | Required | Description |
|------------|------|----------|-------------|
| **Company name** | Text Input | Yes | The official name of the company, pre-filled with existing data. |
| **Registration number** | Text Input | Yes | The business registration number, pre-filled with existing data. |

### Elements
- **Header**: "Edit company" with a generic close (X) button.
- **Actions**: `Cancel` (closes modal without saving) and `Save` (applies the modifications to the database).

---

## Workflow: Corporate Entity Lifecycle

The management lifecycle revolves around accurate record-keeping and state management rather than permanent deletion.

### State 1: Registration
- **Trigger**: Admin clicks `+ Add Company`.
- **Behavior**: The "Create company" modal opens. Admin enters the company name and registration number. Upon clicking `Create Company`, a new record is generated. The system records the current timestamp as both `Create at` and `Update at`. The status commonly defaults to `Active`.

### State 2: Modification
- **Trigger**: Admin clicks the `Edit` button on a specific row.
- **Behavior**: The "Edit company" modal opens with current values. Admin updates the name or registration number. Changes are saved upon clicking `Save`, and the `Update at` timestamp is refreshed to the current date.

### State 3: Status Toggling (Active / Inactive)
To preserve historical data mappings (especially for users and their vehicles), companies are typically not deleted. They are toggled to an Inactive state.
- **Trigger**: Admin clicks the Status badge on a list row, selects a new state from the dropdown menu, and clicks `Apply`.
- **Behavior**: 
  - **Active** (Green): The company operates normally and is available for selection in other modules (e.g., when a driver registers a vehicle and selects an affiliate).
  - **Inactive** (Gray): The company remains in the database for historical and reporting purposes but is hidden from public selection lists for new user mapping. The `Update at` timestamp gets refreshed upon status change.
