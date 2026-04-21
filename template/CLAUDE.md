@AGENTS.md

## Agentic Development Pipeline

This codebase uses a four-stage agent pipeline for implementing features. Specs live in `docs/superpowers/specs/`.

**Skills**
Under `.claude/skills`, you will find a spec/plan/implement/qa workflow you can invoke.

**Typical session:**

```
/spec "rough description of the feature — or docs/superpowers/specs/my-rough-draft.md"
/plan docs/superpowers/specs/<spec-file>.md
/implement
/qa
```

Each stage hands off via files in `tmp/`. Full conventions: `docs/superpowers/specs/SPEC_CONVENTIONS.md`.
