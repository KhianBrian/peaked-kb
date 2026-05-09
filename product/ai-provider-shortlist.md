# AI Provider Shortlist

## Purpose

Peaked needs AI for resume parsing, career evidence extraction, job normalization, match explanations, truthful resume tailoring, cover letters, and interview prep. The testing phase should minimize cost while preserving usage tracking per Peaked user.

## Recommendation

Use OpenRouter for the testing phase if the existing company account is acceptable for internal spend tracking. Wrap it behind a Peaked server-side AI adapter so the app can later switch to direct OpenAI, Google, Anthropic, or another provider without rewriting product code.

## Model Call For Beta

Use this as the first concrete model map for private beta:

| Feature | Primary Model | Fallback / Upgrade | Why |
| --- | --- | --- | --- |
| Resume text cleanup | `google/gemini-2.5-flash-lite` | `openai/gpt-5-mini` | Cheap, fast, enough for cleanup and section detection. |
| Resume parsing to structured sections | `openai/gpt-5-mini` | `google/gemini-2.5-flash` | Better structured output reliability matters here. |
| Career evidence extraction | `openai/gpt-5-mini` | `anthropic/claude-3.5-haiku` or `google/gemini-2.5-flash` | Must avoid inventing evidence; use schema validation and confidence flags. |
| Job description normalization | `google/gemini-2.5-flash-lite` | `openai/gpt-5-mini` | High-volume, low-risk extraction task. |
| Deterministic match scoring | No AI | No AI | Code should compute the score from structured resume/job data. |
| Match explanation | `openai/gpt-5-mini` | `google/gemini-2.5-flash` | Explanation should be grounded and readable, but not expensive. |
| Gap analysis | `openai/gpt-5-mini` | `anthropic/claude-3.5-haiku` | Needs careful wording and truthful gaps. |
| Resume tailoring | `openai/gpt-5-mini` first | `anthropic/claude-sonnet-4` or current best mid/high model only when quality fails | Most trust-sensitive feature; still start cost-controlled. |
| Cover letter generation | `openai/gpt-5-mini` | `anthropic/claude-3.5-haiku` | Needs solid writing, but lower complexity than resume tailoring. |
| Interview prep | `google/gemini-2.5-flash-lite` | `openai/gpt-5-mini` | Structured Q&A output can be cheap unless quality is weak. |
| AI output repair / schema retry | Same model as original call | `openai/gpt-5-mini` | Retry once, then fail safely. |

Do not use frontier/high-cost models by default in beta. They should be manually enabled for quality testing only after cheaper models produce unacceptable results.

Primary testing stack:

| Use Case | Default Model Class | Notes |
| --- | --- | --- |
| Resume text cleanup and section extraction | Cheap/fast model | Use deterministic schema output; retry once only. |
| Career evidence extraction | Cheap/fast or mid model | Needs careful schema validation because hallucinated evidence is dangerous. |
| Job description normalization | Cheap/fast model | Good candidate for cached outputs by job input hash. |
| Match scoring explanation | Cheap/fast or mid model | Deterministic score first, AI explanation second. |
| Resume tailoring | Mid model | Higher-quality output matters; must be grounded only in approved evidence. |
| Cover letter generation | Cheap/fast or mid model | Use evidence and selected job only. |
| Interview prep | Cheap/fast or mid model | Moderate output, cache per resume/job/prompt version. |

## Candidate Providers

### OpenRouter

Best for:

- Testing multiple model families through one API.
- Comparing cost and output quality quickly.
- Centralized credits and analytics.
- Fallback routing if a model/provider is unavailable.

Risks:

- It is an extra vendor layer.
- Organization usage metadata may be visible to organization members.
- App attribution headers can make app-level usage publicly visible in OpenRouter rankings if included.
- Provider-specific behavior can vary behind the same OpenRouter-compatible API.

Testing decision:

Acceptable for development and private beta if:

- API key stays server-side.
- A separate Peaked testing API key is created with a credit limit.
- Usage accounting is enabled in requests.
- Peaked records its own per-user usage events in Supabase.
- App attribution headers are omitted unless public app ranking is intentional.

### Direct OpenAI API

Best for:

- Stable structured-output workflows.
- Strong resume tailoring, reasoning, and writing quality.
- Clear direct-provider billing.

Useful model references:

