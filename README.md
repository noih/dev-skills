# dev-skills

Personal directory of agent skills.

## Install

```bash
npx skills@latest add noih/dev-skills
```

Interactive prompts will guide you through skill and agent selection. Keep each selected skill's supporting files together with its `SKILL.md`; references inside that directory belong to the skill, not a required sibling installation.

```bash
npx skills@latest update
npx skills@latest remove       # project-level
npx skills@latest remove -g    # global
```

## Conventions

These skills record my personal defaults, not universal requirements. Explicit task constraints and existing project conventions take precedence. Preserve package and style preferences when a choice is needed; do not add dependencies, layers, or unrelated cleanup merely to satisfy a default.

The `comments` policy prohibits new comments in application and test source code; an existing comment style alone does not override it. JSDoc consumed by the project's OpenAPI generator is the only exception. Deployment files, operational scripts, configuration, and standalone documentation are outside the prohibition. Resolve conflicting project or skill requirements using the instruction hierarchy and applicable explicit user decisions; ask only when a concrete conflict remains unresolved. Most code needs no explanation; only exceptionally important constraints or decisions worth preserving long-term belong in the existing spec.

Project-wide adoption requires explicit user authorization or project instructions enabling it; loading a skill or fixing a bug alone is not adoption. Authorized adoption removes all existing prohibited source comments without a legacy baseline and, in projects with lint tooling, adds or configures lint enforcement with the narrow OpenAPI exception. Maintain already-adopted requirements without repeated approval or weaker gates. Read-only reviews report gaps without edits; unadopted projects do not acquire a lint gate from skill loading. The `comments` skill defines scope, safe removal, conflict handling, enforcement, and verification.

`bdd` discovers business rules through concrete examples and makes agreed scenarios the backbone of test design and acceptance. OpenSpec main specs hold current requirement intent, active changes hold deltas and decisions, and archives retain history; `.feature` holds the current branch/commit's acceptance scenarios in the deterministic BDD workflow. `sdd` manages development progress and stage readiness for solo or team work. `lead` manages team assignments, coordination, and handoffs. `punch-list` handles findings the user reports after trying delivered work: bounded fixes with checkpointed verification, escalated back to the development workflow per its six rules, never lowering the `testing` / `code-review` bar. They can be used independently or together, sharing progress and verification evidence. `testing` selects verification by risk and total cost, including agent tokens, while preserving the agreed quality bar; it owns execution and evidence rules. `code-review` provides the review method; `project-context` provides repository context and basic resource ownership. Workflow skills apply those rules without creating competing completion criteria.

