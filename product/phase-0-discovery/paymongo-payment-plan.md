# PayMongo Payment Plan

## Purpose

Peaked will use PayMongo Payment Links as the first beta payment path. This keeps payment setup simple while backend auth, plan tracking, admin grants, and webhook automation are still being built.

## Current Decision

Payment provider:

- PayMongo.

Initial beta payment method:

- One-time PayMongo Payment Link.

Initial beta price:

- `PHP 99`

Product label:

- `Peaked Dedicated Beta - 30 days`

Payment link:

- Store the live link securely in operator notes or admin-only configuration.
- Do not commit secret dashboard credentials, API keys, or private account data.
- A public payment link may later be displayed in the app after entitlement handling exists.

## Why PHP 99 First

`PHP 99` is a founding/tester price, not the assumed long-term Dedicated price.

It is acceptable for the first beta because:

- The goal is feedback and payment-flow validation, not revenue optimization.
- PayMongo Payment Links are manual enough that user volume will be controlled.
- AI usage will be quota-limited.
- Dedicated beta access can be manually granted and manually monitored.

Risks:

- `PHP 99` is too low for a mature paid plan if users receive heavy AI usage.
- It may attract less committed users if used publicly too early.
- It leaves less margin for payment fees, AI retries, support, and abuse.

Decision:

Use `PHP 99` only as an early beta/founding tester price. Revisit pricing after the first real usage and AI cost data.

## Beta Payment Flow

Initial manual flow:

1. User signs up or provides the email they will use for Peaked.
2. Operator sends the PayMongo Payment Link.
3. User pays through PayMongo using their preferred supported method.
4. Operator confirms the payment in the PayMongo dashboard.
5. Operator grants `plan_tier = dedicated` manually after backend support exists.
6. Dedicated access lasts 30 days from activation.
7. Operator records the payment reference and activation window.

Until backend support exists, payments should be handled only with known testers.

## Entitlement Rules

Dedicated beta access should require:

- Confirmed PayMongo payment.
- User email or profile ID.
- Activation timestamp.
- Expiration timestamp.
- Manual grant record.

Do not unlock Dedicated access from:

- A screenshot alone.
- A client-side redirect.
- A user saying they paid without PayMongo dashboard confirmation.
- A payment link visit without confirmed payment status.

## Manual Grant Record

When Phase 1 backend exists, each manual grant should capture:

- `profile_id`
- `source = paymongo_payment_link`
- `payment_reference`
- `amount_php = 99`
- `granted_by`
- `starts_at`
- `ends_at`
- `notes`
- `created_at`

Manual grants should be auditable and should not bypass usage logging.

## Beta Limits For PHP 99

Because `PHP 99` is a low founding/tester price, start with tighter limits than the future Dedicated plan:

| Feature | PHP 99 beta limit |
| --- | ---: |
| Active resumes | 1 |
| Resume parse attempts | 1 per 30 days |
| Job matches | 25 per 30 days |
| User-submitted jobs | 5 per 30 days |
| Tailored resumes | 2 per 30 days |
| Cover letters | 2 per 30 days |
| Interview prep sets | 1 per 30 days |
| Regenerations | 1 per artifact |

These limits can be manually expanded for high-value testers, but expansion should be recorded.

## Future Pricing Path

After private beta:

- Keep `PHP 99` as a limited founding tester promo only if useful.
- Consider moving public Dedicated beta pricing to `PHP 199` or higher once AI costs, usage, and conversion are known.
- Consider monthly and one-time credit pack options later if recurring payments are awkward.

## Future Automation Path

Phase 1:

- Add `profiles.plan_tier`.
- Add `profiles.subscription_status`.
- Add manual entitlement grant records.
- Add admin-only entitlement update action.

Later payment automation:

- Add PayMongo API integration if needed.
- Add webhook endpoint if PayMongo flow supports the needed events.
- Verify webhook signatures.
- Store payment events idempotently.
- Upgrade/downgrade users only from trusted server-side events.

## Open Questions

- Exact live Payment Link URL location for operators.
- Whether PayMongo dashboard gives enough data for clean manual reconciliation.
- Whether PayMongo API/webhooks will be used before or after public launch.
- Whether users should receive email instructions after manual activation.

