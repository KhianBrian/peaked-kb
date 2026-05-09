# 2026-05-09 - Phase 0 Decision Docs

## Summary

Created Phase 0 planning documents for Peaked's backend, payment, job-source, ranking, quota, and privacy decisions. The docs prioritize no-cost development, Supabase as the likely backend, PHP-first payments, and Philippine/remote VA jobs for Filipino users.

## Request

Start Phase 0, evaluate no-cost backend options, research NextPay versus PayMongo for GCash/Maya-friendly payments, prioritize Philippine and remote VA jobs, use PHP-first pricing, and create the needed decision files.

## Scope

- Project/folder: `peaked-kb`
- Primary files changed:
  - `product/phase-0-decisions.md`
  - `product/job-source-matrix.md`
  - `product/ai-provider-shortlist.md`
  - `product/paymongo-payment-plan.md`
  - `product/ranking-model-v1.md`
  - `product/quota-and-entitlements-v1.md`
  - `foundation/privacy-and-retention.md`
  - `audits/2026-05-09-phase-0-decision-docs.md`
- Related files inspected:
  - `README.md`
  - `product/phases-of-development.md`
  - `product/future-plans.md`
  - `foundation/architecture.md`
  - `foundation/database-architecture.md`
  - `engineering/ai-api-usage-and-limits.md`
  - `engineering/dependency-and-lovable-safety.md`

## Changes Made

- Documented Supabase as the recommended Phase 1 backend default, with Neon, Firebase, and Cloudflare D1 noted as alternatives.
- Documented PayMongo and NextPay/NextAPI payment findings, including verification and API-access caveats.
- Created a job-source matrix focused on Philippine and remote VA jobs without unauthorized scraping.
- Created initial ranking model, quota/entitlement, and privacy/retention docs.
- Added an AI provider shortlist recommending OpenRouter for testing behind a server-side adapter, with direct OpenAI, Google Gemini, and Anthropic Claude as provider candidates.
- Documented PayMongo Payment Links as the chosen private beta payment path with an initial `PHP 99` founding/tester price.

## Decisions

- Supabase is the default backend recommendation for no-cost MVP development because it combines Postgres, auth, storage, APIs, and RLS.
- PayMongo Payment Links are the chosen lowest-friction private beta payment path, while NextPay/NextAPI remains a candidate only after Pro Plus/API and onboarding requirements are confirmed.
- Job discovery should begin with user-submitted jobs and approved APIs/feeds, not restricted job-board scraping.
- OpenRouter is acceptable for testing if a dedicated limited Peaked API key is used and Peaked records its own user-level usage events.

## Verification

- Read current KB foundation, product, engineering, and audit-format docs.
- Inspected current app stack files to confirm TanStack Start, Lovable config, React 19, Vite, and mock UI state.
- Researched current public provider information for Supabase, Neon, Firebase, Cloudflare D1, PayMongo, and NextPay/NextAPI.
- No code build or tests were run because this was documentation/planning work only.

## Follow-Ups

- Confirm Supabase project creation and whether Lovable Cloud has a competitive no-cost backend option.
- Create provider accounts or sandbox accounts for PayMongo and NextPay to verify account-specific onboarding.
- Choose exact Dedicated user PHP price and beta entitlement policy.

## Lessons

- NextPay/NextAPI supports payment intents and webhooks, but public help docs indicate API access may require Pro Plus and account verification still requires documents.
- PayMongo may be less blocked than initially assumed for Payment Links, but individual payment methods still have activation/review timelines.
