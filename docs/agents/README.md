# Coding agents supported by img2threejs

The base skill's `HOSTS` table (`bin/img2threejs.mjs`) only knows four
agents — `hermes`, `claude`, `codex`, `opencode`. The base-skill `img2
install`/`img2 add` commands link skill directories for those four, and
nothing else.

This directory documents the **other fifteen** agents the skill works
under, broken into three tiers:

- **Tier 1 — mass-market, opinionated.** Cursor, JetBrains Junie, AWS
  Kiro. Each picked its own instruction-loading convention before the
  open `Agent Skills` standard stabilised; all three now ship the
  standard for skills but each has a host-specific rule file that must
  live at a specific path.
- **Tier 3 (CLI) — open-source terminals.** Aider, Pi, Mistral Vibe,
  Droid, Warp, Amp, Auggie, Charm Crush, Trae. All nine converge on the
  `AGENTS.md` convention for project instructions; the `img2` link is a
  directory convention you create by hand.
- **Tier 3 (IDE-with-CLI).** Windsurf, Zed, Continue. Each runs an agent
  from inside an IDE rather than as a standalone CLI; project-rule
  loading is built in.

For every agent in this directory, the rule is the same:

1. Drop the skill where the agent reads it from — every per-agent file
   names the exact path.
2. For a **plugin** `SKILL.md` (an `img2 add <id>` install), none of the
   15 is in the harness `HOSTS` table — read steps by their printed
   absolute path. See [`docs/PLUGINS.md`](../PLUGINS.md) "Host skills".

## Comparison table

| Agent       | Tier | Install command                                         | Skill-loading path                                       | Note                                |
|-------------|------|---------------------------------------------------------|----------------------------------------------------------|-------------------------------------|
| Cursor      | 1    | `curl https://cursor.com/install -fsS \| bash`          | `.cursor/skills/<name>/SKILL.md`, `~/.cursor/skills/`    | Uses `.cursor/rules/*.mdc` (not `.md`) |
| Junie       | 1    | `curl -fsSL https://junie.jetbrains.com/install.sh \| bash` | `.junie/skills/<name>/SKILL.md`, `~/.junie/skills/` | Prefers `.junie/AGENTS.md`          |
| Kiro        | 1    | `curl -fsSL https://cli.kiro.dev/install \| bash`       | `.kiro/skills/<name>/SKILL.md`, `~/.kiro/skills/`        | Steering in `.kiro/steering/*.md`   |
| Aider       | 3    | `pip install aider-chat`                                | project-root `AGENTS.md` / `CONVENTIONS.md`              | Add to `.aider.conf.yml` `read:` list |
| Pi          | 3    | `npm install -g @earendil-works/pi-coding-agent`        | `~/.pi/skills/<name>/SKILL.md`                           | Reads `AGENTS.md` from root         |
| Mistral Vibe| 3    | `curl -LsSf https://mistral.ai/vibe/install.sh \| bash` | project-root `AGENTS.md`                                 | Apache-2.0                          |
| Droid       | 3    | `curl -fsSL https://app.factory.ai/cli \| sh`           | `~/.factory/skills/` (via Plugins) or `/skills`          | Custom Droids + Missions            |
| Warp        | 3    | `curl -fsSL https://app.warp.dev/download/agent-cli \| bash` | scans `.agents/`, `.warp/`, `.claude/`, `.codex/` ... | AGPL-3.0 (terminal)                 |
| Amp         | 3    | `curl -fsSL https://ampcode.com/install.sh \| bash`     | walks cwd + parents for `AGENTS.md`                      | Sourcegraph, proprietary client      |
| Auggie      | 3    | `npm install -g @augmentcode/auggie`                    | walks `AGENTS.md` / `CLAUDE.md`                          | Augment subscription required         |
| Crush       | 3    | `brew install charmbracelet/tap/crush`                  | `./.crush/skills/`, `~/.config/crush/skills/`            | FSL-1.1-MIT (Fair Source)           |
| Trae        | 3    | `git clone … && uv sync --all-extras`                   | `AGENTS.md` opt-in (Settings → Rules & Memories)         | MIT, research / audit log oriented  |
| Windsurf    | 3    | download editor from <https://codeium.com/windsurf>     | `.windsurf/rules/*.md`, `~/.codeium/windsurf/memories/`  | IDE-only (Cascade)                  |
| Zed         | 3    | download editor from <https://zed.dev>                  | `AGENTS.md` (worktree-root only), `~/.config/zed/AGENTS.md` | Agent Panel, IDE-only          |
| Continue    | 3    | `npm i -g @continuedev/cli`                             | `.continue/rules/*.md`, `~/.continue/config.yaml`        | Launcher is `cn`                    |

## Choose your agent

This is the question people ask first. Pick by the workflow you already have,
not by what's newest:

- **You live in an IDE and want AI to feel native.** Cursor, Junie, Kiro,
  Windsurf, Zed, Continue — Tier 1 if you want it to feel like a
  product, Tier 3 if you already use the IDE for other reasons.
- **You live in a terminal and want a scriptable CLI.** Aider, Pi,
  Mistral Vibe, Droid, Warp, Amp, Auggie, Crush, Trae — Tier 3 (CLI).
- **You want to A/B two agents against the same task.** Run one Tier 1
  and one Tier 3 CLI in parallel; both will load the same
  `AGENTS.md` (Tier 1 picks it up as a fallback or via Junie's
  import-on-first-open scan).

The base-skill link (`<host-skills-dir>/img2threejs -> <your checkout>`)
is the same shape regardless of agent — what changes is **where the
agent reads skills from**. Each per-agent file below names the path, the
install command, and the manual symlink you create.

## What's common across all 15

None of the fifteen is in the base-skill `HOSTS` table. That means two
things:

- The **base-skill link** is something you create by hand — the same
  one-checkout/many-symlinks idea from [`INSTALL.md` §1](../INSTALL.md#1-install-the-skill-into-this-host).
  Each per-agent file documents the exact path.
- The **plugin-skill link** (`img2 add <plugin-id>`) is **not** created
  by `img2 add` for any of these hosts — the harness only links
  `claude`, `codex`, `opencode`. To get a plugin's `SKILL.md` visible
  to, say, Cursor, drop the same kind of symlink at the host's skill
  path, or load the absolute path printed by `img2 list` directly in
  the conversation. See [`docs/PLUGINS.md`](../PLUGINS.md) "Host skills".

## AGENTS.md, the standard that connects them

`AGENTS.md` is a host-agnostic project-instructions convention adopted by
Aider, Pi, Mistral Vibe, Droid, Warp, Amp, Auggie, Crush, Zed, and
Continue. Tier 1 hosts (Cursor, Junie, Kiro) read it as a fallback, and
Junie offers an import-on-first-open scan that copies foreign agent
files into `.junie/AGENTS.md`. This repo's canonical shared-instructions
file today is `CLAUDE.md`; drop an `AGENTS.md` next to it if your
primary host is one of the 15 — most will pick it up automatically.

The full per-agent contract is in the per-agent files below.