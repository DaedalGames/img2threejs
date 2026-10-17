# Trae Agent (ByteDance) — optional

**Tier 3 (CLI) — MIT,
[`bytedance/trae-agent`](https://github.com/bytedance/trae-agent),
arXiv:2507.23370.** A research-oriented agent with trajectory recording
for every step and pluggable LLM providers. Pick this only when you
specifically need the audit log or the multi-LLM swappability, since
`AGENTS.md` support is opt-in and still tracked as an open feature
request upstream.

## Install

Clone and bootstrap a venv — there is no prebuilt launcher today:

```bash
git clone https://github.com/bytedance/trae-agent
cd trae-agent && uv sync --all-extras && source .venv/bin/activate
trae
```

## What "working" looks like

```text
$ trae --help
# (Trae Agent help output)
```

On this host (2026-10-17) `trae` is not installed; the install command
above is the documented one (`git clone … && uv sync --all-extras`).

## Skill loading path

`AGENTS.md` is **opt-in**: Settings → Rules & Memories → "Include
`AGENTS.md` in the context". Until that toggle is on, project
instructions do not load. This is the single most common cause of "I
followed the install steps and Trae ignores my instructions."

## Manual symlink

There is no Trae-blessed `<host-skills-dir>/img2threejs` location. The
practical pattern is the same as for Aider and Vibe — place the skill
where Trae's loader can find it, or load `SKILL.md` directly:

```text
# Optional convention — keep the skill alongside the clone for clarity.
# (no canonical path; Trae has no host-skills directory convention)
```

For general coding-agent use, the eight agents above pick up `AGENTS.md`
automatically and are a better starting point.

## Troubleshooting

| Symptom                                                       | Cause                                                                  | Action                                                                                  |
|---------------------------------------------------------------|------------------------------------------------------------------------|-----------------------------------------------------------------------------------------|
| Trae ignores `AGENTS.md`                                      | The toggle is off (Settings → Rules & Memories → "Include AGENTS.md")  | Enable the toggle, or load `SKILL.md` directly in the conversation                       |
| `uv sync` fails on Python 3.13+                                | Trae's lockfile pins a specific older Python; Pypy-version drift       | Use `uv python install 3.12 && uv sync --python 3.12 --all-extras`                       |
| Plugin `SKILL.md` not visible under Trae                      | Trae is not in the harness `HOSTS` table; no `img2 add` link            | Read steps by their printed absolute path (see [`docs/PLUGINS.md`](../PLUGINS.md#host-skills)) |

## Cross-references

- [`docs/INSTALL.md`](../INSTALL.md) — base-skill install (Trae is not
  in the `HOSTS` table).
- [`docs/PLUGINS.md`](../PLUGINS.md#host-skills) — absolute-path
  workaround for plugin `SKILL.md` under Trae.
- Harness `HOSTS` table (`bin/img2threejs.mjs`) — `{hermes, claude,
  codex, opencode}` only; Trae is not a row.
- Licence: MIT.