# 2026-05-10 - Product Docs Reorganization

## Summary

Reorganized scattered `peaked-kb/product` docs into purpose-based folders and added a product index so future work can resume from the right checkpoint faster.

## Request

The user asked where the project left off, to check the latest checkpoint, and to organize scattered product markdown files into folders depending on their use.

## Scope

- Project/folder: `peaked-kb`
- Primary files changed:
  - `product/README.md`
  - `product/current/resume-point.md`
  - `product/roadmap/*`
  - `product/phase-0-discovery/*`
  - `product/phase-1-backend/*`
  - `README.md`
- Related files inspected:
  - `audits/2026-05-10-phase-status-checkpoint.md`
  - `product/roadmap/phases-of-development.md`
  - `product/phase-1-backend/phase-1-backend-foundation-plan.md`

## Changes Made

- Moved current checkpoint material into `product/current`.
- Moved roadmap and future-looking docs into `product/roadmap`.
- Moved Phase 0 decision/research docs into `product/phase-0-discovery`.
- Moved Phase 1 backend, quota, and Supabase email docs into `product/phase-1-backend`.
- Added `product/README.md` as the entry point for product docs.
- Updated KB references to the new product doc paths.

## Decisions

- Kept `quota-and-entitlements-v1.md` in Phase 1 because it directly drives schema, RLS, and server-side quota enforcement work.
- Kept `supabase-auth-email-template.md` in Phase 1 because it is tied to the auth foundation already started.
- Did not change `peaked-219edec1`; this was a KB-only organization pass.

## Verification

- Ran `find peaked-kb/product -maxdepth 2 -type f | sort` to confirm the new folder layout.
- Ran `rg` for old product paths and found no remaining stale references.
- No app build was run because this was documentation-only.

## Follow-Ups

- Resume Phase 1 with Supabase database migration/RLS/profile creation work.

## Lessons

- For KB reorgs, avoid parallel `git mv` commands because Git serializes writes through `.git/index.lock`.
