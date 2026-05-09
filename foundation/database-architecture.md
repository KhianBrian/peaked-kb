# Database Architecture

## Design Goals

The database must support:

- Admin, free user, and dedicated user access.
- Resume upload, parsing, versions, and generated resume artifacts.
- Job discovery, saved jobs, application tracking, and match scoring.
- Cover letter generation.
- Interview preparation questions.
- AI task tracking, usage quotas, caching, and cost visibility.
- Payments, subscriptions, webhooks, and entitlement changes.
- Dashboard analytics.
- Admin audits and operational support.

Use Postgres unless a future backend decision explicitly chooses otherwise.

## Role And Plan Model

Recommended model:

- `profiles.role`: controls authority.
- `profiles.plan_tier`: controls product access.

Enums:

```sql
account_role = 'user' | 'admin'
plan_tier = 'free' | 'dedicated'
subscription_status = 'none' | 'trialing' | 'active' | 'past_due' | 'cancelled' | 'expired'
```

Why not only one `role` enum?

- Admin authority and paid entitlement are different concepts.
- An admin might test free/dedicated behavior.
- A dedicated user should not receive admin permissions.

Website language:

- Show `Free user` for `plan_tier = free`.
- Show `Dedicated user` for `plan_tier = dedicated`.
- Show `Admin` only in internal/admin surfaces.

## Core Tables

### profiles

Stores app-level user identity.

Columns:

- `id uuid primary key`
- `auth_user_id uuid unique not null`
- `email text not null`
- `full_name text`
- `avatar_url text`
- `role account_role not null default 'user'`
- `plan_tier plan_tier not null default 'free'`
- `subscription_status subscription_status not null default 'none'`
- `timezone text`
- `onboarding_completed_at timestamptz`
- `created_at timestamptz not null default now()`
- `updated_at timestamptz not null default now()`
- `deleted_at timestamptz`

Indexes:

- unique `auth_user_id`
- unique lower email if auth provider does not guarantee it
- `role`
- `plan_tier`

### plans

Stores feature limits by tier.

Columns:

- `id uuid primary key`
- `tier plan_tier unique not null`
- `display_name text not null`
- `monthly_price_cents integer`
- `currency text not null default 'PHP'`
- `daily_ai_limit integer not null`
- `monthly_ai_limit integer not null`
- `resume_upload_limit integer not null`
- `job_match_limit integer not null`
- `cover_letter_limit integer not null`
- `interview_prep_limit integer not null`
- `is_active boolean not null default true`
- `created_at timestamptz not null default now()`
- `updated_at timestamptz not null default now()`

### usage_counters

Fast quota lookup by user and period.

Columns:

- `id uuid primary key`
- `profile_id uuid not null references profiles(id)`
- `feature text not null`
- `period text not null`
- `period_start timestamptz not null`
- `period_end timestamptz not null`
- `used integer not null default 0`
- `limit_value integer not null`
- `created_at timestamptz not null default now()`
- `updated_at timestamptz not null default now()`

Indexes:

- unique `(profile_id, feature, period, period_start)`
- `(profile_id, period_end)`

### usage_events

Immutable usage ledger.

Columns:

- `id uuid primary key`
- `profile_id uuid not null references profiles(id)`
- `feature text not null`
- `amount integer not null default 1`
- `source_type text`
- `source_id uuid`
- `metadata jsonb not null default '{}'`
- `created_at timestamptz not null default now()`

Indexes:

- `(profile_id, created_at desc)`
- `(feature, created_at desc)`

## Resume Tables

### resume_files

Uploaded original files.

Columns:

- `id uuid primary key`
- `profile_id uuid not null references profiles(id)`
- `storage_path text not null`
- `original_filename text not null`
- `mime_type text not null`
- `file_size_bytes integer not null`
- `sha256 text not null`
- `status text not null`
- `created_at timestamptz not null default now()`
- `deleted_at timestamptz`

Indexes:

- `(profile_id, created_at desc)`
- `(profile_id, sha256)`

### resumes

Logical resume entity.

Columns:

- `id uuid primary key`
- `profile_id uuid not null references profiles(id)`
- `current_version_id uuid`
- `label text`
- `created_at timestamptz not null default now()`
- `updated_at timestamptz not null default now()`
- `deleted_at timestamptz`

