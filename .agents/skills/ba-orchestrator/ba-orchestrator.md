---
name: ba-orchestrator
description: |
  Orchestrates a full Business Analyst pipeline for each feature request using existing skills:
  - deep-research (research with citations)
  - feature-specification (user stories + acceptance criteria)
  - specification-writing (technical spec for implementers)
  Enforces standardized output files, traceability IDs, and Change Control Mode C (current + patch + git).
license: MIT
metadata:
  author: hienub
  version: "1.0.0"
---

# BA Orchestrator (Meta Controller) — Mode C: current + patch + git

## Objective
Transform a raw request into a Dev-ready BA pack via a strict pipeline:

**Intake → Research (deep-research) → Extraction → Feature Spec (feature-specification) → Quality Gate → Technical Spec (specification-writing) → Dev Handoff Pack**

**Hard rule:** NEVER produce/refresh the Technical Spec unless Quality Gate = **PASSED**.

---

## Output Folder & Files (MANDATORY)
All outputs live under:

`./BA-PACK/<FEATURE_CODE>_<FEATURE_SLUG>/`

### Current files (always represent latest/current version)
- `00_INTAKE.md`
- `01_RESEARCH.md`
- `02_EXTRACTION.md`
- `03_FEATURE_SPEC.md`
- `04_QUALITY_GATE.md`
- `05_TECH_SPEC.md`
- `06_DEV_HANDOFF_PACK.md`

### Change tracking (Mode C)
- `00A_CHANGELOG.md` **(ALWAYS maintain; required)**

### Patch & impact files (ONLY when change request occurs)
- `03A_FEATURE_SPEC_PATCH_CR-XX_vX.Y.md`
- `05A_TECH_SPEC_PATCH_CR-XX_vX.Y.md` (only if tech impacted)
- `04B_IMPACT_ANALYSIS_CR-XX_vX.Y.md` (only if impact is Medium/High)

> Notes:
> - Mode C updates CURRENT files in place and relies on Git for full historical snapshots.
> - Patch/impact files provide human-friendly diffs and impact summaries per change request.

---

## Naming Rules (MANDATORY)
- `FEATURE_CODE`: provided by user (e.g., RMS-210, VA-89). If not provided, use `TEMP-001`.
- `FEATURE_SLUG`: kebab-case, English, max 6 words (e.g., `refund-cancel-contract`).
- Dates: `YYYY-MM-DD`
- Traceability IDs (stable across all files):
  - `BR-01…` Business Rules
  - `EC-01…` Edge Cases
  - `DE-01…` Data Elements
  - `US-01…` User Stories
  - `NFR-01…` Non-functional requirements
  - `CR-01…` Change Requests

---

## Document Header (MANDATORY)
Every CURRENT file (`00..06`) must begin with:

- Feature: `<FEATURE_CODE> <FEATURE_SLUG>`
- Version: `vX.Y`
- Last updated: `YYYY-MM-DD`
- Change request: `CR-XX` (or `N/A` for baseline)

---

## Versioning & Change Control (MANDATORY) — Mode C

### Version
- Maintain `VERSION = vMAJOR.MINOR` in `00A_CHANGELOG.md` and in the header of current files.
- `v1.0` = initial baseline.
- Increment `MINOR` when: clarifications, add AC/edge cases, no breaking change to API/state/data contract.
- Increment `MAJOR` when: changes impacting scope/behavior/state model/data contract/API (breaking or significant).

### Change Request (CR)
- Each requirement change gets a CR ID: `CR-01`, `CR-02`...
- For any CR:
  1) Update CURRENT files (usually `03`, `05`, `06`; and `02`/`04` if impacted)
  2) Generate patch files named with `CR + version` (`03A`, optionally `05A`)
  3) Generate impact analysis file (`04B`) if impact is `Medium/High`
  4) Update `00A_CHANGELOG.md` with CR entry and new `VERSION`
  5) Re-run Quality Gate (`04`) and ensure **PASSED** before producing/refreshing `05_TECH_SPEC.md`

### Source of truth
- CURRENT files = latest truth.
- Historical versions are traced via Git commits/tags and referenced in CHANGELOG (commit hash / PR link if available).

---

## Change Request Mode (Detection & Inputs)
If input indicates an update/revision/change request (keywords like: `change request`, `revise`, `update requirement`, `new stakeholder feedback`, `modify`, `adjust`),
then treat as **CHANGE REQUEST MODE**.

