# dev-skills

Personal directory of Claude Code skills, straight from my `.claude` directory.

## Planning & Design

- **road** — Tool-neutral roadmap management. Work Item status auto-syncs from spec tool locations (openspec, superpowers, generic plans). Supports branching a roadmap to explore alternatives.

## Development Workflow

- **sdd** — Three quality gates for spec-driven-development (grill / test / review) around any spec tool. Works for human and autonomous-agent runs; agents self-grill and write decision logs to `sdd-reports/<slug>.md`.

## Install

All skills install via the [`skills`](https://www.npmjs.com/package/skills) CLI.

**Recommended — global install, symlinked (so `skills update` keeps everything in sync):**

```bash
npx skills@latest add noih/dev-skills/road -g
npx skills@latest add noih/dev-skills/sdd -g
```

Install everything in this repo at once:

```bash
npx skills@latest add noih/dev-skills --all -g
```

> `--all` is shorthand for `--skill '*' --agent '*' -y`. `-g` installs to `~/.claude/skills`; omit it for project-level (`./.claude/skills`).

### Other options

Drop `-g` for project-scoped installs, or pick specific skills/agents:

| Flag                 | Effect                                                                         |
| -------------------- | ------------------------------------------------------------------------------ |
| `-g, --global`       | install to user-level (`~/.claude/skills`) instead of project                  |
| `-a, --agent <name>` | target specific agents (`claude-code`, `cursor`, …) or `*`                     |
| `-s, --skill <name>` | pick specific skills or `*`                                                    |
| `--copy`             | copy files instead of symlinking (portable but `update` won't propagate)       |
| `--full-depth`       | scan all subdirectories even when a root `SKILL.md` exists                     |
| `-y, --yes`          | skip confirmation prompts                                                      |

Examples:

```bash
# project-level, road only, claude-code agent only
npx skills@latest add noih/dev-skills -s road -a claude-code

# global, copy instead of symlink (independent of source repo)
npx skills@latest add noih/dev-skills --all -g --copy
```

## Update

```bash
npx skills@latest update road
npx skills@latest update              # update all installed skills
```

## Remove

```bash
npx skills@latest remove road
npx skills@latest remove              # interactive select
```

## Usage

Once installed, trigger a skill via its matching slash command — e.g. `/road create backend`, `/sdd grill` — or let Claude Code auto-activate it from the skill's `SKILL.md` description.

Full CLI options: `npx skills@latest --help`.

## License

MIT