### resume_versions

Parsed and generated resume versions.

Columns:

- `id uuid primary key`
- `resume_id uuid not null references resumes(id)`
- `source_file_id uuid references resume_files(id)`
- `version_type text not null`
- `title text`
- `raw_text text`
- `structured_data jsonb not null default '{}'`
- `ats_score integer`
- `ats_breakdown jsonb not null default '{}'`
- `prompt_version text`
- `model_name text`
- `created_by_task_id uuid`
- `created_at timestamptz not null default now()`

Version types:

- `uploaded_parse`
- `manual_edit`
- `ai_tailored`
- `exported`

Indexes:

- `(resume_id, created_at desc)`
- `(created_by_task_id)`

### resume_parse_jobs

Tracks parsing status.

Columns:

- `id uuid primary key`
- `profile_id uuid not null references profiles(id)`
- `resume_file_id uuid not null references resume_files(id)`
- `status text not null`
- `error_code text`
- `error_message text`
- `started_at timestamptz`
- `finished_at timestamptz`
- `created_at timestamptz not null default now()`

## Job Tables

### job_sources

Supported job data providers.

Columns:

- `id uuid primary key`
- `name text not null`
- `source_type text not null`
- `base_url text`
- `access_mode text not null`
- `terms_reviewed_at timestamptz`
- `is_enabled boolean not null default false`
- `notes text`
- `created_at timestamptz not null default now()`
- `updated_at timestamptz not null default now()`

Access modes:

- `official_api`
- `partner_feed`
- `licensed_provider`
- `user_submitted_url`
- `manual`
- `scraping_requires_review`

### job_ingestion_runs

Tracks source syncs.

Columns:

- `id uuid primary key`
- `job_source_id uuid not null references job_sources(id)`
- `status text not null`
- `query jsonb not null default '{}'`
- `records_found integer not null default 0`
- `records_created integer not null default 0`
- `records_updated integer not null default 0`
- `error_message text`
- `started_at timestamptz not null default now()`
- `finished_at timestamptz`

### jobs

Canonical jobs displayed in the app.

Columns:

- `id uuid primary key`
- `job_source_id uuid references job_sources(id)`
- `external_id text`
- `canonical_url text not null`
- `apply_url text not null`
- `title text not null`
- `company_name text`
- `location text`
- `employment_type text`
- `remote_type text`
- `salary_text text`
- `description text`
- `skills jsonb not null default '[]'`
- `posted_at timestamptz`
- `expires_at timestamptz`
- `first_seen_at timestamptz not null default now()`
- `last_seen_at timestamptz not null default now()`
- `is_active boolean not null default true`
- `metadata jsonb not null default '{}'`

Indexes:

- `(job_source_id, external_id)`
- `(title)`
- `(company_name)`
- `(posted_at desc)`
- `(is_active, last_seen_at desc)`

### user_job_matches

Resume-to-job scoring.

Columns:

- `id uuid primary key`
- `profile_id uuid not null references profiles(id)`
- `resume_version_id uuid not null references resume_versions(id)`
- `job_id uuid not null references jobs(id)`
- `match_score integer not null`
- `strengths jsonb not null default '[]'`
- `gaps jsonb not null default '[]'`
- `keywords_found jsonb not null default '[]'`
- `keywords_missing jsonb not null default '[]'`
- `recommendation text`
- `created_by_task_id uuid`
- `created_at timestamptz not null default now()`

Indexes:

- unique `(profile_id, resume_version_id, job_id)`
- `(profile_id, match_score desc)`

### saved_jobs

Columns:

- `id uuid primary key`
- `profile_id uuid not null references profiles(id)`
- `job_id uuid not null references jobs(id)`
- `notes text`
- `created_at timestamptz not null default now()`

Indexes:

- unique `(profile_id, job_id)`

### applications

Tracks user application activity.

Columns:

- `id uuid primary key`
- `profile_id uuid not null references profiles(id)`
- `job_id uuid not null references jobs(id)`
- `resume_version_id uuid references resume_versions(id)`
- `cover_letter_id uuid`
- `status text not null default 'interested'`
- `applied_at timestamptz`
- `external_apply_url text`
- `notes text`
- `created_at timestamptz not null default now()`
- `updated_at timestamptz not null default now()`