For new executable BDD acceptance and existing feature-driven projects, deterministic execution is the default. The main flow is discussion of rules/examples → requirement/change records and `.feature` scenarios → scenario/integration readiness → implementation with reviewed bindings → deterministic execution → acceptance evidence → review. Direct Gherkin runners and deterministic test generation are both allowed; no model interprets/translates scenarios or supplies bindings during generation/execution, and generated tests are read-only. Legacy projects retain existing conventions until scoped migration, but a missing runner cannot satisfy requested deterministic acceptance. Track scenario/Examples identities → assertions → execution results in existing artifacts, retaining security and implementation-risk checks. Pure refactors reuse existing behavior evidence; editorial or discussion-only work does not require a runner. See [BDD integration boundaries](bdd/REFERENCE.md#deterministic-contract-and-adoption) for OpenSpec compatibility and adoption limits; this repository supplies policy, not a runner or verified integration.

`issue-tracking` records progress, blockers, handoffs, and QA/review follow-ups in the project's chosen tracker or existing local task record. Resolve the destination in order: explicit user choice → existing project tracker/local record → configured personal/environment default only when no project destination exists; GitHub hosting or unavailable tracker access does not justify choosing GitHub Issues. It is optional alongside `sdd`, `testing`, `code-review`, `lead`, `punch-list`, and `road`; keep project mappings private or in project instructions. Use the selected connector's current tool descriptions and usage guides for platform mechanics. These are workflow instructions, not a background sync service or verified live integrations.

## Compatibility

Skill names, descriptions, and Markdown bodies use the shared Agent Skills format. Preserve existing `user-invocable` fields: in Claude Code, this extension controls direct user invocation, and `false` hides or disables slash-command invocation while still allowing model selection. Codex does not document this field as an invocation control, so this repository makes no claim about its runtime treatment.

Codex can optionally set `policy.allow_implicit_invocation: false` in `agents/openai.yaml` to prevent automatic invocation while keeping explicit `$skill-name` invocation available. This is not a translation of `user-invocable: false`; add it only when explicit-only Codex behavior is requested. None is needed here.

Common-format validation, platform-extension checks, and actual platform loading are distinct. A failure from one validator does not by itself prove runtime rejection. See the official [Claude Code skills](https://code.claude.com/docs/en/skills), [OpenAI skills](https://learn.chatgpt.com/docs/build-skills), and [Agent Skills specification](https://agentskills.io/specification) documentation.

## Skills

> **Optional `sdd` companion:** `grill-me` can supply spec questioning when its installed version and downstream targets are callable through the current harness and invocation policy. Installation alone does not guarantee availability; `sdd` documents fallback behavior for missing, unavailable, or incompatible companions:
>
> ```bash
> npx skills@latest add mattpocock/skills
> ```

Choose the entrypoint for the task below; this is not a mandatory pipeline. Scenario discovery, stage tracking, team orchestration, verification and post-delivery fixes are separate jobs. Skills can work independently or share existing decisions and evidence when combined.

| Skill | Activation | Use when you need to… |
| --- | --- | --- |
| `project-context` | Auto | Find relevant repository/package instructions, commands and versions before work; own resources and handoffs even outside testing. |
| `bdd` | Explicit / natural language | Discover or specify business rules with examples, then connect agreed scenarios to deterministic acceptance. Pure refactors reuse behavior evidence. |
| `sdd` | Explicit / natural language | Track an active spec's progress and test/review readiness in solo or team work; it does not author/archive the spec or manage Git. |
| `lead` | Explicit / natural language | Delegate and adjudicate a team goal with bb threads first (native agents as fallback), exact model routing and owned handoffs; the leader never writes code. |
| `testing` | Auto | Choose sufficient evidence, design tests, run the right scope safely, and reuse valid results without false passes or unnecessary reruns. |
| `code-review` | Auto | Review a concrete change for production risks and evidence gaps; a review request alone does not authorize fixes or new gates. |
| `punch-list` | Explicit / natural language | Repair user-reported deviations after trying delivered work, with intake, bounded fixes and checkpointed verification; new scope returns to development. |
| `issue-tracking` | Natural language / auto | Record meaningful progress, blockers and follow-ups in the chosen tracker/local record under existing authority; no automatic mirror or extra gate. |
| `road` | Explicit / natural language | Manage ordered Work Items pointing to specs and synchronize their location-based status; this is distinct from implementation or issue completion. |
| `daily-work-report` | Explicit / natural language | Draft a PM-facing daily update from work evidence or supplied notes; not implementation, code review or time tracking. |
| `commit-conventions` | Auto | Stage/split coherent commits or write English Conventional Commit messages without AI-attribution trailers, preserving WIP/handoff semantics. |
| `comments` | Auto | Apply the source-comment prohibition and consumed-OpenAPI-JSDoc exception; distinguish daily work, authorized adoption and established enforcement. |
| `code-quality` | Auto | Assess naming, simplicity, abstractions, errors and maintainability without unrelated cleanup. |
| `backend-principles` | Auto | Choose backend responsibility/layer boundaries or domain modeling when justified; preserve existing architecture. |
| `api-design` | Auto | Design/review REST contracts: resources, response shapes, pagination, errors, idempotency and versioning. |
| `nodejs` | Auto | Work on Node.js modules, async errors, processes, filesystem operations and runtime configuration. |
| `react` | Auto | Work on React UI, hooks/state, forms and styling, including exact parity when that is the agreed task. |
| `rust` | Auto | Work on Rust ownership/errors, async code, Cargo and runtime-aware tests. |
| `sql-conventions` | Auto | Choose schema, migration, query, index, transaction and connection conventions without needless existing-data migrations. |
| `typescript` | Auto | Work on TypeScript contracts, narrowing, imports, nullability and precise money representations. |
| `security` | Auto | Inspect input, identity, ownership, secrets, payments/PII, callbacks and other trust boundaries in context. |

Conditional references are read before their relevant action, including a mid-task change of activity. For example, testing's browser recipes apply before browser work; execution recipes apply before reporter overrides, piped/background execution or repetition calculations. Ordinary direct foreground tests do not require reading both. If needed material is unavailable, disclose the gap and hold only operations whose required safeguards cannot be established; continue known-safe alternatives and independent work without claiming unread content was read.

Activation means how a skill is normally triggered:

- `Explicit / natural language` — call the skill directly using your agent's explicit skill invocation mechanism, such as Claude Code slash commands or Codex `$skill` mentions / skill selector, or describe the matching workflow in natural language.
- `Natural language / auto` — ask for the workflow in natural language; compatible agents may also activate it when the task clearly matches.
- `Auto` — supporting rules and conventions that compatible agents apply automatically when the task matches.

## License

MIT
