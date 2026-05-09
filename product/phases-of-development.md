# Phases Of Development

## Product Direction

Peaked should not be positioned as another resume tailoring tool. The stronger goal is:

> Peaked is a job-search command center that finds the best opportunities for a user, ranks them against the user's real career evidence, tailors truthful application materials, prepares the user for interviews, and tracks what works.

The core loop:

```text
Discover -> Rank -> Tailor -> Apply -> Prepare -> Track -> Learn
```

## Current Codebase Scan

The current app is a polished UI prototype.

Implemented surfaces:

- Dashboard route: `peaked-219edec1/src/routes/index.tsx`
- Jobs route: `peaked-219edec1/src/routes/jobs.tsx`
- Resume route: `peaked-219edec1/src/routes/resume.tsx`
- Profile route: `peaked-219edec1/src/routes/profile.tsx`
- Auth route: `peaked-219edec1/src/routes/auth.tsx`
- Onboarding routes: `src/routes/onboarding.*.tsx`
- App shell/navigation: `src/components/layout/AppShell.tsx`, `src/components/layout/TopNav.tsx`
- Paywall UI: `src/components/paywall-modal.tsx`
- Shared mock data: `src/lib/mock-data.ts`

Current missing capabilities:

- Real authentication and session persistence.
- User profiles stored in a database.
- Admin/free/dedicated user access model.
- Career evidence graph from resumes, projects, skills, achievements, and preferences.
- Resume upload, parsing, versions, and exports.
- Job acquisition from approved APIs, user-submitted job URLs/descriptions, and future partnerships.
- Ranking engine for "jobs worth your time today."
- Resume-to-job match scoring.
- AI-generated truthful resume tailoring.
- AI-generated cover letters.
- AI interview preparation.
- Payment checkout and webhook-based entitlement updates.
- Usage limits and quota accounting.
- Real analytics and application tracking.
- Admin operations dashboard.

## Product Roles

- `Admin`: internal operator with access to user management, usage, payment status, job ingestion health, AI task logs, and moderation controls.
- `Free user`: default user with limited daily/monthly AI actions and limited job-search command center features.
- `Dedicated user`: paid user. This is the public website name for premium users because they are paying and committed to the job search.

Implementation rule:

- `role` controls internal authority.
- `plan_tier` controls product access and quota.
- Admin authority and paid entitlement must not be mixed.

## MVP Definition

The MVP should not be:

```text
Upload resume -> paste one job -> tailor resume
```

That pattern already exists across many competitors.

The Peaked MVP should be:

```text
Upload resume
-> build career evidence profile
-> set job goals
-> Peaked recommends best jobs today from approved sources and user-supplied jobs
-> user picks a job
-> Peaked explains match and gaps
-> Peaked tailors resume and cover letter
-> Peaked generates interview prep
-> Peaked tracks application outcomes
```

## Job Acquisition Strategy

Start with sources that do not put the company at legal or operational risk:

- Approved job APIs such as Adzuna, Remotive, USAJOBS, Arbeitnow, RemoteOK, or similar providers after terms review.
- User-submitted job URLs or pasted job descriptions.
- Company ATS feeds where allowed, such as public Greenhouse, Lever, Ashby, or similar sources after terms review.
- Licensed or partner feeds later.

Do not make the MVP depend on unauthorized scraping of LinkedIn, Indeed, OnlineJobs.ph, or similar platforms.

The product can still feel like job discovery because Peaked owns the ranking, recommendation, and application intelligence layer.

## Phase 0 - Discovery And Decisions

Status as of 2026-05-10: **Complete enough to proceed.**

Completed decisions/docs:

- Backend/auth: Supabase selected for Phase 1.
- Payment: PayMongo Payment Link selected for private beta, `PHP 99` founding/tester price.
- AI provider: OpenRouter acceptable for testing behind server-side adapter; concrete beta model map documented.
- Job strategy: Philippine and remote VA jobs for Filipinos prioritized; user-submitted jobs and approved APIs/feeds first.
- Ranking model v1 documented.
- Quota/entitlement v1 documented.
- Privacy and retention v1 documented.
- Supabase confirmation email template documented.

Key Phase 0 docs:

- `product/phase-0-decisions.md`
- `product/paymongo-payment-plan.md`
- `product/ai-provider-shortlist.md`
- `product/job-source-matrix.md`
- `product/ranking-model-v1.md`
- `product/quota-and-entitlements-v1.md`
- `foundation/privacy-and-retention.md`
- `product/supabase-auth-email-template.md`

Goals:

