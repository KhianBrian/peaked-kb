# 2026-05-10 - Manual Dedicated Grants And Phase 1 Plan

## Summary

Added the first admin-operated Dedicated beta grant workflow and updated Phase 1 planning to include production auth email readiness before public beta.

## Request

Continue Phase 1 with the added development plan around public signup email limits and launch readiness.

## Scope

- Project/folder: `peaked-219edec1`, `peaked-kb`
- Primary files changed: `peaked-219edec1/src/routes/admin.tsx`, `peaked-kb/product/roadmap/phases-of-development.md`, `peaked-kb/product/phase-1-backend/phase-1-backend-foundation-plan.md`
- Related files inspected: `peaked-219edec1/package.json`, Peaked UI/director docs, audit format

## Changes Made

- Added a manual Dedicated grant panel to `/admin` for PayMongo-confirmed beta users.
- Grant flow inserts `manual_entitlement_grants`, updates the selected profile to `dedicated` and `active`, and records an `entitlement.manual_grant_created` activity event.
- Admin users table now shows active grant expiration when one exists.
- Updated roadmap and Phase 1 plan with custom SMTP, Site URL, redirect URL, and email-template readiness before public beta.

## Decisions

- Kept PayMongo automation out of scope; beta grants remain manually confirmed by the admin.
- Used existing admin RLS policies and Supabase browser client for the beta admin surface.
- Treated custom SMTP as Phase 1 launch readiness because built-in Supabase email limits are not suitable for public signup.

## Verification

- Ran `bunx prettier --write` on touched app/doc files.
- Ran `bun run build`; passed. Wrangler still emitted the known sandbox log-write warning, but the build exited successfully.
- Started local dev server and confirmed `GET /admin` responds with HTTP 200.

## Follow-Ups

- Apply and verify `202605100003_onboarding_completion_rpc.sql` if not already applied.
- Test the actual admin grant flow against Supabase with a real non-admin profile.
- Add grant revocation and expired-grant downgrade handling.
- Configure custom SMTP before public beta.

## Lessons

- Phase 1 should separate beta manual operations from future PayMongo webhook automation so launch can proceed without pretending subscriptions are automated.
