# 2026-05-09 - Auth And Onboarding Polish

## Summary

Polished the Supabase auth surface and onboarding flow after first real account creation. Added stronger password requirements, password visibility toggle, branded Peaked logo usage, sign-up verification messaging, animated auth tab transitions, and smoother onboarding card motion.

## Request

The user tested account creation and reported missing verification messaging, no show-password control, rough onboarding transitions, missing password requirements, plain Supabase email styling, missing Peaked logo usage, and static sign-in/sign-up switching.

## Scope

- Project/folder: `peaked-219edec1`, `peaked-kb`
- Primary files changed:
  - `peaked-219edec1/src/routes/auth.tsx`
  - `peaked-219edec1/src/routes/auth.verified.tsx`
  - `peaked-219edec1/src/components/onboarding-frame.tsx`
  - `peaked-219edec1/src/routes/onboarding.welcome.tsx`
  - `peaked-219edec1/src/routes/onboarding.how-it-works.tsx`
  - `peaked-219edec1/src/routes/onboarding.get-started.tsx`
  - `peaked-219edec1/public/peaked-email-hero.png`
  - `peaked-kb/product/phase-1-backend/supabase-auth-email-template.md`
  - `peaked-kb/audits/2026-05-09-auth-onboarding-polish.md`
- Related files inspected:
  - `peaked-kb/engineering/component-structure.md`
  - `peaked-kb/engineering/ui-animation-standards.md`
  - `peaked-219edec1/src/components/layout/TopNav.tsx`

## Changes Made

- Added sign-up password rules for uppercase, lowercase, special character, and minimum length.
- Added show/hide password control with lucide icons.
- Added a persistent sign-up success notice telling users to verify their email.
- Reused `/peaked-logo.ico` and Peaked naming on auth/onboarding.
- Added Motion transitions for auth tab switching and onboarding card entrance.
- Added a Supabase confirmation email template doc.
- Added `/auth/verified` so email confirmations land on a branded Peaked page before routing into onboarding.
- Expanded onboarding copy to reflect the MVP command-center direction.
- Added the generated email hero image to the app public folder and documented the production image URL for Supabase emails.

## Decisions

- Password complexity is enforced only on sign-up; sign-in only requires a non-empty password so existing users are not blocked by newer rules.
- Google OAuth remains disabled until provider setup is intentionally configured.
- Supabase email styling is documented for dashboard configuration because it is not controlled by app code.
- Email confirmation redirects should point to `/auth/verified` instead of sending users straight to a generic site root.
- Production auth redirects should include `https://peaked.lovable.app/auth/verified` and `https://peaked.lovable.app/**`.

## Verification

- Ran Prettier on touched auth/onboarding files.
- `npx eslint src/routes/auth.tsx src/components/onboarding-frame.tsx src/routes/onboarding.welcome.tsx src/routes/onboarding.how-it-works.tsx src/routes/onboarding.get-started.tsx` passed.
- `npx eslint src/routes/auth.tsx src/routes/auth.verified.tsx src/routes/onboarding.how-it-works.tsx` passed after the verification route update.
- `npm run build` completed successfully.
- Build still emits the existing Wrangler log-write permission warning outside the workspace.
- Browser verification remains manual in the running local app.

## Follow-Ups

- Paste the branded confirmation email template into Supabase.
- Browser-test sign-up, sign-in, password visibility, tab transitions, and onboarding transitions.
- Continue Phase 1 with profile table/migration work.

## Lessons

- Supabase default email templates should be branded before beta tester invites; otherwise the verification email feels too generic and untrustworthy.
