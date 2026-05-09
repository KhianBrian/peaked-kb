# AI API Usage And Limits

## Purpose

The app will use AI APIs for user tasks. AI features must be useful, cost-aware, abuse-resistant, and safe for production. The default assumption is that AI calls are expensive, failure-prone, and should never be made directly from the browser.

## Core Rules

- Keep AI API keys server-side only.
- Route browser requests through internal app endpoints.
- Validate and authorize before calling an AI provider.
- Enforce user limits before making the provider call.
- Use timeouts and bounded retries.
- Log metadata, not sensitive raw content.
- Return user-safe errors.
- Track usage and cost per user, task type, and provider/model.

## User Limit Model

The app should support limits at multiple levels:

- Anonymous visitor limits.
- Free account limits.
- Paid plan limits.
- Per-feature limits.
- Per-day and per-month limits.
- Burst limits to stop rapid abuse.
- Regeneration limits for AI outputs.

Recommended minimum fields for usage accounting:

```text
user_id
session_id
feature
provider
model
input_tokens
output_tokens
estimated_cost
cache_hit
status
created_at
```

## Request Flow

Preferred AI request flow:

1. Client submits task to internal endpoint.
2. Server authenticates the user or identifies anonymous session.
3. Server validates input size and schema.
4. Server checks quota, plan, cooldown, and abuse rules.
5. Server checks cache for an existing valid result.
6. Server calls AI provider only if needed.
7. Server validates AI output against a schema.
8. Server stores result and usage metadata.
9. Server returns a safe response to the client.

## Limiting The Website

Use layered protection:

- UI disables submit buttons during in-flight requests.
- Client prevents obvious duplicate submissions.
- Server uses idempotency keys for expensive tasks.
- Server enforces per-user quotas.
- Server enforces per-IP or per-session burst limits for anonymous users.
- Server applies max input size limits.
- Server rejects unsupported file types and oversized documents.
- Server requires authentication for expensive or repeatable AI workflows.

Client-side limits are convenience only. Server-side limits are the authority.

## Quota Examples

Possible starting policy:

```text
Anonymous:
  - 1 lightweight AI preview per session
  - No file-heavy analysis

Free user:
  - Limited resume scans per day
  - Limited job match explanations per day
  - Regeneration cooldown

Paid user:
  - Higher daily/monthly allowance
  - Priority access if supported
  - Still protected by burst limits
```

Actual numbers should be chosen with product and cost assumptions, then adjusted using real usage data.

## Cost Controls

- Cap max input length.
- Summarize or extract before sending huge documents.
- Use cheaper models for classification, extraction, and simple rewrites.
- Reserve stronger models for high-value reasoning tasks.
- Cache deterministic or repeatable outputs.
- Stop work early when validation fails.
- Track cost per successful user-visible result.
- Add admin/provider kill switch for runaway usage.

## Prompt And Output Safety

Every AI feature should define:

- System instruction.
- User input boundaries.
- Output schema.
- Refusal/fallback behavior.
- Max tokens.
- Temperature or determinism setting.
- Prompt/template version.

Never trust raw AI output as structured data. Validate it with a schema before using it in UI or storage.

## Prompt Injection Risks

Resume text, job descriptions, and user-uploaded files can contain malicious instructions.

Defenses:

- Treat user content as data, not instructions.
- Delimit user-provided content clearly.
- Never allow uploaded text to override system rules.
- Do not expose secrets, hidden prompts, or internal policy.
- Strip or ignore instructions that ask the model to reveal system behavior.

## Privacy

- Do not log full resumes, private job notes, or personal profile details unless explicitly required.
- Prefer hashes for cache keys.
- Redact sensitive fields in logs.
- Give users clear expectations about what AI features process.
- Do not send unnecessary personal data to providers.

## Failure Handling

AI provider failures should be mapped into clear states:

- Rate limited: ask user to wait or upgrade if appropriate.
- Quota exhausted: show plan-aware limit message.
- Provider unavailable: allow retry later.
- Invalid output: retry once with bounded repair, then fail safely.
- Timeout: explain that the task took too long.

Avoid invisible loops. Every retry costs money and time.

## Implementation Checklist

Before shipping an AI feature:

- Is the provider key server-only?
- Is input validated?
- Are user limits enforced before the provider call?
- Are idempotency and duplicate-submit protections in place?
- Is the AI response schema-validated?
- Is cost/usage logged?
- Are sensitive logs avoided?
- Are cache rules defined?
- Are error states user-safe?
- Does the feature build and run in Lovable?

