---
name: model-limits-audit
description: Audit GPT and Codex model context windows and max-output limits against live data, and propose descriptor or catalog updates. Use when model limits look stale, after OpenAI ships new models, or on a schedule to catch drift.
---

# Model Limits Audit

## Purpose

Detect staleness in hardcoded model limits (`contextWindow`, `maxOutputTokens`)
and propose updates to the right layer. Default mode only reports. Nothing is
written unless `--apply` is passed explicitly.

## Live source

Primary: `https://openrouter.ai/api/v1/models` (public GET, no key).
Relevant fields per entry: `id`, `context_length`,
`top_provider.max_completion_tokens`.

Full ladder when the primary lacks an id — check in order, first hit wins
for context, but max-output needs two agreeing sources:
`https://models.dev/api.json` (`limit.context`, `limit.output`),
the LiteLLM mirror
(`model_prices_and_context_window.json`: `max_input_tokens`,
`max_output_tokens`), official per-model docs pages, then web search.
`https://api.openai.com/v1/models` is id-existence only (carries no limits).

For Codex-route numbers the authoritative source is OpenAI's own Codex
bundled metadata (the `openai/codex` repo; precedent: PR #33972 set the 5.6
family to 272,000) — not the API spec, which describes a different route.

Normalize ids before joining: lowercase, strip any `openai/` prefix and any
`?...` query suffix, drop `:batch` variants.

## Repo layers (in precedence order)

Highest precedence first — this decides WHERE a fix belongs:

1. Exact env override (`CLAUDE_CODE_OPENAI_CONTEXT_WINDOWS`)
2. Built-in route catalog (`src/integrations/vendors/`, `src/integrations/gateways/`)
3. Prefix env override
4. Settings `modelLimits` in settings.json
5. `customProviders` lane default
6. Discovery cache
7. Shared model descriptor (`src/integrations/models/`)

The resolver is `resolveModelRuntimeLimits` in
`src/integrations/runtimeMetadata.ts`. Re-read it before changing precedence.

## Files to inventory

- `src/integrations/models/gpt.ts` — shared descriptors, including the
  intentional 272k Codex-transport caps
- `src/integrations/vendors/openai.ts` — direct-OpenAI catalog (full spec)
- `src/integrations/models/opencode.ts` — Zen and Go gateway models
- `src/integrations/models/openai-compatible-alias.ts` — gateway aliases
- `src/integrations/gateways/opencode.ts`, `github.ts` — catalog references

## Verdict rules

- Codex-constrained models (`gpt-5.6-*`, `gpt-5.5` on the Codex transport at
  `chatgpt.com/backend-api/codex`): KEEP the conservative 272k descriptor even
  when live spec is 1M. Refresh the comment with the fetch date instead.
  Rationale: live spec caused late auto-compact and mid-turn 500s (issue
  #1118). Enforced by `src/utils/context.test.ts` — a failure there means the
  guardrail is working, not broken.
- Direct-route catalogs: update to live spec where stale.
- Missing from live source (e.g. spark variants, new minis): leave untouched,
  flag for docs confirmation. Never invent limits.
- Suspect guard: live value showing a drop over 50 percent or zero
  max-output is quarantined until docs confirm. Never write it.
- Disagreeing live sources (e.g. one mirror says 32k, another 128k): do NOT
  pick a winner. Keep the repo value, annotate the conflict with both numbers
  and the date, and move on. A wrong "correction" truncates generations or
  invites 400s; an annotated conflict costs nothing.
- NEVER add built-in limits to `src/utils/model/openaiContextWindows.ts` —
  that module is override plumbing only. One-off user pins go to `modelLimits`
  or `/set-context-window --save`.

## Procedure

1. Load every repo table listed above into one normalized map.
2. Fetch the live list and build the lookup map.
3. Diff per model: repo context vs live, repo max-output vs live, owning
   layer, verdict per the rules.
4. Report as a markdown table with a `lastChecked` date and the source URL.
5. With `--apply` only: write descriptor or catalog edits, refresh
   provenance comments with the fetch date, and update `gpt56Catalog.test.ts`
   expectations in the same change.
6. Run the focused suites
   (`gpt56Catalog`, `runtimeMetadata.modelLimits`, `context`) and require
   green before finishing.
7. Remind the operator to check shadowing overrides: a session
   (`/set-context-window status`) or `modelLimits` pin outranks the tables on
   routes with no catalog entry, so a stale personal pin can hide a corrected
   table. Tables fixed + pin stale = job half done.

## Reference

- Worked example: the 2026-10-13 audit this skill was built from (design,
  plan, report, dry-run evidence) lives alongside it in the author's plans
  directory — ask the operator where their plans folder is if you need it.
- Authoring rules: `docs/integrations/how-to/add-model.md`, relative to the
  openclaude repo checkout this skill runs in.