In CHANGE REQUEST MODE, the input SHOULD include (if not present, infer and mark assumptions):
- What changed (summary)
- Why changed (reason)
- Requested by (stakeholder/team)
- Urgency / target release (optional)

---

## 00A_CHANGELOG.md Template (MANDATORY)
Create if missing, update on every baseline and CR.

# <FEATURE_CODE> — Change Log

## Current Version
- **Version:** vX.Y
- **As of:** YYYY-MM-DD
- **Status:** Draft | Review | Approved | In Progress | Released
- **Baseline source:** (ticket/epic/link)

## Change Requests
### CR-01 — YYYY-MM-DD — Short title
- **Version:** v1.1
- **Requested by:** name/team
- **Reason:** why
- **Summary of changes:**
  - ...
- **Impacted files:**
  - 03_FEATURE_SPEC.md
  - 05_TECH_SPEC.md (if any)
  - ...
- **Artifacts:**
  - 03A_FEATURE_SPEC_PATCH_CR-01_v1.1.md
  - 04B_IMPACT_ANALYSIS_CR-01_v1.1.md (if any)
  - 05A_TECH_SPEC_PATCH_CR-01_v1.1.md (if any)
- **Git reference:** commit hash or PR link if available
- **Notes/Decisions:** ...

---

# Orchestration Pipeline (MANDATORY)

## Step 0 — Identify Feature & Mode
1) Determine `FEATURE_CODE` + `FEATURE_SLUG`.
2) Determine Mode:
   - Baseline Mode → create/maintain `v1.0` if first time.
   - Change Request Mode → create `CR-XX`, compute next `VERSION` (vMAJOR.MINOR).
3) Ensure output folder exists: `./BA-PACK/<FEATURE_CODE>_<FEATURE_SLUG>/`
4) Create/Update `00A_CHANGELOG.md` accordingly (baseline entry or CR entry skeleton).

---

## Step 1 — 00_INTAKE.md (Orchestrator writes)
Purpose: normalize the request.

Must include:
A. Problem Statement (1–2 sentences)  
B. Goals & Success Metrics (measurable)  
C. Actors/Personas  
D. Scope In / Scope Out  
E. Constraints (platform, compliance, locale, SLA, security, audit)  
F. Known Systems & Dependencies  
G. Risks & Assumptions  
H. Open Questions (Top 10, prioritized)  
I. Glossary  
J. Confidence (High/Medium/Low) + rationale  

---

## Step 2 — 01_RESEARCH.md (Delegate to `deep-research`)
**MUST activate and follow skill `deep-research` for this stage.  
Do not produce `01_RESEARCH.md` without applying the skill.**

Input:
- `00_INTAKE.md` (especially Open Questions, Constraints, Dependencies)

Output file: `01_RESEARCH.md` (normalize to this structure even if the skill uses a different template):
- Executive Summary
- Key Findings (with citations [1], [2]… if sources exist)
- Detailed Analysis (organized by Intake open questions)
- Areas of Consensus / Debate (if relevant)
- Sources (with credibility notes)
- Gaps and Further Research (remaining unknowns)

Rules:
- If no sources are available, write “Research Plan + Hypotheses” and DO NOT fabricate citations.
- Keep research scoped to what impacts the feature’s design/spec.

---

## Step 3 — 02_EXTRACTION.md (Orchestrator writes)
Purpose: extract implementation-driving essentials from:
- `00_INTAKE.md` + `01_RESEARCH.md`

Must include:

A. Business Rules (`BR-01…`)  
B. Edge Cases (`EC-01…`)  
C. Data Elements (`DE-01…`) table with:
   - name, type, validation, source of truth, nullable?, default?, notes  
D. State Model:
   - statuses, transitions, triggers, ownership (system vs manual)  
E. Permissions Matrix:
   - role → allowed actions (CRUD + approvals + overrides)  
F. Non-functional Requirements (`NFR-01…`):
   - performance, security, audit/logging, monitoring, accessibility (if UI)  
G. Risks/Fraud/Abuse scenarios (if relevant)  
H. Traceability mapping:
   - BR/EC/DE/NFR back to Intake items and Research findings  

---

## Step 4 — 03_FEATURE_SPEC.md (Delegate to `feature-specification`)
**MUST activate and follow skill `feature-specification` for this stage.  
Do not produce `03_FEATURE_SPEC.md` without applying the skill.**

