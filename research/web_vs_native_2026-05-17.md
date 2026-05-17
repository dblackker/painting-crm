# Web App vs Mobile App for PaintFlow
**Date:** 2026-05-17

## Research Summary

From PaintTalk and competitor analysis:
- Painters live on their phones, 90% of work happens in field
- Need: quick photo capture, time logging, check schedule, send estimate
- Current tools: Jobber and Housecall Pro offer native iOS/Android apps
- Many solos use mobile web versions and are frustrated by clunky UI

## PWA Capabilities in 2026

**What works well:**
- Camera access via `getUserMedia` - receipt photos, job photos
- Geolocation for time clock punch
- Offline storage via IndexedDB + Service Workers
- Background Sync API for queuing time entries when offline
- Push notifications on Android fully supported, iOS 16.4+ supports Web Push but requires Add to Home Screen
- Install to home screen, fullscreen, splash screen
- File System Access API for exports
- Biometric auth via WebAuthn

**Limitations:**
- iOS background sync is unreliable - app must be open to sync
- Push notification opt-in rate lower than native (no pre-prompt)
- No access to contacts, SMS inbox, or call logs
- Background location tracking not possible
- App Store discoverability missing
- Perceived as less "professional" by some

## Native App Pros/Cons

**Pros:**
- Reliable offline mode with SQLite
- Background location for geofenced time clock
- Push notifications just work
- Camera is faster and more reliable
- Biometric auth seamless
- App Store presence = credibility and discovery
- Access to contacts for quick lead import

**Cons:**
- 2x development cost (iOS + Android or React Native)
- App Store review delays
- Update cycle slower
- Need separate codebase or React Native complexity
- $99/year Apple dev fee, 15-30% cut if selling through store

## Recommendation for PaintFlow MVP

**Start with PWA, plan for native later.**

**Why PWA is sufficient for MVP:**
1. Core workflows are online-first: sending estimates, checking schedule, logging time. Offline is nice-to-have, not blocker for solos.
2. Camera for receipts works fine in PWA
3. Push notifications work on Android (majority of tradespeople) and iOS if user installs
4. Ship in 10 weeks vs 20 weeks for native
5. One codebase = faster iteration
6. PaintTalk users care more about speed and simplicity than native feel

**PWA Must-Haves for MVP:**
- Install prompt after 2nd visit
- Offline queue for time entries and expenses - sync when back online
- Camera capture with compression before upload
- Push notifications for estimate accepted, payment received, drip reminders
- Home screen icon and splash screen
- Bottom nav thumb-friendly

**When to build native:**
- After 500 paying orgs, if analytics show:
  - <40% of users install PWA
  - Offline sync failures >5%
  - Feature requests for background location or contacts sync
  - Competitors winning deals citing "real app"

**Hybrid path:**
Build PWA with Capacitor.js from day one. Same web codebase, can compile to native later with minimal changes. Gives optionality.

## Competitor Approach

- **Jobber:** Native iOS/Android + web. Started web, added native later.
- **Housecall Pro:** Native first.
- **PaintScout:** Web app, mobile-responsive, no native app.
- **DripJobs:** Web app.

PaintScout and DripJobs succeed without native apps, proving PWA is viable for this market.

## Decision

**MVP:** PWA with offline queue and install prompt. Re-evaluate at 500 orgs.

Document in ADR-006.
