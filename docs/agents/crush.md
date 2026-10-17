# Charm Crush

**Tier 3 (CLI) — FSL-1.1-MIT (Fair Source, source-visible with use
restrictions; not OSI-approved).** Built on the Charm stack (Bubble Tea
/ Lip Gloss / Glamour); first-class terminal support on macOS, Linux,
Windows (PowerShell + WSL), and the BSDs. Best when you want LSP-aware
edits and a SQLite-backed session model.

## Install

Homebrew or any of nine other package managers (npm, yay/Arch, nix,
FreeBSD pkg, Windows winget/scoop, Debian/RPM repos, `go install`):

```bash
brew install charmbracelet/tap/crush
# or:
npm install -g @charmland/crush
# or: yay -S crush-bin, nix run github:numtide/nix-ai-tools#crush,
#     winget install charmbracelet.crush, scoop install crush,
#     go install github.com/charmbracelet/crush@latest
```

## What "working" looks like

```text
$ crush --version
# (Crush version line)
```

On this host (2026-10-17) `crush` is not installed; the install command
above is the documented one (`brew install charmbracelet/tap/crush`).

## Skill loading path

Crush reads `AGENTS.md` — `crush.json`'s `options.initialize_as`
defaults to `AGENTS.md` — plus `CRUSH.md`, `CLAUDE.md`, `GEMINI.md`,
`.cursorrules`, `.github/copilot-instructions.md`, and their `.local`
variants.

Project skills directories default to `./skills` and `./.crush/skills`;
the global ones are `~/.config/crush/skills` and
`~/.config/agents/skills` (override the global list with
`CRUSH_SKILLS_DIR=<path>`).

LSP-enhanced (`gopls`, `typescript-language-server`, `rust-analyzer`,
`nil`, …) for language-aware context, MCP for adding stdio / HTTP /
SSE context servers.

## Manual symlink

```text
# Project skills directory (default).
./.crush/skills/img2threejs  ->  <your checkout>     # workspace, per-repo

# Global skill directory.
~/.config/crush/skills/img2threejs  ->  <your checkout>    # global, every project
# or
~/.config/agents/skills/img2threejs -> <your checkout>    # cross-agent (Warp, Junie, Pi)
```

Crush is not in `HOSTS`; reach for the absolute-path workaround in
[`docs/PLUGINS.md`](../PLUGINS.md#host-skills) when a run needs plugin
context under Crush.

## Troubleshooting

| Symptom                                                       | Cause                                                                | Action                                                                                  |
|---------------------------------------------------------------|----------------------------------------------------------------------|-----------------------------------------------------------------------------------------|
| Crush doesn't load `AGENTS.md`                                | `crush.json` overrides `options.initialize_as`                       | Set `options.initialize_as: AGENTS.md` in `crush.json` (project or `~/.config/crush/`)   |
| Skill folder created but Crush doesn't see it                  | Skill path is outside Crush's defaults                               | Verify `.crush/skills/` and `~/.config/crush/skills/`; override with `CRUSH_SKILLS_DIR=<path>` |
| Plugin `SKILL.md` not visible under Crush                     | Crush is not in the harness `HOSTS` table; no `img2 add` link         | Read steps by their printed absolute path (see [`docs/PLUGINS.md`](../PLUGINS.md#host-skills)) |

## Cross-references

- [`docs/INSTALL.md`](../INSTALL.md) — base-skill install (Crush is not
  in the `HOSTS` table).
- [`docs/PLUGINS.md`](../PLUGINS.md#host-skills) — absolute-path
  workaround for plugin `SKILL.md` under Crush.
- Harness `HOSTS` table (`bin/img2threejs.mjs`) — `{hermes, claude,
  codex, opencode}` only; Crush is not a row.
- Licence: FSL-1.1-MIT (Fair Source, not OSI-approved).