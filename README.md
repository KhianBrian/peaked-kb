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

## How To Use This KB

Read the relevant file before starting work:

- [01-architecture.md](01-architecture.md): system boundaries, routing, data flow, deployment assumptions.
- [02-component-structure.md](02-component-structure.md): where UI belongs and how components should be shaped.
- [03-caching-strategy.md](03-caching-strategy.md): browser, query, server, AI, and CDN caching rules.
- [04-ai-api-usage-and-limits.md](04-ai-api-usage-and-limits.md): AI calls, user limits, safety, cost control, abuse protection.
- [05-senior-developer-mindset-and-debugging.md](05-senior-developer-mindset-and-debugging.md): engineering posture and debugging process.
- [06-traps-and-lessons.md](06-traps-and-lessons.md): self-learning log for painful bugs and repeated mistakes.
- [07-planning-expert.md](07-planning-expert.md): phased planning format for new features.
- [08-dependency-and-lovable-safety.md](08-dependency-and-lovable-safety.md): version discipline and Lovable compatibility checks.

## Default Agent Workflow

1. Identify the target feature, bug, or system area.
2. Read the relevant KB file.
3. Inspect the actual app code before making claims.
4. Make the smallest complete change that solves the request.
5. Run lint, typecheck, build, or targeted checks when available.
6. If debugging took several attempts, update [06-traps-and-lessons.md](06-traps-and-lessons.md).

