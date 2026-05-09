# 2026-05-09 - Supabase Auth Client

## Summary

Installed the Supabase JavaScript client through Bun and wired the existing auth form to real Supabase email/password sign-up and sign-in.

## Request

After the user pasted Supabase env values and configured Supabase Auth, continue with the next Phase 1 auth setup step.

## Scope

- Project/folder: `peaked-219edec1`, `peaked-kb`
- Primary files changed:
  - `peaked-219edec1/package.json`
  - `peaked-219edec1/bun.lock`
  - `peaked-219edec1/src/lib/supabase/client.ts`
  - `peaked-219edec1/src/routes/auth.tsx`
  - `peaked-kb/audits/2026-05-09-supabase-auth-client.md`
- Related files inspected:
  - `peaked-219edec1/.gitignore`
  - `peaked-219edec1/.env.example`
  - `peaked-219edec1/tsconfig.json`

## Changes Made

- Added `@supabase/supabase-js`.
- Added a browser-safe Supabase client wrapper using `VITE_SUPABASE_URL` and `VITE_SUPABASE_ANON_KEY`.
- Replaced mock auth navigation with Supabase `signUp` and `signInWithPassword` calls.
- Disabled the Google button until OAuth is configured.
- Added user-facing loading, error, and email-confirmation states.

## Decisions

- Kept Supabase calls browser-side for this first slice using only the anon/publishable key.
- Did not add service-role or admin operations yet.
- Left profile creation and database-backed plan state for the next slice.

## Verification

- `npx eslint src/routes/auth.tsx src/lib/supabase/client.ts` passed.
- `npm run build` completed successfully.
- Full `npm run lint` is still blocked by unrelated pre-existing Prettier errors across the app.
- Build emitted a Wrangler log-write permission warning outside the workspace, but produced client and server output.

## Follow-Ups

- Start the local dev server and manually test Supabase sign-up/sign-in.
- Add Phase 1 database migration for profiles, plans, usage, activity, and manual entitlement grants.
- Ensure new sign-ups create app profiles after the database schema exists.

## Lessons

- Bun was unavailable in Codex, so the user installed the Supabase dependency locally with Bun to preserve `bun.lock` as the source of truth.
