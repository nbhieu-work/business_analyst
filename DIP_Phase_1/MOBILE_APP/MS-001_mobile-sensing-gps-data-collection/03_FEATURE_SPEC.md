Feature: MS-001 mobile-sensing-gps-data-collection
Version: v1.0
Last updated: 2026-03-09
Change request: N/A

# Feature Specification

## Overview
This specification covers the client-side behaviors for the Mobile Sensing feature. The app collects location data securely, caches it locally in an SQLite database, and synchronizes it with the backend server reliably using exponential backoff to handle intermittent network connectivity.

## Proto-persona
- **Field Worker / Driver:** Frequently drives through areas with poor cellular coverage (tunnels, rural roads). Needs the app to track their path without constantly draining their phone battery or consuming their monthly data plan heavily.

## User Stories
- **US-01 [Consent Flow]:** As an End User, I want to clearly understand why the app needs my location, so that I can provide informed consent before tracking begins.
- **US-02 [Background Tracking]:** As a Backend System / Ops Team, I need the mobile app to record GPS coordinates dynamically based on distance/time criteria even when backgrounded, so that we have an accurate breadcrumb trail of the user's shift.
- **US-03 [Offline Buffering]:** As an End User in a dead zone, I want the app to temporarily store my location data on my device, so that my trip path is not lost.
- **US-04 [Batch Synchronizing]:** As the Backend System, I need the app to bulk upload buffered GPS points efficiently when network is restored, so that API loads are minimized and cellular data is preserved.
- **US-05 [Admin Debug View]:** As an App Developer/Admin, I want a hidden debug screen in the app to view the current buffer count and force a sync, so that I can troubleshoot field issues.

## Acceptance Criteria

**US-01 [Consent Flow]:**
- **Given** I am a new user launching the app or starting a "shift" for the first time
- **When** the app requests location
- **Then** a custom full-screen Prominent Disclosure explaining the purpose of tracking must appear BEFORE the native OS permission prompt.
- **And** the user must accept to trigger tracking.

**US-02 [Background Tracking]:**
- **Given** the user has consented and is "On Duty"
- **When** the user moves > 10 meters OR 60 seconds elapses
- **Then** the OS/SDK wakes up and generates a `GpsPoint` with a unique `uuid`.
- **And** writes it locally to the SQLite DB with status `PENDING`.

**US-03 [Offline Buffering] & US-04 [Batch Synchronizing]:**
- **Given** the device has 120 `PENDING` points in SQLite
- **When** the background Sync Worker fires (e.g., network restored)
- **Then** the app compresses (GZIP) up to 500 rows into a JSON array payload.
- **And** POSTs to `/api/v1/telemetry/sync`
- **And** upon receiving HTTP 202, the app deletes those 120 rows from SQLite.

**US-05 [Admin Debug View]:**
- **Given** I tap the app version number 7 times rapidly
- **When** the Debug screen opens
- **Then** I see the count of `PENDING` rows in the database.
- **And** a "Force Upload Now" button that triggers the Sync Worker.

## Flows
- **Happy Path Collection:** User goes "On Duty" -> Foreground Service starts (Android) -> CoreLocation starts (iOS) -> Distance delta triggers `locationManager` -> Row written to SQLite -> Network is good -> Sync Worker triggers randomly within next minute -> Battery preserved.
- **Offline Recovery Flow:** Network drops -> DB grows to 2000 points -> User enters Wifi zone -> Sync Worker wakes -> Syncs in batches of 500 -> 4 payloads sent -> SQLite DB clears out -> No data lost.

## Traceability
| US | BR | EC | NFR |
|---|---|---|---|
| US-01 | BR-01 | EC-03 | |
| US-02 | BR-05 | EC-01 | NFR-01 |
| US-03 | BR-02 | EC-02 | |
| US-04 | BR-03, BR-04 | | NFR-02 |
| US-05 | | | |
