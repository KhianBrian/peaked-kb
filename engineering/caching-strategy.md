# Caching Strategy

## Goal

Caching should make the app feel fast without serving stale, private, or expensive-to-correct data. AI results, user account data, and job/resume state require different cache rules.

## Cache Categories

### Static Assets

Examples:

- Icons
- Images
- Built JS/CSS chunks
- Fonts

Rule:

- These can use long-lived cache headers when filenames are content-hashed.
- Avoid caching mutable assets forever unless the URL changes when the file changes.

### Public Or Low-Risk Data

Examples:

- Public marketing content.
- Static configuration.
- Non-sensitive lists.

Rule:

- Cache with clear invalidation rules.
- Prefer short-to-medium TTLs unless the content rarely changes.

### User Data

Examples:

- Profile.
- Resume data.
- Saved jobs.
- Billing/subscription state.

Rule:

- Cache only per authenticated user.
- Do not share across users.
- Invalidate after mutation.
- Avoid long browser caching for private responses.

### AI Results

Examples:

- Resume analysis.
- Job match explanation.
- Generated suggestions.
- Profile improvement tasks.

Rule:

- Cache by stable task input hash, user ID, model/provider version, and feature version.
- Do not cache raw private inputs in plain text if a hash or encrypted storage is enough.
- Store enough metadata to know when the result is obsolete.
- Invalidate when the source resume/profile/job changes.

## TanStack Query Guidance

Use TanStack Query for client-visible server state.

Query keys should include all meaningful scope:

```ts
["resume-analysis", userId, resumeId, analysisVersion]
```

Avoid vague keys:

```ts
["data"]
```

Suggested defaults:

- User profile: short stale time, invalidate after profile update.
- Resume analysis result: longer stale time if input hash is unchanged.
- Job list: short-to-medium stale time depending on source freshness.
- Billing/quota: short stale time and refetch after any usage-changing action.

## AI Cache Key Design

For AI outputs, the cache key should include:

- User ID or anonymous session ID.
- Feature name.
- Input hash.
- Prompt/template version.
- Model/provider name.
- Output schema version.
- Relevant account plan or entitlement if the result depends on it.

Example:

```text
ai:resume_score:v3:user_123:resume_hash:model_name:schema_v2
```

## Invalidation Rules

Invalidate cached AI outputs when:

- The resume changes.
- The job description changes.
- The user profile changes in a way that affects the answer.
- The prompt template changes.
- The model changes.
- The output schema changes.
- A bug fix makes prior results untrustworthy.

## Rate Limit And Cache Interaction

Cache hits should not count the same as fresh AI calls.

Recommended:

- Count fresh provider calls against AI quota.
- Count cache hits separately as low-cost usage if product analytics need it.
- Prevent users from repeatedly forcing regeneration without quota impact.
- Add a cooldown for regeneration buttons.

## Avoid These Mistakes

- Caching private user data in shared CDN cache.
- Using cache keys that omit user identity.
- Reusing AI results after the input changed.
- Retrying failed AI calls without a cap.
- Treating cache as the source of truth for quota or billing.
- Showing stale subscription state after payment changes.

## Implementation Checklist

Before adding a cache:

- What is the source of truth?
- Who is allowed to see this cached value?
- What invalidates it?
- How long can it be stale?
- Does it affect money, quota, privacy, or AI cost?
- Can Lovable deployment support the storage/cache layer being used?

