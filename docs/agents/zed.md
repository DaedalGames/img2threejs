# Zed (Agent Panel)

**Tier 3 (IDE-with-CLI) — open source (GPL/Apache).** Zed is an editor
with an Agent Panel — there is no standalone CLI; the Agent Panel
opens via the Command Palette (`agent: new thread`). Best for
developers already using Zed as their editor.

## Install

Download the editor from <https://zed.dev> (macOS, Linux, Windows). The
Agent Panel is built in; open it from the Command Palette.

## What "working" looks like

No CLI to verify on disk — the install is the editor binary. On this
host (2026-10-17) Zed is not installed; the editor binary is at
<https://zed.dev>.

## Skill loading path

Project instructions are loaded from the **first matching file** at the
repo root, in this order:

- `AGENTS.md` (Zed's primary)
- `.rules`
- `.windsurfrules`
- `.clinerules`
- `.github/copilot-instructions.md`
- `AGENT.md`
- `CLAUDE.md`
- `GEMINI.md`

Personal instructions (loaded on top of the project file) live at
`~/.config/zed/AGENTS.md`.

**Important:** Zed's current implementation reads the **worktree-root**
file only — nested `AGENTS.md` files in monorepos are silently
ignored, unlike Codex CLI / Claude Code / Cursor which walk up from
cwd.

## Manual symlink

There is no Zed-blessed `<host-skills-dir>/img2threejs` location for
project skills. The practical pattern is the same as for Windsurf:

```text
# Project instructions file Zed picks up at the worktree root.
AGENTS.md                       # at the repo root, summarising or
                                # pointing at SKILL.md

# Personal instructions, stacked on top of the project file.
~/.config/zed/AGENTS.md         # at the user level
```

For plugin `SKILL.md`, use the absolute-path workaround in
[`docs/PLUGINS.md`](../PLUGINS.md#host-skills) — Zed is not in the
harness `HOSTS` table.

## Troubleshooting

| Symptom                                                       | Cause                                                                  | Action                                                                                  |
|---------------------------------------------------------------|------------------------------------------------------------------------|-----------------------------------------------------------------------------------------|
| Zed ignores `AGENTS.md` from a subdirectory                   | Zed reads the worktree-root file only                                  | Place `AGENTS.md` at the repo root (no nesting)                                          |
| Personal instructions file not stacking                       | Path wrong; Zed's personal path is exact                                | Verify `~/.config/zed/AGENTS.md`                                                         |
| `AGENTS.md` not picked up at all                              | A higher-priority file (`.rules`, `.windsurfrules`, `.clinerules`, …) wins | Zed uses first-match; pick one of the supported filenames for your repo                  |

## Cross-references

- [`docs/INSTALL.md`](../INSTALL.md) — base-skill install (Zed is not
  in the `HOSTS` table).
- [`docs/PLUGINS.md`](../PLUGINS.md#host-skills) — absolute-path
  workaround for plugin `SKILL.md` under Zed.
- Harness `HOSTS` table (`bin/img2threejs.mjs`) — `{hermes, claude,
  codex, opencode}` only; Zed is not a row.