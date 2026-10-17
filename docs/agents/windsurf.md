# Windsurf (Cascade)

**Tier 3 (IDE-with-CLI) — proprietary, free tier + paid plans.** Best
for developers already using Windsurf as their editor; Cascade is the
agent inside the IDE. There is no standalone CLI — Windsurf is editor
only.

## Install

Download the editor from <https://codeium.com/windsurf>. Cascade is
bundled; sign in once at first launch.

## What "working" looks like

No CLI to verify on disk — the install is the editor binary, the
launcher is the application icon / desktop entry. On this host
(2026-10-17) Windsurf is not installed; the editor binary is at
<https://codeium.com/windsurf>.

## Skill loading path

Cascade picks up project-level rules from any of these at the repo
root:

- `.windsurf/rules/*.md` — modern format: one `.md` file per rule,
  each with YAML frontmatter declaring its activation mode
  (`always_on`, `model_decision`, `glob`, `manual`). Per-file cap:
  12 000 characters.
- `.windsurfrules` — legacy flat-file format, still supported; all
  rules in one numbered list, all always-on.
- `AGENTS.md` — simple directory-scoped fallback, no frontmatter.

Global rules that apply across every project live at
`~/.codeium/windsurf/memories/global_rules.md` (6 000-character cap)
and stack on top of the project rules.

## Manual symlink

Windsurf has no host-specific **skill** directory; it uses
**rules**. The practical pattern is:

```text
# Project rules directory — Windsurf scans this on open.
.windsurf/rules/img2threejs.md  # a one-rule file with @-mention or
                                 # short body summarising SKILL.md

# Or — use AGENTS.md as the host-agnostic fallback.
AGENTS.md   # at the repo root, summarising or pointing at SKILL.md
```

There is no Windsurf-blessed `<host-skills-dir>/img2threejs` location;
follow [`INSTALL.md` §1](../INSTALL.md#1-install-the-skill-into-this-host)'s
manual rule and link the skill where Cascade's loader can reach it
(`.windsurf/rules/*.md` or `AGENTS.md`). For plugin `SKILL.md`, use
the absolute-path workaround in
[`docs/PLUGINS.md`](../PLUGINS.md#host-skills).

## Troubleshooting

| Symptom                                                       | Cause                                                                | Action                                                                                  |
|---------------------------------------------------------------|----------------------------------------------------------------------|-----------------------------------------------------------------------------------------|
| Rule dropped in `.windsurf/rules/foo.md` is always-off        | File is missing the YAML frontmatter (`always_on`, `model_decision`, `glob`, `manual`) | Add frontmatter; or use the legacy `.windsurfrules` flat file                           |
| Cascade ignores `AGENTS.md` from a sub-directory                | `AGENTS.md` only loads at the project root                            | Move/symlink the file to the repo root                                                   |
| Global rule not stacked on top of project rules                | Path wrong; Windsurf's global path is exact                          | Verify `~/.codeium/windsurf/memories/global_rules.md`                                    |

## Cross-references

- [`docs/INSTALL.md`](../INSTALL.md) — base-skill install (Windsurf is
  not in the `HOSTS` table).
- [`docs/PLUGINS.md`](../PLUGINS.md#host-skills) — absolute-path
  workaround for plugin `SKILL.md` under Windsurf.
- Harness `HOSTS` table (`bin/img2threejs.mjs`) — `{hermes, claude,
  codex, opencode}` only; Windsurf is not a row.