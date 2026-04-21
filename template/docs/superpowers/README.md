# Agentic Development Pipeline

This directory contains the knowledge layer for a four-stage AI-assisted development pipeline. Humans provide strategic direction and rough ideas. Claude and its skills translate that into high-fidelity specs, implementation plans, working code, and verified output.

## How It Works

```
  HUMAN
    │  rough ideas · strategic direction · "why we're building this"
    │
    │  (every session)
    ▼
  ╔══════════════════════════════════════╗
  ║  CLAUDE.md  (ambient, always loaded) ║
  ║  • pipeline overview                 ║
  ║  • /spec /plan /implement /qa        ║
  ╚══════════════════════════════════════╝
    │
    │  /spec [rough-draft-path or inline notes]
    ▼
  ┌──────────────────────────────────────────────────────────────────┐
  │  spec skill  (fork)                                              │
  │                                                                  │
  │  reads ──► docs/superpowers/specs/TEMPLATE.md                    │
  │            docs/superpowers/specs/SPEC_CONVENTIONS.md            │
  │                                                                  │
  │  writes ──► docs/superpowers/specs/spec-NNN.md   (NEW)           │
  │             REQ-N items, Context Discovery, DoD, Constraints     │
  └──────────────────────────────────────────────────────────────────┘
    │
    │  /plan docs/superpowers/specs/spec-NNN.md
    ▼
  ┌──────────────────────────────────────────────────────────────────┐
  │  plan skill  (fork · agent: Plan)                                │
  │                                                                  │
  │  reads ──► docs/superpowers/specs/spec-NNN.md                    │
  │            src/  (pattern discovery from Context Discovery)      │
  │                                                                  │
  │  writes ──► tmp/implementation-plan.md                           │
  └──────────────────────────────────────────────────────────────────┘
    │
    │  /implement
    ▼
  ┌──────────────────────────────────────────────────────────────────┐
  │  implement skill  (fork · manual-only)                           │
  │                                                                  │
  │  reads ──► tmp/implementation-plan.md                            │
  │            src/  (pattern files listed in plan)                  │
  │                                                                  │
  │  writes ──► src/  (feature code + tests)                         │
  │             git commits  (one per task)                          │
  │             tmp/implementation-result.md                         │
  └──────────────────────────────────────────────────────────────────┘
    │
    │  /qa
    ▼
  ┌──────────────────────────────────────────────────────────────────┐
  │  qa skill  (fork)                                                │
  │                                                                  │
  │  reads ──► tmp/implementation-plan.md                            │
  │            tmp/implementation-result.md                          │
  │                                                                  │
  │  runs ───► {{TYPECHECK_CMD}} · {{LINT_CMD}} · {{TEST_CMD}} · {{FORMAT_CHECK}}
  │                                                                  │
  │  writes ──► tmp/qa-report.md                                     │
  │  updates ──► docs/superpowers/specs/spec-NNN.md  (status→done)   │
  └──────────────────────────────────────────────────────────────────┘
    │
    ▼
  HUMAN  (reads qa-report.md · reviews diff · merges)
```

## Typical Session

```bash
/spec docs/superpowers/specs/my-rough-draft.md
/plan docs/superpowers/specs/spec-001.md
/implement
/qa
```

## The Role of docs/superpowers/specs/

The specs directory is the durable store that the pipeline reads from and writes back to. `tmp/` files are ephemeral per-session handoff. Skills are the process layer connecting them.

| Stage          | Role                                                             |
| -------------- | ---------------------------------------------------------------- |
| `/spec` reads  | Convention source — `TEMPLATE.md` + `SPEC_CONVENTIONS.md`        |
| `/spec` writes | `spec-NNN.md` created here from rough human input                |
| `/plan` reads  | `spec-NNN.md` is the source of truth for the implementation plan |
| `/qa` updates  | `spec-NNN.md` status flipped to `completed`                      |

## Division of Responsibility

**Humans provide:** strategic direction, product reasoning, "why we're building this", rough functional ideas, open questions.

**The pipeline provides:** precise requirements (`REQ-N`), file-level implementation tasks, type contracts, pattern discovery, code, tests, and quality verification.

The spec is the contract between human intent and agent execution. `/spec` is where ambiguity gets resolved before any code is written.

## Directory Structure

```
docs/superpowers/
  README.md                        ← you are here
  specs/
    TEMPLATE.md                    ← generic spec structure
    SPEC_CONVENTIONS.md            ← authoring rules and status lifecycle
    spec-NNN-[title].md            ← individual specs (pending → completed)
    backend/TEMPLATE.md            ← (optional) backend-specific spec template
    frontend/TEMPLATE.md           ← (optional) frontend-specific spec template
  prompts/
    README.md                      ← drop extended agent prompts here if the SKILL.md bodies grow too large
  best-practices/
    README.md                      ← drop stack-specific patterns here and link from AGENTS.md
```

## Skills Reference

Skills live in `.claude/skills/` and are invoked with `/skill-name`. See each `SKILL.md` for the full prompt each skill runs.

| Skill       | Trigger                    | Input                        | Output                                   |
| ----------- | -------------------------- | ---------------------------- | ---------------------------------------- |
| `spec`      | `/spec [draft-path]`       | rough notes or draft file    | `docs/superpowers/specs/spec-NNN.md`     |
| `plan`      | `/plan [spec-path]`        | refined spec                 | `tmp/implementation-plan.md`             |
| `implement` | `/implement` (manual only) | `tmp/implementation-plan.md` | commits + `tmp/implementation-result.md` |
| `qa`        | `/qa`                      | both tmp files               | `tmp/qa-report.md` + spec status updated |
