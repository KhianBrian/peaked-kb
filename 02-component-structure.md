# Component Structure

## Goals

Components should be easy to locate, easy to reuse, and boring to maintain. The app should feel consistent across onboarding, dashboard, profile, jobs, resume, paywall, and future AI task flows.

## Existing Directories

Current signals from the app:

- `src/routes`: route files and page-level composition.
- `src/components`: shared or feature-level components.
- `src/components/ui`: reusable UI primitives, likely shadcn/Radix-style components.
- `src/components/layout`: shell, sidebar, bottom navigation, and layout pieces.
- `src/hooks`: shared React hooks.
- `src/lib`: utilities, data helpers, API clients, validation, and shared logic.

## Placement Rules

### Route Files

Use route files for:

- Page composition.
- Route-level data loading.
- Route params/search params.
- Navigation redirects.
- High-level loading/error boundaries.

Avoid putting large component trees, complex business logic, or provider-specific API calls directly in route files.

### Feature Components

If a component belongs to one feature and is not broadly reusable, keep it close to that feature's route or place it under a clearly named feature folder.

Example future structure:

```text
src/components/resume/
  resume-score-panel.tsx
  resume-upload-zone.tsx
  resume-insight-list.tsx
```

### Shared Components

Use `src/components` for reusable app components such as:

- Score displays.
- Paywall modals.
- Onboarding frames.
- Shared empty states.
- Reusable AI task panels.

### UI Primitives

Use `src/components/ui` for low-level primitives only:

- Button
- Dialog
- Input
- Select
- Tabs
- Tooltip
- Card

Do not put product-specific logic or API calls in `ui` primitives.

### Layout

Use `src/components/layout` for:

- App shell.
- Sidebar.
- Bottom navigation.
- Header/navigation structure.
- Responsive layout scaffolding.

Layout components should not know detailed business logic beyond active navigation, auth display, and shell-level state.

## Naming

- Use descriptive file names: `resume-upload-zone.tsx`, not `Upload.tsx`.
- Prefer one primary component per file.
- Hooks should start with `use`.
- Utility functions should be plain verbs or nouns that describe behavior.
- Avoid vague names like `Helper`, `Manager`, `Common`, or `NewComponent`.

## Props

Prefer props that describe product intent:

```tsx
<ResumeScorePanel score={82} trend="up" />
```

Avoid props that leak internal rendering details:

```tsx
<Panel number={82} green={true} arrow={true} />
```

## State Ownership

Keep state as low as possible, but no lower.

- Local UI state belongs in the component.
- Server state belongs in TanStack Query or route loaders.
- Auth and account state belongs in a shared provider or server-aware context.
- AI task state should be tracked enough to prevent duplicate cost and confusing retries.

## Loading, Empty, Error, Success

Every product component that fetches or mutates data should account for:

- Loading state.
- Empty state.
- Error state.
- Success state.
- Disabled state during submission.

For AI tasks, also include:

- Limit reached.
- Task queued or processing.
- Provider unavailable.
- Result expired or regenerated.

## Accessibility

- Use Radix primitives where available.
- Buttons must be buttons, links must be links.
- Inputs need labels or accessible names.
- Dialogs need titles and meaningful close behavior.
- Do not rely on color alone for status.
- Keyboard interaction should work for menus, dialogs, tabs, and forms.

## Review Checklist

Before finishing a component change:

- Is the file in the right folder?
- Is product logic out of `src/components/ui`?
- Are states complete?
- Is the component responsive?
- Is text fitting inside its container?
- Is it consistent with existing styles?
- Did the change avoid unnecessary new dependencies?

