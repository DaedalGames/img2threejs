# Amp (Sourcegraph)

**Tier 3 (CLI) — proprietary client, free during early access.** Best
when you already use Sourcegraph code search and want the same
provider on a CLI; Amp walks up the directory tree for `AGENTS.md` and
understands `@doc/...` mentions inside it.

## Install

Sourcegraph's install script (Homebrew and npm are also published):

```bash
curl -fsSL https://ampcode.com/install.sh | bash
# or: brew install amp
# or: npm install -g @sourcegraph/amp@latest   # @ampcode/cli is the newer package name
```

## What "working" looks like

```text
$ amp --version
# (Amp version line)
```

After install, `amp login` opens browser-based auth (Sourcegraph
account). On this host (2026-10-17) `amp` is not installed; the install
command above is the documented one from `https://ampcode.com/install.sh`.

## Skill loading path

Amp runs on macOS, Linux, and Windows through WSL. It reads `AGENTS.md`
from:

- the cwd
- every parent directory
- each subtree (best-effort)
- `~/.config/amp/AGENTS.md`
- `~/.config/AGENTS.md`
- `/etc/ampcode/AGENTS.md`
  (`/Library/Application Support/ampcode/AGENTS.md` on macOS,
  `%ProgramData%\ampcode\AGENTS.md` on Windows)

Disable with `AMP_IGNORE_GUIDANCE_FILES='*'` if a project ships
guidance you do not want loaded. Amp also supports `@doc/...` mentions
inside `AGENTS.md` to import further instruction files (relative paths
resolve against the file containing the mention).

## Manual symlink

Amp has no host-specific skill directory convention; place the skill at
a path Amp's guidance loader already watches, or load `SKILL.md`
directly in the conversation. The most natural fit is the user-level
guidance file:

```text
# User-level guidance that applies to every project Amp sees.
~/.config/amp/AGENTS.md   # drop an @doc mention pointing at SKILL.md
# or, more directly:
~/.config/agents/skills/img2threejs  ->  <your checkout>
```

Like the others, Amp is not in `HOSTS`; reach for the absolute-path
workaround in [`docs/PLUGINS.md`](../PLUGINS.md#host-skills) when a run
needs plugin context under Amp.

## Troubleshooting

| Symptom                                                       | Cause                                                                | Action                                                                                  |
|---------------------------------------------------------------|----------------------------------------------------------------------|-----------------------------------------------------------------------------------------|
| Amp ignores an `AGENTS.md` in a parent dir                     | AMP_IGNORE_GUIDANCE_FILES or terminal wasn't in the right cwd        | `amp` from the repo root; `echo "$AMP_IGNORE_GUIDANCE_FILES"` (unset if present)          |
| `@doc/...` mention inside `AGENTS.md` is not resolved         | The mention is broken (relative path or typo), or the import is gated | Inspect the resolved path; `Amp` resolves relative paths against the file containing the mention |
| Plugin `SKILL.md` not visible under Amp                        | Amp is not in the harness `HOSTS` table; no `img2 add` link           | Read steps by their printed absolute path (see [`docs/PLUGINS.md`](../PLUGINS.md#host-skills)) |

## Cross-references

- [`docs/INSTALL.md`](../INSTALL.md) — base-skill install (Amp is not
  in the `HOSTS` table).
- [`docs/PLUGINS.md`](../PLUGINS.md#host-skills) — absolute-path
  workaround for plugin `SKILL.md` under Amp.
- Harness `HOSTS` table (`bin/img2threejs.mjs`) — `{hermes, claude,
  codex, opencode}` only; Amp is not a row.