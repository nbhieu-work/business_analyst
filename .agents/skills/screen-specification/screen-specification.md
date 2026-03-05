---
name: screen-specification
description: |
  Describe and specify UI screens in a developer- and designer-friendly way.
  Use when you need a structured screen-by-screen specification: layout, components, fields, states,
  empty/error/loading, interactions, permissions, validations, and copy guidelines.
metadata:
  author: hienub
  version: "1.0.0"
---

# Screen Specification

## Purpose
Produce **screen-by-screen UI specifications** that are:
- Clear for developers to implement
- Clear for designers to design
- Traceable to user stories and acceptance criteria

## Inputs
- Feature spec (user stories, acceptance criteria, flows)
- Extraction (data elements, state model, permissions, NFR)
- Any existing design reference (Figma link, screenshots) if available

If design reference is missing, describe screens **functionally** (no visual styling).

## Output
Return a **single markdown document** containing a screen inventory and detailed specs per screen.

## Required Output Structure
Use this template:

```markdown
# Screen Spec — <FEATURE_CODE> — vX.Y
**Last updated:** YYYY-MM-DD  
**Related:** US-xx, BR-xx, DE-xx, NFR-xx  

## 1) Screen Inventory
| Screen ID | Screen Name | Type | Primary Actor | Entry Points | Exit Points | Related US |
|---|---|---|---|---|---|---|
| SCR-01 | ... | List/Detail/Form/Modal | ... | ... | ... | US-01 |

## 2) Screen Details

### SCR-01 — <Screen Name>
**Purpose:**  
**Primary Actor:**  
**Related Stories:** US-xx  
**Permissions:** roles allowed + denied  
**Entry Conditions:** (preconditions / required state)  
**Exit Conditions:** (what happens after success/cancel)  

#### A. Layout / Sections (functional)
- Header: ...
- Filter/Toolbar: ...
- Content area: ...
- Footer/Actions: ...

#### B. Components & Data Binding
| Component | Type | Data Source (DE-xx / API) | Rules/Validation | Visibility/Role | Notes |
|---|---|---|---|---|---|
| Vehicle list | Table/List | DE-01.. | ... | ... | ... |

#### C. User Interactions
- Click actions (buttons/rows)
- Navigation routes
- Inline actions (edit/delete)
- Bulk actions (if any)

#### D. States
- Loading state (skeleton/spinner + what disabled)
- Empty state (message + CTA)
- Error state (message + retry)
- Partial failure (some rows failed)
- Permission denied state

#### E. Validations & Error Messages
- Field validations (min/max/format)
- Error messages (guideline + examples)

#### F. Analytics / Audit Events
| Event | Trigger | Properties |
|---|---|---|
| ui.export_csv_clicked | click Export | userId, filters, rowCount |

#### G. Edge Cases to Handle
- EC-xx...
- Concurrency (data changed while viewing)
- Large data sets (pagination, rate limits)

#### H. Design Notes (optional)
- If there is a design system: component references
- Accessibility notes (keyboard nav, focus, aria)

#### I. Rules
- MUST reference IDs (US/BR/DE/EC/NFR) where applicable.
- MUST specify screen states (loading/empty/error).
- MUST specify permissions and disabled/hidden behavior.
- Avoid visual styling directions unless provided by design system.
- Keep copy text concise; provide example messages where needed.