# Extended Agent Prompts

The `.claude/skills/{spec,plan,implement,qa}/SKILL.md` files are intentionally terse — they're read in full every time a skill runs, so keeping them under a few hundred lines matters.

When a prompt outgrows that budget (new detailed checklists, stack-specific guidance, long output schemas), drop the extended version here as:

```
prompts/
  planner-agent-prompt.md
  implementer-agent-prompt.md
  qa-agent-prompt.md
```

Then reference it from the SKILL.md body with a short "read `docs/superpowers/prompts/<file>.md` first" instruction.

This is purely opt-in — the pipeline works fine without anything in this directory.
