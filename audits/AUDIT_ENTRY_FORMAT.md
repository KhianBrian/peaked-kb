# Audit Entry Format

Use this format for audit entries after meaningful work in `peaked-219edec1` or project-governing changes in `peaked-kb`.

Audit entries are for continuity. They should help the next agent understand what changed, why it changed, how it was verified, and what to watch next without rereading the whole conversation.

## File Naming

Use this format:

```text
YYYY-MM-DD-short-kebab-title.md
```

Examples:

```text
2026-05-09-dashboard-motion-polish.md
2026-05-09-jobs-filter-fix.md
2026-05-09-ai-quota-ui-states.md
```

## Entry Template

```md
# YYYY-MM-DD - Short Title

## Summary

One to three sentences describing the change.

## Request

What did the user ask for?

## Scope

- Project/folder:
- Primary files changed:
- Related files inspected:

## Changes Made

- Concrete change 1.
- Concrete change 2.
- Concrete change 3.

## Decisions

- Decision and why it was made.
- Tradeoff or alternative considered, if relevant.

## Verification

- Command or browser check performed.
- Result.
- Any check that could not be run.

## Follow-Ups

- Remaining improvement, risk, or next step.
- Use `None` if there is nothing known.

## Lessons

- Add anything that should speed up future work.
- If debugging took multiple attempts, also update `../06-traps-and-lessons.md`.
```

## What Deserves An Audit

Add an audit when work includes:

- User-visible UI changes.
- Route/page behavior changes.
- Navigation or layout changes.
- Dependency additions, removals, or upgrades.
- Lovable, Vite, build, SSR, or deployment changes.
- AI API, quota, caching, or rate-limit behavior.
- Bug fixes that required investigation.
- KB, guardrail, or workflow changes that affect future agents.

Tiny typo fixes usually do not need an audit unless they clarify a critical workflow.

## Writing Rules

- Be brief but specific.
- Link or name exact files.
- Mention verification honestly.
- Do not paste long diffs.
- Do not include secrets, private user data, raw prompts, API keys, or sensitive logs.

