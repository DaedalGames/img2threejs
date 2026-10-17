# Pi (`@earendil-works/pi-coding-agent`)

**Tier 3 (CLI) — open-source.** Best for npm-native JS/TS stacks and
for the playground container, which pins the same family. Reads
`AGENTS.md` from the project root.

## Install

`@mariozechner/pi-coding-agent` is the same upstream package (the
`pi-mono` monorepo); install either:

```bash
npm install -g @earendil-works/pi-coding-agent
# or: npm install -g @mariozechner/pi-coding-agent
```

`playground/Dockerfile.procedural` pins
`@earendil-works/pi-coding-agent@0.85.1` for the procedural image, so
anything linked under `~/.pi/skills/` on the host resolves inside the
playground container when its `~/.pi` is mounted.

## What "working" looks like

```text
$ pi --version
# (Pi version line)
```

On this host (2026-10-17) `pi` is not installed; the install command
above is the documented one (`npm install -g @earendil-works/pi-coding-agent`).

## Skill loading path

Pi reads `AGENTS.md` from the project root and discovers skills under
its own per-user directory:

```text
~/.pi/skills/img2threejs  ->  <your checkout>   # Pi's skill path
```

## Manual symlink

The harness (`img2`) does not link Pi's skill path on `img2 add` — Pi is
not in `HOSTS`. The link above is the same "register this link as
yourself" workaround described for Hermes in
[`docs/PLUGINS.md`](../PLUGINS.md#host-skills): the plugin still lives
under `$IMG2_HOME/plugins/<id>`, the symlink is what makes its
`SKILL.md` visible to the agent.

## Troubleshooting

| Symptom                                                       | Cause                                                              | Action                                                                                  |
|---------------------------------------------------------------|--------------------------------------------------------------------|-----------------------------------------------------------------------------------------|
| `pi: command not found` after install                         | Global `node_modules/.bin` not on `PATH` for the current shell      | `npm config get prefix` → append `/bin` to `PATH` in `~/.bashrc` / `~/.zshrc`            |
| Skill link present but Pi doesn't see it                      | Symlink target wrong; check the link resolves                      | `readlink -f ~/.pi/skills/img2threejs` should point at the checkout directory            |
| Playground container can't see the host skill                 | Container's `~/.pi` is not mounted and `skills/` is not populated   | Mount the host's `~/.pi/` into the container, or copy/link the skill folder per build   |

## Cross-references

- [`docs/INSTALL.md`](../INSTALL.md) — base-skill install (Pi is not in
  the `HOSTS` table).
- [`docs/PLUGINS.md`](../PLUGINS.md#host-skills) — absolute-path
  workaround for plugin `SKILL.md` under Pi.
- Harness `HOSTS` table (`bin/img2threejs.mjs`) — `{hermes, claude,
  codex, opencode}` only; Pi is not a row.