- Choose backend provider: Lovable Cloud/Supabase or another Postgres-backed backend.
- Confirm auth provider and deployment constraints.
- Confirm payment provider: NextPay NextAPI first, with fallback options if subscriptions are awkward.
- Confirm first approved job data sources.
- Define the first version of the ranking model.
- Define free vs dedicated quotas.

Tasks:

- Review Lovable Cloud/Supabase patterns.
- Verify NextPay sandbox access, webhook signing, idempotency, and recurring payment fit.
- Create a job-source matrix for approved APIs, user-submitted jobs, company ATS feeds, and future partner targets.
- Decide the first source set for "jobs worth your time today."
- Define user goal fields: target roles, locations, remote preference, salary target, seniority, industries, dealbreakers.
- Define AI model/provider shortlist.
- Define privacy and retention rules for resumes and generated documents.

Exit criteria:

- Backend provider chosen.
- Payment path chosen.
- Initial job-source strategy chosen.
- Ranking inputs chosen.
- Initial quota numbers chosen.

## Phase 1 - Backend Foundation

Status as of 2026-05-10: **In progress.**

Completed:

- Supabase project created.
- Supabase email/password auth enabled and tested.
- User sign-up appears in Supabase Auth Users.
- Production app works with Supabase publishable browser config after Lovable updates.
- `/auth` uses Supabase email/password sign-up and sign-in.
- `/auth/verified` exists for branded email-confirmation landing and onboarding handoff.
- Auth and onboarding UI polished with Peaked logo, show-password control, sign-up password requirements, verification notice, and Motion transitions.
- `SUPABASE_SERVICE_ROLE_KEY` plan approved conceptually for Lovable encrypted server-only secrets, but confirm whether the secret was actually added before using admin/server operations.

Still missing / resume here:

- Add Supabase database schema and migrations for `profiles`, `plans`, `usage_counters`, `usage_events`, `activity_events`, and `manual_entitlement_grants`.
- Add RLS policies for user-owned reads and admin/server-only writes.
- Add private storage buckets for future resumes and generated documents.
- Ensure new auth users get app `profiles`.
- Replace mock Profile page data with real profile/plan/usage data.
- Add safe manual Dedicated beta grant flow for PayMongo-confirmed users.
- Decide whether Google OAuth belongs in this phase or after email/password/profile is stable.

Goals:

- Replace the UI-only prototype with real accounts, roles, plans, quotas, and storage.

Tasks:

- Add database schema and migrations.
- Add auth session handling.
- Add user profiles.
- Add `Admin`, `Free user`, and `Dedicated user` access model.
- Add server-side authorization helpers.
- Add plan and quota tables.
- Add usage event logging.
- Add activity event logging.
- Add private storage buckets for resumes and generated documents.
- Replace mock profile and usage data with database-backed reads.

Exit criteria:

- Users can sign up/sign in with persistent profiles.
- Server can identify role and plan tier.
- Quotas can be checked before expensive actions.
- Profile page can show real account and plan data.

## Phase 2 - Career Evidence And Resume Parsing

Goals:

- Create the foundation that makes Peaked truthful and differentiated.

Tasks:

- Implement PDF/DOCX upload with file validation.
- Store original files privately.
- Extract raw text.
- Parse resume into structured sections.
- Build career evidence records: roles, companies, projects, skills, metrics, tools, industries, achievements, education, certifications.
- Flag low-confidence parsed data for user review.
- Allow user correction of parsed evidence.
- Compute initial resume readiness and ATS score.
- Store resume versions.

Exit criteria:

- User can upload a resume.
- Peaked creates a structured career evidence profile.
- Resume page shows real parsed data and score.
- Future AI generations can be grounded in user-approved evidence.

## Phase 3 - Job Command Center MVP

Goals:

- Deliver the first version of "jobs worth your time today."

Tasks:

- Add job goals/preferences onboarding.
- Add job source adapters for approved APIs.
- Add user-submitted job URL/description flow.
- Normalize jobs into a shared schema.
- Add job freshness, source, remote/location, salary, seniority, and skill fields.
- Compute resume-to-job match score.
- Compute opportunity score using match, preferences, hard requirements, freshness, salary/location fit, and tailoring effort.
- Show ranked daily job recommendations.
- Explain why each job is recommended.
- Store saved jobs and dismissed jobs.
- Redirect users to original apply URLs.

Exit criteria:

- User sees a ranked list of jobs worth applying to today.
- Recommendations are based on the user's resume, career evidence, and goals.
- User can save, dismiss, or open jobs at the source.
- Job source compliance is documented.

## Phase 4 - Tailored Application Materials

Goals:

- Turn a recommended job into a ready application package.

Tasks:

