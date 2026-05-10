# Resume Point

Last updated: 2026-05-10

## Current State

Peaked is in **Phase 1 - Backend Foundation**.

Phase 0 is complete enough to proceed:

- Supabase selected for backend/auth.
- PayMongo Payment Link selected for private beta payments.
- OpenRouter selected as acceptable AI testing router, server-side only later.
- Philippine and remote VA job strategy documented.
- Ranking, quota, privacy, and email-template docs exist.

Phase 1 has started:

- Supabase project exists.
- Supabase email/password auth works.
- Production Lovable app works with Supabase browser config.
- `/auth` uses real Supabase sign-up/sign-in.
- `/auth/verified` handles email-confirmation landing and onboarding handoff.
- Auth/onboarding UI is polished enough for beta testing.
- Supabase migration scaffold exists in `peaked-219edec1/supabase`.
- First backend foundation migration is drafted at `peaked-219edec1/supabase/migrations/202605100001_backend_foundation_v1.sql`.
- First admin email is `briansismundo@gmail.com`; the migration promotes that email to `role = admin` when the Supabase Auth user/profile exists.
- Migration now includes a profile backfill for Supabase Auth users who existed before the profile trigger was installed.
- `/admin` exists as the first admin surface for viewing users, roles, plans, usage counters, and token totals from usage event metadata.
- Profile, top nav avatar, dashboard greeting, and legacy sidebar identity now read from the real Supabase profile instead of the `Alex Morgan` mock user.
- Browser Supabase client is lazy-loaded so `bun run dev` on Node 20 does not crash during SSR with a Realtime WebSocket transport error.
- Second migration exists at `peaked-219edec1/supabase/migrations/202605100002_career_intents.sql` and must be applied before saving career intent in the app.
- Third migration exists at `peaked-219edec1/supabase/migrations/202605100003_onboarding_completion_rpc.sql` and must be applied before onboarding can mark itself complete.
- Onboarding now includes Career Intent Lite on `/onboarding/get-started`.
- Sign-in now sends users with incomplete onboarding to `/onboarding/welcome`; verified email handoff also routes based on onboarding status when a session exists.
- Profile usage cards now read plan limits and usage counters from Supabase, with empty-counter fallback states.
- Profile includes the full editable Career Intent section.
- Generated email hero image is in `peaked-219edec1/public/peaked-email-hero.png`.

## Important Repos

- App: `peaked-219edec1`
- Knowledge base: `peaked-kb`
- `ResuMatch2` remains read-only unless explicitly overridden.

## Latest Known App Commit

Pulled latest from GitHub/Lovable on 2026-05-10.

Latest app head observed:

```text
6954fa0 Update plan
```

Latest KB head observed:

```text
9e22675 Document Phase 0 decisions and backend setup
```

## Where To Resume

Continue with **Phase 1 Slice 2 - Database Migration V1** by applying and smoke-testing the drafted migration.

Next implementation goals:

1. Apply and verify the drafted Supabase schema/migration for:
   - `account_role`
   - `plan_tier`
   - `subscription_status`
   - `profiles`
   - `plans`
   - `usage_counters`
   - `usage_events`
   - `activity_events`
   - `manual_entitlement_grants`

2. Smoke-test RLS policies:
   - Users can read their own profile and usage.
   - Users cannot update their own `role`, `plan_tier`, or `subscription_status`.
   - Manual entitlement grants are admin/server-controlled.

3. Confirm profile creation trigger:
   - New Supabase Auth users should get an app profile automatically.
   - `briansismundo@gmail.com` should become admin after signup/profile creation.

4. Apply and verify `202605100002_career_intents.sql` and `202605100003_onboarding_completion_rpc.sql`.

5. Test email verification, sign-in routing, Onboarding Career Intent Lite, and Profile full Career Intent save/read behavior as admin and normal user.

6. Add manual Dedicated beta grant flow for confirmed PayMongo Payment Link users.

## Secrets And Key Policy

Browser-safe:

- Supabase URL.
- Supabase publishable/anon key.

Server-only:

- `SUPABASE_SERVICE_ROLE_KEY`
- future `OPENROUTER_API_KEY`
- future `PAYMONGO_SECRET_KEY`
- future `PAYMONGO_WEBHOOK_SECRET`

Never put server-only secrets in:

- `VITE_*`
- React components
- client bundle
- markdown docs
- committed source files

If Lovable cannot reliably provide server-only secrets for TanStack Start server code, use Supabase Edge Functions for AI/payment secret work.

## Known Caveats

- Full project lint has pre-existing Prettier issues unrelated to recent auth work.
- Builds complete, but Wrangler emits a log-write permission warning in the local sandbox.
- Supabase email sending may rate-limit repeated signup tests.
- Lovable credits are currently depleted, so avoid depending on Lovable for immediate changes.
