# Warp (Warp Agent CLI)

**Tier 3 (CLI) — AGPL-3.0 (open-sourced 2026-04-28,
[`warpdotdev/warp`](https://github.com/warpdotdev/warp)).** Best when
you want a terminal-native agent that scans a broad conventions list —
a repository already configured for any of the §1 hosts (or other §7
agents) is picked up by Warp without an extra link.

## Install

The **Warp Agent CLI** is a standalone `warp` command that runs in any
terminal, separate from the Warp GUI:

```bash
curl -fsSL https://app.warp.dev/download/agent-cli | bash
# or, on macOS 14+ / Linux:
brew install --cask warp-agent-cli
```

## What "working" looks like

```text
$ warp --version
# (Warp Agent CLI version line)
```

On this host (2026-10-17) `warp` is not installed; the install command
above is the documented one from `https://app.warp.dev/download/agent-cli`.

## Skill loading path

The CLI reads `AGENTS.md` (or the legacy `WARP.md`) automatically from
the repository root and the current working directory, with
subdirectory rules as best-effort, plus a personal `~/.agents/AGENTS.md`
that applies across every project.

Skill discovery scans a **broad conventions list**:

- `.agents/skills/`
- `.warp/skills/`
- `.claude/skills/`
- `.codex/skills/`
- `.cursor/skills/`
- `.gemini/skills/`
- `.copilot/skills/`
- `.factory/skills/`
- `.github/skills/`
- `.opencode/skills/`

So a repository already configured for any of the §1 hosts (or the
other §7 agents) is picked up by Warp without an extra link.

## Manual symlink

There is no Warp-blessed `<host-skills-dir>/img2threejs` location
beyond the conventions list; follow the same one-checkout/many-symlinks
rule from [`INSTALL.md` §1](../INSTALL.md#1-install-the-skill-into-this-host)
and point the symlink at whichever of the above paths the CLI will
scan, **or** drop an `AGENTS.md` that names the checkout.

```text
# Pick any one of the conventions-list paths above; the CLI will scan it.
~/.config/warp/skills/img2threejs -> <your checkout>   # not auto-scanned; illustrative
```

The harness does not link any of those paths on `img2 add` (Warp is
not in `HOSTS`); the absolute-path workaround in
[`docs/PLUGINS.md`](../PLUGINS.md#host-skills) is what makes a plugin's
`SKILL.md` visible under Warp.

## Troubleshooting

| Symptom                                                       | Cause                                                                              | Action                                                                                          |
|---------------------------------------------------------------|------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------|
| Warp doesn't pick up the project's `AGENTS.md`                | Run from outside the repo root; or `AGENTS.md` is at a non-convention path         | Run `warp` from the repo root, or symlink `AGENTS.md` into `.agents/`                            |
| Skill folder created but Warp doesn't see it                  | Skill path is outside the conventions-list above                                    | Symlink it into one of: `.agents/skills/`, `.warp/skills/`, etc.                                |
| Plugin `SKILL.md` missing under Warp                          | Warp is not in the harness `HOSTS` table; no `img2 add` link                        | Read steps by their printed absolute path (see [`docs/PLUGINS.md`](../PLUGINS.md#host-skills))   |

## Cross-references

- [`docs/INSTALL.md`](../INSTALL.md) — base-skill install (Warp is not
  in the `HOSTS` table).
- [`docs/PLUGINS.md`](../PLUGINS.md#host-skills) — absolute-path
  workaround for plugin `SKILL.md` under Warp.
- Harness `HOSTS` table (`bin/img2threejs.mjs`) — `{hermes, claude,
  codex, opencode}` only; Warp is not a row.
- Licence: AGPL-3.0 (terminal).