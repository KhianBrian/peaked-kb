# Dependency And Lovable Safety

## Purpose

The app is connected to Lovable. Codex and Claude should develop the project in code, while Lovable handles production deployment. Dependency choices must protect local development and Lovable deployment.

## Current Stack Signals

Check the app before making changes, but current signals include:

- `bun.lock`, so Bun may be the intended package manager.
- Vite scripts in `package.json`.
- `@lovable.dev/vite-tanstack-config`.
- TanStack Start and TanStack Router.
- React 19.
- Tailwind CSS 4.
- Cloudflare-related Vite tooling.

## Hard Rules

- Do not install libraries casually.
- Do not upgrade core framework packages unless the task requires it.
- Do not add duplicate Vite plugins already included by Lovable config.
- Do not mix package managers unless there is a deliberate project decision.
- Do not remove or regenerate lockfiles without understanding the deployment impact.
- Do not assume a package works in SSR, Cloudflare, or Lovable just because it works in the browser.

## Before Adding A Dependency

Check:

- Does the project already have a library that solves this?
- Is the package compatible with React 19?
- Is it compatible with Vite?
- Is it compatible with SSR or server rendering?
- Is it compatible with Cloudflare-style runtime constraints if used server-side?
- Does it require Node APIs unavailable in the target runtime?
- Does it have peer dependency conflicts?
- Does it increase bundle size meaningfully?
- Is it maintained?
- Can the feature be built simply without it?

## Version Selection

Prefer:

- Existing project versions.
- Stable releases.
- Versions compatible with current peer dependencies.
- Libraries that support ESM and Vite cleanly.
- Libraries that do not require custom build plugins.

Avoid:

- Experimental releases.
- Deprecated packages.
- Packages with unresolved peer conflicts.
- Packages that require Webpack-only behavior.
- Server packages that assume Node APIs when the deployment target may be edge-like.

## Install Workflow

1. Inspect `package.json` and lockfile.
2. Identify the package manager from lockfiles and scripts.
3. Check compatibility.
4. Install the minimum required dependency.
5. Review `package.json` and lockfile changes.
6. Run install/build/lint checks that fit the project.
7. Document any compatibility assumptions.

## Lovable Compatibility Checklist

Before considering a dependency change done:

- Local install succeeds.
- `package.json` scripts still make sense.
- Build succeeds.
- No duplicate Lovable/Vite plugins were added.
- No server-only package is imported into client code.
- No browser-only package is imported into SSR/server code.
- Lockfile is updated consistently.
- The change does not require secret env vars unavailable in Lovable without documenting them.

## AI Provider Dependency Notes

For AI API work:

- Prefer server-side fetch or a lightweight official SDK only if compatible.
- Keep provider calls behind an internal adapter.
- Avoid SDKs that pull in Node-only modules if deploying to edge-like runtimes.
- Never expose provider API keys to client bundles.
- Include timeouts and retries at the adapter layer.

