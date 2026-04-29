# dev-skills

Personal directory of agent skills.

## Quick Start

All skills install via the [`skills`](https://www.npmjs.com/package/skills) CLI. For full CLI options, run `npx skills@latest --help`.

Install everything globally for all detected agents:

```bash
npx skills@latest add noih/dev-skills --all -g
```

Install everything globally for specific agents:

```bash
npx skills@latest add noih/dev-skills --skill '*' -a claude-code -a codex -g -y
```

Update all installed skills:

```bash
npx skills@latest update
```

Remove globally installed skills interactively:

```bash
npx skills@latest remove -g
```

> `--all` is shorthand for `--skill '*' --agent '*' -y`. Use `-a codex` for Codex and `-a claude-code` for Claude Code. Omit `-g` for project-level agent directories. The CLI may keep canonical copies in shared agent-skill locations and sync them to the selected agent targets; by default, installs are symlinked so `skills update` keeps everything in sync.

## Individual Skills

Install one skill:

```bash
npx skills@latest add noih/dev-skills/road
```

Update one installed skill:

```bash
npx skills@latest update road
```

Remove one globally installed skill:

```bash
npx skills@latest remove road -g
```

Remove uses the installed skill name (`road`), not the source package path (`noih/dev-skills/road`). Add `-g` when removing globally installed skills.

## Skills

| Skill | Activation | Description |
| --- | --- | --- |
| `agent-autonomy` | Auto | Standalone vs team-mode rules for custom agents. |
| `backend-api-design` | Auto | REST API naming, response shapes, pagination, errors, and versioning conventions. |
| `backend-database` | Auto | Schema design, migration, indexing, soft delete, and query optimization conventions. |
| `backend-principles` | Auto | Layered Architecture and DDD adoption guidance. |
| `code-review` | Auto | Review lens for correctness, security, architecture, maintainability, simplicity, and performance findings. |
| `comments` | Auto | Guidance for adding, updating, or removing comments. |
| `frontend-react` | Auto | React component, hook, state, styling, and performance conventions. |
| `lang-rust` | Auto | Rust naming, ownership, error handling, modules, async, testing, and tooling conventions. |
| `lang-typescript` | Auto | TypeScript typing, narrowing, imports, nullability, and money-handling conventions. |
| `road` | Explicit / natural language | Tool-neutral roadmap management with Work Item status sync from spec tool locations. |
| `rule-code-quality` | Auto | General code quality and comment conventions. |
| `rule-git` | Auto | Conventional Commit and git workflow rules. |
| `rule-security` | Auto | Security principles and vulnerability patterns. |
| `rule-testing` | Auto | Test strategy, case design, and framework recommendations. |
| `runtime-nodejs` | Auto | Node.js module, async, error handling, filesystem, process, and config conventions. |
| `sdd` | Explicit / natural language | Three quality gates for spec-driven development: grill, test, and review. |

Activation means how a skill is normally triggered:

- `Explicit / natural language` — call the skill directly using your agent's explicit skill invocation mechanism, such as Claude Code slash commands or Codex `$skill` mentions / skill selector, or describe the matching workflow in natural language.
- `Natural language / auto` — ask for the workflow in natural language; compatible agents may also activate it when the task clearly matches.
- `Auto` — supporting rules and conventions that compatible agents apply automatically when the task matches.

## License

MIT