Input:
- `00_INTAKE.md` + `01_RESEARCH.md` + `02_EXTRACTION.md`

Output file: `03_FEATURE_SPEC.md` (normalize to include these items even if the skill template differs):
- Overview (goal, scope)
- (If persona docs missing) Proto-persona (5 lines): actor/goal/pain points/context
- User Stories (`US-01…`) using INVEST mindset
- Acceptance Criteria (Given/When/Then) per US
- Flows:
  - Main flow + Alternative flows + Error flows
- Error handling & messaging guidelines
- Analytics/Events (event name, properties)
- Edge Case coverage mapping (which EC covered by which US/AC)
- Non-functional Requirements references (NFR-xx)
- Dependencies
- Out of Scope
- Open Questions carried forward
- Traceability table: `US ↔ BR/EC/DE/NFR`

---

## Step 5 — 04_QUALITY_GATE.md (Orchestrator writes)
Purpose: validate completeness BEFORE technical spec.

Checklist categories (each must be PASS/WARN/FAIL with notes):
1. Scope & Actors clarity
2. Rules + Edge Cases coverage
3. Data dictionary minimum viability (DE list actionable)
4. State model clarity (transitions + ownership)
5. Permissions clarity (role/action matrix)
6. NFR clarity (audit/logging/security/performance)
7. Testability (AC measurable & unambiguous)
8. Consistency (terminology/IDs stable across docs)
9. Dependencies & risks identified
10. Open questions manageable (<=10, prioritized)

Overall:
- **PASSED** only if no FAIL items.
- If FAILED:
  - Classify each FAIL item as **Minor** or **Major**:
    - **Minor** = missing small details (e.g., 1–3 AC, a couple of EC mappings, a few DE validations, a missing NFR note) without changing overall structure.
    - **Major** = missing/unclear core structure (e.g., missing flows, incomplete story set, unclear state model, inconsistent terminology across docs, unclear permission model).
  - Apply fixes using the correct method:
    - For **Minor** FAIL items: patch the relevant CURRENT files directly (`02_EXTRACTION.md` and/or `03_FEATURE_SPEC.md`) while preserving IDs and headings.
    - For **Major** FAIL items: re-delegate to the respective skill and regenerate the affected file(s) to preserve the skill’s formatting:
      - Feature issues → re-invoke `feature-specification` to regenerate `03_FEATURE_SPEC.md`
      - Research issues → re-invoke `deep-research` to regenerate `01_RESEARCH.md`
      - Tech issues are not applicable here because `05_TECH_SPEC.md` must not exist before PASS
  - Re-run Quality Gate until PASSED (max 2 iterations).
  - If still failing: include a `NEEDS_HUMAN_INPUT` section listing blocking decisions/questions.

In CHANGE REQUEST MODE:
- If impact is Medium/High, also generate:
  - `04B_IMPACT_ANALYSIS_CR-XX_vX.Y.md` using the template below.

---

## Step 5B — 04B_IMPACT_ANALYSIS_CR-XX_vX.Y.md (Only if Medium/High impact)

# Impact Analysis — <FEATURE_CODE> — CR-XX — vX.Y
**Date:** YYYY-MM-DD  
**Impact level:** Low | Medium | High

## Change summary
- ...

## Impact areas
- Business rules impacted: BR-xx...
- Data impacted: DE-xx...
- State transitions impacted: ...
- Permissions impacted: ...
- API/contract impacted: ...
- UX/flow impacted: ...

## Risks introduced & mitigations
- ...

## Regression scope (must test)
- ...

---

## Step 6 — 05_TECH_SPEC.md (Delegate to `specification-writing`) ONLY IF QUALITY GATE PASSED
**MUST activate and follow skill `specification-writing` for this stage.  
Do not produce `05_TECH_SPEC.md` without applying the skill.**

Inputs:
- `00_INTAKE.md` (constraints, SLAs, glossary, assumptions)
- `03_FEATURE_SPEC.md`
- `02_EXTRACTION.md`
- `04_QUALITY_GATE.md` (must be PASSED)
- `01_RESEARCH.md` (as research findings reference)

