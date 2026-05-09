# Future Plans

## Purpose

This file captures product expansion ideas that should not block the MVP but should shape the platform architecture.

## Job Board Outreach And Partnerships

Peaked's long-term job coverage should come from approved channels, not unauthorized scraping.

Target categories:

- Job boards with official APIs.
- Remote job boards with public feeds.
- Local/regional job boards.
- Company ATS providers.
- Data providers and aggregators.
- Niche job communities.

Potential targets:

- OnlineJobs.ph
- Indeed
- LinkedIn
- JobStreet
- Kalibrr
- RemoteOK
- Remotive
- We Work Remotely
- Greenhouse-hosted career pages
- Lever-hosted career pages
- Ashby-hosted career pages

## Partnership Pitch

Peaked should pitch itself as a qualified applicant engine, not a replacement marketplace.

Core message:

```text
Peaked helps job seekers find roles they are genuinely matched for, improves their application quality, and redirects applications back to the original job post.
```

Value for job boards:

- Better-qualified applicants.
- More intentional applications.
- Traffic back to original job posts.
- Better user experience for job seekers.
- Optional attribution and partner branding.
- Potential affiliate or revenue-share model.

What Peaked should request:

- API access.
- Feed access.
- Affiliate/referral terms.
- Permission to index selected listings.
- Branding/attribution requirements.
- Rate limits and data retention rules.
- Rules for storing job descriptions.
- Rules for displaying apply links.

## Outreach Email Skeleton

```text
Subject: Partnership request: qualified applicant referrals for [Job Board]

Hi [Name],

I am building Peaked, a job-search command center that helps applicants identify roles they are genuinely matched for, tailor truthful application materials, and apply back through the original job listing.

We would like to explore approved access to [Job Board] listings through an API, feed, affiliate arrangement, or other partner-approved method.

Our goal is not to replace your marketplace. Peaked would preserve attribution, link users back to the original job post, and help send more qualified applicants to employers.

Could you point me to the right person or process for API/feed/partner access?

Thanks,
[Name]
```

## Source Compliance Requirements

For every source, record:

- Source name.
- Access method.
- Terms URL.
- Whether permission is explicit.
- Attribution requirements.
- Storage rules.
- Rate limits.
- Apply URL requirements.
- Contact person.
- Renewal or review date.

Do not add a source to production ingestion without this information.

## Browser Extension Research

A browser extension could let users analyze job pages they are already viewing.

Potential user flow:

```text
User opens a job page
-> clicks "Analyze with Peaked"
-> extension extracts visible job text
-> Peaked compares it to the user's resume
-> Peaked saves the job and suggests an application package
```

Why this matters:

- It supports jobs from many sites without server-side mass scraping.
- The user initiates the action.
- It complements approved APIs and partnerships.

Risks to research:

- Browser store policies.
- Site terms.
- User consent.
- Data privacy.
- Handling login-gated content.

## Long-Term Data Strategy

Start with:

- User-submitted job descriptions and URLs.
- One or two approved job APIs.
- Company ATS feeds where allowed.

Expand to:

- Partnerships.
- Affiliate feeds.
- Licensed providers.
- Browser extension.
- Email/job-alert importers.

Avoid:

- Unauthorized mass scraping.
- Circumventing access controls.
- Storing restricted job databases without permission.
- Presenting another platform's data as Peaked-owned content.

