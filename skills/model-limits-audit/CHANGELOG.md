# Changelog — model-limits-audit

## v5 (2026-10-13)

- Frontmatter `version:` stamp, so `show` answers "which version where" at a
  glance instead of `Version: -`.
- This CHANGELOG itself, backfilling v1–v4 below from the audit record.

## v4 (2026-10-13)

- Dynamic file inventory (new family files like `gpt6.ts` can no longer fall
  through a closed list).
- Effort ladders marked out of scope, routed to the reasoning-effort
  checklist.
- Prompted by GPT-6 Astra scoping.

## v3 (2026-10-13)

- Procedure step 0: locate the checkout first (`src/integrations/models/gpt.ts`
  under CWD); absent → ask once, never hunt the filesystem.
- Prompted by a run from a non-checkout folder that went directory-hunting.

## v2 (2026-10-13)

- Full live-source ladder (OpenRouter → models.dev → LiteLLM → official
  docs → search), max-output requiring two agreeing sources.
- Disagreeing-sources quarantine rule (prompted by the spark 32k-vs-128k
  conflict).
- Codex bundled metadata as the Codex-route authority.
- Override-shadow check as final step (stale personal pins hide corrected
  tables).

## v1 (2026-10-13)

- Initial publish: purpose, OpenRouter primary source, precedence layers,
  file inventory, Codex-guardrail verdicts, dry-run-default procedure.
