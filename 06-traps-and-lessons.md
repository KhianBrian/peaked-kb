# Traps And Lessons

This is the self-learning log. When debugging takes many tries, or the final fix reveals that the team was looking in the wrong place, add an entry here.

The goal is not blame. The goal is to make the next similar bug faster.

## When To Add An Entry

Add an entry when:

- A bug took several attempts to solve.
- The root cause was non-obvious.
- The team repeatedly checked the wrong layer.
- A dependency, generated file, cache, or deployment assumption caused confusion.
- The fix should become a future guardrail.
- An AI feature produced cost, quota, safety, or output-shape surprises.

## Entry Template

```md
## YYYY-MM-DD - Short Title

### Symptom

What looked broken?

### Impact

Who was affected and how serious was it?

### False Leads

What did we try that did not solve it?

### Root Cause

What actually caused it?

### Fix

What changed?

### Why It Took So Long

What assumption, missing signal, or workflow gap slowed us down?

### Future Guardrail

What should we check first next time?

### Related Files

- `path/to/file.ts`
```

## Current Lessons

No lessons recorded yet.

Replace this line with dated entries as the project learns.

## Common Traps To Watch For

### Lovable Vite Config Duplication

The Lovable TanStack Vite config already includes important plugins. Do not manually add duplicate TanStack, React, Tailwind, Cloudflare, tsconfig paths, env injection, dedupe, or sandbox plugins unless the current config explicitly supports it.

Check `vite.config.ts` before changing build plugins.

### Generated Route Files

Generated route tree files can become stale or tempting to edit directly. Prefer framework generation and route file changes over manual edits.

### Server And Browser Boundary

SSR-capable apps can break when browser-only APIs run on the server or server-only modules leak into the client. Check imports carefully.

### AI Retry Loops

Retries can multiply cost. Every AI retry needs a cap, timeout, and user-visible failure path.

### Cache Looks Like Truth

Cache can hide state changes. If profile, billing, quota, resume, or AI result behavior looks wrong, verify the source of truth before changing UI.

