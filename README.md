# claude-project-factory

A factory for scaffolding new projects with an opinionated Claude context architecture — genericized so it works for any stack.

## What you get

Dropped into any target directory:

```
<target>/
├── CLAUDE.md                             ← top-level: loads AGENTS.md + pipeline overview
├── AGENTS.md                             ← project-specific rules (stub for you to fill)
├── .claude/
│   ├── settings.json                     ← formatting hook preconfigured
│   └── skills/
│       ├── SKILL_TEMPLATE.md
│       ├── spec/SKILL.md                 ← /spec:   idea     → docs/superpowers/specs/spec-NNN.md
│       ├── plan/SKILL.md                 ← /plan:   spec     → tmp/implementation-plan.md
│       ├── implement/SKILL.md            ← /implement: plan  → code + commits
│       └── qa/SKILL.md                   ← /qa:     verifies → tmp/qa-report.md
└── docs/
    └── superpowers/
        ├── README.md                     ← pipeline overview
        ├── specs/
        │   ├── TEMPLATE.md
        │   ├── SPEC_CONVENTIONS.md
        │   ├── backend/TEMPLATE.md       ← fullstack / backend-only profiles
        │   └── frontend/TEMPLATE.md      ← fullstack / frontend-only profiles
        ├── prompts/README.md             ← drop extended agent prompts here when needed
        └── best-practices/README.md      ← drop stack-specific patterns here
```

All files are parameterized with `{{PLACEHOLDERS}}` so the `spec/plan/implement/qa` pipeline is wired to the correct package manager and command set for your stack.

## Usage

The factory is itself a Claude project. Open Claude in this directory and invoke:

```
/scaffold-project ~/path/to/new-project my-project-name
```

The `scaffold-project` skill will ask any questions it needs (package manager, stack profile), copy the `template/` tree into the target, substitute placeholders, and print next steps.

### Arguments

- `target-dir` (required) — where to scaffold. Absolute path or `~/` prefix.
- `project-name` (required) — lowercase-kebab-case identifier used in CLAUDE.md/AGENTS.md.

### Interactive prompts (unless overridden inline)

- **Package manager** — `bun` (default), `pnpm`, `npm`, `yarn`, `uv`, `poetry`, `go`, `cargo`, `none`.
- **Stack profile**:
  - `fullstack` (default) — includes backend + frontend spec templates
  - `backend-only` — omits `specs/frontend/`
  - `frontend-only` — omits `specs/backend/`
  - `minimal` — only the top-level `specs/TEMPLATE.md` + `SPEC_CONVENTIONS.md`

## Installing globally (optional)

If you want `/scaffold-project` available from any project, symlink the skill into your global skills directory:

```bash
mkdir -p ~/.claude/skills
ln -s "$(pwd)/.claude/skills/scaffold-project" ~/.claude/skills/scaffold-project
```

When invoked via symlink, the skill resolves `template/` relative to the factory repo using `$CLAUDE_PROJECT_DIR`. If resolution fails, it will tell you which paths it tried. Pass an explicit third argument to override:

```
/scaffold-project ~/path/to/new-project my-project-name ~/path/to/claude-project-factory/template
```

## Adapting the template

The template tree is just files. Edit anything in `template/` and every future scaffold picks up the change. Common reasons to edit:

- You've landed on a new hook you want in every project → edit `template/.claude/settings.json`
- You've found a spec-writing convention that saves time → edit `template/docs/superpowers/specs/SPEC_CONVENTIONS.md`
- You want a new default best-practices doc → drop it in `template/docs/superpowers/best-practices/`

## Intentional omissions

- **Night Agent / GitHub Actions** — this factory does not scaffold any CI. Keep CI in its own factory if you want one.
- **Project code / dependencies** — no `package.json`, `pyproject.toml`, `go.mod`, etc. Use your language's normal `init` command alongside this scaffold.
- **Stack-specific best-practices docs** — `best-practices/` ships as a stub on purpose. Filling it with Next.js rules would be wrong for a Go project.