- Add AI task orchestration.
- Generate truthful ATS-compliant resume versions from approved career evidence.
- Highlight what changed and why.
- Generate cover letters based on the selected job and user evidence.
- Add preview, edit, regenerate, and export actions.
- Store generated artifacts as versions.
- Track prompt version, model, input hash, schema version, and cost.
- Enforce free vs dedicated quotas and regeneration cooldowns.

Exit criteria:

- User can select a recommended job and generate a tailored resume.
- User can generate a cover letter.
- Generated content is grounded in career evidence and does not invent experience.
- Quotas are enforced server-side.

## Phase 5 - Payment And Dedicated User Entitlements

Goals:

- Turn the paywall into real revenue and entitlement logic.

Tasks:

- Add free and dedicated plan records.
- Integrate NextPay sandbox if approved.
- Create checkout/payment intent endpoint.
- Add payment webhook endpoint.
- Verify webhook signatures.
- Store payment customers, payment intents, invoices, subscriptions, and webhook events.
- Upgrade users to `Dedicated user` only after trusted webhook confirmation.
- Add downgrade, cancel, past-due, and expired states.
- Add billing status UI in Profile.
- Add admin payment visibility.

Exit criteria:

- Users can start a payment flow.
- Payment webhooks update plan tier.
- Dedicated users receive higher quotas and gated features.
- Failed or expired payments do not unlock paid features.

## Phase 6 - Dedicated User Application Prep

Goals:

- Make paid usage feel meaningfully better than free usage.

Tasks:

- Generate job-specific interview questions.
- Generate answer coaching using user career evidence.
- Generate recruiter outreach and follow-up messages.
- Add advanced job filters.
- Add resume history and comparison view.
- Add application package checklist per job.
- Add higher usage limits and priority processing.

Exit criteria:

- Dedicated users get more than higher quota.
- Each job can have a full application package: resume, cover letter, prep questions, notes, and status.

## Phase 7 - Application Tracking And Learning Loop

Goals:

- Help users learn which applications work.

Tasks:

- Add application statuses: interested, saved, applied, interviewing, offer, rejected, archived.
- Track which resume version and cover letter were used.
- Track response outcomes.
- Add follow-up reminders.
- Add analytics for response rate by role, source, match score, and resume version.
- Feed insights back into job ranking.

Exit criteria:

- Dashboard reflects real user activity.
- Peaked can learn which job types and resume versions perform best for the user.

## Phase 8 - Admin And Operations

Goals:

- Give operators control over users, payments, usage, AI costs, and job ingestion.

Tasks:

- Build admin-only routes.
- Add user search and profile view.
- Add quota override tools.
- Add payment/subscription status view.
- Add AI task logs and cost summaries.
- Add job source ingestion health.
- Add provider kill switches.
- Add abuse indicators and rate-limit controls.
- Add admin audit logs.

Exit criteria:

- Admin can inspect and support users safely.
- Admin can pause risky providers or expensive AI features.
- Sensitive user data remains protected.

## Phase 9 - Partnerships And Expanded Job Coverage

Goals:

- Expand job data without relying on unauthorized scraping.

Tasks:

- Reach out to job boards and data providers.
- Request API/feed/affiliate access.
- Propose traffic-sharing partnerships where Peaked redirects qualified applicants to original postings.
- Add partner-specific attribution and source rules.
- Add licensed data providers if needed.
- Add browser extension research for user-initiated job analysis.

Exit criteria:

- Peaked has broader job coverage through approved channels.
- Partner/source obligations are tracked in the system.
- Users get better recommendations without legal fragility.

## Phase 10 - Hardening, Compliance, And Launch Readiness

Goals:

- Prepare for production reliability.

Tasks:

- Add rate limits and abuse detection.
- Add privacy controls for resume deletion and generated artifact deletion.
- Add AI prompt/version auditability.
- Add payment reconciliation.
- Add error monitoring.
- Add backup and migration strategy.
- Add security review for admin, storage, and webhooks.
- Add final Lovable deployment verification.

Exit criteria:

- Production data flows are protected.
- Payments reconcile.
- AI costs are observable.
- Job data sources are compliant.
- Build and deployment are repeatable.

## Recommended First Build Slice

Build this first:

1. Auth and profiles.
2. Roles and plan tiers.
3. Usage quotas.
4. Resume upload and career evidence parsing.
5. Job goals/preferences.
6. User-submitted job URL/description analysis.
7. One approved job API integration.
8. Ranked "jobs worth your time today" dashboard.

This gives Peaked a differentiated command-center shape early while avoiding the hardest partnership and scraping risks until the platform has traction.
