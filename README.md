# personal_ai_config

Mike Beaumier's Claude Code plugins, published as a plugin marketplace.

Each plugin is a bundle of skills (and sometimes agents) that is switched on
or off as a unit, for every project or for one directory.

## Install

Register the marketplace once:

```bash
claude plugin marketplace add Jollyhrothgar/personal_ai_config
```

Then install a plugin, either for every project or for the one you are in:

```bash
claude plugin install vault@jollyhrothgar                    # every project
claude plugin install write@jollyhrothgar -s project         # this directory only
```

`claude plugin list` shows what is installed, `claude plugin disable <name>`
turns one off without removing it, and `claude plugin uninstall <name>` removes
it.

## Plugins

| Plugin | What it gives Claude | Skills |
| --- | --- | --- |
| `vault` | Thread notes in a personal knowledge vault, so context survives a task switch | `note`, `recall`, `remind`, `prune` — [repo](https://github.com/Jollyhrothgar/vault-plugin) |
| `write` | A blind check and an informed repair of prose before it leaves the session | `check`, `harvest-fixture`, plus the `clarity-detector` and `clarity-repair` agents — [repo](https://github.com/Jollyhrothgar/write-plugin) |
| `session` | Find and resume past Claude Code and OpenCode sessions | `find` — [repo](https://github.com/Jollyhrothgar/session-plugin) |
| `arch` | A source-grounded, navigable HTML explanation of a system's architecture | `visualize` — [repo](https://github.com/Jollyhrothgar/arch-plugin) |
| `track-splitter` | Split a long rehearsal recording into named songs | [repo](https://github.com/Jollyhrothgar/track-splitter) |

Skills are namespaced by their plugin: `/vault:note`, `/write:check`,
`/session:find`, `/arch:visualize`. A plugin is named for its subject and each
skill for the action, so `/arch:` lists everything there is to do with
architecture.

### What each plugin expects

- **`vault`** reads and writes a vault checked out at
  `~/workspace/knowledge-vault/main`, and `remind` uses macOS Reminders.
- **`session`** needs `python3`.
- **`track-splitter`** needs [uv](https://docs.astral.sh/uv/) and macOS.

## Layout

This repository is the catalog. Each plugin lives in its own repository and
is listed in `.claude-plugin/marketplace.json` by its GitHub source. A plugin
repository holds:

```
.claude-plugin/plugin.json   # name, description, author, repository
skills/<skill>/SKILL.md      # plus scripts/ and references/ beside it
agents/<agent>.md
README.md
```

Plugins do not share code. Things that have to change together belong in one
plugin; a plugin that needs another declares it in `dependencies`.

## Adding or changing a plugin

1. Work in the plugin's own repository. `claude --plugin-dir .` loads a
   working copy without installing it.
2. For a new plugin, add an entry to `.claude-plugin/marketplace.json` with a
   `github` source.
3. Run `claude plugin validate .` in the plugin repository and here.

Plugins carry no `version`, so every commit to a plugin's default branch is an
update. A `version` would hold everyone on that version until it is changed.
Pin a plugin from someone else's repository with `sha`.

Conventions that keep a skill usable outside this repository:

- A skill never embeds knowledge that belongs to one codebase. It points at a
  path in the project it is running in.
- A skill calls its own scripts through `${CLAUDE_PLUGIN_ROOT}`, never through
  a hardcoded install path.
- `DEV.md` beside a skill records where it is, where it should be, and known
  limits. The skill does not read it.
