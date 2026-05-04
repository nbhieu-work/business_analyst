Feature: MS-001 mobile-sensing-gps-data-collection
Version: v1.0
Last updated: 2026-03-09
Change request: N/A

# Dev Handoff Pack

## A. TL;DR
Build a Mobile Sensing Module tracking GPS coordinates (background capable). The mobile app acts as an offline-first data logger using SQLite. The backend acts as a highly scalable, idempotent ingestion engine.

## B. In-scope / Out-of-scope
- **In-scope:** Client-side GPS tracking (iOS/Android background services), Prominent Privacy Disclosure UI, SQLite Buffering, Sync Worker (Batch + GZIP), Backend Ingestion API, Deduplicating DB schema.
- **Out-scope:** Any UI rendering maps or tracks (this module is purely data collection pipelines).

## C. Key assumptions + decisions
- **Idempotency is king:** The mobile app must generate a UUID for every single GPS point. If the network drops during an HTTP POST and the app retries the same batch later, the Backend DB *must* silently ignore the duplicates using `ON CONFLICT DO NOTHING`.
- **GZIP is required:** 500 GPS points in JSON format expands quickly. The mobile `POST` must be gzipped and the backend server config (e.g. Nginx/App Server) must be configured to decompress request bodies.
- **Sampling logic:** Do not request continuous 1-second GPS. Use "Distance > 10m OR Time > 60s" filters natively provided by mobile OS SDKs.

## D. Must-have User Stories list
- **US-01:** Consent/Privacy flow (OS Permissions).
- **US-02:** Background path tracking (writing to SQLite).
- **US-03/04:** Offline Buffering & Batch Syncing to API.
- **US-05:** 7-Tap Hidden Debug Menu to view local buffer counts.

## E. Endpoints summary
- `POST /api/v1/telemetry/sync`
  - Accepts a JSON array.
  - Returns `202 Accepted` when data is safely stored in DB.

## F. Data fields summary (Top elements)
- `GpsPoint.uuid` (Generated globally unique per point by the phone).
- `GpsPoint.session_id` (Generated per shift/trip).

## G. State transitions summary
- Mobile SQLite: PENDING -> HTTP POST -> HTTP 202 -> DELETED from mobile DB.

## H. Permission matrix summary
- **End User:** Requires valid JWT for their own user account to `POST` to the sync API.

## I. Test checklist
- [ ] **Airplane Mode Test:** Go into airplane mode, walk 50 meters, turn off airplane mode -> Confirm points appear accurately timestamped in Backend DB.
- [ ] **Duplicate Payload Test:** Send the exact same JSON payload array twice via Postman. Confirm `COUNT(*)` in DB only increments once.
- [ ] **Privacy Test:** Ensure NO location tracking icon appears on the phone if the user is not actively "On Duty".

## J. Open questions for sprint planning
- Should we enable PostGIS on the PostgreSQL database immediately, or just store standard Decimal coordinates for Phase 1 MVP?
- Which engineer is building the native iOS vs native Android location services? (Cross-platform wrappers like React Native often struggle with deep background location persistence).
