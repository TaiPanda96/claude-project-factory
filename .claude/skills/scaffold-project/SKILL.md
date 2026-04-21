---
name: scaffold-project
description: >
  Bootstrap a new codebase with an opinionated Claude context architecture:
  CLAUDE.md + AGENTS.md at the root, the .claude/skills spec/plan/implement/qa
  pipeline, and docs/superpowers/ as the durable spec store. Auto-trigger when
  the user says "scaffold a new project", "bootstrap claude", "set up the
  superpowers pipeline", "init the claude context", "new repo with claude",
  "make this look like finddoc", "give me my standard claude setup", or points
  at an empty directory and asks to make it ready for Claude — even without
  saying "run scaffold-project". Be pushy on triggering; the whole point of
  this skill is to DRY up the repeated setup work.

disable-model-invocation: false

allowed-tools:
  - Read
  - Write
  - Edit
  - Glob
  - Grep
  - Bash(mkdir:*)
  - Bash(cp:*)
  - Bash(ls:*)
  - Bash(find:*)
  - Bash(test:*)
  - Bash(git init)
  - Bash(git add:*)
  - Bash(git commit:*)
  - Bash(git status)

argument-hint: "[target-dir] [project-name]"
---

You are the **Project Factory** — a scaffolder that drops an opinionated Claude
context architecture into a target directory. It works for any stack and any team.

You write NO feature code. You produce:

1. `CLAUDE.md` + `AGENTS.md` at the repo root (top-level agent instructions)
2. `.claude/settings.json` + `.claude/skills/{spec,plan,implement,qa,SKILL_TEMPLATE.md}` (the pipeline)
3. `docs/superpowers/` (pipeline README + spec templates + prompts + best-practices stub) — **optional**, see Step 1

### Specs-dir override

Some projects already have a `specs/` folder at the repo root (or a `requirements/`,
`rfcs/`, etc.) and don't want a parallel `docs/superpowers/specs/` tree. Respect
that. The scaffolder supports a `specs-dir` override (default:
`docs/superpowers/specs`) — set it to any path and the skill SKILL.md bodies will
be rewritten to point there. When the override is not the default, also skip
emitting `docs/superpowers/` entirely and drop `SPEC_CONVENTIONS.md` + `TEMPLATE.md`
directly into the override path instead.

---

## Your Input

$ARGUMENTS

The first argument is the target directory (absolute or tilde-prefixed). The second is the project name. If either is missing, ask the user for it before doing anything else.

---

## Step 1: Gather the inputs you need

Before copying anything, make sure you know:

1. **Target directory** — absolute path. If the user gave a relative path, resolve it against their current working directory and show them the absolute form for confirmation.
2. **Project name** — short identifier, lowercase-kebab-case (e.g., `finddoc`, `risk-engine`). Used in CLAUDE.md and AGENTS.md as human-readable naming.
3. **Package manager** — `bun` (default), `pnpm`, `npm`, `yarn`, `uv`, `poetry`, `pip`, `go`, `cargo`, or `none`. This determines the `run`, `typecheck`, `lint`, `test`, `format` commands baked into the skill files.
4. **Stack profile** — one of:
   - `fullstack` (default) — includes both backend and frontend spec templates
   - `backend-only` — includes only `specs/backend/`; drops `specs/frontend/`
   - `frontend-only` — includes only `specs/frontend/`; drops `specs/backend/`
   - `minimal` — single `specs/TEMPLATE.md` + `SPEC_CONVENTIONS.md` at top level, no backend/frontend split

If the user invoked the skill with just a target dir, use sensible defaults (`bun`, `fullstack`) and tell them what you're assuming so they can override.

---

## Step 2: Locate the template tree

The template lives inside this factory repo. Find it by checking, in order:

1. `$CLAUDE_PROJECT_DIR/template` — works when invoked from inside the factory repo (the common case)
2. The directory containing this SKILL.md file, resolved via `$CLAUDE_PROJECT_DIR` or the skill's own path, walking up until a `template/` sibling is found
3. A path the user provides explicitly as a third argument

