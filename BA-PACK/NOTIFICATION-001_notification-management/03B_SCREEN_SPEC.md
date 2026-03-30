# Screen Spec — NOTIFICATION-001 — v1.0
**Last updated:** 2026-03-30
**Related:** US-Notification Management, BR-Notification Setup

## 1) Screen Inventory
| Screen ID | Screen Name | Type | Primary Actor | Entry Points | Exit Points |
|---|---|---|---|---|---|
| SCR-01 | Notification Type List | List | Administrator | Admin Sidebar > Notification management | -> SCR-02, -> SCR-03 |
| SCR-02 | Add Notification Type | Modal | Administrator | SCR-01 ("+ Add Types") | -> SCR-01 |
| SCR-03 | Notification Type Details | Side-panel / Drawer | Administrator | SCR-01 (Click row or Edit) | -> SCR-01 |

---

## 2) Screen Details

### SCR-01 — Notification Type List
**Purpose:** Manage and view all notification types configured in the system.  
**Primary Actor:** Administrator  
**Entry Conditions:** Navigate to Admin > Notification management.

#### A. Layout / Sections (functional)
- **Breadcrumb/Header:** "Notification / Notification Type" with total count badge (e.g., `24`).
- **Toolbar:** `+ Add Types` button (black) and `Search Notification Type` input field.
- **Filter Tabs:** `ALL`, `ON`, `OFF`.
- **Content Area:** Paginated Data table (e.g., Rows per page dropdown: 30, pagination controls).
  - **Columns:**
    - No
    - Notification Type Name (Clickable link, e.g., "Change status")
    - Trigger (Event key, e.g., `CHANGE_STT`)
    - Description
    - Status (Badge: `ON`/`OFF` with a dropdown arrow for inline toggling)
    - Create at (YYYY-MM-DD)
    - Update at (YYYY-MM-DD)
    - Action `Edit` text link.

#### B. User Interactions
- **Search:** Instant search or enter to filter by Type Name or Trigger key.
- **Filter Tabs:** Click to filter table rows by ON/OFF status.
- **Click row / "Edit" / Name:** Opens details drawer (SCR-03).
- **Click "+ Add Types":** Opens Add Modal (SCR-02).

---

### SCR-02 — Add Notification Type
**Purpose:** Create a new notification type foundational block (Name, Trigger Key).  
**Primary Actor:** Administrator  

#### A. Layout / Sections (functional)
- **Component:** Centered Modal Pop-up.
- **Header:** Title "Add Notification Type" + Close (X) icon.
- **Form Fields:**
  - **Type Name:** Text input. _Placeholder: "e.g., Payment Completed"_
  - **Trigger/Event Key:** Text input. _Placeholder: "e.g., Payment Completed"_ - (**Validation:** Must be unique, UPPERCASE with underscores commonly used).
  - **Description:** Textarea. _Placeholder: "Brief description (optional - for internal admin)"_
- **Footer Buttons:** `Cancel` (Secondary), `Create` (Primary Black).

---

### SCR-03 — Notification Type Details
**Purpose:** Comprehensive configuration for a notification type including delivery scheduling, deduplication, rules, and templates.  
**Primary Actor:** Administrator  
**Entry Conditions:** Click on a record in SCR-01.

#### A. Layout (Persistent Drawer Shell)
- **Component:** Right-side Slide-out Drawer.
- **Top Bar:** Close (X), Title "Notification Type Details", Next/Prev pagination arrows (`<` `>`).
- **Main Header Profile:**
  - Name of Notification (e.g., "Change status").
  - Status Badge: `ON`/`OFF` with dropdown to toggle.
  - Actions: `Send Test` button (Light Blue), `Edit` button (Primary Blue) to edit basic info.
- **Tabbed Navigation:**
  - `Template`
  - `Schedule` (Active in spec)
  - `Sending Rules` (Active in spec)

---

#### B. Tab: Schedule
**Purpose:** Determine *when* the notification is triggered and sent.

- **SELECT SENDING OPTION (Radio Cards):**
  1. **Send immediately:** Send as soon as the event is triggered.
  2. **Relative schedule:** Send after a specified delay from trigger.
  3. **Fixed date/time:** Send at a specific date and time.
  4. **Recurring schedule:** Send on a repeating schedule.

**Dynamic SCHEDULE SETTING sections:**
- **If `Relative schedule` is selected:**
  - **Max wait time:** Input field (e.g., `1.5`) alongside a unit Dropdown (e.g., `minutes`, `hours`).
  - *Hint text:* "If queue is busy, still attempt to send within this timeframe".
- **If `Fixed date/time` is selected:**
  - **Fixed Date & Time Configuration:** Combined Date/Time picker (e.g., `2026-01-12 | 12:00`).
- **If `Recurring schedule` is selected:**
  - **Frequency:** Radio buttons (`Daily`, `Weekly`, `Monthly`).
  - **Send time:** Time input field (HH:MM).
  - **Days of week:** Dropdown selector (e.g., "Custom selection") followed by Day Toggles (`Mon`, `Tue` (inactive), `Wed`, etc.) to choose specific days.
  - **Start date:** Date picker.
  - **End date (optional):** Date picker.
- **Footer Action:** `Schedule Notification` button (Black).

---

#### C. Tab: Sending Rules
**Purpose:** Restrict or refine *who* gets the notification and *how often* they can be spammed.

- **ELIGIBILITY (Define who can receive this notification):**
  - **Target Audience:** Radio buttons -> `All users` vs `Specific Users`.
    - *Dependency:* If `Specific Users` is chosen, display an Upload container: "Select file" button (Accepts `.xlsx` or `.xls`. Max 100MB).
  - **Only active users:** Master Toggle Switch (ON/OFF).
    - *Hint:* "Users who haven't logged in for 30+ days will not receive this notification".

- **DEDUPLICATION (Prevent duplicate notifications within a time window):**
  - **Toggle Switch:** ON/OFF.
  - **Deduplication strategy (Shown if ON):** Radio options -> `By user + notification type` vs `By user + trigger reference`.

- **RATE LIMITING (Control notification frequency per user):**
  - **Toggle Switch:** ON/OFF.
  - **Max per day (Shown if ON):** Input block (Hint: "Per user, per 24 hours").
  - **Max per week (Shown if ON):** Input block (Hint: "Per user, per 7 days").

- **RETRY & FAILURE HANDLING (Automatic retry for failed deliveries):**
  - **Toggle Switch:** ON/OFF.
  - **Maximum retries (Shown if ON):** Counter component with `-` [number] `+`.
  - **Max retry window (hours) (Shown if ON):** Number input block (e.g., `24`).

- **Footer Action:** `Save Rules` button (Black).
