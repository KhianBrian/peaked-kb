# 2026-05-10 - Profile Usage And Career Intent

## Summary

Added the second Supabase migration for career intent and upgraded Profile from mock usage cards to Supabase-backed plan/usage display with an editable career intent form.

## Request

The user asked to continue with the next Phase 1 work after agreeing Peaked should collect user job-search goals for better intelligence.

## Scope

- Project/folder: `peaked-219edec1`, `peaked-kb`
- Primary files changed:
  - `peaked-219edec1/supabase/migrations/202605100002_career_intents.sql`
  - `peaked-219edec1/supabase/migrations/202605100003_onboarding_completion_rpc.sql`
  - `peaked-219edec1/src/routes/auth.tsx`
  - `peaked-219edec1/src/routes/auth.verified.tsx`
  - `peaked-219edec1/src/routes/onboarding.get-started.tsx`
  - `peaked-219edec1/src/routes/profile.tsx`
  - `peaked-219edec1/supabase/README.md`
  - `peaked-kb/product/current/resume-point.md`
  - `peaked-kb/product/phase-1-backend/phase-1-backend-foundation-plan.md`
- Related files inspected:
  - `peaked-219edec1/src/components/ui/input.tsx`
  - `peaked-219edec1/src/components/ui/select.tsx`
  - `peaked-219edec1/src/components/ui/textarea.tsx`
  - `peaked-219edec1/src/hooks/use-current-profile.ts`

## Changes Made

- Added `career_intents` migration with RLS for own-profile reads/writes and admin reads.
- Replaced Profile mock usage cards with plan limits and usage counters from Supabase.
- Added active Dedicated grant date display when present.
- Added Career Intent Lite to onboarding.
- Added sign-in routing so users with incomplete onboarding go to `/onboarding/welcome`.
- Added verified-email routing that sends active sessions to onboarding or dashboard based on onboarding status.
- Added a narrow onboarding-completion RPC so users can mark only their own onboarding complete.
- Added full editable Career Intent form in Profile for target roles, industries, locations, remote preference, seniority, salary range, schedule, dealbreakers, work authorization, and notes.
- Documented contextual missing-info prompts in `product/roadmap/future-plans.md`.
- Updated Supabase README and Phase 1 KB docs.

## Decisions

- Created a second migration instead of editing the already-used foundation migration.
- Created a third migration for onboarding completion instead of broadening profile update RLS.
- Used comma-separated inputs for multi-value intent fields to keep the first UI lightweight.
- Chose progressive intake: onboarding asks only essentials, Profile remains the full editor, future workflows can ask contextual missing-info prompts.
- Kept career intent in Profile for now; split into dedicated components if the form grows.

## Verification

- `bunx prettier --check` passed for touched TypeScript files.
- `bun run build` completed successfully.
- Build emitted the known local Wrangler log-write permission warning.
- `bun run dev --host 127.0.0.1 --port 4177` started successfully and `GET /profile` plus `GET /onboarding/get-started` returned HTTP 200.

## Follow-Ups

- Apply `202605100002_career_intents.sql` in Supabase.
- Apply `202605100003_onboarding_completion_rpc.sql` in Supabase.
- Test email verification, sign-in routing, Onboarding Career Intent Lite, and Profile full Career Intent save/read behavior as admin and normal user.
- Add manual Dedicated beta grant flow next.

## Lessons

- Career intent can be collected before AI exists; the value is making future AI and ranking grounded from the start.