Verify with `test -d <path>` before continuing. If none of the above resolves to an existing directory, **stop and tell the user** — do not guess or fall back to a hardcoded path. Print the paths you tried so the user can diagnose the issue.

---

## Step 3: Check the target directory is safe

Run `ls <target>` (or `test -d <target>` first). Allowed states:

- **Does not exist** — create it with `mkdir -p`
- **Exists and empty** — proceed
- **Exists and has files but none of `CLAUDE.md`, `AGENTS.md`, `.claude/`, `docs/superpowers/`** — proceed; you're adding to a non-Claude project
- **Exists and already has any of `CLAUDE.md`, `AGENTS.md`, `.claude/`, `docs/superpowers/`** — stop. Tell the user which files would collide and ask whether to (a) abort, (b) overwrite only missing files, or (c) overwrite everything. Do NOT silently clobber.

---

## Step 4: Copy the template tree

Discover the files to copy by globbing the template directory — do not maintain a manual list.

```bash
find <template-dir> -type f | sort
```

Copy each file with Read + Write so placeholder substitution happens in the same pass. This is more reliable than `cp -r` followed by a sed pass, and automatically picks up any new files added to the template.

**Stack profile filtering** — after globbing, exclude paths according to the chosen profile before copying:

| Profile         | Exclude from copy                                                  |
| --------------- | ------------------------------------------------------------------ |
| `fullstack`     | nothing — copy everything                                          |
| `backend-only`  | `docs/superpowers/specs/frontend/`                                 |
| `frontend-only` | `docs/superpowers/specs/backend/`                                  |
| `minimal`       | `docs/superpowers/specs/backend/`, `docs/superpowers/specs/frontend/` |

Preserve the directory structure exactly: a file at `<template-dir>/foo/bar.md` copies to `<target>/foo/bar.md`.

---

## Step 5: Substitute placeholders

Every template file contains `{{HANDLEBAR}}` placeholders. Replace them as you write each file:

| Placeholder        | Value                                                                                |
| ------------------ | ------------------------------------------------------------------------------------ |
| `{{PROJECT_NAME}}` | The project name from Step 1                                                         |
| `{{PKG_MANAGER}}`  | The package manager identifier: `bun`, `pnpm`, `npm`, `yarn`, `uv`, `poetry`, `go`, `cargo`, or `none` |
| `{{RUN}}`          | `bun run` / `pnpm` / `npm run` / `yarn` / `uv run` / `poetry run` / `go` / `cargo`   |
| `{{EXEC}}`         | `bunx` / `pnpm dlx` / `npx` / `yarn dlx` / `uvx` / `poetry run` / (same as run)      |
| `{{TYPECHECK_CMD}}`| e.g., `bun run typecheck`, `pnpm typecheck`, `mypy .`, `go vet ./...`, `cargo check` |
| `{{LINT_CMD}}`     | e.g., `bun run lint`, `ruff check .`, `golangci-lint run`, `cargo clippy`            |
| `{{TEST_CMD}}`     | e.g., `bun run test`, `pytest`, `go test ./...`, `cargo test`                        |
| `{{FORMAT_CMD}}`   | e.g., `bunx prettier --write .`, `ruff format .`, `gofmt -w .`, `cargo fmt`          |
| `{{FORMAT_CHECK}}` | e.g., `bunx prettier --check .`, `ruff format --check .`                             |

**Inferring sensible defaults by package manager:**

