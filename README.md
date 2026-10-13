# openclaude-skills

Focused skill collection for **OpenClaude** — skills that name OpenClaude paths, commands, or repo layout. Nothing here is meant to be harness-generic.

## Focused vs general: where a skill belongs

This repo holds skills that only make sense inside OpenClaude (or its source tree). The split rule:

- Mentions a harness name, repo path, or provider-specific command → **here**
- Works pasted into any agent (Claude Code, Codex, opencode, ...) → the **general skills repo**, cloned to `~/.agents/skills` (OpenClaude scans that as fallback, so general skills stay visible here for free)

## Skills

| Skill | Version | What it does | Use when |
|---|---|---|---|
| `model-limits-audit` | 6 | Diffs hardcoded GPT/Codex `contextWindow` + `maxOutputTokens` against live data and proposes descriptor/catalog fixes. Dry-run by default, writes only with `--apply`. | Model limits look stale, OpenAI ships new models, or on a schedule to catch drift |

## Install

From a clone of this repo. Replace `<skill>` with the folder name.

**Into the current project** (travels with the repo for every contributor):

```bash
openclaude skills install ./skills/<skill>
```

**Into your user directory** (available on every machine where you clone this repo):

```bash
openclaude skills install ./skills/<skill> --global
```

**Directly from GitHub** (no clone):

```bash
openclaude skills install https://raw.githubusercontent.com/MotionDevLab/openclaude-skills/main/skills/<skill>/SKILL.md --sha256 <64-hex-digest>
```

Compute the digest of the exact file being installed:

```bash
# Windows
certutil -hashfile skills/<skill>/SKILL.md SHA256
# macOS / Linux
sha256sum skills/<skill>/SKILL.md
```

**Manage afterwards:**

```bash
openclaude skills list            # what is installed, with status
openclaude skills show <skill>    # source, trust tier, full text
openclaude skills verify          # check revocations for registry installs
openclaude skills remove <skill>  # delete a project skill (--global for user)
```

## Adding a skill

1. Copy the layout: `skills/<name>/SKILL.md` (frontmatter `name` + `description` required).
2. Follow `docs/skill-template.md` — keep it procedural, no credentials, no `curl | sh`.
3. Validate locally before pushing: `openclaude skills validate ./skills/<name>`.
4. One-line entry in the table above.
5. CI validates every skill folder on push and PR.

## Versioning

No `skill.json` sidecars until a registry exists — hand-written skills verify as
"skipped, not failed," and a hand-made sidecar buys nothing. When publishing to
a registry, the installer writes `skill.json` (id, version, digest) itself.

## License

MIT — matches the OpenClaude ecosystem. Change this file if you choose otherwise.
