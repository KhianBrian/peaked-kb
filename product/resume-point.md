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

Start with **Phase 1 Slice 2 - Database Migration V1**.

Next implementation goals:

1. Create Supabase schema/migration for:
   - `account_role`
   - `plan_tier`
   - `subscription_status`
   - `profiles`
   - `plans`
   - `usage_counters`
   - `usage_events`
   - `activity_events`
   - `manual_entitlement_grants`

2. Add RLS policies:
   - Users can read their own profile and usage.
   - Users cannot update their own `role`, `plan_tier`, or `subscription_status`.
   - Manual entitlement grants are admin/server-controlled.

3. Decide profile creation approach:
   - Supabase DB trigger on auth user creation, or
   - TanStack server function after sign-up/sign-in, or
   - controlled hybrid.

4. Replace Profile page mock account/plan/usage data with real Supabase reads.

5. Add manual Dedicated beta grant flow for confirmed PayMongo Payment Link users.

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

