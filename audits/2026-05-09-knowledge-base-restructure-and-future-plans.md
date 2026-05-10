# 2026-05-09 - Knowledge Base Restructure And Future Plans

## Summary

Reorganized the Peaked knowledge base from numbered root Markdown files into named folders. Updated the development roadmap around the job-search command center MVP and added future plans for job-board outreach and partnerships.

## Request

Remove numbered KB file names, sort the docs into folders, rename the roadmap into phases of development, update the development phases with the new MVP and platform goal, and add a new future plans file for pitching/reaching out to large job sites.

## Scope

- Project/folder: `peaked-kb`
- Primary files changed:
  - `README.md`
  - `foundation/architecture.md`
  - `foundation/database-architecture.md`
  - `product/roadmap/phases-of-development.md`
  - `product/roadmap/future-plans.md`
  - `engineering/*`
  - `operations/*`
  - `audits/AUDIT_ENTRY_FORMAT.md`
  - `audits/2026-05-09-knowledge-base-restructure-and-future-plans.md`
- Related files inspected:
  - Previous root-level numbered KB files.
  - Current roadmap and database architecture docs.

## Changes Made

- Moved architecture/database docs into `foundation`.
- Moved phases and future product strategy into `product`.
- Moved component, caching, AI, dependency, and UI animation docs into `engineering`.
- Moved debugging, traps, and planning docs into `operations`.
- Rewrote the README with the new folder map.
- Reframed the MVP as a job-search command center: discover, rank, tailor, apply, prepare, track, learn.
- Added job-board outreach and partnership planning in `product/roadmap/future-plans.md`.

## Decisions

- Removed numeric prefixes to make the KB easier to browse by purpose.
- Kept audits in their existing `audits` folder because the audit workflow was already clear.
- Kept historical audit entries even if they mention previous file paths, because they reflect the state at the time of the audit.

## Verification

- Listed the new file tree.
- Searched for stale numbered-doc references.
- Updated active README and workflow-doc references to the new paths.

## Follow-Ups

- Commit and push the KB after review.

## Lessons

- Product strategy docs should live under `product`, while implementation rules should live under `engineering`; this makes the KB easier to scan before work.
