# Job Source Matrix

## Purpose

Peaked should help Filipino job seekers find Philippine and remote VA opportunities without relying on unauthorized scraping. This matrix tracks which sources are suitable for MVP ingestion, manual user submission, partnership outreach, or future research.

## Source Policy

Production job ingestion requires:

- Source name.
- Access method.
- Terms or documentation URL.
- Permission status.
- Attribution requirements.
- Storage rules.
- Rate limits.
- Apply URL requirements.
- Review date.

Do not ingest from platforms where access depends on bypassing controls, mass scraping, or unclear reuse rights.

## MVP Source Ranking

| Priority | Source | Access method | MVP use | Notes |
| --- | --- | --- | --- | --- |
| 1 | User-submitted job URLs/descriptions | User initiated | Yes | Lowest legal risk; user provides the job they want analyzed. |
| 2 | Approved remote job APIs/feeds | Official API/feed | Yes, after terms review | Good for remote roles and early ranking tests. |
| 3 | Company ATS feeds | Public/allowed company feeds | Yes, after terms review | Useful for direct apply links and source attribution. |
| 4 | Philippine job-board partnerships | Partner/API/feed | Later | Important for local relevance, but requires outreach. |
| 5 | Browser extension | User-initiated page analysis | Later research | Lets users analyze pages they are already viewing without server-side mass scraping. |

## Candidate Sources

| Source | Market fit | Access status | Recommended Phase | Decision |
| --- | --- | --- | --- | --- |
| User-submitted job description | Excellent | Internal feature | Phase 3 | Include. |
| User-submitted job URL | Excellent | Internal feature | Phase 3 | Include with URL normalization and source attribution. |
| Remotive | Good for remote jobs | Needs terms/API review | Phase 3 candidate | Research. |
| RemoteOK | Good for remote jobs | Needs terms/API review | Phase 3 candidate | Research carefully before storing descriptions. |
| Arbeitnow | Good for remote/global jobs | Needs terms/API review | Phase 3 candidate | Research. |
| Adzuna | Broad job API | Needs API/account/terms review | Phase 3 candidate | Research. |
| USAJOBS | Official API, US-focused | Lower PH/VA fit | Optional later | Not first priority unless users target US public-sector roles. |
| Greenhouse-hosted career pages | Company-specific | Terms vary | Phase 3 or later | Use only where allowed. |
| Lever-hosted career pages | Company-specific | Terms vary | Phase 3 or later | Use only where allowed. |
| Ashby-hosted career pages | Company-specific | Terms vary | Phase 3 or later | Use only where allowed. |
| OnlineJobs.ph | Excellent market fit | Partnership/permission needed | Later | Do not scrape. Outreach target. |
| JobStreet | Strong PH fit | Partnership/permission needed | Later | Do not scrape. Outreach target. |
| Kalibrr | Strong PH fit | Partnership/permission needed | Later | Do not scrape. Outreach target. |
| LinkedIn | Broad | Restricted platform | Later/extension research only | Do not scrape. |
| Indeed | Broad | Restricted platform | Later/partnership only | Do not scrape. |

## User-Submitted Job Flow

Phase 3 should support:

1. User pastes a job URL or job description.
2. Peaked stores the source URL and raw submitted text under the user's account.
3. Server validates size and content type.
4. Server normalizes the job into the shared job schema.
5. Ranking runs against the user's career evidence and goals.
6. Peaked links the user back to the original apply URL.

Rules:

- Treat submitted job content as user-provided data.
- Do not claim ownership of external job content.
- Store source attribution.
- Do not use user-submitted content as a backdoor to build a restricted job database.

## Philippine And Remote VA Focus

Ranking and filters should prioritize:

- Remote jobs open to Philippines-based applicants.
- VA, executive assistant, admin support, customer support, operations, sales support, lead generation, appointment setting, bookkeeping, ecommerce support, and social media support roles.
- Night-shift, timezone, salary, and work-hours compatibility.
- Payment arrangement clarity when available.
- English communication requirements.
- Required tools such as Google Workspace, Slack, HubSpot, Shopify, QuickBooks, Canva, Notion, Airtable, Zendesk, Gorgias, and CRMs.

## Compliance Checklist Per Source

Before enabling a source:

- Terms reviewed.
- Permission model documented.
- Apply URL preserved.
- Attribution rules documented.
- Storage rules documented.
- Rate limits documented.
- Data refresh/delete rules documented.
- Contact or support path recorded.
- Source can be disabled with a feature flag or config switch.

## Open Research Items

- Which remote job API has the best overlap with VA work for Filipinos?
- Whether any Philippine boards offer startup-friendly API, affiliate, or partner access.
- Whether OnlineJobs.ph, JobStreet, or Kalibrr have realistic partnership channels.
- Whether browser-extension-assisted, user-initiated analysis is acceptable under browser store policies and target-site terms.

