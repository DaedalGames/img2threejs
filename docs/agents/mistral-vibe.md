# Mistral Vibe

**Tier 3 (CLI) — open-source, Apache-2.0.** Best when you want Mistral
models behind a `uv` tool install with no extra dependencies — Vibe
ships its own installer that puts `uv` in place if it's missing.

## Install

Bootstrap with the official installer; it places `uv` (if missing) and
then installs `mistral-vibe` as a `uv` tool:

```bash
curl -LsSf https://mistral.ai/vibe/install.sh | bash
```

## What "working" looks like

```text
$ vibe --version
# (Mistral Vibe version line)
```

On this host (2026-10-17) `vibe` is not installed; the install command
above is the documented one from `https://mistral.ai/vibe/install.sh`.

## Skill loading path

Mistral Vibe reads `AGENTS.md` from the project root plus whatever
project-level rules you ship. Like Aider, it has no host-skills
directory baked in.

Practical pattern:

- Drop an `AGENTS.md` at the repo root that links to this skill, **or**
- Load the skill's `SKILL.md` directly in the conversation.

## Manual symlink

There is no Vibe-blessed `<host-skills-dir>/img2threejs` location; the
practical pattern is the same as for Aider — drop an `AGENTS.md` at the
repo root pointing at the checkout, or load `SKILL.md` per session.

```text
# Optional convention — not auto-picked-up; place for organisation.
# (no canonical path; use AGENTS.md as the practical link)
```

For harness-installed plugins, read steps by their printed absolute
path — [`docs/PLUGINS.md`](../PLUGINS.md#host-skills).

## Troubleshooting

| Symptom                                                       | Cause                                                                                | Action                                                                                          |
|---------------------------------------------------------------|--------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------|
| `uv: command not found` after a partial install                | The installer puts `uv` in `~/.local/bin`; current shell hasn't picked it up          | `export PATH="$HOME/.local/bin:$PATH"`; re-run                                                 |
| Vibe doesn't see `AGENTS.md`                                  | Vibe only reads `AGENTS.md` from the cwd / project root                               | Verify you're running from the repo root, and that `AGENTS.md` exists at that path             |
| Plugin `SKILL.md` not visible                                 | Vibe is not in the harness `HOSTS` table; no `img2 add` link                          | Read steps by their printed absolute path (see [`docs/PLUGINS.md`](../PLUGINS.md#host-skills))   |

## Cross-references

- [`docs/INSTALL.md`](../INSTALL.md) — base-skill install (Vibe is not
  in the `HOSTS` table).
- [`docs/PLUGINS.md`](../PLUGINS.md#host-skills) — absolute-path
  workaround for plugin `SKILL.md` under Vibe.
- Harness `HOSTS` table (`bin/img2threejs.mjs`) — `{hermes, claude,
  codex, opencode}` only; Vibe is not a row.
- Licence: Apache-2.0.