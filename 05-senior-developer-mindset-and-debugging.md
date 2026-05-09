# Senior Developer Mindset And Debugging

## Mindset

Senior development is not about writing the most code. It is about reducing uncertainty, protecting the product, and making changes that future developers can understand.

Default posture:

- Read before editing.
- Confirm the actual stack before installing or changing tooling.
- Prefer boring, compatible solutions.
- Make changes that are small enough to review.
- Keep user value in sight.
- Treat deployment and operations as part of the feature.
- Leave the codebase easier to reason about than you found it.

## Before Coding

Ask:

- What user problem is this solving?
- What existing pattern already handles something similar?
- What files own this behavior?
- What could break?
- How will I verify the change?
- Does this affect Lovable deployment?
- Does this affect AI cost, privacy, quotas, or caching?

## Good Change Shape

A good change usually has:

- One clear purpose.
- Minimal blast radius.
- Type-safe boundaries.
- Good loading/error states.
- No unnecessary dependency additions.
- Tests or manual verification proportional to risk.
- Documentation updates when behavior or architecture changes.

## Debugging Process

Use this order:

1. Reproduce the issue.
2. Identify the expected behavior.
3. Identify the actual behavior.
4. Read the relevant code path.
5. Check recent changes and generated files.
6. Form one hypothesis at a time.
7. Test the hypothesis with the smallest useful check.
8. Fix the root cause.
9. Verify the fix.
10. Add a lesson to `06-traps-and-lessons.md` if the debugging path was long or surprising.

## Debugging Questions

- Is this client, server, build, routing, styling, or deployment?
- Is it data shape, timing, auth, cache, environment, or dependency version?
- Does it reproduce locally?
- Does it only happen after build?
- Does it only happen in Lovable?
- Is a generated file stale?
- Is server-only code leaking into the client?
- Is browser-only code running during SSR?
- Is a duplicate Vite plugin or framework plugin being added?

## Logging

Good logs are:

- Narrow.
- Temporary unless intentionally useful.
- Free of secrets and private user content.
- Removed or downgraded before finishing.

For AI flows, log:

- Feature name.
- User/session identifier.
- Provider/model.
- Token/cost estimate.
- Cache hit/miss.
- Quota decision.
- Duration.
- Safe error category.

Do not log raw resumes, full prompts, API keys, or provider secrets.

## Verification

Use the strongest practical check:

- Typecheck for type and import issues.
- Lint for code quality and rules.
- Build for bundling, SSR, and deployment problems.
- Browser check for visual and interaction issues.
- Targeted manual test for product flow.

If a command is unavailable, document that gap and use the next best check.

## When To Stop And Ask

Ask the user before proceeding when:

- The change affects billing, subscriptions, or hard quota numbers.
- A dependency upgrade has meaningful compatibility risk.
- There are conflicting product interpretations.
- Fixing the issue requires changing project architecture.
- User data privacy expectations are unclear.

