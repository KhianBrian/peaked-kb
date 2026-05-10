# 2026-05-10 - Supabase Backend Foundation Migration

## Summary

Created the first Supabase backend foundation migration for Peaked Phase 1 and documented the first admin email.

## Request

The user asked to create the admin email `briansismundo@gmail.com` and create the Supabase migration folder with all needed components.

## Scope

- Project/folder: `peaked-219edec1`, `peaked-kb`
- Primary files changed:
  - `peaked-219edec1/supabase/README.md`
  - `peaked-219edec1/supabase/migrations/202605100001_backend_foundation_v1.sql`
  - `peaked-kb/product/current/resume-point.md`
  - `peaked-kb/product/phase-1-backend/phase-1-backend-foundation-plan.md`
- Related files inspected:
  - `peaked-219edec1/package.json`
  - `peaked-219edec1/src/lib/supabase/client.ts`
  - `peaked-kb/product/phase-1-backend/quota-and-entitlements-v1.md`
  - `peaked-kb/foundation/database-architecture.md`

## Changes Made

- Added `peaked-219edec1/supabase` scaffolding.
- Added a backend foundation migration with enums, `profiles`, `plans`, usage tables, activity events, manual entitlement grants, profile creation trigger, RLS policies, seeded plans, and private storage buckets.
- Set `briansismundo@gmail.com` as the first admin email in the migration; the profile is promoted to `role = admin` when that Supabase Auth user/profile exists.
- Added profile backfill SQL for Supabase Auth users that existed before the profile trigger was installed.
- Updated Phase 1 KB docs to mark the migration as drafted and the next step as apply/smoke-test.

## Decisions

- Did not insert directly into `auth.users`; Supabase Auth remains the source of truth for identity.
- Used a database trigger for profile creation so new auth users reliably get app profiles.
- Kept profile writes admin/server-controlled for now instead of letting users update profile rows directly before a narrow safe update path exists.

## Verification

- Confirmed `supabase` CLI is not installed locally, so the migration was not applied from this environment.
- Reviewed the generated SQL and checked the expected folder/files exist.
- No app build was run because this change only adds Supabase SQL scaffolding and KB docs.

## Follow-Ups

- Apply `202605100001_backend_foundation_v1.sql` to Supabase.
- Sign up or confirm the `briansismundo@gmail.com` Supabase Auth user, run the backfill if needed, and verify its profile has `role = admin`.
- Run RLS smoke tests for own-profile reads, cross-user denial, and admin-only entitlement writes.

## Lessons

- Creating an admin email in Phase 1 means defining profile promotion behavior, not manually creating a Supabase Auth user in SQL.
