# Screen Spec — MS-001 — v1.0
**Last updated:** 2026-03-09  
**Related:** US-01, US-05, BR-01, EC-03

## 1) Screen Inventory
| Screen ID | Screen Name | Type | Primary Actor | Entry Points | Exit Points | Related US |
|---|---|---|---|---|---|---|
| SCR-01 | Prominent Disclosure (Privacy) | Modal/Full | End User | First-time "Go On Duty" | Accept -> Native Prompt, Deny -> Cancel | US-01 |
| SCR-02 | Telemetry Debug Hub | Detail | Admin/Dev | Tap App Version 7x | Back to Home | US-05 |

---

## 2) Screen Details

### SCR-01 — Prominent Disclosure (Privacy)
**Purpose:** Ensure GDPR / App Store / Play Store compliance by explaining exactly *why* and *how* location data is collected in the background before asking for OS-level permissions.  
**Primary Actor:** End User  
**Related Stories:** US-01  
**Permissions:** 
- `ALLOWED`: All Users (Requires no special auth)
**Entry Conditions:** User presses "Start Shift" or "Go On Duty" but has not yet granted `ACCESS_BACKGROUND_LOCATION` (Android) or `Always` (iOS).  
**Exit Conditions:** User clicks "I Understand" triggering native OS prompt, or clicks "Cancel".  

#### A. Layout / Sections (functional)
- **Header:** Illustration (Map/Pin icon)
- **Title:** "We need your location to track your route"
- **Body Text:** "This app collects location data to enable route tracking and accurate mileage calculation even when the app is closed or not in use. This tracking only occurs while you are actively 'On Duty'."
- **Action Area:** Primary Button ("I Understand"), Secondary Button ("Cancel/Not Now").

#### B. Components & Data Binding
| Component | Type | Data Source (DE / API) | Rules/Validation | Visibility/Role | Notes |
|---|---|---|---|---|---|
| "I Understand" | Button | Native Action | - | All | Triggers OS permission dialog |
| "Cancel" | Button | - | - | All | Closes modal, stops flow |

#### C. User Interactions
- **Click "I Understand":** The screen fades and the native iOS/Android location permission pop-up appears immediately.

#### D. States
- **Loading State:** N/A (Static content).
- **Error State:** N/A.

#### E. Validations & Error Messages
- N/A

#### F. Analytics / Audit Events
| Event | Trigger | Properties |
|---|---|---|
| `ui.privacy_prompt_viewed` | Modal opened | userId |
| `ui.privacy_prompt_accepted` | Clicked "I Understand" | userId |
| `ui.privacy_prompt_denied` | Clicked "Cancel" | userId |

#### G. Edge Cases to Handle
- **Permission Permanently Denied:** If the user previously clicked "Don't ask again" on the OS prompt, clicking "I Understand" should route them directly to the App Settings page deep-link.

#### H. Design Notes
- Text must be highly legible. Google Play policy literally requires the phrase "even when the app is closed or not in use" so do not alter the body copy heavily.

---

### SCR-02 — Telemetry Debug Hub
**Purpose:** Give field engineers and developers a quick way to diagnose if GPS points are syncing correctly without plugging the phone into a debugger.  
**Primary Actor:** Admin, Developer, Field Tester  
**Related Stories:** US-05  
**Permissions:** 
- `ALLOWED`: Any user who knows the 7-tap secret.
**Entry Conditions:** 7 rapid taps on the App Version string in the Settings menu.  
**Exit Conditions:** Back button.  

#### A. Layout / Sections (functional)
- **Header:** Title ("Telemetry Debug")
- **Metrics Grid:** Count of Pending points in SQLite, Count of Uploaded points this session, Last Sync Timestamp.
- **Controls Section:** "Force Sync Now" button, "Clear Local Buffer" button (Danger zone).
- **Log View:** Scrolling text area showing recent background worker outcomes (e.g., `2026-03-09 10:14 - Sync failed: Network timeout`).

#### B. Components & Data Binding
| Component | Type | Data Source (DE / API) | Rules/Validation | Visibility/Role | Notes |
|---|---|---|---|---|---|
| Pending Count | Text | SQLite (`SELECT COUNT(*)`) | Updates every 1s | All | - |
| Force Sync | Button | Worker Trigger | - | All | Changes text to "Syncing..." |
| Clear Buffer | Button | SQLite (`DELETE FROM`) | Requires confirmation | All | Red button |

#### C. User Interactions
- **Click Force Sync:** Bypasses all battery/network logic and attempts an HTTP POST immediately.
- **Click Clear Buffer:** Pops an alert. Clears the SQLite DB completely.

#### D. States
- **Loading State:** Fetching SQLite counts.
- **Error State:** N/A

#### E. Validations & Error Messages
- N/A

#### F. Analytics / Audit Events
- N/A (Debug actions are generally excluded from product analytics).

#### G. Edge Cases to Handle
- **Buffer size is huge:** Querying `COUNT(*)` on 50k rows in SQLite on a slow thread might jitter the UI. Ensure DB queries are strictly non-blocking.

#### H. Design Notes
- Utilitarian developer UI. Dark theme/Console-like appearance is acceptable.
