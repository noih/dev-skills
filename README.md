# dev-skills

Personal directory of Claude Code skills, straight from my `.claude` directory.

## Planning & Design

- **road** — Tool-neutral roadmap management. Work Item status auto-syncs from spec tool locations (openspec, superpowers, generic plans). Supports branching a roadmap to explore alternatives.

## Development Workflow

- **sdd** — Three quality gates for spec-driven-development (grill / test / review) around any spec tool. Works for human and autonomous-agent runs; agents self-grill and write decision logs to `sdd-reports/<slug>.md`.

## Install

All skills install via the [`skills`](https://www.npmjs.com/package/skills) CLI:

```bash
npx skills@latest add noih/dev-skills/road
npx skills@latest add noih/dev-skills/sdd
```

Install everything in this repo:

```bash
npx skills@latest add noih/dev-skills --all
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
