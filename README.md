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

## Conventions

These skills record my personal defaults, not universal requirements. Explicit task constraints and existing project conventions take precedence. Preserve package and style preferences when a choice is needed; do not add dependencies, layers, or unrelated cleanup merely to satisfy a default.

The `comments` policy prohibits comments in application and test source code; an existing comment style alone does not override it. JSDoc consumed by the project's OpenAPI generator is the only exception. Deployment files, operational scripts, configuration, and standalone documentation are outside the prohibition. If an explicit project requirement mandates prohibited comments, report the conflict for user resolution rather than silently choosing either rule. Most code needs no explanation; only exceptionally important constraints or decisions worth preserving long-term belong in the existing spec.

Projects with lint tooling require a lint rule that rejects prohibited comments as errors and narrowly permits the OpenAPI annotations; add or configure it if missing. Policy adoption includes removing all existing prohibited source comments, without a legacy baseline. The `comments` skill defines safe removal, conflict handling, enforcement, and verification requirements.

`bdd` discovers business rules through concrete examples and makes agreed scenarios the backbone of test design and acceptance. OpenSpec main specs hold current requirement intent, active changes hold deltas and decisions, and archives retain history; `.feature` holds the current branch/commit's acceptance scenarios in the deterministic BDD workflow. `sdd` manages development progress and stage readiness for solo or team work. `lead` manages team assignments, coordination, and handoffs. They can be used independently or together, sharing progress and verification evidence. `testing` selects verification by risk and total cost, including agent tokens, while preserving the agreed quality bar; it owns execution and evidence rules. `code-review` provides the review method; `project-context` provides repository context and basic resource ownership. Workflow skills apply those rules without creating competing completion criteria.

For new deterministic BDD work, the main flow is discussion of rules/examples → requirement/change records and `.feature` scenarios → scenario/integration readiness → implementation with reviewed bindings → deterministic execution → acceptance evidence → review. Direct Gherkin runners and deterministic test generation are both allowed; no model interprets/translates scenarios or supplies bindings during generation/execution, and generated tests are read-only. Legacy projects retain existing conventions until scoped migration, but a missing runner cannot satisfy requested deterministic acceptance. Track scenario/Examples identities → assertions → execution results in existing artifacts, retaining security and implementation-risk checks. Pure refactors reuse existing behavior evidence; editorial or discussion-only work does not require a runner. See [BDD integration boundaries](bdd/REFERENCE.md#deterministic-contract-and-adoption) for OpenSpec compatibility and adoption limits; this repository supplies policy, not a runner or verified integration.

## Compatibility

Skill names, descriptions, and Markdown bodies use the shared Agent Skills format. Preserve existing `user-invocable` fields: in Claude Code, this extension controls direct user invocation, and `false` hides or disables slash-command invocation while still allowing model selection. Codex does not document this field as an invocation control, so this repository makes no claim about its runtime treatment.

Codex can optionally set `policy.allow_implicit_invocation: false` in `agents/openai.yaml` to prevent automatic invocation while keeping explicit `$skill-name` invocation available. This is not a translation of `user-invocable: false`; add it only when explicit-only Codex behavior is requested. None is needed here.

Common-format validation, platform-extension checks, and actual platform loading are distinct. A failure from one validator does not by itself prove runtime rejection. See the official [Claude Code skills](https://code.claude.com/docs/en/skills), [OpenAI skills](https://learn.chatgpt.com/docs/build-skills), and [Agent Skills specification](https://agentskills.io/specification) documentation.

## Skills

> **Optional `sdd` companion:** install `grill-me` for its spec-questioning workflow; `sdd` documents fallback behavior when unavailable:
>
> ```bash
> npx skills@latest add mattpocock/skills
> ```

| Skill | Activation | Description |
| --- | --- | --- |
| `api-design` | Auto | REST API naming, response shapes, pagination, errors, and versioning conventions. |
| `backend-principles` | Auto | Personal layering preferences and criteria for adopting DDD. |
| `bdd` | Explicit / natural language | Business scenario discovery and specification, scenario-driven tests, and traceable acceptance; also used by the main development workflow. |
| `code-quality` | Auto | General code quality conventions. |
| `code-review` | Auto | Review lens for correctness, security, architecture, maintainability, simplicity, and performance findings. |
| `comments` | Auto | Prohibit source comments, with an exception for JSDoc consumed by an OpenAPI generator. |
| `commit-conventions` | Auto | Commit message conventions and git commit workflow rules. |
| `daily-work-report` | Explicit / natural language | Daily progress and plans for project managers, based on activity across work sessions. |
| `nodejs` | Auto | Node.js module, async, error handling, filesystem, process, and config conventions. |
| `project-context` | Auto | Pre-work workflow for reading repository instructions, testing docs, and local conventions before acting. |
| `react` | Auto | React component, hook, state, styling, and performance conventions. |
| `road` | Explicit / natural language | Tool-neutral roadmap management with Work Item status sync from spec tool locations. |
| `rust` | Auto | Rust naming, ownership, error handling, modules, async, testing, and tooling conventions. |
| `sdd` | Explicit / natural language | Development progress and stage readiness for solo/team spec workflows, with grill, test, and review gates. |
| `lead` | Explicit / natural language | Leader mode for orchestrating dev, test, and review agents (bb threads first, native subagents as fallback); the leader dispatches and adjudicates but never writes code. |
| `security` | Auto | Security principles and vulnerability patterns. |
| `sql-conventions` | Auto | SQL schema, migration, query, indexing, and transaction conventions. |
| `testing` | Auto | Test strategy, case design, framework recommendations, and browser automation hygiene. |
| `typescript` | Auto | TypeScript typing, narrowing, imports, nullability, and money-handling conventions. |

Activation means how a skill is normally triggered:

- `Explicit / natural language` — call the skill directly using your agent's explicit skill invocation mechanism, such as Claude Code slash commands or Codex `$skill` mentions / skill selector, or describe the matching workflow in natural language.
- `Natural language / auto` — ask for the workflow in natural language; compatible agents may also activate it when the task clearly matches.
- `Auto` — supporting rules and conventions that compatible agents apply automatically when the task matches.

## License

MIT
