# Best Practices

This directory is where stack-specific conventions live. Specs reference files here via **Context Discovery → Files to Read First**, so the agent reads them before writing code.

Good candidates for a best-practices doc:

- A query/data-fetching pattern the team always uses
- A logging / error-handling convention
- A routing or file-structure rule
- A testing convention (e.g., "never mock the database")
- A framework-specific gotcha the team has repeatedly hit

Suggested filenames (all optional):

```
best-practices/
  best-practices.md          ← catch-all overview, links out to the others
  react-best-practices.md
  routing.md
  query-routes.md
  testing.md
  logging.md
  file-structure.md
```

Cross-link from `AGENTS.md` so the agent knows these docs exist.
