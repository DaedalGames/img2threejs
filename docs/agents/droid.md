# Droid (Factory AI)

**Tier 3 (CLI) — open-core.** Best for agent-shape features the other
Tier-3 hosts do not have: **Custom Droids** (`/droids`), **Missions**
(`/missions`, `droid exec --mission`), and a `Plugins` mechanism
(`/plugins`, `droid plugin install`).

## Install

Factory's official script (Homebrew, npm, and Windows variants exist
too):

```bash
curl -fsSL https://app.factory.ai/cli | sh
# or: brew install --cask droid
# or: npm install -g droid
```

## What "working" looks like

```text
$ droid --version
# (Droid CLI version line)
```

On this host (2026-10-17) `droid` is not installed; the install command
above is the documented one from `https://app.factory.ai/cli`.

## Skill loading path

Droid reads `AGENTS.md` from the project root when present. The
`/skills` and `/plugins` slash commands are the entry points for
placing skill folders; the practical pattern is:

```text
# Optional convention — the directory `/plugins` watches.
~/.factory/skills/img2threejs  ->  <your checkout>
```

For a Custom Droid's system prompt, place the skill next to it.

## Manual symlink

```text
# Optional convention — the directory Droid's `/plugins`/`/skills`
# commands look in.
~/.factory/skills/img2threejs  ->  <your checkout>
```

Like the others, Droid is not in `HOSTS`, so a plugin's `SKILL.md` is
not auto-linked; reach for the absolute-path workaround in
[`docs/PLUGINS.md`](../PLUGINS.md#host-skills) when a run needs plugin
context under Droid.

## Troubleshooting

| Symptom                                                       | Cause                                                              | Action                                                                                  |
|---------------------------------------------------------------|--------------------------------------------------------------------|-----------------------------------------------------------------------------------------|
| `droid: command not found` after install                      | The installer shim isn't on `PATH` for the current shell            | `export PATH="$HOME/.local/bin:$PATH"` (or wherever the installer placed it); re-source   |
| Droid ignores `AGENTS.md`                                     | `AGENTS.md` only loads when present at the cwd or project root     | Run `droid` from the repo root, or symlink the file in                                       |
| Custom Droid not loading the skill system prompt              | Skill not next to the Droid's prompt file                          | Move / symlink so the Droid's `system_prompt.md` lives alongside `skills/img2threejs/`     |

## Cross-references

- [`docs/INSTALL.md`](../INSTALL.md) — base-skill install (Droid is not
  in the `HOSTS` table).
- [`docs/PLUGINS.md`](../PLUGINS.md#host-skills) — absolute-path
  workaround for plugin `SKILL.md` under Droid.
- Harness `HOSTS` table (`bin/img2threejs.mjs`) — `{hermes, claude,
  codex, opencode}` only; Droid is not a row.