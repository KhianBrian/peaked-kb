# Peaked Knowledge Base

This folder is the working memory for the Peaked application. Use it before planning, coding, debugging, adding dependencies, or touching AI/API behavior.

## Current App Context

- Main app folder: `../peaked-219edec1`
- Deployment surface: Lovable
- Primary developers: Codex and Claude
- Current stack signals:
  - TanStack Start and TanStack Router
  - Vite
  - React 19
  - TypeScript
  - Tailwind CSS 4
  - Radix UI primitives
  - TanStack Query
  - Cloudflare/Vite deployment tooling
  - Bun lockfile present

## Folder Map

### Foundation

- [foundation/architecture.md](foundation/architecture.md): system boundaries, routing, data flow, deployment assumptions.
- [foundation/database-architecture.md](foundation/database-architecture.md): database tables, roles, plans, AI usage, payments, jobs, and analytics architecture.

### Product

- [product/README.md](product/README.md): product docs index, current checkpoint, and phase folders.
- [product/roadmap/phases-of-development.md](product/roadmap/phases-of-development.md): phased development plan for the job-search command center MVP and full platform.
- [product/roadmap/future-plans.md](product/roadmap/future-plans.md): future partnerships, job-board outreach, browser extension research, and long-term job data strategy.

### Engineering

- [engineering/component-structure.md](engineering/component-structure.md): where UI belongs and how components should be shaped.
- [engineering/caching-strategy.md](engineering/caching-strategy.md): browser, query, server, AI, and CDN caching rules.
- [engineering/ai-api-usage-and-limits.md](engineering/ai-api-usage-and-limits.md): AI calls, user limits, safety, cost control, abuse protection.
- [engineering/dependency-and-lovable-safety.md](engineering/dependency-and-lovable-safety.md): version discipline and Lovable compatibility checks.
- [engineering/ui-animation-standards.md](engineering/ui-animation-standards.md): Peaked UI quality bar, motion rules, visual QA, and stack-compatible animation guidance.

### Operations

- [operations/senior-developer-mindset-and-debugging.md](operations/senior-developer-mindset-and-debugging.md): engineering posture and debugging process.
- [operations/traps-and-lessons.md](operations/traps-and-lessons.md): self-learning log for painful bugs and repeated mistakes.
- [operations/planning-expert.md](operations/planning-expert.md): phased planning format for new features.

### Checkpoints

- [checkpoints/current.md](checkpoints/current.md): compact resume point for the next session. Read this first when the user asks where development left off or asks to resume from a checkpoint.
- [checkpoints/README.md](checkpoints/README.md): checkpoint usage rules and format.

### Audits

- [audits/AUDIT_ENTRY_FORMAT.md](audits/AUDIT_ENTRY_FORMAT.md): format for optional audit entries. Create audits only when the user explicitly asks to log development or record changes.

## Default Agent Workflow

1. Identify the target feature, bug, or system area.
2. Read the relevant KB file.
3. Inspect the actual app code before making claims.
4. Make the smallest complete change that solves the request.
5. Run lint, typecheck, build, or targeted checks when available.
6. If debugging took several attempts, update [operations/traps-and-lessons.md](operations/traps-and-lessons.md).
7. For UI or animation work, use the `peaked-ui-director` Codex skill and read [engineering/ui-animation-standards.md](engineering/ui-animation-standards.md).
8. Do not add audit entries automatically. If the user asks to save a checkpoint, update [checkpoints/current.md](checkpoints/current.md) instead of writing a full audit.
