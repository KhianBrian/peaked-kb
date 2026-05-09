# Phase 1 - Backend Foundation Plan

## Outcome

Replace the UI-only prototype with a real backend foundation: Supabase auth, app profiles, role/plan separation, manual Dedicated beta grants, quota accounting, activity logging, and private storage buckets.

Phase 1 should make Peaked able to identify a signed-in user, know whether they are Free, Dedicated, or Admin, and check quotas before future AI work.

## Current State

The app is no longer purely a frontend prototype; the first auth slice is live, but most backend foundation work remains.

Confirmed current state as of 2026-05-10:

- Supabase dependency exists in `peaked-219edec1/package.json`.
- Browser Supabase client exists at `src/lib/supabase/client.ts`.
- `/auth` performs Supabase email/password sign-up and sign-in.
- `/auth/verified` exists for email confirmation handoff to onboarding.
- Supabase URL and publishable key are browser-safe config. Lovable currently supports production by ensuring those values are available to the hosted app.
- `SUPABASE_SERVICE_ROLE_KEY` server-only setup was discussed/approved conceptually through Lovable's secure secret flow, but confirm it is actually stored before writing admin/server code that requires it.
- No migrations or `supabase` directory.
- No app profile table yet.
- No plan/quota/usage/activity tables yet.
- Profile route reads mock user and usage data.
- PayMongo Payment Link exists outside the app and should remain private until entitlement handling exists.

## Non-Goals

Phase 1 should not:

- Build resume parsing.
- Call OpenRouter or any AI provider.
- Add PayMongo API/webhook automation.
- Ingest real jobs.
- Replace all mock dashboard/job/resume data.
- Add a public admin dashboard beyond what is needed for manual grants.
- Store the PayMongo live link in client code.

## Decisions Needed

Before coding:

- Create the Supabase project.
- Confirm the Supabase project URL.
- Confirm the Supabase anon/publishable key.
- Confirm whether Google OAuth should be enabled immediately or after email/password works.
- Decide where local env values live for Lovable-safe development.
- Decide the first admin account email.

Recommended defaults:

- Start with email/password auth.
- Add Google OAuth after base auth and profile creation work.
- Keep the PayMongo link private until signed-in profile/plan state exists.

## Architecture Plan

Frontend:

- `/auth` becomes real sign-in/sign-up.
- `/profile` reads real profile, plan, and usage counters.
- App shell can later conditionally show authenticated state.

Server/client boundary:

- Browser uses Supabase client only with anon/publishable key.
- Server-only operations use service-role credentials only in server code, never client bundles.
- Admin/manual grants should require server-side admin authorization.

Database:

- Supabase Postgres with RLS.
- App profile is separate from Supabase auth user.
- Role and plan remain separate.

Storage:

- Create private buckets for future resume and generated document storage.
- Do not wire uploads in Phase 1 unless needed for profile flow.

## Data Model Impact

Phase 1 should add these schema pieces first:

- `account_role` enum: `user`, `admin`
- `plan_tier` enum: `free`, `dedicated`
- `subscription_status` enum: `none`, `trialing`, `active`, `past_due`, `cancelled`, `expired`
- `usage_feature` enum or constrained text strategy
- `profiles`
- `plans`
- `manual_entitlement_grants`
- `usage_counters`
- `usage_events`
- `activity_events`

Recommended new table for beta:

```sql
manual_entitlement_grants
- id uuid primary key
- profile_id uuid not null references profiles(id)
- source text not null
- payment_reference text
- amount_php integer
- granted_by uuid references profiles(id)
- starts_at timestamptz not null
- ends_at timestamptz not null
- notes text
- created_at timestamptz not null default now()
- revoked_at timestamptz
```

Why:

- PayMongo Payment Links are manual during beta.
- Manual grants need an audit trail.
- This avoids pretending we have subscription webhooks before they exist.

## API And Server Plan

Create helpers around these responsibilities:

- `getCurrentUser`
- `getCurrentProfile`
- `requireUser`
- `requireAdmin`
- `getPlanForProfile`
- `checkQuota`
- `recordUsageEvent`
- `recordActivityEvent`
- `grantManualEntitlement`
- `revokeManualEntitlement`

Expected later server functions/routes:

- Sign-up/sign-in actions if TanStack Start server functions are used.
- Profile loader/action.
- Admin manual grant action.
- Quota check helper for future AI features.

## UI/UX Plan

Auth:

- Email/password sign up and sign in should create or load a profile.
- Auth errors should be user-safe.
- Google button can remain disabled/hidden until OAuth is configured.

Profile:

- Replace mock user with real profile.
- Show `Free user` or `Dedicated user`.
- Show admin status only if `role = admin`.
- Show current beta access expiration if present.
- Show usage counters from real tables when available.

Paywall:

- Keep PayMongo link out of app until users can sign in and plan state is trustworthy.
- Once entitlement state exists, the Upgrade CTA can open the PayMongo link or show beta payment instructions.

## Security, Privacy, And Abuse Controls

Must-have Phase 1 controls:

- Enable RLS on all user-owned tables.
- Users can read only their own profile and usage data.
- Users cannot update their own `role`, `plan_tier`, or `subscription_status`.
- Admin-only grant actions must be server-side.
- Service-role key must never be exposed to client code.
- Manual grant records must be auditable.

## Quotas, Billing, Or Entitlements

Seed plans:

- `free`
- `dedicated`

Add beta handling:

- `PHP 99` founding beta grants Dedicated access for 30 days.
- Manual grant sets `plan_tier = dedicated`, `subscription_status = active`, and creates a grant record.
- Expired grants should eventually downgrade the user unless another active grant/payment exists.

