<!-- BEGIN:project-agent-rules -->

# {{PROJECT_NAME}} — Agent Rules

Add project-specific rules here. This is the first file every agent reads in this repo; keep it short, specific, and up to date. Suggested sections below — delete the ones you don't need.

## Stack (replace with actuals)

- Language / runtime: TODO
- Framework: TODO
- Package manager: `{{PKG_MANAGER}}`
- Run prefix: `{{RUN}}` (e.g., `{{RUN}} test`)
- Tests: `{{TEST_CMD}}`
- Lint: `{{LINT_CMD}}`
- Typecheck: `{{TYPECHECK_CMD}}`
- Format: `{{FORMAT_CMD}}`

## Hard rules

- TODO: list rules that, if violated, should make the agent stop and ask. Example:
  "Never modify the production schema without a migration file."
- TODO: add patterns the team always avoids.

<!-- END:project-agent-rules -->

## Authoritative docs

If this repo has any documents that take precedence over anything an agent would derive from the code alone, list them here. Example:

> All UI work (components, styling, animation, tokens) MUST follow `docs/design-system.md`.
> When the code and the doc disagree, one of them is wrong — update both in the same commit.

When a doc like this exists, consider wiring it into `.claude/hooks/` so it gets injected into relevant prompts automatically. See `.claude/settings.json` for the hook config.
