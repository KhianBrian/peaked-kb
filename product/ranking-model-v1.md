# Ranking Model V1

## Purpose

The first ranking model should make Peaked feel like a job-search command center, not a generic job board. The output should answer: "Which jobs are worth this user's time today?"

## Model Outputs

Each ranked job should produce:

- `match_score`: how well the user's evidence fits the role.
- `opportunity_score`: whether the job is worth pursuing now.
- `fit_summary`: short explanation of why it ranks where it does.
- `strengths`: evidence-backed user advantages.
- `gaps`: missing or weak requirements.
- `dealbreakers`: hard conflicts, if any.
- `tailoring_effort`: low, medium, or high.

## Ranking Inputs

User inputs:

- Target roles.
- Remote preference.
- Location and timezone.
- Salary target.
- Seniority.
- Industries.
- Dealbreakers.
- Work schedule preference.
- Career evidence from resume and user corrections.

Job inputs:

- Title.
- Company.
- Source.
- Apply URL.
- Location.
- Remote eligibility.
- Salary range.
- Employment type.
- Schedule/timezone.
- Seniority.
- Required skills.
- Preferred skills.
- Responsibilities.
- Qualifications.
- Posted date or freshness.

## Initial Scoring Weights

Suggested starting weights:

| Factor | Weight | Notes |
| --- | ---: | --- |
| Role/title fit | 15 | Match target roles and adjacent roles. |
| Required skill fit | 20 | Penalize missing hard requirements. |
| Career evidence fit | 20 | Use resume/projects/achievements, not just keywords. |
| Seniority fit | 10 | Avoid under/over-level mismatch. |
| Remote/location fit | 10 | Critical for Philippine and remote VA users. |
| Schedule/timezone fit | 8 | Important for VA/night-shift roles. |
| Salary fit | 7 | Use when available; avoid over-penalizing missing salary. |
| Freshness | 5 | Prefer recent roles. |
| Source trust/compliance | 5 | Prefer approved, stable, attributable sources. |

Total: 100

## Hard Filters

Hard filters should remove or sharply penalize jobs when:

- The job is not open to Philippines-based applicants and the user requires PH-eligible remote work.
- The location conflicts with the user's dealbreakers.
- The schedule conflicts with declared availability.
- Required language, certification, or legal work authorization is missing.
- The job source is not approved for display or storage.

## VA-Specific Signals

For remote VA roles, detect:

- Executive assistant/admin assistant language.
- Customer support, sales support, ecommerce support, lead generation, appointment setting, bookkeeping, and social media operations.
- Required tools: Google Workspace, Microsoft 365, Slack, Zoom, Notion, Airtable, Trello, Asana, HubSpot, Salesforce, Shopify, Zendesk, Gorgias, Canva, QuickBooks.
- Schedule details such as EST/PST/AEST, graveyard shift, part-time, full-time, flexible hours.
- Communication requirements such as C1/C2 English, phone support, written English, client-facing work.

## Explanation Rules

Every recommendation should explain itself with evidence:

- Cite user evidence in plain language.
- Mention exact job requirements where possible.
- Separate strengths from gaps.
- Avoid saying the user is qualified when hard requirements are missing.
- Avoid inventing user experience.

## AI Usage

V1 ranking should be deterministic first, AI-assisted second.

Recommended flow:

1. Normalize the job into structured fields.
2. Compare structured job fields to structured career evidence.
3. Compute deterministic scores.
4. Use AI only to generate explanation text or extract fields when needed.
5. Cache normalized jobs and explanations using user/session scope, input hash, prompt/model/schema version, and feature name.

## Verification

Use test fixtures before production:

- Strong match VA job.
- Weak match VA job.
- Remote job not open to PH applicants.
- Job missing salary.
- Job with hard certification requirement.
- Job with vague description.
- User with incomplete career evidence.

The ranking should be understandable and should not over-rank jobs because of keyword overlap alone.

