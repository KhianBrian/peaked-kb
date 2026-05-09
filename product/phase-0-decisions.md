# Phase 0 Decisions

## Purpose

Phase 0 exists to choose the foundation for Peaked before backend implementation starts. The current priority is to build without monthly platform spend while still keeping the architecture ready for paid Dedicated users, Philippine payments, and job discovery for Filipino applicants.

## Current Product Priority

Peaked should prioritize:

- Philippine job seekers.
- Remote VA and remote-friendly work for Filipinos.
- PHP-first pricing.
- Low-friction payments that do not require a credit card from users.
- Zero or near-zero infrastructure spend during early development.

## Backend Decision

Recommendation: use Supabase as the default backend for Phase 1 unless Lovable Cloud offers a clearly better no-cost path with Postgres, auth, private storage, and server-side compatibility.

Why Supabase fits this stage:

- It provides Postgres, auth, storage, APIs, and row-level security in one backend.
- The free tier is enough for early MVP development: 50,000 monthly active users, 500 MB database, 1 GB file storage, 5 GB egress, 5 GB cached egress, and 500,000 edge function invocations.
- It supports the planned schema in `foundation/database-architecture.md` without forcing a document-database redesign.
- It keeps the role/plan split clean: `profiles.role` for authority, `profiles.plan_tier` for entitlement.
- It has a clear upgrade path to Pro later if inactivity pauses, backups, storage, or production reliability become blockers.

Known free-tier tradeoffs:

- Free projects pause after one week of inactivity.
- The database limit is small for resume-heavy usage.
- Automatic backups are not included on the free plan.
- Free storage is only 1 GB, so private resume files must be size-limited and cleaned up.

Primary alternatives:

| Option | Free-stage fit | Strengths | Risks |
| --- | --- | --- | --- |
| Supabase | Best default | Postgres, auth, storage, RLS, simple MVP path | 500 MB DB, 1 GB storage, inactivity pause |
| Neon | Strong database option | Generous serverless Postgres, branching, scale-to-zero | Auth/storage would need separate services or more integration work |
| Firebase | Good free tier, weaker fit | Auth and free Spark plan are easy to start | Firestore is not ideal for the relational schema; paid/Blaze features can introduce billing risk |
| Cloudflare D1 | Useful later if Cloudflare-native | Free D1 limits are generous for prototypes | SQLite semantics, weaker fit for complex relational product and Supabase-style auth/storage |

Decision:

Use Supabase for Phase 1 planning and schema design. Revisit only if Lovable Cloud provides a first-party backend with comparable no-cost Postgres, auth, private storage, and deployment ergonomics.

Sources checked:

- Supabase pricing: https://supabase.com/pricing
- Neon pricing: https://neon.com/pricing
- Firebase pricing plans: https://firebase.google.com/docs/projects/billing/firebase-pricing-plans
- Cloudflare D1 pricing: https://developers.cloudflare.com/d1/platform/pricing/

## Auth Decision

Recommendation: use Supabase Auth if Supabase is selected.

Phase 1 auth should support:

- Email/password sign-up and sign-in.
- Google OAuth.
- Server-side session lookup.
- Profile creation on first sign-up.
- Role and plan claims read from the database, not trusted from client state.

Do not mix auth identity with product entitlement. Supabase Auth owns identity; `profiles` owns app role, plan, quota, and billing state.

## Payment Decision

Recommendation: keep PayMongo and NextPay/NextAPI both in Phase 0 research, but do not implement subscriptions until requirements and account access are verified.

Current direction:

- User payment methods should include GCash and Maya.
- PHP should be the primary currency.
- Users should not need a credit card.
- Entitlements should unlock only after trusted provider confirmation, preferably webhook-based.

PayMongo findings:

- PayMongo Payment Links advertise no setup fee, no monthly charge, and support for GCash, Maya, cards, QRPh, GrabPay, ShopeePay, online banking, and BNPL.
- PayMongo docs indicate individuals/freelancers are a supported business type.
- Activating payment methods depends on business type review, and timelines vary by channel. PayMongo lists approximate review timelines such as 3-7 business days for GCash/Maya and 5-10 business days for cards.
- Payment Links may be useful before full API integration, but API/webhook/subscription readiness still needs account-level verification.

NextPay / NextAPI findings:

