# Architecture

## Purpose

Peaked should be built as a reliable, production-ready web app where product behavior is predictable, AI features are cost-controlled, and Lovable can deploy without needing to repair code.

## Current Shape

The app currently appears to use:

- TanStack Start for app structure and server-aware routing.
- TanStack Router with file-based routes in `src/routes`.
- Vite for dev/build.
- React 19 and TypeScript for UI.
- Tailwind CSS 4 and Radix UI for styling and primitives.
- TanStack Query for server state and cache behavior.
- Cloudflare/Vite tooling for deployment output.
- Lovable-specific Vite config through `@lovable.dev/vite-tanstack-config`.

Before changing architecture, confirm this is still accurate by checking `package.json`, `vite.config.ts`, `src/router.tsx`, `src/routes`, and deployment config.

## Architectural Principles

- Keep user-facing flows route-driven and easy to reason about.
- Keep route files focused on page composition, data loading, and navigation-level concerns.
- Keep reusable UI in `src/components`.
- Keep app-wide helpers, adapters, API clients, validation schemas, and shared business logic in `src/lib`.
- Keep generated files generated. Do not hand-edit generated route files unless the framework requires it and there is no better option.
- Put secrets, private API keys, and AI provider calls on the server side only.
- Treat deployment compatibility as part of architecture, not as a final cleanup task.

## Boundaries

### Client

The client should handle:

- UI rendering and interaction.
- Form state and local validation.
- Optimistic UI only when rollback behavior is clear.
- Calling internal app endpoints, not third-party AI APIs directly.
- Displaying loading, empty, error, and success states.

The client should not handle:

- AI provider secrets.
- Billing or quota authority.
- Trust decisions based only on local state.
- Long-running retries that can multiply cost.

### Server

The server should handle:

- Authenticated API routes.
- AI provider calls.
- Rate limit checks.
- Quota enforcement.
- Input validation and sanitization.
- Logging of AI request metadata without storing sensitive content unnecessarily.
- Cost and usage accounting.

### External Services

Any external service should have:

- A single adapter layer in the app.
- Typed request and response handling.
- Timeouts.
- Error mapping into user-safe messages.
- Logging that supports debugging without leaking private data.

## Routing And Data Flow

Use TanStack Router conventions unless there is a strong reason to deviate.

Preferred flow:

1. Route composes the page.
2. Page calls hooks or loaders for data.
3. Hooks use TanStack Query or internal API clients.
4. API clients call server routes or trusted services.
5. Server routes validate, authorize, rate limit, then perform work.

Avoid route files becoming giant product modules. If a route has complex UI, split it into feature components near the route or in `src/components` if reusable.

## Error Handling

Every async boundary should have a known failure state:

- Form validation errors.
- Network errors.
- Authentication errors.
- Rate limit errors.
- AI provider failures.
- Empty results.
- Partial success.

Never surface raw provider errors, stack traces, prompt text, or secret-bearing payloads to users.

## Observability

For important flows, log metadata that answers:

- What operation was attempted?
- Which user or anonymous session attempted it?
- Was it allowed by quota and rate limits?
- Which provider/model was used?
- How long did it take?
- Did it fail, and at what layer?

Do not log full resumes, private profile content, access tokens, API keys, or raw AI prompts unless there is a deliberate privacy policy and retention plan.

## Architecture Change Checklist

Before merging a meaningful architectural change:

- Confirm it fits TanStack Start, Vite, React 19, and Lovable.
- Confirm it does not duplicate plugins already included by `@lovable.dev/vite-tanstack-config`.
- Confirm server-only code does not leak into client bundles.
- Confirm generated route artifacts are handled by the framework.
- Confirm build still works locally.
- Update this KB if the system shape changes.