Output file: `05_TECH_SPEC.md` must include (normalize if needed):
- Header (Date/Status/Author) + required document header above
- Overview
- Motivation: Current state → problems → desired state (mark assumptions if unknown)
- Research Findings (summarize from 01_RESEARCH.md)
- Design Decisions table (with rationale)
- Architecture (ASCII diagrams)
- API Contract draft:
  - endpoints list + request/response JSON examples + error codes
- Data Model outline (entities, keys, constraints)
- AuthN/AuthZ mapping (tie to Permissions Matrix)
- Audit & Logging (events, fields, retention)
- Implementation Plan (Phase 1 detailed)
- Edge Cases (tie to EC-xx)
- Open Questions
- Success Criteria (checkboxes)
- Traceability: API/Model/Decisions ↔ US/BR/DE/NFR

Hard rule:
- If Quality Gate is not PASSED, do not create or refresh this file.

In CHANGE REQUEST MODE:
- If the change impacts APIs/data model/state/permissions/NFR, you MUST refresh `05_TECH_SPEC.md` after Quality Gate PASSED and generate `05A_TECH_SPEC_PATCH_CR-XX_vX.Y.md`.
---

## Step 6A — Patch files for CHANGE REQUEST MODE (generate as needed)
### 03A_FEATURE_SPEC_PATCH_CR-XX_vX.Y.md (ALWAYS for CR)

# Feature Spec Patch — <FEATURE_CODE> — CR-XX — vX.Y
**Date:** YYYY-MM-DD  
**Requested by:** name/team  
**Reason:** why

## What changed (Summary)
- ...

## Spec sections changed
- **Section:** heading/path
  - **Before:** 1–3 bullets or short excerpt
  - **After:** 1–3 bullets or short excerpt

## Impacted items
- User Stories: US-xx...
- Business Rules: BR-xx...
- Edge Cases: EC-xx...
- Data Elements: DE-xx...
- State model: status/transition
- NFR: NFR-xx...

## Dev action checklist
- [ ] Update implementation for ...
- [ ] Update tests for ...


### 05A_TECH_SPEC_PATCH_CR-XX_vX.Y.md (ONLY if tech impacted)

# Tech Spec Patch — <FEATURE_CODE> — CR-XX — vX.Y
**Date:** YYYY-MM-DD  

## What changed (Summary)
- ...

## Contract changes
- Endpoints impacted:
  - METHOD PATH — change: ...
- Schema changes:
  - field: before → after
- Error codes changes:
  - ...

## Backward compatibility / migration
- ...

## Dev/QA checklist
- [ ] Update backend...
- [ ] Update frontend...
- [ ] Regression tests...


---

## Step 7 — 06_DEV_HANDOFF_PACK.md (Orchestrator writes)
Purpose: one-stop summary for developers.

Must include:
A. TL;DR (what to build)  
B. In-scope / Out-of-scope  
C. Key assumptions + decisions  
D. Must-have User Stories list (`US-xx`)  
E. Endpoints summary (from Tech Spec)  
F. Data fields summary (top 20 `DE-xx`)  
G. State transitions summary  
H. Permission matrix summary  
I. Test checklist: map AC → tests (smoke + edge)  
J. Open questions for sprint planning  

In CHANGE REQUEST MODE:
- Add a “What changed since last version” section that points to patch/impact files.

---

## Execution Order (MANDATORY)
1) Step 0 — Identify Feature & Mode; update `00A_CHANGELOG.md`
2) Step 1 — Write `00_INTAKE.md`
3) Step 2 — Generate `01_RESEARCH.md` via `deep-research`
4) Step 3 — Write `02_EXTRACTION.md`
5) Step 4 — Generate `03_FEATURE_SPEC.md` via `feature-specification`
6) Step 5 — Write `04_QUALITY_GATE.md` (and `04B` if needed)
7) Step 6 — If PASSED, generate/refresh `05_TECH_SPEC.md` via `specification-writing`
8) Step 6A — If CR mode, generate patch files (`03A`, optionally `05A`)
9) Step 7 — Write `06_DEV_HANDOFF_PACK.md`

---

## Hard Constraints (MANDATORY)
- Do not create/refresh `05_TECH_SPEC.md` unless Quality Gate is PASSED.
- IDs (BR/EC/DE/US/NFR/CR) must remain stable and consistent across files.
- Normalize skill outputs to the required section headings and file contracts of this orchestrator.
- Do not fabricate research citations; if sources missing, provide plan/hypotheses.
