# 2026-05-09 - Scope Map And Audit System

## Summary

Added a Peaked scope map to the workspace guardrails so future agents can narrow reads for page, tab, UI, deployment, dependency, and AI tasks. Added the audit-entry system under `peaked-kb/audits`.

## Request

Scan the Peaked codebase and modify `AGENTS.md` so agents can quickly narrow scope for tasks like editing specific tabs/pages, saving token usage. Add an `audits` folder in `peaked-kb` with its own Markdown format instructions.

## Scope

- Project/folder: `AGENTS.md`, `peaked-kb`, and read-only inspection of `peaked-219edec1`.
- Primary files changed:
  - `AGENTS.md`
  - `peaked-kb/audits/AUDIT_ENTRY_FORMAT.md`
  - `peaked-kb/audits/2026-05-09-scope-map-and-audit-system.md`
- Related files inspected:
  - `peaked-219edec1/package.json`
  - `peaked-219edec1/vite.config.ts`
  - `peaked-219edec1/wrangler.jsonc`
  - `peaked-219edec1/src/routes/*`
  - `peaked-219edec1/src/components/layout/*`
  - `peaked-219edec1/src/components/onboarding-frame.tsx`
  - `peaked-219edec1/src/components/paywall-modal.tsx`
  - `peaked-219edec1/src/components/score-ring.tsx`
  - `peaked-219edec1/src/components/ui/glass-card.tsx`
  - `peaked-219edec1/src/lib/mock-data.ts`
  - `peaked-219edec1/src/styles.css`
  - `peaked-219edec1/src/router.tsx`
  - `peaked-219edec1/src/server.ts`
  - `peaked-219edec1/src/start.ts`

## Changes Made

- Added a `Peaked Scope Map` to `AGENTS.md` with targeted starting files for dashboard, jobs, resume, profile, auth, onboarding, navigation, UI animation, dependencies, deployment, AI, quotas, and caching.
- Added guidance to create audit entries after meaningful fixes, modifications, dependency changes, deployment changes, or debugging sessions.
- Created `peaked-kb/audits/AUDIT_ENTRY_FORMAT.md` with file naming, entry template, audit criteria, and writing rules.
- Created this first audit entry to establish the pattern.

## Decisions

- Kept the scope map in root `AGENTS.md` because all future agents should see it before scanning.
- Put detailed audit instructions inside `peaked-kb/audits` so the audit format can evolve without bloating root guardrails.
- Noted that `Sidebar.tsx` and `BottomNav.tsx` exist but are not currently mounted by `AppShell`, which should prevent agents from editing unused navigation first.

## Verification

- Inspected the current route, layout, style, data, server, config, and package files with shell reads.
- Verified the new files were created through the patch operation.
- No app build was run because this was a documentation and workflow update only.

## Follow-Ups

- Consider adding a compact `peaked-kb/codebase-map.md` if the app grows beyond the root scope map.
- Future code changes should add audit entries only when they meet the criteria in `AUDIT_ENTRY_FORMAT.md`.

## Lessons

- The current Peaked app is route-centric. For tab/page edits, starting with the route file plus `mock-data` and the relevant shared components should usually be enough.
- The active navigation is `TopNav`; `Sidebar` and `BottomNav` are present but not mounted in the current shell.