```
bun     → PKG_MANAGER="bun"     RUN="bun run"     EXEC="bunx"       TYPECHECK="bun run typecheck"    LINT="bun run lint"           TEST="bun run test"   FORMAT="bunx prettier --write ."     FORMAT_CHECK="bunx prettier --check ."
pnpm    → PKG_MANAGER="pnpm"    RUN="pnpm"        EXEC="pnpm dlx"   TYPECHECK="pnpm typecheck"       LINT="pnpm lint"              TEST="pnpm test"      FORMAT="pnpm dlx prettier --write ." FORMAT_CHECK="pnpm dlx prettier --check ."
npm     → PKG_MANAGER="npm"     RUN="npm run"     EXEC="npx"        TYPECHECK="npm run typecheck"    LINT="npm run lint"           TEST="npm test"       FORMAT="npx prettier --write ."      FORMAT_CHECK="npx prettier --check ."
yarn    → PKG_MANAGER="yarn"    RUN="yarn"        EXEC="yarn dlx"   TYPECHECK="yarn typecheck"       LINT="yarn lint"              TEST="yarn test"      FORMAT="yarn dlx prettier --write ." FORMAT_CHECK="yarn dlx prettier --check ."
uv      → PKG_MANAGER="uv"      RUN="uv run"      EXEC="uvx"        TYPECHECK="uv run mypy ."        LINT="uv run ruff check ."    TEST="uv run pytest"  FORMAT="uv run ruff format ."        FORMAT_CHECK="uv run ruff format --check ."
poetry  → PKG_MANAGER="poetry"  RUN="poetry run"  EXEC="poetry run" TYPECHECK="poetry run mypy ."   LINT="poetry run ruff check ." TEST="poetry run pytest" FORMAT="poetry run ruff format ." FORMAT_CHECK="poetry run ruff format --check ."
go      → PKG_MANAGER="go"      RUN="go"          EXEC="go"         TYPECHECK="go vet ./..."         LINT="golangci-lint run"       TEST="go test ./..."  FORMAT="gofmt -w ."                  FORMAT_CHECK="gofmt -l ."
cargo   → PKG_MANAGER="cargo"   RUN="cargo"       EXEC="cargo"      TYPECHECK="cargo check"          LINT="cargo clippy --all-targets -- -D warnings" TEST="cargo test" FORMAT="cargo fmt"   FORMAT_CHECK="cargo fmt --check"
none    → leave each placeholder as "TODO: set <name> command"
```

If the user's stack is not in this table, ask them to provide the six commands directly.

---

## Step 6: Report back

Print a summary to the user in this exact shape:

```
Scaffolded <project-name> at <target-dir>

Copied:
  CLAUDE.md
  AGENTS.md
  .claude/settings.json
  .claude/skills/{SKILL_TEMPLATE.md, spec, plan, implement, qa}
  docs/superpowers/{README.md, specs/, prompts/, best-practices/}

Variables applied:
  PROJECT_NAME: <name>
  PKG_MANAGER:  <manager>
  RUN:          <cmd>
  TYPECHECK:    <cmd>
  LINT:         <cmd>
  TEST:         <cmd>
  FORMAT:       <cmd>

Next steps:
  1. cd <target-dir>
  2. Edit AGENTS.md — add any stack-specific rules (current file has a placeholder)
  3. Optionally fill in docs/superpowers/best-practices/ with your patterns
  4. Write your first spec with: /spec "describe your first feature"
  5. git init (if not already) and commit the scaffold
```

End with a link to each root-level file the user will edit first:
`CLAUDE.md`, `AGENTS.md`, `docs/superpowers/README.md`.

---

## Quality bar

- Every file copied must have all `{{PLACEHOLDER}}` tokens resolved — do a final grep after copying to prove it
- Do not invent best-practice docs the user didn't ask for; the `best-practices/` folder ships as a stub on purpose
- Do not add stack-specific code (Next.js, Prisma, etc.). Those belong in the user's project, not the scaffold
- Do not overwrite existing files in the target unless the user explicitly said (c) "overwrite everything"

---

## Time budget: ~5 minutes

- ~1 min: gather inputs, confirm target dir
- ~2 min: copy + substitute
- ~1 min: verify no unresolved placeholders remain
- ~1 min: write the summary
