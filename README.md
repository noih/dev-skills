# dev-skills

Personal directory of agent skills.

## Install

```bash
npx skills@latest add noih/dev-skills
```

Interactive prompts will guide you through skill and agent selection.

```bash
npx skills@latest update
npx skills@latest remove       # project-level
npx skills@latest remove -g    # global
```

## Skills

> **`sdd` dependency:** install `grill-me` before using `sdd`:
>
> ```bash
> npx skills@latest add mattpocock/skills
> ```

| Skill | Activation | Description |
| --- | --- | --- |
| `agent-autonomy` | Auto | Standalone vs team-mode rules for custom agents. |
| `api-design` | Auto | REST API naming, response shapes, pagination, errors, and versioning conventions. |
| `backend-principles` | Auto | Layered Architecture and DDD adoption guidance. |
| `code-quality` | Auto | General code quality and comment conventions. |
| `code-review` | Auto | Review lens for correctness, security, architecture, maintainability, simplicity, and performance findings. |
| `comments` | Auto | Guidance for adding, updating, or removing comments. |
| `commit-conventions` | Auto | Commit message conventions and git commit workflow rules. |
| `nodejs` | Auto | Node.js module, async, error handling, filesystem, process, and config conventions. |
| `project-context` | Auto | Pre-work workflow for reading repository instructions, testing docs, and local conventions before acting. |
| `react` | Auto | React component, hook, state, styling, and performance conventions. |
| `road` | Explicit / natural language | Tool-neutral roadmap management with Work Item status sync from spec tool locations. |
| `rust` | Auto | Rust naming, ownership, error handling, modules, async, testing, and tooling conventions. |
| `sdd` | Explicit / natural language | Three quality gates for spec-driven development: grill, test, and review. |
| `security` | Auto | Security principles and vulnerability patterns. |
| `sql-conventions` | Auto | SQL schema, migration, query, indexing, and transaction conventions. |
| `testing` | Auto | Test strategy, case design, and framework recommendations. |
| `typescript` | Auto | TypeScript typing, narrowing, imports, nullability, and money-handling conventions. |

Activation means how a skill is normally triggered:

- `Explicit / natural language` — call the skill directly using your agent's explicit skill invocation mechanism, such as Claude Code slash commands or Codex `$skill` mentions / skill selector, or describe the matching workflow in natural language.
- `Natural language / auto` — ask for the workflow in natural language; compatible agents may also activate it when the task clearly matches.
- `Auto` — supporting rules and conventions that compatible agents apply automatically when the task matches.

## License

MIT
