# dev-skills

Personal directory of Claude Code skills, straight from my `.claude` directory.

## Quick Start

All skills install via the [`skills`](https://www.npmjs.com/package/skills) CLI. For full CLI options, run `npx skills@latest --help`.

Install everything globally:

```bash
npx skills@latest add noih/dev-skills --all -g
```

Update all installed skills:

```bash
npx skills@latest update
```

Remove installed skills interactively:

```bash
npx skills@latest remove
```

> `--all` is shorthand for `--skill '*' --agent '*' -y`. `-g` installs to `~/.claude/skills`; omit it for project-level (`./.claude/skills`). By default, installs are symlinked so `skills update` keeps everything in sync.

## Individual Skills

Install one skill:

```bash
npx skills@latest add noih/dev-skills/road
```

Update one installed skill:

```bash
npx skills@latest update road
```

Remove one installed skill:

```bash
npx skills@latest remove road
```

## Skills

| Skill | Activation | Description |
| --- | --- | --- |
| `agent-autonomy` | Auto | Standalone vs team-mode rules for custom agents. |
| `agent-teams` | Natural language / auto | Team workflow rules for coordinated architect / developer / QA / reviewer agent runs. |
| `backend-api-design` | Auto | REST API naming, response shapes, pagination, errors, and versioning conventions. |
| `backend-database` | Auto | Schema design, migration, indexing, soft delete, and query optimization conventions. |
| `backend-principles` | Auto | Layered Architecture and DDD adoption guidance. |
| `code-review` | Auto | Review lens for correctness, security, architecture, maintainability, simplicity, and performance findings. |
| `comments` | Auto | Guidance for adding, updating, or removing comments. |
| `frontend-react` | Auto | React component, hook, state, styling, and performance conventions. |
| `lang-rust` | Auto | Rust naming, ownership, error handling, modules, async, testing, and tooling conventions. |
| `lang-typescript` | Auto | TypeScript typing, narrowing, imports, nullability, and money-handling conventions. |
| `road` | Slash command / natural language | Tool-neutral roadmap management with Work Item status sync from spec tool locations. |
| `rule-code-quality` | Auto | General code quality and comment conventions. |
| `rule-git` | Auto | Conventional Commit and git workflow rules. |
| `rule-security` | Auto | Security principles and vulnerability patterns. |
| `rule-testing` | Auto | Test strategy, case design, and framework recommendations. |
| `runtime-nodejs` | Auto | Node.js module, async, error handling, filesystem, process, and config conventions. |
| `sdd` | Slash command / natural language | Three quality gates for spec-driven development: grill, test, and review. |

Activation means how a skill is normally triggered:

- `Slash command / natural language` — call it directly, such as `/road ...` or `/sdd ...`, or describe the matching workflow in natural language.
- `Natural language / auto` — ask for the workflow in natural language; Claude Code may also activate it when the task clearly matches.
- `Auto` — supporting rules and conventions that Claude Code applies automatically when the task matches.

## License

MIT
