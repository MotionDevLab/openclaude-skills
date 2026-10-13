# Skill template

Copy this shape for every new skill in this repo. It mirrors what
`openclaude skills validate` checks: frontmatter fields, skill name, size
limits, and banned content patterns.

```markdown
---
name: <folder-name-must-match>
description: <one line: what it does + when to use it. This is the trigger text — write it for the router, not the reader.>
---

# <Title>

## Purpose

<2–4 sentences. State default mode: report-only unless a flag says otherwise.>

## Prerequisites

<Binaries, network access, repo paths the operator needs. Fail fast if missing.>

## Procedure

<Numbered steps the agent follows. Prefer checks over actions; writes only
behind an explicit flag. Name the exact files and test commands.>

## Guardrails

<What the skill must NEVER do. Be specific: paths, modules, irreversible
actions.>

## Reference

<Repo-relative links only — no absolute machine paths. Point at the docs and
tests that own the behavior.>
```

## Rules

- `name` matches the folder name exactly.
- Description answers "what" and "when" in one line.
- No credentials, no secret collection, no piped-shell installs — validation
  rejects these and so does review.
- No absolute paths. No machine-specific assumptions (OS, username, editor).
- Reference the tests that enforce the behavior the skill touches, so a green
  run means something.
