# 2026-05-10 - Admin User Usage Overview

## Summary

Added the first admin-only app surface for monitoring Peaked users, roles, plan state, usage counters, and token totals.

## Request

The user confirmed the admin profile works and asked for admin role features that are distinct from normal users, starting with checking app users, roles, and token usage.

## Scope

- Project/folder: `peaked-219edec1`, `peaked-kb`
- Primary files changed:
  - `peaked-219edec1/src/routes/admin.tsx`
  - `peaked-219edec1/src/components/layout/TopNav.tsx`
  - `peaked-219edec1/src/routeTree.gen.ts`
  - `peaked-kb/product/current/resume-point.md`
  - `peaked-kb/product/phase-1-backend/phase-1-backend-foundation-plan.md`
- Related files inspected:
  - `peaked-219edec1/src/routes/profile.tsx`
  - `peaked-219edec1/src/routes/auth.tsx`
  - `peaked-219edec1/src/lib/supabase/client.ts`
  - `peaked-kb/engineering/component-structure.md`
  - `peaked-kb/engineering/ui-animation-standards.md`

## Changes Made

- Added `/admin` route.
- Added admin access states for loading, signed-out, forbidden, error, empty, and ready.
- Admin users can view profiles with role, plan tier, subscription status, usage counter summaries, and token totals from usage event metadata.
- Added an Admin top-nav item.
- Let TanStack regenerate `src/routeTree.gen.ts`.

## Decisions

- Used RLS-backed client reads for this first read-only admin surface; non-admin users are denied by both UI checks and database policies.
- Token totals are computed from existing `usage_events.metadata` keys such as `total_tokens`, `tokens`, `input_tokens`, and `output_tokens`.
- Deferred manual grants and support actions to the next admin slice.

## Verification

- `bun run build` completed successfully.
- Build emitted the known local Wrangler log-write permission warning.
- `bun run lint` still fails on pre-existing repo-wide Prettier issues; the new `admin.tsx` and touched `TopNav.tsx` pass Prettier.
- Started local Vite dev server and confirmed `GET /admin` returns HTTP 200.

## Follow-Ups

- Browser-test `/admin` while signed in as `briansismundo@gmail.com`.
- Add manual Dedicated grant/revoke actions.
- Add richer usage drill-down after real AI token events are recorded.

## Lessons

- The current admin route can display token totals only after provider usage events start recording token metadata.
