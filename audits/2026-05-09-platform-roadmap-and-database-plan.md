# 2026-05-09 - Platform Roadmap And Database Plan

## Summary

Scanned the current Peaked app and documented the missing backend, database, AI, payment, job aggregation, and analytics work needed to turn the UI prototype into a real platform.

## Request

Scan the Peaked codebase, identify missing features, plan database architecture for admin/free/dedicated users, consider NextPay/NextAPI payments, AI APIs, resume parsing, job finding, cover letters, interview prep, and structure development in phases without timelines.

## Scope

- Project/folder: `peaked-kb` planning documentation with read-only inspection of `peaked-219edec1`.
- Primary files changed:
  - `peaked-kb/README.md`
  - `peaked-kb/product/roadmap/phases-of-development.md`
  - `peaked-kb/foundation/database-architecture.md`
  - `peaked-kb/audits/2026-05-09-platform-roadmap-and-database-plan.md`
- Related files inspected:
  - `peaked-219edec1/.lovable/plan.md`
  - `peaked-219edec1/package.json`
  - `peaked-219edec1/src/routes/index.tsx`
  - `peaked-219edec1/src/routes/jobs.tsx`
  - `peaked-219edec1/src/routes/resume.tsx`
  - `peaked-219edec1/src/routes/profile.tsx`
  - `peaked-219edec1/src/routes/auth.tsx`
  - `peaked-219edec1/src/lib/mock-data.ts`

## Changes Made

- Added a phased platform roadmap covering backend foundation, resume parsing, job matching, AI generation, payments, dedicated user features, admin tools, analytics, and launch hardening.
- Added a database architecture document with recommended tables, indexes, role/plan model, payment tables, AI tables, job tables, storage buckets, and RLS direction.
- Documented external integration cautions for NextPay, LinkedIn, Indeed, and OnlineJobs.ph.

## Decisions

- Recommended separating `role` from `plan_tier` so admin authority and dedicated paid entitlement do not get mixed.
- Recommended starting job workflows with user-submitted job URLs/descriptions before attempting multi-site scraping.
- Treated NextPay NextAPI as the first payment provider to research, but not as final until sandbox, webhook, and recurring billing fit are confirmed.

## Verification

- Read the current route files, mock data, package scripts, and Lovable plan.
- Checked current official docs for NextPay, Indeed, LinkedIn, and OnlineJobs.ph where needed.
- No app build was needed because no `peaked-219edec1` code changed.

## Follow-Ups

- Decide backend provider: Lovable Cloud/Supabase or another Postgres-backed service.
- Verify NextPay sandbox access and recurring billing behavior.
- Decide first job data approach: user-submitted jobs, official APIs, partner feeds, or licensed provider.
- Convert the database architecture into migrations after backend provider selection.

## Lessons

- The fastest safe first backend slice is auth, profiles, roles/plans, quota usage, resume upload, and one AI parsing pipeline.
- Full job scraping should not be the first backend milestone because source permissions and terms vary significantly.
