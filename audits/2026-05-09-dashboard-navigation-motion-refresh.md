# 2026-05-09 - Dashboard Navigation Motion Refresh

## Summary

Documented the current pending Peaked app changes before pushing: Motion was added, the shell moved from sidebar/bottom navigation to a top navigation, and the dashboard was redesigned with animated cards and denser action surfaces.

## Request

Push all current changes to GitHub in their respective repositories.

## Scope

- Project/folder: `peaked-219edec1`
- Primary files changed:
  - `package.json`
  - `bun.lock`
  - `src/components/layout/AppShell.tsx`
  - `src/components/layout/TopNav.tsx`
  - `src/routes/index.tsx`
  - `src/routeTree.gen.ts`
  - `public/peaked-logo.ico`
- Related files inspected:
  - `src/components/layout/Sidebar.tsx`
  - `src/components/layout/BottomNav.tsx`
  - `src/lib/mock-data.ts`
  - `src/components/score-ring.tsx`
  - `src/components/ui/glass-card.tsx`

## Changes Made

- Added `motion` as a dependency and updated the Bun lockfile.
- Replaced the mounted sidebar/bottom navigation shell with a sticky animated top navigation.
- Added page transition animation in `AppShell` with reduced-motion handling.
- Redesigned the dashboard route with animated readiness, weekly signal, match board, quick actions, and activity sections.
- Added the Peaked logo asset used by `TopNav`.
- Let the generated route tree include TanStack Start registration metadata.

## Decisions

- Motion is appropriate for this UI work because the app already targets React 19/Vite and the KB standard prefers `motion/react` for meaningful layout and page transitions.
- `Sidebar.tsx` and `BottomNav.tsx` were left in the codebase but are no longer mounted by `AppShell`.
- The app changes were treated as existing pending work to publish, not as new edits in this audit pass.

## Verification

- Reviewed the pending git diff and inspected the new `TopNav` file.
- Build was not run because `bun` is not currently available in the shell.
- GitHub CLI is also not installed, so this push flow will use plain `git` rather than creating PRs with `gh`.

## Follow-Ups

- Install Bun locally and run `bun install` plus `bun run build` before a production deployment.
- Consider removing unused `Sidebar` and `BottomNav` later if the top navigation is final.
- Visually verify the dashboard and navigation in desktop and mobile preview after dependencies are installed.

## Lessons

- When pushing existing UI work, capture an audit before publishing so future agents know which changes were already present and what still needs verification.
