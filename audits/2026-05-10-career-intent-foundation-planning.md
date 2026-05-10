# 2026-05-10 - Career Intent Foundation Planning

## Summary

Updated the product roadmap and Phase 1 backend plan to explicitly include career intent/preferences as a core intelligence input for Peaked.

## Request

The user confirmed Peaked is a job-search command center and suggested collecting information about what users want or plan for their careers so AI has a better foundation.

## Scope

- Project/folder: `peaked-kb`
- Primary files changed:
  - `product/roadmap/phases-of-development.md`
  - `product/phase-1-backend/phase-1-backend-foundation-plan.md`
  - `product/current/resume-point.md`
- Related files inspected:
  - `product/phase-1-backend/phase-1-backend-foundation-plan.md`
  - `product/roadmap/phases-of-development.md`

## Changes Made

- Added a four-layer intelligence model: account foundation, career intent, career evidence, and outcome feedback.
- Added career intent/preferences to Phase 1 as a backend foundation concern.
- Proposed a `career_intents` table for target roles, industries, seniority, locations, remote preference, salary range, schedule preference, dealbreakers, work authorization, and notes.
- Added a Phase 1 implementation slice for career intent storage and first editable UI.
- Updated the resume point so future work knows career intent should come before AI recommendations.

## Decisions

- Career intent belongs in Phase 1 as data foundation, while AI-powered recommendations remain a later phase.
- Peaked should not infer desired career direction from resume history alone.

## Verification

- Documentation-only change; no build was run.

## Follow-Ups

- Add `career_intents` to the Supabase migration or a follow-up migration.
- Build the first career intent/profile preferences UI after real Profile usage data is wired.

## Lessons

- A job-search command center needs explicit user intent in addition to resume evidence; otherwise AI recommendations risk optimizing for the user's past rather than their desired next move.
