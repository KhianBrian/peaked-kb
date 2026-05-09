# Quota And Entitlements V1

## Purpose

Quotas should let Peaked develop and launch without uncontrolled AI or storage costs. Entitlements must be enforced server-side and should separate internal authority from paid product access.

## Role And Plan Split

Authority:

- `profiles.role = user`
- `profiles.role = admin`

Product access:

- `profiles.plan_tier = free`
- `profiles.plan_tier = dedicated`

Do not use `role` to represent paid users. A Dedicated user is not an admin, and an admin may need to test Free and Dedicated behavior.

## Starting Plan Limits

These are conservative defaults for early MVP cost control. Adjust after real usage data.

| Feature | Free user | Dedicated user |
| --- | ---: | ---: |
| Resume uploads | 1 active resume | 5 active resumes |
| Resume parse attempts | 2 per month | 20 per month |
| Job matches | 5 per day | 50 per day |
| User-submitted jobs | 3 per day | 30 per day |
| Resume tailoring | 2 per month | 30 per month |
| Cover letters | 2 per month | 30 per month |
| Interview prep sets | 1 per month | 20 per month |
| Regenerations | 1 per artifact | 5 per artifact |

Anonymous visitors:

- No resume uploads.
- No durable saved jobs.
- Optional 1 lightweight preview per session if cost permits.

## PHP 99 Founding Beta Limits

The initial PayMongo Payment Link price is `PHP 99` for `Peaked Dedicated Beta - 30 days`. Because this is a low founding/tester price, use tighter limits than the future Dedicated plan:

| Feature | PHP 99 beta limit |
| --- | ---: |
| Active resumes | 1 |
| Resume parse attempts | 1 per 30 days |
| Job matches | 25 per 30 days |
| User-submitted jobs | 5 per 30 days |
| Resume tailoring | 2 per 30 days |
| Cover letters | 2 per 30 days |
| Interview prep sets | 1 per 30 days |
| Regenerations | 1 per artifact |

These limits can be manually expanded for unusually valuable testers, but expansion should be auditable.

## Enforcement Rules

Server-side checks are the source of truth:

- Authenticate user.
- Load profile.
- Load active plan.
- Check feature entitlement.
- Check daily/monthly quota.
- Check burst/cooldown limits.
- Check cache.
- Only then call expensive providers.
- Record usage event after success or billable attempt, depending on feature policy.

Client-side disabled states are only convenience and must not be trusted.

## Billing Status Rules

Dedicated access should require:

- `plan_tier = dedicated`
- trusted payment confirmation
- active subscription or valid paid period
- non-expired entitlement

Payment states should map separately:

- `none`
- `trialing`
- `active`
- `past_due`
- `cancelled`
- `expired`

No client-side redirect from checkout should directly unlock Dedicated access.

## Manual Beta Entitlements

Before full payment integration, admins may manually grant Dedicated access for private beta users.

Rules:

- Manual grants must be auditable.
- Include granted by, reason, start date, end date, and notes.
- Prefer expiration dates for all manual grants.
- Manual grants should not bypass usage logging.

## Cost Controls

Use:

- Max file size limits.
- Max extracted text length.
- Input hashing and caching.
- Idempotency keys for AI tasks.
- Cooldowns for regeneration.
- Provider/model selection by task complexity.
- Admin kill switch for AI tasks.
- Source-specific job ingestion caps.

## Initial Storage Limits

While using free infrastructure:

- Keep resume upload size conservative.
- Store only necessary generated artifacts.
- Support deletion early.
- Avoid large duplicate files by hashing uploads.
- Do not store job boards' full data unless source terms allow it.

## Open Questions

- Exact public Dedicated monthly price in PHP.
- Whether public Dedicated should be monthly only at first or support one-time packages.
- Whether Free users should get any resume tailoring before payment.
- Whether job matching should be higher-limit than generation because it may be cheaper.
- Whether beta users get temporary Dedicated access during testing.