- NextAPI supports payment intents, QRPh-style collection, and webhooks with event handling for paid/expired payment intents.
- NextPay help content says API access is currently only available to Pro Plus subscribers.
- NextPay account verification still asks for documents. For unregistered businesses and freelancers, listed requirements include owner selfie, government-issued ID, and proof of billing address.
- NextPay appears promising for QRPh and bank/e-wallet flows, but may not be the lowest-hassle option if API access requires a paid subscription or additional onboarding.

Decision:

Use PayMongo Payment Links as the chosen earliest manual payment path for private beta. The initial founding/tester price is `PHP 99` for `Peaked Dedicated Beta - 30 days`. Keep NextPay/NextAPI as a candidate for API-driven QRPh/payment-intent flows only after confirming Pro Plus cost, onboarding, and collection support.

Implementation rule:

No payment provider should unlock `plan_tier = dedicated` from client-side return URLs alone. Dedicated access must be granted from trusted server-side verification, preferably signed webhook events. If early Payment Links are used before full webhooks, admin-confirmed manual activation is acceptable for a private beta.

See `product/paymongo-payment-plan.md`.

Sources checked:

- PayMongo Payment Links: https://www.paymongo.com/products/payment-channels/payment-link
- PayMongo hosted checkout: https://www.paymongo.com/products/payment-channels/hosted-checkout
- PayMongo payment method eligibility: https://developers.paymongo.com/docs/payment-method-eligibility
- PayMongo verification guidelines: https://developers.paymongo.com/docs/business-verification
- NextPay requirements: https://help.nextpay.world/en/articles/8989130-what-are-the-requirements
- NextPay API access: https://help.nextpay.world/en/articles/8995706-where-can-i-access-the-api-documentation
- NextAPI payment intents: https://developers.nextpay.world/docs/guides/money-in/create-payment-intent
- NextAPI product page: https://nextpay.world/product/nextapi

## Job Market Decision

Recommendation: prioritize Philippine and remote VA work for Filipinos.

Phase 0 should evaluate sources in this order:

1. User-submitted job URLs and pasted job descriptions.
2. Remote-friendly APIs or feeds with explicit terms.
3. Company ATS feeds where allowed.
4. Philippine job-board partnerships or approved access.
5. Browser-extension research later for user-initiated analysis.

Do not make the MVP dependent on unauthorized scraping of OnlineJobs.ph, LinkedIn, Indeed, JobStreet, or similar platforms.

## Pricing Direction

Recommendation: PHP-first pricing for Dedicated users.

Initial pricing should be simple:

- Free user: limited daily/monthly usage.
- Dedicated beta user: `PHP 99` founding/tester payment link for 30 days, with tight AI quotas and manual activation.
- Future Dedicated user: paid plan with higher quotas and access to application materials/interview prep.
- Admin: internal authority, separate from paid entitlement.

The `PHP 99` price is a private beta/testing price, not the assumed mature public plan price. Revisit pricing after real AI usage and support data.

## AI Provider Direction

Phase 0 should shortlist AI providers by:

- Free credits or low-cost development usage.
- Server-side API compatibility.
- Strong structured output reliability.
- Good performance for resume parsing, job normalization, ranking explanations, and truthful application drafting.
- Clear data usage/privacy terms.

Current recommendation:

- Use OpenRouter for the testing phase if a dedicated Peaked testing API key can be created under the company account with a strict credit limit.
- Keep the AI provider behind a server-side Peaked adapter so the app can later switch to direct OpenAI, Google Gemini, Anthropic Claude, or another provider.
- Track Peaked user-level usage in the app database rather than relying only on provider dashboards.
- Use cheap/fast models for extraction, normalization, and explanations; reserve stronger models for resume tailoring when quality matters.

Provider calls must remain server-side, and every expensive action must be quota-checked before the call.

See `product/ai-provider-shortlist.md`.

## Privacy And Retention Direction

Early defaults:

- Uploaded resumes are private user files.
- Generated resumes and cover letters are private user artifacts.
- Raw resumes and private prompts should not be written to normal logs.
- Users should be able to delete uploaded files and generated artifacts.
- AI usage logs should store metadata, status, model, token/cost estimates, task type, and IDs, not full private content.
- File upload limits should be conservative while using free storage.

## Phase 0 Exit Criteria

Phase 0 is complete when these are written and accepted:

- Backend/auth decision.
- Payment feasibility decision.
- Job-source matrix.
- Ranking model v1.
- Quota and entitlement v1.
- Privacy and retention v1.
