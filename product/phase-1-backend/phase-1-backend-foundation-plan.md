# Phase 1 - Backend Foundation Plan

## Outcome

Replace the UI-only prototype with a real backend foundation: Supabase auth, app profiles, role/plan separation, user career intent, manual Dedicated beta grants, quota accounting, activity logging, and private storage buckets.

Phase 1 should make Peaked able to identify a signed-in user, know whether they are Free, Dedicated, or Admin, understand what the user is targeting in their job search, check quotas before future AI work, and support public-beta auth email reliability through custom SMTP.

## Current State

The app is no longer purely a frontend prototype; the first auth slice is live, but most backend foundation work remains.

Confirmed current state as of 2026-05-10:

- Supabase dependency exists in `peaked-219edec1/package.json`.
- Browser Supabase client exists at `src/lib/supabase/client.ts`.
- `/auth` performs Supabase email/password sign-up and sign-in.
- `/auth/verified` exists for email confirmation handoff to onboarding.
- Supabase URL and publishable key are browser-safe config. Lovable currently supports production by ensuring those values are available to the hosted app.
- `SUPABASE_SERVICE_ROLE_KEY` server-only setup was discussed/approved conceptually through Lovable's secure secret flow, but confirm it is actually stored before writing admin/server code that requires it.
- Supabase migration scaffold exists at `peaked-219edec1/supabase`.
- Backend foundation migration has been drafted at `peaked-219edec1/supabase/migrations/202605100001_backend_foundation_v1.sql`, but still needs to be applied and smoke-tested against Supabase.
- App profile, plan/quota/usage/activity tables are defined in the drafted migration, but are not confirmed live until the migration is applied.
- Migration includes a profile backfill for existing Supabase Auth users, so users created before the profile trigger can still get `profiles` rows.
- Initial admin route exists at `/admin` for viewing users, roles, plans, usage counters, and token totals recorded in usage event metadata.
- Profile route reads real Supabase account identity and plan/role state, but usage cards still use mock stats until quota counters are wired into the UI.
- Top nav avatar, dashboard greeting, and legacy sidebar account block read real Supabase profile identity.
- Browser Supabase client is lazy-loaded to avoid constructing Supabase Realtime during TanStack SSR on Node 20.
- Career intent/preferences migration has been drafted at `peaked-219edec1/supabase/migrations/202605100002_career_intents.sql`; apply it before saving career intent in the app.
- Onboarding includes Career Intent Lite on `/onboarding/get-started`.
- Profile usage cards read real plan limits and usage counters, with empty-counter fallback states.
- Profile includes the full editable Career Intent section.
- `/admin` includes the first manual Dedicated grant workflow for PayMongo-confirmed beta users.
- Onboarding completion RPC is drafted at `peaked-219edec1/supabase/migrations/202605100003_onboarding_completion_rpc.sql`; apply it before relying on onboarding completion in Supabase.
- PayMongo Payment Link exists outside the app and should remain private until entitlement handling exists.

## Non-Goals

Phase 1 should not:

- Build resume parsing.
- Call OpenRouter or any AI provider.
- Generate AI recommendations from career intent.
- Add PayMongo API/webhook automation.
- Ingest real jobs.
- Replace all mock dashboard/job/resume data.
- Add a public admin dashboard beyond what is needed for usage visibility and manual grants.
- Store the PayMongo live link in client code.

## Decisions Needed

Before coding:

- Create the Supabase project.
- Confirm the Supabase project URL.
- Confirm the Supabase anon/publishable key.
- Confirm whether Google OAuth should be enabled immediately or after email/password works.
- Decide where local env values live for Lovable-safe development.
- First admin account email decided: `briansismundo@gmail.com`.

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
- `career_intents`
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

Recommended new table for career intent:

```sql
career_intents
- id uuid primary key
- profile_id uuid unique not null references profiles(id)
- target_roles text[] not null default '{}'
- target_industries text[] not null default '{}'
- seniority text
- preferred_locations text[] not null default '{}'
- remote_preference text
- salary_target_min_php integer
- salary_target_max_php integer
- schedule_preference text
- dealbreakers text[] not null default '{}'
- work_authorization text
- notes text
- created_at timestamptz not null default now()
- updated_at timestamptz not null default now()
```

Why:

- Peaked should not infer the user's desired career direction from resume history alone.
- Job ranking needs explicit goals and constraints before AI explanations or recommendations are trustworthy.
- This data can power onboarding, profile preferences, job matching, and later AI prompt grounding.

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
- `getCareerIntent`
- `updateCareerIntent`
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

Career intent:

- Add a lightweight onboarding intake for essential job-search goals.
- Add a full user-editable job-search goals section in Profile.
- Capture target roles, preferred locations, remote preference, seniority, salary range, industries, schedule preference, and dealbreakers.
- Treat these fields as structured AI grounding inputs, not decorative profile fields.
- Allow users to leave fields empty at first, but make missing intent visible before job recommendations.

Paywall:

- Keep PayMongo link out of app until users can sign in and plan state is trustworthy.
- Once entitlement state exists, the Upgrade CTA can open the PayMongo link or show beta payment instructions.

