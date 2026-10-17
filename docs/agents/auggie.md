# Auggie (Augment Code)

**Tier 3 (CLI) — Augment custom proprietary licence.** Best for
enterprise codebases that already license the Augment context engine.
The same package powers the Augment VS Code and JetBrains extensions,
so an Augment IDE user is an Auggie user and vice-versa. An active
Augment subscription is required.

## Install

The npm CLI (Node.js 20+):

```bash
npm install -g @augmentcode/auggie
```

Sign in with `auggie login`, then `auggie "your prompt"` for the
full-screen interactive terminal — `--print` for non-interactive / CI,
`--quiet` to drop the trailing chatter.

## What "working" looks like

```text
$ auggie --version
# (Auggie version line)
```

On this host (2026-10-17) `auggie` is not installed; the install command
above is the documented one (`npm install -g @augmentcode/auggie`).

## Skill loading path

Auggie reads `AGENTS.md` and `CLAUDE.md` hierarchically from the
workspace root downward into parent directories — only `AGENTS.md` /
`CLAUDE.md` are discovered this way. `.augment/rules/*.md` files are
loaded only from the workspace root.

The `--rules <path>` flag appends an extra file on top of the
discovered ones, and `~/.augment/rules/` adds user-wide rules that
apply across every project.

## Manual symlink

```text
# Optional convention — workspace-level rules directory Auggie watches.
.augment/rules/img2threejs  ->  <your checkout>      # workspace, root only

# User-wide rules that apply across every project.
~/.augment/rules/img2threejs -> <your checkout>      # global
```

The harness does not write to any Auggie path on `img2 add` (Augment
is not in `HOSTS`); the absolute-path workaround in
[`docs/PLUGINS.md`](../PLUGINS.md#host-skills) is the supported way to
read a plugin's `SKILL.md` under Auggie.

## Troubleshooting

| Symptom                                                       | Cause                                                                  | Action                                                                                  |
|---------------------------------------------------------------|------------------------------------------------------------------------|-----------------------------------------------------------------------------------------|
| `auggie: command not found` after install                     | Global `node_modules/.bin` not on `PATH`                                | `npm config get prefix` → append `/bin` to `PATH` in `~/.bashrc` / `~/.zshrc`            |
| `auggie login` redirects but the session doesn't persist       | Headless server / missing keyring; Augment auth needs interactive flow | Run from a desktop session, or set the Augment API key env var documented in `auggie login --help` |
| Plugin `SKILL.md` not visible under Auggie                    | Augment is not in the harness `HOSTS` table; no `img2 add` link         | Read steps by their printed absolute path (see [`docs/PLUGINS.md`](../PLUGINS.md#host-skills)) |

## Cross-references

- [`docs/INSTALL.md`](../INSTALL.md) — base-skill install (Auggie is
  not in the `HOSTS` table).
- [`docs/PLUGINS.md`](../PLUGINS.md#host-skills) — absolute-path
  workaround for plugin `SKILL.md` under Auggie.
- Harness `HOSTS` table (`bin/img2threejs.mjs`) — `{hermes, claude,
  codex, opencode}` only; Auggie is not a row.
- Licence: Augment proprietary — active subscription required;
  redistribution is not permitted; CLI distributed only via Augment's
  official npm registry.