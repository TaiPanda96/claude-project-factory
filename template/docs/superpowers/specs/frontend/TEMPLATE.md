---
id: spec-000
title: "[Imperative title: Add X component / Fix Y interaction / Redesign Z]"
status: pending
priority: medium
scope: standard
created: YYYY-MM-DD
pr: null
---

## Summary

One paragraph. What UI is being built or changed, why it exists, and what user problem it solves. Write as if explaining to an engineer who has never seen the codebase.

---

## Context Discovery

### Files to Read First

| File                                 | Why                                               |
| ------------------------------------ | ------------------------------------------------- |
| `src/components/<similar-component>` | Pattern to follow for structure/styling/animation |
| `docs/design-system.md` (if present) | Authoritative tokens and interaction rules        |
| `app/(routes)/<similar-route>/page`  | Route-level composition pattern                   |

### Similar Implementations to Study

| File                 | What to learn from it                  |
| -------------------- | -------------------------------------- |
| `src/components/foo` | State, props, suspense, error boundary |

---

## Requirements

### Functional Requirements

- [ ] REQ-1: [Observable user-visible behavior]
- [ ] REQ-2: [Observable user-visible behavior]
- [ ] REQ-3: Respects `prefers-reduced-motion` — animations disabled/shortened when set

### Non-Functional Requirements

- [ ] Type safety: component props are fully typed; no unchecked casts
- [ ] Accessibility: semantic HTML, appropriate ARIA, keyboard-navigable, visible focus ring
- [ ] No layout shift on hydration
- [ ] No new lint warnings introduced

---

## Design Requirements

> If the project has a `docs/design-system.md`, follow it. List any specific tokens/patterns that apply.

- Tokens to use: [spacing, color, typography — reference the design system doc]
- Animation: [duration, easing, reduced-motion behavior]
- States: [default, hover, focus, active, disabled, error]

---

## Files to Create or Modify

| Action | Path                                | Purpose              |
| ------ | ----------------------------------- | -------------------- |
| create | `src/components/foo`                | New component        |
| modify | `app/(routes)/some-page/page`       | Render new component |
| create | `src/components/__tests__/foo.test` | Component tests      |

---

## Test Requirements

- [ ] Renders in its default state
- [ ] Handles each interactive state (hover, focus, disabled, error)
- [ ] Fires expected callbacks with expected arguments
- [ ] Does not crash when given missing/undefined optional props

---

## Constraints and Anti-Patterns

- **Do not** introduce a new styling system — use the project's existing CSS/utility framework
- **Do not** inline hex colors or pixel spacing — use design tokens
- **Do not** mark the component `"use client"` unless client-side state/effects are actually needed

---

## Definition of Done

- [ ] All functional requirements implemented
- [ ] `{{TYPECHECK_CMD}}` passes with zero errors
- [ ] `{{LINT_CMD}}` passes with zero warnings
- [ ] `{{TEST_CMD}}` passes — no regressions
- [ ] `{{FORMAT_CMD}}` applied and `{{FORMAT_CHECK}}` passes
- [ ] Visual review: matches design reference (screenshot/Figma link in spec)
- [ ] Spec status updated to `completed`

---

## Open Questions

- [ ] [Any unresolved design or behavior decision]

---

## Implementation Notes

- [Optional hints, gotchas, or pre-made decisions]
