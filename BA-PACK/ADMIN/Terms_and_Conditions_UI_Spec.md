# Terms and Conditions Management - UI Specification & Workflow

Manage the lifecycle of legal terms, policies, and system agreements. This specification defines the UI components and workflows for administrators to draft, schedule, publish, and track user agreements for platform terms.

## Documentation Overview

This module allows administrators to:
- Maintain multiple versions of terms and conditions.
- Schedule policies to go live at a specific date and time.
- Track exactly which users have agreed to which version of a term.
- Organize terms by target audience and display placement.

## 1. Terms and Conditions List View

The central dashboard for managing all platform terms. 

### Features
- **Tab Navigation**: Filter terms quickly by `ALL`, `PUBLISHED`, `SCHEDULED`, and `DRAFT`.
- **Top Bar Actions**: 
  - `+ Add` button to create a new term.
  - `Display Page` dropdown filter (e.g., All, Register, Plan Detail, Profile).
  - Search bar to find terms by name.
- **Data Table**:
  - **No**: Auto-incrementing index.
  - **Term name**: The title of the agreement.
  - **Target**: Where the term is displayed (e.g., Register, Plan Detail, Profile).
  - **Latest version**: The version number of the most recent draft or publication (e.g., 1.0).
  - **Status**: The current state of the term (Draft [Gray], Scheduled [Purple], Published [Green]).
  - **Last update**: Date of the last modification (YYYY-MM-DD).
- **Pagination**: Standard controls (Rows per page: 30, First, Previous, Page Indicator, Next, Last).

---

## 2. Add / Edit Terms and Conditions

The main authoring interface for creating a new term or editing an existing one as a new draft.

### Layout Breakdown
The screen consists of a top action bar, a main content editor, and a right-hand settings panel.

#### Top Action Bar
- Breadcrumb navigation (`Folder Icon Terms and Conditions / Add Terms and Conditions`).
- Actions: 
  - `Save draft` (Saves progress without versioning or publishing).
  - `Publish` (Triggers the Publish Version modal).

#### Main Editor Area
A What-You-See-Is-What-You-Get (WYSIWYG) rich text editor.
- **Toolbar**: Headings (H1, H2, etc.), Bold, Italic, Underline, Strikethrough, Unordered List, Ordered List, Table, Emoji.
- **Helper Tip**: *"Tip: Use [H2] tag to create a block for each clause."*
- **Canvas**: The "Enter / Select a block" area for typing the agreement text.

#### Right Settings Panel (Setting Tab)
Configuration meta-data for the term.

| Field Name | Type | Description |
|------------|------|-------------|
| **Term Name** | Text Input | Title of the term (e.g., "Service Agreement"). Max 20 characters. |
| **Show term name as title** | Checkbox | Toggles whether the Term Name appears as an H1 heading automatically in the UI. Checked by default. |
| **Display Page** | Dropdown Menu | Controls where the term appears. If set to `Other`, a secondary input explicitly asks to "Enter new Display Page". |
| **Term Type** | Radio Buttons | Defines user obligation: `Read only`, `Required agreement`, `Optional agreement`. |
| **Agreement note** | Toggle + Text Input | If toggled ON, allows admin to provide a custom text note next to the checkbox (e.g., "I accept the privacy policy") up to 50 characters. |

---

## 3. Term Details & Version History

Clicking on a term opens a detailed split-view layout to manage its historical versions and content.

### Layout Breakdown
The screen is divided into two primary sections:

1. **Left Panel: Version History Timeline**
   - Displays a chronological vertical timeline of all versions for the selected term.
   - Shows total version count at the top.
   - **For each version node**:
     - **Status Bubble & Version Title** (e.g., `🟢 20260104`).
     - **Timestamp**: Published/Scheduled date and time (e.g., `published on 2026-02-12 06:22`).
     - **Agreement Stats**: A clickable link showing the number of users who accepted this version (e.g., `👥 1.3k agreed`).
     - **Context Menu (⋮)**: Actions for the specific version (e.g., Edit, Delete, Duplicate).

2. **Right Panel: Content Preview & Settings**
   - **Tabs**: `Setting`, `History` (Active), `Preview`.
   - **Header**: Title of the term with a generic close (X) button.
   - **Content Area**: Shows the raw text/HTML of the term policy.
   - **Footer (Preview)**: Shows how the agreement checkbox will appear to the user (`[x] I agree`).

---

## 4. Publish Version Modal

The interface used when pushing a term version from Draft to Scheduled or Published.


### Form Fields
| Field Name | Type | Required | Description |
|------------|------|----------|-------------|
| **Version title** | Text Input | Yes | A unique identifier for the version (typically a date code like `20260201`). |
| **Description** | Text Area | No | Internal notes on what changed in this version. |
| **Publish date & time** | Date/Time Picker | Yes | The exact date and time the term goes live (e.g., `2026-02-23` `19:00`). |

### Elements
- **Information Alert**: Warns the administrator: *"Terms with the same Display Page and publish date time will be displayed simultaneously in the user policy update notification."*
- **Actions**: `Cancel` (closes modal) and `Publish` (saves the state and triggers scheduling).

---

## 5. Agreed Users Modal

The interface that appears when an admin clicks the "X agreed" link in the Version History timeline.

### Features
- **Header**: Displays the total count of agreements (e.g., `Agreed users (1,123)`).
- **User List**: 
  - Each row shows the user's Profile Avatar, Name (e.g., `홍민서`), and identifying account name/ID (e.g., `kim_hana`).
- **Interaction**: This is strictly a read-only list for auditing purposes.

---

## Workflow: Term Publishing Lifecycle

The lifecycle of a term version operates on a strict state machine.

### State 1: Draft
- **Trigger**: An admin creates a new term or clicks "Edit" on an existing version to create a new iteration.
- **Behavior**: The term is saved in the database but is entirely invisible to end-users. The status label is **Draft (Gray)**.

### State 2: Scheduled
- **Trigger**: From a Draft state, the admin fills out the "Publish version" modal and sets a "Publish date & time" that is in the *future*.
- **Behavior**: The system queues the term to go live. The status label changes to **Scheduled (Purple)**. It is still invisible to end-users. Admin can cancel scheduling to revert to Draft.

### State 3: Published
- **Trigger**: The server's clock reaches the scheduled "Publish date & time", OR the admin sets the time to "Now" during the publish modal.
- **Behavior**: 
  - The status label changes to **Published (Green)**.
  - The term is immediately visible to users on the specified "Display Page" (Target).
  - If a user triggers the associated target page, they are prompted to agree to this new version.
  - Tracking begins for the "Agreed Users" count.