Initial beta limits should follow `product/quota-and-entitlements-v1.md`.

## Observability And Analytics

Phase 1 should log:

- `auth.signup_completed`
- `auth.signin_completed`
- `profile.created`
- `profile.updated`
- `entitlement.manual_grant_created`
- `entitlement.manual_grant_revoked`
- `quota.checked`
- `quota.blocked`

Do not log payment links, API keys, raw private user data, or future resume contents.

## Implementation Slices

### Slice 1 - Supabase Project And Env

Status: **Mostly complete.**

Completed:

- Supabase project created.
- Email/password auth enabled and tested.
- Supabase client dependency installed.
- Browser client configured.
- Lovable production app can read browser-safe Supabase config.

Remaining:

- Confirm `SUPABASE_SERVICE_ROLE_KEY` is stored in Lovable's server-only encrypted secrets before using server/admin operations.
- Keep service-role key out of client code and out of `VITE_*` variables.

Purpose:

- Establish the backend connection safely.

Files/areas likely touched:

- Supabase dashboard.
- `peaked-219edec1/package.json`
- `peaked-219edec1/.env.example`
- local uncommitted `.env` if needed.

Contracts:

- `VITE_SUPABASE_URL`
- `VITE_SUPABASE_ANON_KEY`
- server-only service key later if admin actions need it.

Risks:

- Accidentally exposing service-role key.
- Mixing package managers.

Verification:

- Install succeeds.
- Build still works.
- Env docs are clear.

### Slice 2 - Database Migration V1

Status: **Next slice to start.**

Purpose:

- Add profiles, plans, usage, activity, and manual entitlement grant schema.

Files/areas likely touched:

- `peaked-219edec1/supabase/migrations/*`
- `peaked-kb/foundation/database-architecture.md` if schema evolves.

Contracts:

- Enums and tables listed above.
- RLS policies for user-owned data.
- Seed rows for `free` and `dedicated`.

Risks:

- Weak RLS.
- Admin operations too broad.

Verification:

- Migration applies cleanly in Supabase.
- RLS policy smoke tests.

### Slice 3 - Auth Client And Profile Creation

Status: **Partially complete.**

Completed:

- Supabase browser client exists.
- Sign-up/sign-in are wired to Supabase Auth.
- Email confirmation redirects through `/auth/verified`.

Remaining:

- Add app profile creation for new Supabase auth users.
- Decide whether profile creation happens through DB trigger, server function, or post-auth client/server flow.
- Persist role, plan tier, subscription status, and onboarding completion.

Purpose:

- Make sign-up/sign-in real and ensure each auth user has a profile.

Files/areas likely touched:

- `src/lib/supabase/client.ts`
- `src/lib/auth/*`
- `src/routes/auth.tsx`
- `src/routes/__root.tsx` if session context is needed.

Contracts:

- Signed-up users default to `role = user`, `plan_tier = free`.

Risks:

- Profile creation race conditions.
- Session state mismatch between client and server.

Verification:

- Sign up creates a Supabase auth user and profile.
- Sign in persists across reload.
- Sign out clears session.

### Slice 4 - Profile Page Real Data

Purpose:

- Replace mock profile and usage summary with database-backed reads.

Files/areas likely touched:

- `src/routes/profile.tsx`
- `src/lib/profile/*`
- `src/lib/quota/*`

Contracts:

- Profile page receives profile, plan, usage counters, and active grant expiration.

Risks:

- Leaking admin-only fields.
- Empty states when usage rows do not exist yet.

Verification:

- Free user sees Free user state.
- Dedicated beta user sees Dedicated state and expiration.
- Admin account shows admin-only indicator if needed.

### Slice 5 - Manual Dedicated Grant Flow

Purpose:

- Let an admin grant 30-day Dedicated beta access after PayMongo confirmation.

Files/areas likely touched:

- Server/admin helper.
- Minimal admin route or script.
- `manual_entitlement_grants`.
- `activity_events`.

Contracts:

- Input: profile email or profile ID, PayMongo reference, amount, start/end date.
- Output: updated profile plan and grant record.

Risks:

- Letting normal users grant themselves access.
- No expiration handling.

Verification:

- Admin can grant access.
- Non-admin cannot grant access.
- Grant creates an activity event.

## Verification Plan

Run after implementation slices:

- `bun install` if dependency changes.
- `bun run lint`
- `bun run build`
- Manual auth smoke test.
- Manual RLS smoke test in Supabase.
- Profile page browser check after real auth is wired.

## Rollout And Fallback

Rollout:

- Start locally with a Supabase development project.
- Keep beta payment link manual.
- Use known testers only.
- Promote to Lovable only after auth/profile/build pass.

Fallback:

- If Supabase integration blocks Lovable builds, pause app code changes and keep the KB/migrations.
- If auth is unstable, keep public payment link out of the app.
- If manual grant UI is not ready, use a guarded admin script or Supabase dashboard temporarily.

## Documentation And Audit Updates

Update after implementation:

- `foundation/database-architecture.md` if schema differs.
- `product/paymongo-payment-plan.md` if grant flow changes.
- `product/quota-and-entitlements-v1.md` if limits change.
- Add a new audit entry under `peaked-kb/audits`.

## Open Questions

- What is the Supabase project URL?
- What is the Supabase anon/publishable key?
- Which email should be the first admin?
- Should Google OAuth be configured in Phase 1 or after email/password is stable?
- Should manual grants be done through a minimal admin route or a local/server-only script first?
