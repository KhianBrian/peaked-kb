# 2026-05-09 - Supabase Env Setup

## Summary

Added Lovable/Vite-safe Supabase environment placeholders for Phase 1 backend setup and tightened env file ignore rules so local secrets stay out of git.

## Request

The user asked to set up the `VITE_SUPABASE_*` values so they can paste their Supabase keys locally, then proceed with auth settings.

## Scope

- Project/folder: `peaked-219edec1`, `peaked-kb`
- Primary files changed:
  - `peaked-219edec1/.gitignore`
  - `peaked-219edec1/.env.example`
  - `peaked-219edec1/.env.local`
  - `peaked-kb/audits/2026-05-09-supabase-env-setup.md`
- Related files inspected:
  - `peaked-219edec1/package.json`

## Changes Made

- Added tracked `.env.example` with `VITE_SUPABASE_URL` and `VITE_SUPABASE_ANON_KEY`.
- Added local `.env.local` placeholders for the user's real Supabase values.
- Updated `.gitignore` to ignore `.env` and `.env.*` while allowing `.env.example`.

## Decisions

- Use `.env.local` for real local values because it is ignored and works with Vite.
- Only browser-safe Supabase values are included; service-role keys must not be placed in client env files.

## Verification

- Confirmed the project had no prior env files.
- No build was run because no runtime code changed.

## Follow-Ups

- User should paste Supabase Project URL and anon/publishable key into `.env.local`.
- Configure Supabase Auth email provider and URL settings.
- Install Supabase client and implement auth/profile wiring in the next Phase 1 slice.

## Lessons

- The repo previously ignored `*.local` but not plain `.env`; explicit env ignore rules reduce accidental secret exposure.