Admin:

- `/admin` is the first operator surface.
- Admin users can view app users, roles, plan tiers, subscription states, usage counters, and token totals when usage event metadata includes token fields.
- Admin users can manually grant Dedicated beta access after confirming PayMongo Payment Link payment.
- Future admin work should add grant revocation, usage drill-down, and user support actions.

Production auth email:

- Configure custom SMTP before public beta so signup confirmation is not limited by Supabase's built-in development sender.
- Set Supabase Site URL and Redirect URLs for local, staging, and production.
- Keep confirmation and recovery templates branded, concise, and pointed at `/auth/verified` or the correct production equivalent.
- Smoke-test signup confirmation, password recovery, and onboarding handoff with fresh test accounts.

## Security, Privacy, And Abuse Controls

Must-have Phase 1 controls:

- Enable RLS on all user-owned tables.
- Users can read only their own profile and usage data.
- Users cannot update their own `role`, `plan_tier`, or `subscription_status`.
- Admin-only grant actions must be server-side.
- Service-role key must never be exposed to client code.
- Manual grant records must be auditable.
- Custom SMTP credentials must live only in Supabase/provider settings, never in client code or repo files.

## Quotas, Billing, Or Entitlements

Seed plans:

- `free`
- `dedicated`

Add beta handling:

- `PHP 99` founding beta grants Dedicated access for 30 days.
- Manual grant sets `plan_tier = dedicated`, `subscription_status = active`, and creates a grant record.
- Expired grants should eventually downgrade the user unless another active grant/payment exists.
- For the first beta, admins confirm the PayMongo Payment Link payment outside the app, then use `/admin` to grant access.

Initial beta limits should follow `product/phase-1-backend/quota-and-entitlements-v1.md`.

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

### Slice 2 - Production Auth Email Readiness

Status: **Planned before public beta.**

Tasks:

- Choose SMTP provider for public beta.
- Configure Supabase custom SMTP.
- Set Site URL and allowed redirect URLs for local and production.
- Apply branded confirmation and recovery email templates.
- Test signup, confirmation, sign-in, password recovery, and onboarding redirect with fresh accounts.

Exit criteria:

- Signup confirmation can handle more than the built-in Supabase 2-email/hour development limit.
- Confirmation links return users to the intended Peaked auth/onboarding path.
- Password recovery works with branded email copy.
- No SMTP credentials exist in the app repository.

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

Status: **Drafted; apply and smoke-test next.**

Purpose:

- Add profiles, plans, usage, activity, and manual entitlement grant schema.

Files/areas likely touched:

- `peaked-219edec1/supabase/migrations/*`
- `peaked-kb/foundation/database-architecture.md` if schema evolves.

Contracts:

- Enums and tables listed above.
- RLS policies for user-owned data.
- Seed rows for `free` and `dedicated`.
- `briansismundo@gmail.com` is promoted to `role = admin` when that Supabase Auth user/profile exists.

Risks:

- Weak RLS.
- Admin operations too broad.
- Migration has not been applied yet because the local Supabase CLI is not installed in this environment.

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

Status: **Partially complete.**

Purpose:

- Replace mock profile and usage summary with database-backed reads.

Completed:

- Profile identity, role, and plan tier read from Supabase.
- Usage cards read plan limits and usage counters from Supabase.
- Empty usage counters fall back to `0 / plan limit`.

Remaining:

- Show active beta grant details more fully.
- Add richer usage history once real usage events exist.

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

### Slice 6 - Career Intent Foundation

Status: **Drafted in migration, onboarding lite UI, and profile full UI; apply and smoke-test next.**

Purpose:

- Store what the user wants next in their job search before Peaked starts ranking jobs or generating AI outputs.

Files/areas likely touched:

- `peaked-219edec1/supabase/migrations/*`
- `src/routes/profile.tsx`
- `src/components/profile/*` if the UI grows beyond the route file.
- `src/lib/profile/*`

Contracts:

- Each profile can have one career intent record.
- Users can read and update only their own career intent.
- Admins can view career intent for support and future quality review.
- Career intent fields remain structured enough for ranking and AI prompt grounding.

Risks:

- Asking too much too early and making onboarding feel heavy.
- Treating user goals as permanent when job-search intent changes often.
- Letting free-form notes become the only source of truth.

Verification:

- User can save and edit career intent.
- Another user cannot read or update it.
- Admin can inspect it.
- Empty career intent has a clear UI state.

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
- `product/phase-0-discovery/paymongo-payment-plan.md` if grant flow changes.
- `product/phase-1-backend/quota-and-entitlements-v1.md` if limits change.
- Add a new audit entry under `peaked-kb/audits`.

## Open Questions

- What is the Supabase project URL?
- What is the Supabase anon/publishable key?
- Has `briansismundo@gmail.com` signed up in Supabase Auth after the migration is applied, or does the existing auth user need profile backfill verification?
- Should Google OAuth be configured in Phase 1 or after email/password is stable?
- Should manual grants be done through a minimal admin route or a local/server-only script first?
