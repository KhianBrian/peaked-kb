# Current Checkpoint

Last updated: 2026-05-10

## Phase

- Current phase: Phase 1 - Backend Foundation.
- Status: In progress. Auth, profile, career intent, admin overview, and manual Dedicated grant flow are implemented locally. Supabase migrations need live-project confirmation and smoke testing.

## Completed Since Last Checkpoint

- Organized product docs into clearer phase/roadmap/current folders.
- Added Supabase migration scaffold and Phase 1 migrations:
  - `202605100001_backend_foundation_v1.sql`
  - `202605100002_career_intents.sql`
  - `202605100003_onboarding_completion_rpc.sql`
- Wired real Supabase profile identity into dashboard/profile/nav surfaces.
- Added Career Intent Lite to onboarding and full career intent editing in Profile.
- Added `/admin` for users, roles, usage counters, token totals, and manual Dedicated beta grants.
- Added Phase 1 launch-readiness plan item for custom SMTP and auth email redirect settings before public beta.

## Current Working State

- App/code state: Many Phase 1 files are modified and uncommitted in `peaked-219edec1`.
- Database/Supabase state: User confirmed migrations 001 and 002 were applied. Migration 003 may still need to be applied unless user confirms otherwise.
- Docs state: `peaked-kb` docs were reorganized. Audits should no longer be automatic; only write audits when user explicitly asks.

## Next Actions

1. Confirm/apply `202605100003_onboarding_completion_rpc.sql` in Supabase.
2. Test a fresh normal user: sign up, confirm email, sign in, complete onboarding, edit Profile career intent.
3. Test admin flow: sign in as `briansismundo@gmail.com`, open `/admin`, grant Dedicated access to the test user, verify the user profile updates.
4. Add grant revocation/expired-grant handling if Phase 1 continues.
5. Before public beta, configure custom SMTP, Site URL, Redirect URLs, and branded recovery/confirmation templates.

## Known Blockers Or Risks

- Supabase built-in email sender is too limited for public signup; custom SMTP is required before beta launch.
- Manual grant flow is beta-only and depends on the admin confirming PayMongo payments outside the app.
- Expired Dedicated grants do not yet auto-downgrade users.
- `bun run build` passes, but Wrangler emits a known sandbox log-write warning.

## Files To Read First

- `peaked-kb/checkpoints/current.md`
- `peaked-kb/product/roadmap/phases-of-development.md`
- `peaked-kb/product/phase-1-backend/phase-1-backend-foundation-plan.md`
- `peaked-219edec1/src/routes/admin.tsx`
- `peaked-219edec1/src/routes/onboarding.get-started.tsx`
- `peaked-219edec1/src/routes/profile.tsx`
- `peaked-219edec1/supabase/migrations/202605100003_onboarding_completion_rpc.sql`
