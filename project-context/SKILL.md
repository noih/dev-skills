---
name: project-context
description: "Use when: starting work in a repository, entering an unfamiliar codebase, before editing code, before running tests, before reviewing, before spawning subagents, or when choosing project commands/conventions. Focus on project-specific operating docs that are not guaranteed to be preloaded, especially TESTING.md, README.md, package docs, scripts, and local workflow notes."
user-invocable: false
---

# Project Context

Before changing code, running tests, reviewing, or delegating work in a repository, load the project's own operating knowledge that is not already guaranteed by the current agent runtime. Repository instructions override generic habits when they are specific and current.

## Read First

Look for project-specific operating docs at the workspace root and relevant package/service root:

- `TESTING.md`
- `CONSTRAINTS.md`, when present — use the existing quality bar; do not create one merely to begin work
- `README.md`
- `AGENTS.md`
- `docs/`
- `scripts/` usage notes or script headers
- framework-specific or package-specific instruction files

Read the relevant entrypoints and follow references needed for this task; `docs/` and `scripts/` are search locations, not an instruction to load every file. Reuse context already read unless it changed.

Do not spend attention re-reading standard AI instruction entrypoints that the current runtime already loads, such as `CLAUDE.md` for Claude Code or `.github/copilot-instructions.md` for GitHub Copilot. Read them only when you are outside that runtime, unsure whether they were applied, diagnosing instruction behavior, or preparing context for a zero-knowledge subagent.

Quick-scan `CONTRIBUTING.md` only when it exists and only for workflow rules that affect the task: setup, test, lint, format, commit, CI, migrations, generated files, or release commands. Do not spend context on community contribution process unless the task involves PR/release workflow.

When working in a monorepo, read the closest relevant package instructions, not only the workspace root. If a task touches `services/<service>`, prefer `services/<service>/TESTING.md` over a generic root testing section when they conflict.

## Apply The Context

- Use project-provided test commands and scripts before generic framework commands.
- Follow documented setup, environment, database, fixture, and reset rules.
- Respect local agent instructions for review, implementation, and handoff.
- Preserve project-specific naming, API, data, and architecture conventions.
- If instructions conflict, prefer the most local and task-specific file, then ask only if the conflict blocks safe progress.

For UI work with an existing reference, first distinguish a new feature, behavior migration, and exact UI/UX parity. A new feature does not require demo observation or source copying. For a relevant migration, inspect the source and, when runnable, observe the demo before implementation; check permission/licensing, framework and dependency compatibility, security, and maintenance constraints before deciding on full migration, partial reuse, or necessary reimplementation. Inspect tokens, fonts, global CSS, import order, class merging, shared primitives, assets, and component dependencies only when they affect the requested UI, then pass material findings to any implementation worker. Source observation is context, not acceptance verification. If the demo cannot run, report that limitation and continue from available source when safe rather than inventing a blocking gate.

## Version-Sensitive Decisions

For version-sensitive APIs, check the resolved version in the lockfile or installed package, then consult matching official docs; use local source/types or a focused executable check to resolve gaps. State material uncertainty and cite non-obvious version-dependent decisions. Newer docs alone do not justify dependency upgrades or rewrites.

## Tests And Tooling

Never assume the default test command is the fastest or safest command. Many projects wrap test runners to reuse expensive setup such as compilation, containers, databases, browsers, emulators, or seeded fixtures.

Before running tests, check project docs for:

- preferred fast local command
- integration vs unit test split
- database or fixture isolation strategy
- commands for running multiple affected test files in one invocation
- ignored, external, sandbox, or destructive test suites
- CI-only commands and local alternatives

If the project provides a custom runner, use it unless you are explicitly diagnosing the runner or the user asks for raw framework behavior.

## Resource Ownership (Including Solo Work)

These rules apply to ordinary development, debugging, builds, and review without a leader or subagents.

- Before creating temporary resources, identify the project's teardown commands and record ownership in existing task notes: process/session identity, exact paths, purpose, cleanup method, and lifetime. Include test data, browser profiles, snapshots/worktrees, isolated build outputs, and containers/volumes. Distinguish disposable resources from retained project caches and shared services.
- Reuse the project's runner and compatible build cache. For an isolated experiment or review, prefer one task-owned temporary root containing its disposable source copy, build output, and test data. Keep deliverables and small reproduction evidence outside the disposable root before removal. Do not create another large cache per retry by default.
- Clean at the actual end of each experiment, review round, or development phase, including cancellation and failed attempts; a context compaction or worker relay alone does not end continuing work. Reuse compatible task-owned state when the next owner accepts it and can access it, keep shared phase services under one stable owner, and close unused or non-transferable handles without rebuilding the rest. Stop owned writers, release handles, remove disposable data, and verify both process exit and path removal. Confirm exact path ownership before deletion; names, age, or a temporary location alone do not prove a resource is unused. Preserve unrelated resources and uncommitted work.
- For large builds or repeated suites, check free space and relevant output sizes before starting and at phase boundaries. Keep reusable project caches deliberately, with an owner and a size/expiry review point; prune only when no build or consumer uses them. If space is insufficient, resolve owned leftovers or report the constraint before launching more heavy work. Do not globally wipe caches to make room.

When running tests or browser QA, also apply the `testing` skill's fixture and process teardown rules when available. Fix recurring leaks in the responsible fixture/runner when in scope; manual cleanup alone does not prevent the next run from leaking.

## Subagents

When spawning or briefing a subagent, include the relevant project context in the prompt or direct it to read the same instruction files first. A zero-knowledge subagent should not guess test commands, setup rules, or repo conventions from generic language knowledge.

## Handoff

When reporting results, mention the project-specific command or convention used, especially for tests. If you intentionally did not run a documented command, state why.

For resources created during the task, report `closed/removed (verified)`, `retained (owner, reason, expiry)`, or `cleanup blocked (owner, resource, error)`, including paths and sizes for substantial retained outputs. A successor must accept retained resources; without a successor, the current task owns cleanup. Context handoff or thread archival proves neither process exit nor disk reclamation. After an interrupted run, reconcile its recorded resources before creating replacements.
