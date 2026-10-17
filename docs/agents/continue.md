# Continue CLI (`cn`)

**Tier 3 (IDE-with-CLI) — open source (Apache-2.0).** The same package
powers the Continue extension in VS Code and JetBrains, so a Continue
user is a `cn` user and vice-versa. Best for developers who already
use Continue's extension and want the same rules engine on the CLI.

## Install

`npm i -g @continuedev/cli` (Node.js 20+). The launcher is `cn`:

```bash
npm i -g @continuedev/cli
```

## What "working" looks like

```text
$ cn --version
# (Continue CLI version line)
```

On this host (2026-10-17) `cn` is not installed; the install command
above is the documented one (`npm i -g @continuedev/cli`).

## Skill loading path

Configuration lives in `config.yaml` (default `~/.continue/config.yaml`,
also selectable per-invocation via `--config <path-or-hub-slug>`).
Rules are scanned from `.continue/rules/*.md` directories and from the
config, or passed inline with `--rule <file|hub-slug|string>`
(repeatable).

`cn` does **not** auto-load `AGENTS.md` — it *creates* one via
`cn init`, but does not read it back. Drop the install runbook into
`.continue/rules/install.md`, or invoke `cn --rule /path/to/AGENTS.md`
per session.

## Manual symlink

```text
# Project rules directory — Continue scans this on run.
.continue/rules/img2threejs.md  # a one-rule file with @-mention or
                                # short body summarising SKILL.md

# Global config that applies to every project.
~/.continue/config.yaml         # rules list, or per-session --rule flag
```

For plugin `SKILL.md`, use the absolute-path workaround in
[`docs/PLUGINS.md`](../PLUGINS.md#host-skills) — Continue is not in
the harness `HOSTS` table.

## Troubleshooting

| Symptom                                                       | Cause                                                                | Action                                                                                  |
|---------------------------------------------------------------|----------------------------------------------------------------------|-----------------------------------------------------------------------------------------|
| `cn: command not found` after install                        | Global `node_modules/.bin` not on `PATH`                              | `npm config get prefix` → append `/bin` to `PATH` in `~/.bashrc` / `~/.zshrc`            |
| `cn` ignores `AGENTS.md`                                      | `cn` writes `AGENTS.md` via `cn init` but does not read it back       | Drop the install runbook into `.continue/rules/install.md` or pass `cn --rule /path/to/AGENTS.md` per session |
| Rules load but the rule body is wrong                         | YAML config refers to a hub slug that doesn't resolve                | Use a local file path (`rules: - file: ./install.md`) instead of a hub slug               |

## Cross-references

- [`docs/INSTALL.md`](../INSTALL.md) — base-skill install (Continue is
  not in the `HOSTS` table).
- [`docs/PLUGINS.md`](../PLUGINS.md#host-skills) — absolute-path
  workaround for plugin `SKILL.md` under Continue.
- Harness `HOSTS` table (`bin/img2threejs.mjs`) — `{hermes, claude,
  codex, opencode}` only; Continue is not a row.