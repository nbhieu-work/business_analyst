Feature: MS-001 mobile-sensing-gps-data-collection
Version: v1.0
Last updated: 2026-03-09
Change request: N/A

# Technical Specification

## Overview
This tech spec outlines the architecture for the Mobile Sensing module. It consists of a Mobile SDK/Service (handling OS location APIs, SQLite buffering, and batch HTTP syncing) and a Backend Ingestion API (handling Auth, JSON validation, and idempotent bulk database inserts).

## Motivation
Current state -> No native tracking capability.
Problems -> Operations cannot track actual routes taken by drivers in the field.
Desired state -> A background-capable, battery-efficient, offline-resilient GPS logger.

## Research Findings
- Using exact 1-second interval querying drains battery rapidly. Standard OS features (iOS `significantLocationChangeMonitoring` or `minDistanceFilter=10m` combined with Android Fused Location `PRIORITY_BALANCED_POWER_ACCURACY`) solve the baseline battery requirements while maintaining high resolution.
- Database upserts (`ON CONFLICT (uuid) DO NOTHING`) on the server are the best way to handle transient network retry bugs without generating duplicate routing points.

## Design Decisions
1. **Client-side UUID Generation:** The mobile app generates UUIDv4 for every point `uuid`. This shifts the deduplication burden to the database unique constraint rather than complex application-layer state machines.
2. **Batch Synchronizer:** App uploads in batches of max 500 records. Payloads are GZIP encoded.
3. **Database Selection:** For the backend, GPS data is extremely high volume (time-series). Recommend partitioning the PostgreSQL `telemetry_gps` table by month/week, or utilizing a true TSDB like TimescaleDB if volume > 100M rows/month.

## Architecture (Draft)
```text
[Mobile OS Location API] 
       | (Filters: 10m / 60s)
       v
[Local SQLite DB (points_buffer)] -> PENDING state
       | 
       | (Network Available -> Sync Worker wakes)
       v
[Batch & GZIP Compressor]
       | (POST /api/v1/telemetry/sync [Bearer Token])
       v
[Ingestion API Service] -> Validates Token & Payload Schema
       |
       v
[Database (PostgreSQL/TimescaleDB)] -> ON CONFLICT(uuid) DO NOTHING
       | 
       | (202 Accepted Returned)
       v
[Mobile App] -> DELETES matching rows from SQLite
```

## API Contract draft
- `POST /api/v1/telemetry/sync`
  - **Headers:** `Authorization: Bearer <token>`, `Content-Encoding: gzip`, `Content-Type: application/json`
  - **Body (Array of GpsPoint):**
    ```json
    {
      "session_id": "uuid",
      "points": [
        {
          "uuid": "uuid",
          "timestamp": "2026-03-09T10:00:00Z",
          "latitude": 37.5665,
          "longitude": 126.9780,
          "accuracy": 5.0,
          "speed": 12.5,
          "heading": 180.0,
          "altitude": 50.0
        }
      ]
    }
    ```
  - **Response 202 Accepted:** `{ "status": "success", "processed_count": 1 }`
  - **Response 400 Bad Request:** Validations failed.

## Data Model outline (Backend DB)
- **Table: telemetry_gps**
  - `uuid` (UUID, Primary Key / Unique globally)
  - `user_id` (UUID, FK to Users, Indexed)
  - `session_id` (UUID, Indexed)
  - `timestamp` (Timestamp with Timezone, Indexed for TimescaleDB)
  - `geom` (PostGIS Geometry Point(SRID=4326), optional but recommended for fast spatial querying)
  - `latitude` (Decimal 9,6)
  - `longitude` (Decimal 9,6)
  - `accuracy` (Float)
  - `speed` (Float)
  - `heading` (Float)
  - `altitude` (Float)
  - **Indices:** `(user_id, timestamp)`, `(session_id, timestamp)`

## AuthN/AuthZ mapping
- **Mobile SDK:** Requires standard user Auth token (JWT) to access the Sync API.

## Implementation Plan
1. **Phase 1 (Backend):** Create `telemetry_gps` table (with PostGIS/TimescaleDB if agreed), implement `POST /sync` ingestion endpoint with GZIP support and Idempotent inserts.
2. **Phase 2 (Mobile Core):** Implement SQLite `points_buffer` table and background Sync Worker logic.
3. **Phase 3 (Mobile Location):** Implement Prominent Disclosure UI (SCR-01), hook up OS Location Managers to write to SQLite.
4. **Phase 4 (Admin UI):** Implement SCR-02 for field testing and QA.

## Edge Cases
- **EC-03 (Permission Revoke):** If the server rejects the token (401 Unauthorized), the mobile Sync Worker must pause until a new token is fetched, retaining the buffer (EC-02 FIFO limits still apply).

## Success Criteria
- [ ] Database correctly ignores duplicate records on repeated POST requests.
- [ ] Mobile app captures points in airplane mode and successfully syncs them when wifi is re-enabled.
- [ ] Background battery drain < 3% per hour in field test.

## Traceability
- Data Model -> Extraction C.
- APIs -> Feature Spec US-03, US-04.
- Architecture -> Intake Constraints (Offline, Idempotency).
