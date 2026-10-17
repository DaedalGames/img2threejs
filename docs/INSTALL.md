# Installing the skill — agent runbook

What *you* (an agent) should run, in what order, and what "working" looks like. This page is the
procedure and the expected output; the normative detail stays where it already is — the README's
[§1 Install the base skill](../README.md#1-install-the-base-skill) and
[§5 Audit and maintain the installation](../README.md#5-audit-and-maintain-the-installation) — and
the plugin surface in [`docs/PLUGINS.md`](PLUGINS.md). Where this page and those disagree, they win.

## 0. Two CLIs, one skill — do not conflate them

|| | Base skill CLI | Plugin harness CLI |
||---|---|---|
|| Package | `img2threejs` | `@img2threejs/img2` |
|| Installs | **this skill** into agent hosts | **plugins** (domain steps, gates, targets) |
|| Hosts it writes skill links for | `hermes`, `claude`, `codex`, `opencode` | `claude`, `codex`, `opencode` |
|| Run as | `npx --yes img2threejs@latest <cmd>` | `npx --yes @img2threejs/img2@latest <cmd>` |
|| `--version --json` shape | `key: value` text (2 lines; `--json` is silently ignored) | real JSON (one line, parseable) |

**Hermes name-collision note**: on a Hermes-equipped host, `which img2threejs` may resolve to
`~/.local/bin/img2threejs`, a Hermes profile launcher (it runs `hermes -p img2threejs …`, not the
npm CLI). If the bare name does not match the npm CLI, always invoke the base skill via
`npx --yes img2threejs@latest <cmd>` — never type `img2threejs` directly.

Two CLIs, two `--version --json` shapes. The base skill CLI prints human-readable text:

```text
img2threejs CLI: 0.1.1
default skill ref: v2.0.0
```

The `--json` flag on the base skill CLI is **silently ignored** — the output is the same two-line
text whether you pass `--json` or not; do not pipe it through `jq`. If you need a machine-parseable
version of the base-skill CLI, the `default skill ref` line is the only field stable across releases.

The harness CLI prints a single-line JSON object with the keys you can read programmatically
(`harness`, `maxPluginSchema`, `coreApi`, `contract`, `commands`). Pipe the harness one through
`jq`; do not pipe the base-skill one.

Installing one does **not** install the other, and adding a plugin does not generate a model.
Reach for the harness only when a domain plugin serves the item (CS2 skins, animated characters);
a `generic` or `character` run is satisfied by this checkout alone.

Both work **before** anything is installed — every command below is also valid as
`npx --yes <package>@latest <cmd>` — so "the CLI is missing" is never the blocker it looks like.

## 1. Install the skill into this host

```bash
npx --yes img2threejs@latest install --dry-run     # plan only: no downloads, no filesystem changes
npx --yes img2threejs@latest install               # detect and install into every present host
npx --yes img2threejs@latest install --host hermes # or one host: hermes|claude|codex|opencode|all
npx --yes img2threejs@latest doctor                # base-skill host/link report
```

Expected dry-run line, and how detection works — a host is "present" when its directory exists, so
the plan names exactly those:

```
→ install skill img2threejs/img2threejs @ v2.0.0 into: hermes, claude
```

- `--ref` takes a **semantic tag or a 40-char commit SHA**; branches and short SHAs are refused
  (the installer requires Git and network for a real run).
- Checkouts live under `~/.img2threejs/releases/<sha>/img2threejs`; hosts installing the same commit
  share one checkout.
- The first `--yes` belongs to `npx` (package download) — it is not an overwrite flag.
- The installer refuses to overwrite a manual install or an unrelated link rather than clobbering it.

Manual alternative — one checkout, every host entering it through a symlink, so copies cannot drift
(`~/.config/opencode` may be `$XDG_CONFIG_HOME/opencode`; Hermes is profile-scoped — see the note below):

```text
# Hermes: BOTH paths may be needed (profile-scoped for Hermes, global for `img2threejs doctor`).
~/.hermes/profiles/<profile>/skills/img2threejs -> <your checkout>
~/.hermes/skills/img2threejs                    -> <your checkout>

# Other hosts (single path per host):
~/.claude/skills/img2threejs                       -> <your checkout>
~/.codex/skills/img2threejs                        -> <your checkout>
~/.config/opencode/skills/img2threejs              -> <your checkout>
```

Hermes note: skills are not global — they live under `~/.hermes/profiles/<profile>/skills/`.
`HERMES_ACTIVE_PROFILE` (default `img2threejs`) decides which profile a session reads from, so the
symlink must be repeated for every profile that should see the skill. A runbook with `HERMES_ACTIVE_PROFILE`
set to a profile without the link will report "Unknown skill" and continue without the router. **The
base-skill `img2threejs doctor` only validates the global path `~/.hermes/skills/`**, not the
profile-scoped one — if `doctor` is part of your preflight, create both links.

Choose the CLI **or** the manual clone for a given path, never both. A manual clone follows the
repository's default branch; the CLI pins a release.

## 2. Install the harness, only if a domain plugin serves the item

```bash
npx --yes @img2threejs/img2@latest --version --json   # works with nothing installed
npx --yes @img2threejs/img2@latest doctor             # exits 1 until a harness checkout exists
npx --yes @img2threejs/img2@latest install --yes      # clones the harness, links a launcher
npx --yes @img2threejs/img2@latest add cs2 --yes      # a plugin by catalog id
```

On a machine with no `$IMG2_HOME`, `doctor` exits **1** and prints a `FAIL` line beginning
`no harness checkout at <home>/harness; run 'img2 install'` (where `<home>` is `$IMG2_HOME` if
set, otherwise `~/.img2`), while `--version` and `list` still answer. If no writable directory on
`PATH` qualifies, `install` links **no** launcher and warns `No writable launcher directory on
PATH; use npx instead`. **Auto-detected hosts may be modified even if you do not use them** —
`img2 install` always touches Claude's settings when `~/.claude/` exists, regardless of whether
Claude Code is on `PATH` or even installed, by appending `$IMG2_HOME` to
`permissions.additionalDirectories` in `~/.claude/settings.json` so its file tools can reach the
harness. There is no `img2 install` flag that scopes this today — to skip the Claude settings
write, back up `~/.claude/settings.json` before `install` and restore it after, or remove
`~/.claude/` first (the host detection is purely directory-existence based). Full behaviour,
host-skill linking and the `$IMG2_HOME` trap: [`docs/PLUGINS.md`](PLUGINS.md).

## 3. Verify, and know what each check proves

```bash
npx --yes img2threejs@latest doctor   # this skill's links per host
img2 doctor                           # registered plugins: static audit, fail-loud
img2 list                             # what the ACTIVE $IMG2_HOME has registered
```

Neither `doctor` proves visual likeness or animation quality — that needs the reconstruction's own
render and domain gates. `img2 list` answering a profile the pipeline does not offer is the
signature of two `$IMG2_HOME`s with the plugin registered in the inactive one.

## 4. When something is off

| Symptom | Cause | Action |
|---|---|---|
| `img2threejs: command not found` (or `img2`) | the CLI is not on `PATH` | run the `npx --yes <package>@latest …` form — no install required |
| Host missing from the dry-run plan | that host's directory does not exist | create/point it, or install with an explicit `--host` |
| `install` refuses a ref | branch or short SHA | pass a semantic tag or the full 40-char SHA |
| Dry-run refuses with `refusing to replace an unmanaged skill at …` | a manual clone or unrelated link occupies that path | `checkLinks()` runs **before** the dry-run gate — on a managed install, `--dry-run` will not print the plan when a manual entry exists; move the entry yourself first, then run `--dry-run`. On a Hermes host the check walks the **non-profile-scoped** `~/.hermes/skills/<id>` path, not the profile-scoped `~/.hermes/profiles/<profile>/skills/<id>` path §1 documents — if your manual install only created the profile-scoped link, the global link is still empty and the check passes; if your manual install created the global link too, move or remove that global entry first |
| Unmanaged base skill reported | a manual clone occupies that skill path | keep it, or move that entry deliberately before using the CLI |
| Base `doctor` says `hermes … (no link)` on Hermes despite a profile-scoped symlink | the doctor validates `~/.hermes/skills/`, not `~/.hermes/profiles/<profile>/skills/` | create the **global** symlink too; the doctor and Hermes discovery use different paths |
| `img2threejs` runs the wrong CLI | the bare name resolves to a Hermes profile launcher on this host | always use `npx --yes img2threejs@latest …`; check `which img2threejs` if unsure |
| A profile your run needs is missing | the plugin is not registered in the **active** `$IMG2_HOME` | `img2 list`, then `img2 add …` or fix `$IMG2_HOME` |
| `img2 install` modified `~/.claude/settings.json` unexpectedly | `~/.claude/` exists — the harness always adds `$IMG2_HOME` to `permissions.additionalDirectories` when it detects Claude, regardless of whether Claude Code is installed | back up `settings.json` first and restore after, or remove `~/.claude/` before install; this is directory-detected, not installed-Code-detected |
| A plugin is registered but its `SKILL.md` is missing from your model context under Hermes | the harness `HOSTS` table does not include Hermes, so `img2 add` does not link `~/.hermes/profiles/<active>/skills/img2-<id>` | `ln -s "$IMG2_HOME/plugins/<id>" ~/.hermes/profiles/${HERMES_ACTIVE_PROFILE:-img2threejs}/skills/img2-<id>` per plugin, or read steps by their printed absolute path — see [`docs/PLUGINS.md`](PLUGINS.md) "Host skills" |
| `FAIL … opencode link missing` | a host skill link is absent | inspect the reported path; `sync` regenerates harness data, not host links |
| Private plugin source 404 / access denied | no Git credentials for that repository | sign in with an authorized account; no installer can bypass source permissions |

## 5. Where to read next

- README [§1](../README.md#1-install-the-base-skill) and [§5](../README.md#5-audit-and-maintain-the-installation) — the authoritative install and maintenance text for this skill.
- [`docs/PLUGINS.md`](PLUGINS.md) — what a run actually sees: registry, `domain.json` splice, gates, host skills.
- Harness [`PLUGIN_CONTRACT.md`](https://github.com/img2threejs/img2/blob/main/docs/PLUGIN_CONTRACT.md) — every plugin rule, including the gate runner and the emission-target socket.
- [`CONTRIBUTING.md`](../CONTRIBUTING.md) — packaging and publication checks for this repository's own CLI.

## 6. Other popular open-source coding agents

Sections §1–§5 cover the four hosts the base-skill CLI knows about —
`hermes`, `claude`, `codex`, `opencode` (the `HOSTS` table in
`bin/img2threejs.mjs`, §1). The four agents below are open-source, popular,
multi-provider, and **not** in that table today; the install command ships
its own launcher, but the base-skill link is something you create by hand —
the same one-checkout / many-symlinks idea from §1. None of them adds a row
to `HOSTS`; the link is purely a directory convention the agent reads on
startup, so a fresh agent release can keep working without code changes here.

All four read **`AGENTS.md`** from the project root when it exists — the
`agents.md` standard that Aider, Mistral Vibe, Pi and most other open-source
agents adopt. For this skill that file is **not** checked in today (the
canonical shared-instructions file in this repo is `CLAUDE.md`, §1); drop an
`AGENTS.md` next to it when an open-source agent is your host and the agents
in §6 will pick it up automatically.

### 6.1 Aider

Install the Python CLI; it pulls from PyPI:

```bash
pip install aider-chat
```

Aider reads conventions from any of these, in order:

- `AGENTS.md` at the project root (the `agents.md` standard) — enabled by
  adding it to Aider's `read` list.
- `CONVENTIONS.md` at the project root — the historical convention; load
  with `/read CONVENTIONS.md` or `aider --read CONVENTIONS.md`.
- `.aider.conf.yml` (also `.aider.model.settings.yml`) — per-project YAML,
  always loaded, where you can pin `read:` (single file or list) so
  `AGENTS.md` / `CONVENTIONS.md` is loaded automatically on every run.

`.aider*` files in the repo root are Aider-managed state (history,
caches, model settings) — they are **not** the skill link. For the skill
itself, follow §1's manual rule:

```text
# Single path — Aider does not auto-pick up skills, but you can point
# `read:` at the skill's SKILL.md or load it via `/read <abs-path>`.
~/.aider/skills/img2threejs  ->  <your checkout>   # optional convention
```

There is no Aider-blessed `<host-skills-dir>/img2threejs` location; the
practical pattern is `read: [SKILL.md]` (relative to the repo root) in
`.aider.conf.yml`, or pass it on the command line:

```bash
aider --read SKILL.md
```

The same pattern lets the harness reach a plugin in a non-default
`$IMG2_HOME`: read steps by their printed absolute path — see
[`docs/PLUGINS.md`](PLUGINS.md#host-skills) "Host skills".

### 6.2 Pi (`@earendil-works/pi-coding-agent`)

Install the npm CLI; `@mariozechner/pi-coding-agent` is the same upstream
package (the `pi-mono` monorepo):

```bash
npm install -g @earendil-works/pi-coding-agent
# or: npm install -g @mariozechner/pi-coding-agent
```

Pi looks for skills under its own per-user directory and reads
`AGENTS.md` from the project root:

```text
~/.pi/skills/img2threejs  ->  <your checkout>   # Pi's skill path
```

`playground/Dockerfile.procedural` pins `@earendil-works/pi-coding-agent@0.85.1`
for the procedural image — the same family, an older release — so anything
you link in `~/.pi/skills/` for a host agent also resolves inside the
playground container when its `~/.pi` is mounted or its `skills/` is
populated by the same step.

The harness (`img2`) does not link Pi's skill path on `img2 add` —
Pi is not in `HOSTS` (see [`docs/PLUGINS.md`](PLUGINS.md#host-skills)).
The link above is the same "register this link as yourself" workaround the
PLUGINS page describes for Hermes: the plugin still lives under
`$IMG2_HOME/plugins/<id>`, the symlink above is what makes its `SKILL.md`
visible to the agent.

### 6.3 Mistral Vibe

Bootstrap with the official installer; it places `uv` (if missing) and
then installs `mistral-vibe` as a `uv` tool:

```bash
curl -LsSf https://mistral.ai/vibe/install.sh | bash
```

Mistral Vibe (Apache-2.0) reads `AGENTS.md` from the project root plus
whatever project-level rules you ship; like Aider it has no host-skills
directory baked in, so the practical pattern is the same:

- Drop an `AGENTS.md` at the repo root that links to this skill, or
- load the skill's `SKILL.md` directly in the conversation.

For harness-installed plugins, read steps by their printed absolute path —
[`docs/PLUGINS.md`](PLUGINS.md#host-skills).

### 6.4 Droid (Factory AI)

Install via Factory's official script (Homebrew, npm and Windows variants
exist as well):

```bash
curl -fsSL https://app.factory.ai/cli | sh
# or: brew install --cask droid
# or: npm install -g droid
```

Droid manages its own auth and plans, and exposes agent-shape features
the other three do not: **Custom Droids** (specialised sub-agents managed
via `/droids`), **Missions** (multi-agent projects run interactively with
`/missions` or headlessly with `droid exec --mission`), and a `Plugins`
mechanism for bundles of commands, droids, skills and hooks
(`/plugins`, `droid plugin install`). It reads `AGENTS.md` from the
project root when present.

For this skill, the equivalent of §1's manual link is to place the skill
where Droid's `/skills` (or `/plugins`) will pick it up, or to symlink
it next to a Custom Droid's system prompt. Like the others, Droid is
not in `HOSTS`, so a plugin's `SKILL.md` is not auto-linked; reach for
the absolute-path workaround in [`docs/PLUGINS.md`](PLUGINS.md#host-skills)
when a run needs plugin context under Droid.

### What this section does NOT do

- It does **not** add Aider, Pi, Mistral Vibe or Droid to the `HOSTS` table
  in `bin/img2threejs.mjs`. That is upstream work; until it lands, the link
  is your responsibility on each host.
- It does **not** ship an `AGENTS.md` in this repository. The
  `agents.md` standard is host-agnostic — drop one next to `CLAUDE.md`
  if your open-source host is your primary one.
- It does **not** touch the npm tarball or the `DEFAULT_REF` constant —
  this page is docs-only.
