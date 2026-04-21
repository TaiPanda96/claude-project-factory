---
id: spec-000
title: "[Imperative title: Add X / Fix Y / Migrate Z]"
status: pending
priority: medium
scope: standard
created: YYYY-MM-DD
pr: null
---

## Summary

One paragraph. What is being built, why it exists, what user or system problem it solves. Write as if explaining to an engineer who has never seen the codebase.

---

## Context Discovery

### Files to Read First

| File                                                | Why                                            |
| --------------------------------------------------- | ---------------------------------------------- |
| `src/lib/actions/example-action`                    | Follow this pattern for new server actions     |
| `[schema file]`                                     | Understand existing schema before adding to it |
| `docs/superpowers/best-practices/<x>.md`            | Required pattern                               |

### Similar Implementations to Study

| File                              | What to learn from it                   |
| --------------------------------- | --------------------------------------- |
| `src/lib/actions/some-feature`    | Pattern for error handling and logging  |

---

## Requirements

### Functional Requirements

- [ ] REQ-1: [Specific, observable behavior]
- [ ] REQ-2: [Specific, observable behavior]

### Non-Functional Requirements

- [ ] Type safety: no unchecked escape hatches without a justification comment
- [ ] All existing tests continue to pass
- [ ] New logic has test coverage
- [ ] No new lint warnings introduced

---

## Files to Create or Modify

| Action | Path                                           | Purpose                 |
| ------ | ---------------------------------------------- | ----------------------- |
| create | `src/lib/actions/foo-action`                   | Server action for X     |
| create | `src/lib/queries/foo-query`                    | Data fetching for X     |
| create | `src/lib/actions/__tests__/foo-action.test`    | Tests for server action |

---

## Test Requirements

- [ ] Unit test: `foo-action` returns expected shape when given valid input
- [ ] Unit test: `foo-action` returns typed error when [specific failure condition]
- [ ] Integration test: [if applicable — end-to-end data flow]
- [ ] No new test files that mock the database — use the real DB via test setup

---

## Constraints and Anti-Patterns

- **Do not** use raw SQL — use the ORM/query-builder the project has standardized on
- **Do not** introduce a new pattern for X — follow the existing pattern in `src/lib/actions/`
- **Do not** modify shared library files without checking impact on other consumers
- Schema changes **require** a migration file — do not edit schema without running the project's migration command
- All server actions must use the project's logger — never raw stdout logging

---

## Definition of Done

- [ ] All functional requirements (REQ-1 through REQ-N) implemented
- [ ] `{{TYPECHECK_CMD}}` passes with zero errors
- [ ] `{{LINT_CMD}}` passes with zero warnings
- [ ] `{{TEST_CMD}}` passes — no regressions
- [ ] `{{FORMAT_CMD}}` applied and `{{FORMAT_CHECK}}` passes
- [ ] Spec status updated to `completed`

---

## Open Questions

- [ ] [Any unresolved decision that must be answered before /plan can run]

---

## Implementation Notes

- [Optional hints, gotchas, or pre-made decisions]
