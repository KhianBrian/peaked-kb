# 2026-05-10 - Real Profile Identity Wiring

## Summary

Replaced the visible `Alex Morgan` placeholder account on mounted app surfaces with the signed-in Supabase profile identity.

## Request

The user reported that admin and normal accounts still opened into the placeholder Alex Morgan account.

## Scope

- Project/folder: `peaked-219edec1`, `peaked-kb`
- Primary files changed:
  - `peaked-219edec1/src/hooks/use-current-profile.ts`
  - `peaked-219edec1/src/routes/profile.tsx`
  - `peaked-219edec1/src/routes/index.tsx`
  - `peaked-219edec1/src/components/layout/TopNav.tsx`
  - `peaked-219edec1/src/components/layout/Sidebar.tsx`
  - `peaked-kb/product/current/resume-point.md`
  - `peaked-kb/product/phase-1-backend/phase-1-backend-foundation-plan.md`
- Related files inspected:
  - `peaked-219edec1/src/lib/mock-data.ts`
  - `peaked-219edec1/src/routes/admin.tsx`

## Changes Made

- Added `useCurrentProfile` to load the current Supabase Auth user and matching `profiles` row.
- Made the browser Supabase client lazy so `createClient` is not constructed during SSR under Node 20.
- Updated Profile to show real email, role, plan tier, initials, loading state, signed-out state, and missing-profile/error states.
- Updated Profile sign-out to call `supabase.auth.signOut()` before routing to auth.
- Updated TopNav avatar and Dashboard greeting to use the signed-in profile instead of `mock-data.user`.
- Updated legacy Sidebar identity so the placeholder does not return if the sidebar is mounted later.

## Decisions

- Kept mock usage/stat cards for now because real quota counters are not wired into the user Profile UI yet.
- Used email-derived display names until editable `full_name` support exists.

## Verification

- `bunx prettier --check` passed for touched profile identity files.
- `bun run build` completed successfully after the lazy browser-client fix.
- `bun run dev --host 127.0.0.1 --port 4177` started successfully, and `GET /profile` returned HTTP 200 without the Supabase Realtime WebSocket SSR error.
- Build emitted the known local Wrangler log-write permission warning.

## Follow-Ups

- Replace remaining mock usage cards on Profile with `usage_counters`.
- Add editable profile name/timezone through a safe profile update path.
- Add a separate server-safe Supabase client only when server-side data loading requires it.

## Lessons

- Real auth does not remove placeholder identity by itself; mounted UI must stop importing `mock-data.user`.
- Supabase's browser client should not be constructed during TanStack SSR on Node 20 because Realtime requires native WebSocket or a custom transport.