- `gpt-5-mini`: low-cost, good default for well-defined tasks.
- `gpt-5` or newer stronger model: reserve for harder tailoring or quality-sensitive generation.

Pricing checked:

- OpenAI pricing lists `gpt-5-mini` at $0.25 per 1M input tokens and $2.00 per 1M output tokens.
- Stronger GPT-5-class models cost more and should be reserved for higher-value tasks.

### Google Gemini API

Best for:

- Low-cost high-volume extraction/normalization tests.
- Free-tier experimentation.
- Very cheap paid-tier options such as Gemini Flash-Lite.

Pricing checked:

- Gemini 2.5 Flash-Lite paid tier lists $0.10 per 1M text input tokens and $0.40 per 1M output tokens.
- Gemini 2.5 Flash lists $0.30 per 1M text input tokens and $2.50 per 1M output tokens.
- Gemini 2.5 Pro lists $1.25 per 1M input tokens and $10.00 per 1M output tokens for prompts up to 200k tokens.

Risk:

- Free-tier prompts may be used to improve Google products. Paid tier says prompts are not used to improve products.

### Anthropic Claude

Best for:

- High-quality writing and nuanced document generation.
- Conservative wording where tone and truthfulness matter.

Pricing checked:

- Claude Haiku 3.5 is listed at $0.80 per 1M input tokens and $4.00 per 1M output tokens.
- Claude Sonnet 4 is listed at $3.00 per 1M input tokens and $15.00 per 1M output tokens.

Risk:

- More expensive than cheap OpenAI/Gemini options for high-volume tasks.

## OpenRouter Account Visibility

If using an OpenRouter account under the company name:

- Peaked end users do not see the OpenRouter account, dashboard, API key, or token usage unless Peaked exposes that information in the app.
- OpenRouter organization admins and members may see organization-wide activity metadata when viewing the organization context.
- OpenRouter documentation says organization activity shows metadata such as model usage, costs, timing, and request metadata, and can be filtered by API key.
- The app should store Peaked-side `usage_events` per user, feature, model, provider, token counts, estimated cost, status, and created date.
- Do not rely only on OpenRouter analytics for user-level product billing or quotas.

Recommended setup:

1. Create a dedicated OpenRouter API key named `peaked-testing`.
2. Set a strict credit limit on that key.
3. Keep the key server-side only.
4. Enable OpenRouter usage accounting with `usage: { include: true }`.
5. Store returned usage/cost metadata in Peaked's database.
6. Omit `HTTP-Referer` and `X-OpenRouter-Title` during private testing unless public app attribution is desired.

## Cost Planning For Beta Price

Assume the ₱199 Dedicated beta plan includes limited usage rather than unlimited AI.

Starting limits should remain:

- 1 active resume.
- 2 resume parse attempts per month.
- 50 job matches per month or 5 per day.
- 10 user-submitted jobs per month.
- 5 tailored resumes per month.
- 5 cover letters per month.
- 3 interview prep sets per month.
- 1 regeneration per artifact.

Pricing implication:

- With cheap models and strict quotas, AI token cost per normal beta user should be well below ₱199.
- The dangerous cost is not a single generation; it is unbounded retries, unlimited regeneration, huge pasted resumes/jobs, and no per-user quota.
- ₱199 remains a reasonable beta price if usage limits are enforced server-side.

## Implementation Rules

- Never call AI providers directly from the browser.
- Do not expose OpenRouter, OpenAI, Google, or Anthropic keys to client bundles.
- Quota-check before provider calls.
- Use idempotency keys for expensive tasks.
- Store token/cost metadata per user and feature.
- Schema-validate AI outputs.
- Do not log full resumes, private prompts, or generated documents in normal logs.
- Cache repeatable outputs by user/session scope, input hash, prompt/model/schema version, and feature name.

## Sources Checked

- OpenRouter usage accounting: https://openrouter.ai/docs/use-cases/usage-accounting
- OpenRouter API keys and credit limits: https://openrouter.ai/docs/api-keys
- OpenRouter organization management: https://openrouter.ai/docs/organization-management
- OpenRouter app attribution: https://openrouter.ai/docs/app-attribution
- OpenAI pricing: https://platform.openai.com/docs/pricing/
- Google Gemini API pricing: https://ai.google.dev/pricing
- Anthropic pricing: https://docs.anthropic.com/en/docs/about-claude/pricing