Statuses:

- `interested`
- `saved`
- `applied`
- `interviewing`
- `offer`
- `rejected`
- `archived`

## Generated Content Tables

### cover_letters

Columns:

- `id uuid primary key`
- `profile_id uuid not null references profiles(id)`
- `job_id uuid not null references jobs(id)`
- `resume_version_id uuid not null references resume_versions(id)`
- `title text`
- `content text not null`
- `tone text`
- `prompt_version text`
- `model_name text`
- `created_by_task_id uuid`
- `created_at timestamptz not null default now()`

### interview_prep_sets

Columns:

- `id uuid primary key`
- `profile_id uuid not null references profiles(id)`
- `job_id uuid not null references jobs(id)`
- `resume_version_id uuid references resume_versions(id)`
- `title text not null`
- `created_by_task_id uuid`
- `created_at timestamptz not null default now()`

### interview_questions

Columns:

- `id uuid primary key`
- `prep_set_id uuid not null references interview_prep_sets(id)`
- `question text not null`
- `category text`
- `difficulty text`
- `suggested_answer_outline text`
- `resume_evidence jsonb not null default '[]'`
- `sort_order integer not null default 0`

## AI Tables

### ai_tasks

Tracks every AI workflow.

Columns:

- `id uuid primary key`
- `profile_id uuid references profiles(id)`
- `feature text not null`
- `status text not null`
- `idempotency_key text`
- `input_hash text not null`
- `prompt_version text not null`
- `model_name text not null`
- `schema_version text not null`
- `source_type text`
- `source_id uuid`
- `result_type text`
- `result_id uuid`
- `error_code text`
- `error_message text`
- `started_at timestamptz`
- `finished_at timestamptz`
- `created_at timestamptz not null default now()`

Indexes:

- unique `(profile_id, feature, idempotency_key)` where `idempotency_key` is not null
- `(profile_id, created_at desc)`
- `(feature, status, created_at desc)`
- `(input_hash, prompt_version, model_name, schema_version)`

### ai_usage_events

Token and cost ledger.

Columns:

- `id uuid primary key`
- `ai_task_id uuid not null references ai_tasks(id)`
- `profile_id uuid references profiles(id)`
- `provider text not null`
- `model_name text not null`
- `input_tokens integer`
- `output_tokens integer`
- `estimated_cost_cents integer`
- `cache_hit boolean not null default false`
- `created_at timestamptz not null default now()`

### ai_result_cache

Reusable outputs.

Columns:

- `id uuid primary key`
- `feature text not null`
- `input_hash text not null`
- `prompt_version text not null`
- `model_name text not null`
- `schema_version text not null`
- `result jsonb not null`
- `expires_at timestamptz`
- `created_at timestamptz not null default now()`

Indexes:

- unique `(feature, input_hash, prompt_version, model_name, schema_version)`
- `(expires_at)`

## Payment Tables

### payment_customers

Columns:

- `id uuid primary key`
- `profile_id uuid not null references profiles(id)`
- `provider text not null`
- `provider_customer_id text`
- `metadata jsonb not null default '{}'`
- `created_at timestamptz not null default now()`
- `updated_at timestamptz not null default now()`

Indexes:

- unique `(provider, provider_customer_id)`
- `(profile_id)`

### subscriptions

Columns:

- `id uuid primary key`
- `profile_id uuid not null references profiles(id)`
- `plan_id uuid not null references plans(id)`
- `provider text not null`
- `provider_subscription_id text`
- `status subscription_status not null`
- `current_period_start timestamptz`
- `current_period_end timestamptz`
- `cancel_at_period_end boolean not null default false`
- `created_at timestamptz not null default now()`
- `updated_at timestamptz not null default now()`

Indexes:

- `(profile_id, status)`
- unique `(provider, provider_subscription_id)` where `provider_subscription_id` is not null

### payment_intents

Columns:

