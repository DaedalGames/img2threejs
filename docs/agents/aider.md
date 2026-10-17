# Aider

**Tier 3 (CLI) — open-source.** Best for diff-style edits on a single
file or branch where you want every change shown and reversible.
Aider is Python, MIT, and predates most of the Tier-1 crowd.

## Install

Pull from PyPI. Pick whichever Python package manager is on `PATH`:

```bash
pip install aider-chat         # canonical; needs pip
# or: pipx install aider-chat   # isolated env, no system pip needed
# or: uv tool install aider-chat # when uv is present but pip is not
```

`aider-chat` depends on `scipy`, whose build pulls in `meson` and
needs a Fortran compiler (`gfortran` and `pkg-config` on Debian/Ubuntu).
On a pip-less host where `uv` is the only Python tool, `uv tool install`
will surface the build failure clearly — install `gfortran` and
`pkg-config` first, or switch to a prebuilt wheel (`pip install
aider-chat --only-binary=:all:`).

Pin a specific provider model with `--model` (e.g. `aider --model
gpt-4o`); the install command itself doesn't require any API keys —
configure per-provider via env vars (`OPENAI_API_KEY`,
`ANTHROPIC_API_KEY`, …) before the first run.

## What "working" looks like

```text
$ aider --version
# (Aider version line)
```

On this host (2026-10-17) `aider` is not installed; the install command
above is the documented one (`pip install aider-chat`). Aider-chat v0.86+
pulls scipy 1.15+, whose build needs `gfortran` + `pkg-config` on
Debian/Ubuntu — without those, `uv tool install` fails at the meson
stage before any `aider` binary is produced.

## Skill loading path

Aider reads conventions from any of these, in order:

- `AGENTS.md` at the project root (`agents.md` standard) — add it to
  Aider's `read` list.
- `CONVENTIONS.md` at the project root — the historical convention;
  load with `/read CONVENTIONS.md` or `aider --read CONVENTIONS.md`.
- `.aider.conf.yml` (also `.aider.model.settings.yml`) — per-project
  YAML, always loaded. Pin `read:` so `AGENTS.md` / `CONVENTIONS.md`
  loads automatically.

`.aider*` files in the repo root are Aider-managed state (history,
caches, model settings) — **not** the skill link.

## Manual symlink

There is no Aider-blessed `<host-skills-dir>/img2threejs` location. The
practical pattern is `read: [SKILL.md]` in `.aider.conf.yml`, or pass it
on the command line:

```text
# Optional convention — not auto-picked-up by Aider, but a place to
# keep the skill alongside other agent directories.
~/.aider/skills/img2threejs  ->  <your checkout>

# Practical pattern — point `read:` at the skill's SKILL.md.
# In .aider.conf.yml:
#   read: [SKILL.md]
```

Or invoke per session:

```bash
aider --read SKILL.md
```

## Troubleshooting

| Symptom                                                       | Cause                                                                                | Action                                                                                          |
|---------------------------------------------------------------|--------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------|
| Aider doesn't read `AGENTS.md` automatically                  | `read:` not set in `.aider.conf.yml`; Aider does not auto-pick up files outside `read:` | Add `read: [AGENTS.md]` to `.aider.conf.yml`, or pass `aider --read AGENTS.md` per session       |
| Files in `~/.aider/` are Aider-managed, not the skill link     | Aider writes history / cache into the `~/.aider` tree; confusing it with a skill dir is easy | Keep the skill symlink at `~/.aider/skills/img2threejs`, but don't expect Aider to walk `skills/`  |
| Plugin `SKILL.md` missing under Aider                         | Aider is not in the harness `HOSTS` table; no `img2 add` link                          | `aider --read <abs-path>` per session, or `read:` in `.aider.conf.yml`; see [`docs/PLUGINS.md`](../PLUGINS.md#host-skills) |

## Cross-references

- [`docs/INSTALL.md`](../INSTALL.md) — base-skill install (Aider is not
  in the `HOSTS` table).
- [`docs/PLUGINS.md`](../PLUGINS.md#host-skills) — absolute-path
  workaround for plugin `SKILL.md` under Aider.
- Harness `HOSTS` table (`bin/img2threejs.mjs`) — `{hermes, claude,
  codex, opencode}` only; Aider is not a row.