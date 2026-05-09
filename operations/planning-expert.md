# Planning Expert

## Purpose

Every meaningful feature should be broken into phases so development flows cleanly, risks are visible early, and Lovable deployment remains predictable.

Use this file when adding features, changing architecture, introducing AI behavior, or touching cross-cutting systems.

## Planning Principles

- Start with the user outcome.
- Define the smallest complete version first.
- Separate foundation, UI, API, data, AI, and deployment concerns.
- Identify risky assumptions before coding.
- Keep each phase shippable or at least verifiable.
- Avoid adding dependencies during planning unless truly needed.

## Feature Plan Template

```md
# Feature: Name

## Outcome

What should the user be able to do?

## Non-Goals

What is intentionally not included?

## Existing Code To Inspect

- `src/routes/...`
- `src/components/...`
- `src/lib/...`

## Phase 0 - Discovery

- Confirm current stack and route/component ownership.
- Confirm data source and API needs.
- Confirm AI, quota, cache, or billing impact.
- Confirm Lovable deployment constraints.

## Phase 1 - Foundation

- Types and schemas.
- Internal API shape.
- Storage or state model.
- Feature flags if needed.

## Phase 2 - UI Flow

- Route/page shell.
- Components.
- Loading, empty, error, and success states.
- Responsive behavior.

## Phase 3 - Data And Integrations

- Server calls.
- External APIs.
- Cache behavior.
- Auth and authorization.

## Phase 4 - AI Behavior

- Prompt/template.
- Input validation.
- Quota checks.
- Output schema validation.
- Usage logging.
- Failure handling.

## Phase 5 - Verification

- Lint/typecheck/build.
- Manual flow test.
- Edge cases.
- Lovable compatibility check.

## Phase 6 - Follow-Up

- Known gaps.
- Metrics to watch.
- Documentation updates.
```

## Phase Sizing

Prefer small phases:

- 1 to 3 files for simple UI changes.
- 3 to 8 files for moderate feature additions.
- Separate PRs or sessions for auth, billing, AI provider integration, or persistence changes.

If a phase touches routing, server code, AI providers, cache, and billing all at once, split it.

## AI Feature Planning Additions

For AI features, include:

- Which user action triggers the AI call.
- What input is sent.
- What input is never sent.
- Which provider/model is used.
- Expected cost per run.
- User-visible limit.
- Abuse protection.
- Cache and regeneration rules.
- Output schema.
- Fallback behavior.

## Planning Review Checklist

Before implementation starts:

- Is there a clear MVP?
- Are non-goals explicit?
- Are risky parts front-loaded?
- Is there a verification plan?
- Are dependencies justified?
- Are quota and caching rules defined for AI features?
- Can this run in Lovable?