- `id uuid primary key`
- `profile_id uuid not null references profiles(id)`
- `provider text not null`
- `provider_intent_id text`
- `amount_cents integer not null`
- `currency text not null default 'PHP'`
- `status text not null`
- `checkout_url text`
- `idempotency_key text not null`
- `metadata jsonb not null default '{}'`
- `created_at timestamptz not null default now()`
- `updated_at timestamptz not null default now()`

Indexes:

- unique `(provider, provider_intent_id)` where `provider_intent_id` is not null
- unique `(profile_id, idempotency_key)`

### invoices

Columns:

- `id uuid primary key`
- `profile_id uuid not null references profiles(id)`
- `subscription_id uuid references subscriptions(id)`
- `provider text not null`
- `provider_invoice_id text`
- `amount_cents integer not null`
- `currency text not null default 'PHP'`
- `status text not null`
- `paid_at timestamptz`
- `created_at timestamptz not null default now()`

### payment_webhook_events

Store every webhook exactly once.

Columns:

- `id uuid primary key`
- `provider text not null`
- `provider_event_id text not null`
- `event_type text not null`
- `payload jsonb not null`
- `signature_valid boolean not null default false`
- `processed_at timestamptz`
- `processing_error text`
- `created_at timestamptz not null default now()`

Indexes:

- unique `(provider, provider_event_id)`
- `(event_type, created_at desc)`

## Analytics And Admin Tables

### activity_events

User-visible timeline and analytics source.

Columns:

- `id uuid primary key`
- `profile_id uuid not null references profiles(id)`
- `event_type text not null`
- `entity_type text`
- `entity_id uuid`
- `summary text not null`
- `metadata jsonb not null default '{}'`
- `created_at timestamptz not null default now()`

Indexes:

- `(profile_id, created_at desc)`
- `(event_type, created_at desc)`

### dashboard_snapshots

Optional precomputed dashboard metrics.

Columns:

- `id uuid primary key`
- `profile_id uuid not null references profiles(id)`
- `period text not null`
- `period_start timestamptz not null`
- `period_end timestamptz not null`
- `metrics jsonb not null`
- `created_at timestamptz not null default now()`

Indexes:

- unique `(profile_id, period, period_start)`

### admin_audit_logs

Tracks admin actions.

Columns:

- `id uuid primary key`
- `admin_profile_id uuid not null references profiles(id)`
- `action text not null`
- `target_type text`
- `target_id uuid`
- `reason text`
- `metadata jsonb not null default '{}'`
- `created_at timestamptz not null default now()`

Indexes:

- `(admin_profile_id, created_at desc)`
- `(target_type, target_id)`

## Storage Buckets

Recommended private buckets:

- `resume-originals`
- `resume-generated`
- `cover-letters`
- `exports`

Rules:

- Users can access only their own files through signed URLs or server-mediated downloads.
- Admin access should be explicit and logged.
- Original resumes and generated documents need deletion support.

## Row-Level Security Policy Direction

If using Supabase/Lovable Cloud:

- Users can select/update their own `profiles` row except protected fields.
- Users cannot change their own `role`, `plan_tier`, or `subscription_status`.
- Users can access only their own resumes, jobs saved, applications, generated content, usage events, and AI tasks.
- Admins can read operational data through admin-only policies or server routes.
- Payment webhook writes should use a service role/server-only path.
- AI task writes should use a server-only path.

## Core Query Patterns

Dashboard:

- latest resume score
- saved job count
- application count by status
- AI usage for current day/month
- top job matches
- recent activity events

Jobs:

- active jobs filtered by source, location, remote type, title, skills
- user's saved jobs
- user's match scores for selected resume

Resume:

- latest resume version
- parse status
- generated tailored versions
- score trend over versions

Admin:

- users by plan/status
- AI cost by day/provider/feature
- payment failures
- job ingestion failures
- quota-heavy users

## Migration Order

1. Enums and profiles.
2. Plans and usage.
3. Resume files, resumes, versions, parse jobs.
4. Job sources, jobs, matches, saved jobs, applications.
5. AI tasks, usage events, result cache.
6. Cover letters and interview prep.
7. Payment customers, intents, subscriptions, invoices, webhook events.
8. Activity events, dashboard snapshots, admin audit logs.
9. RLS policies and indexes.

