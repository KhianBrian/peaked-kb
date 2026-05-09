# UI Animation Standards

## Purpose

Peaked UI should feel polished, fast, trustworthy, and product-specific. Animation should clarify state, guide attention, and make workflows feel responsive. It should not become decoration that slows users down or creates deployment risk.

Use this file for visual design, component polish, interaction states, page transitions, dashboards, onboarding, AI task flows, paywalls, and responsive QA.

## Stack-Compatible Choices

Current preferred stack:

- React 19
- TanStack Start and TanStack Router
- Vite
- TypeScript
- Tailwind CSS 4
- Radix UI primitives
- shadcn-style local components in `src/components/ui`
- TanStack Query for server state
- Lovable deployment

Preferred animation library:

- `motion` imported from `motion/react`

Use CSS transitions for simple hover, focus, color, opacity, and transform changes. Use Motion for stateful animations, layout transitions, enter/exit transitions, gestures, scroll-triggered reveals, and orchestrated sequences.

Avoid GSAP unless the product needs timeline-heavy animation that Motion cannot handle cleanly.

## Quality Bar

Every polished UI change should satisfy:

- Clear visual hierarchy.
- Strong alignment and spacing.
- Consistent typography scale.
- Responsive behavior across mobile and desktop.
- Accessible Radix-backed interactions where possible.
- Complete loading, empty, error, disabled, and success states.
- Motion that supports the workflow.
- No text overflow or incoherent overlap.
- No generic placeholder content.
- No unnecessary dependencies.
- Build remains Lovable-safe.

## Motion Principles

Use motion to:

- Confirm a user action.
- Show continuity when layout changes.
- Smooth entrance and exit of panels, dialogs, drawers, and lists.
- Indicate progress for AI tasks without faking completion.
- Guide attention to important changes.
- Make drag, tap, hover, and focus states feel tactile.

Do not use motion to:

- Distract from task completion.
- Hide slow data loading.
- Animate everything on the page.
- Create nausea-inducing movement.
- Make controls hard to click.
- Delay primary actions.

## Timing Guidelines

Default ranges:

- Micro-interactions: 100-180ms.
- Dialogs, sheets, menus: 150-240ms.
- Page or major panel transitions: 200-350ms.
- Staggered lists: 30-70ms between children.
- AI/progress animation: calm looping motion, no frantic pulsing.

Prefer easing that feels decisive:

- Quick entrance, soft settle.
- Short exits.
- Springs for layout and tactile elements.
- Tweens for opacity and color.

Respect reduced motion:

- Do not rely on animation to communicate critical information.
- Use Motion or CSS reduced-motion support when adding significant movement.

## Component Patterns

### Buttons

- Use icons from `lucide-react` where a familiar icon exists.
- Include hover, focus-visible, disabled, loading, and pressed states.
- Do not shift layout when loading text or icons change.

### Cards And Panels

- Use cards for repeated items, modals, and framed tools.
- Avoid nesting cards inside cards.
- Use Motion layout transitions for cards that expand, reorder, or appear after filters.

### Dialogs, Drawers, Menus

- Keep Radix accessibility behavior.
- Animate content, not the accessibility contract.
- Use entrance/exit animation that is short and predictable.
- Ensure focus management still works.

### AI Task UI

AI task screens need:

- Clear start state.
- In-progress state that prevents duplicate expensive requests.
- Quota or limit-reached state.
- Provider unavailable state.
- Safe retry behavior.
- Result state with regeneration controls if allowed.
- Cached-result indication only if it helps the user.

Do not make AI progress look deterministic unless progress is truly known.

## Visual Design Rules

- Prefer dense but readable operational layouts for app screens.
- Avoid generic oversized marketing sections inside actual app workflows.
- Do not use one-note palettes dominated by a single hue family.
- Avoid decorative gradient blobs or meaningless background effects.
- Keep border radii restrained unless the existing design system says otherwise.
- Make every screen feel like it belongs to Peaked, not a generic SaaS template.

## Browser Verification

After significant UI work, verify with the Browser plugin or a real browser:

- Desktop viewport.
- Mobile viewport.
- First load.
- Loading state.
- Error/empty state if practical.
- Hover/focus states.
- Dialog/menu/drawer behavior.
- Animation entrance and exit.
- Text fitting and overlap.

For complex animation, inspect:

- Does motion trigger at the right moment?
- Is it smooth?
- Does it interrupt gracefully?
- Does it preserve layout stability?
- Does reduced-motion fallback remain usable?

## Dependency Checklist

Before adding an animation or UI package:

- Check if CSS, Radix, existing components, or Motion can solve it.
- Confirm React 19 compatibility.
- Confirm Vite compatibility.
- Confirm SSR safety.
- Confirm Lovable deployment safety.
- Confirm peer dependencies.
- Confirm bundle impact.
- Update `engineering/dependency-and-lovable-safety.md` if a new rule is learned.

## Recommended Workflow

1. Read the relevant feature plan or create one.
2. Read `engineering/component-structure.md`.
3. Read this file.
4. Inspect existing UI patterns.
5. Build the smallest complete version.
6. Add motion only where it improves understanding or feel.
7. Verify in browser across breakpoints.
8. Run available checks.
9. Update `operations/traps-and-lessons.md` if debugging took too many attempts.
