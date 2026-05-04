Feature: MS-001 mobile-sensing-gps-data-collection
Version: v1.0
Last updated: 2026-03-09
Change request: N/A

# Deep Research

## Executive Summary
Developing a resilient mobile GPS sensing architecture demands explicit handling of mobile OS background limitations, robust data structures for local buffering, and highly scalable server ingestion. We researched best-practices for battery-optimized telemetry to address Intake constraints.

## Key Findings
- **Background Execution:** iOS strictly limits persistent background location unless the blue bar (or background indicator) is visible and "Always" permission is granted. Android requires a Foreground Service with a sticky notification for continuous location collection.
- **Data Model Identifiers:** Tying location to a `userId` combined with a transient `session_id` (representing one operational "shift" or trip) is superior to strict `deviceId` bonding, as devices are replaced and sometimes shared.

## Detailed Analysis
### 1. Sampling & Battery Policy
- **Hypothesis:** We should employ a "Dynamic Distance-Time" sampling policy.
- **Why:** Fixed 1-second interval querying severely drains battery. Using Android Fused Location / iOS CoreLocation with rules like `minTime=5s` & `minDistance=10m` drops unnecessary identical pings. 

### 2. Idempotent Ingestion & Deduplication
- **Constraint Check:** Requirement is "No duplicate points".
- **Proposed Architecture:** Clients assign a strict `uuid` (UUIDv4) to *every single GPS recorded coordinate* at the time of creation. 
- **Server behavior:** The Backend DB sets this `uuid` as a Unique Constraint. If a batch upload fails mid-flight and the client retries, the DB safely bulk-ignores Duplicate Key constraints (`ON CONFLICT DO NOTHING`).

### 3. Buffering and Batch Logic
- **Constraint Check:** Network instability buffering requirement.
- **Proposed Logic:** The mobile app writes immediately to SQLite. A Sync Worker wakes up every 60 seconds (or immediately based on network type WiFi/Cellular), queries `SELECT * FROM location_buffer WHERE status='PENDING' LIMIT 500`, sends to `POST /api/telemetry`, and upon `202 Accepted`, deletes these rows or marks them `SYNCED`.

## Areas of Consensus / Debate
- **Debate:** Should we encrypt the local SQLite data on the device?
- **Recommendation:** No, unless the data implies extreme medical/military confidentiality. The OS sandbox (Apple Secure Enclave/Android Keychain sandboxing) is sufficient. We focus heavily on HTTPS/TLS encryption for transit.

## Gaps and Further Research
- Data Retention thresholds need review by the legal/compliance teams (e.g., GDPR / Korea PIPA normally dictate dropping non-anonymous exact tracking logs after 90 or 180 days unless aggressively anonymized).
