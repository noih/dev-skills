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
- `README.md`
- `AGENTS.md`
- `docs/`
- `scripts/` usage notes or script headers
- framework-specific or package-specific instruction files

Do not spend attention re-reading standard AI instruction entrypoints that the current runtime already loads, such as `CLAUDE.md` for Claude Code or `.github/copilot-instructions.md` for GitHub Copilot. Read them only when you are outside that runtime, unsure whether they were applied, diagnosing instruction behavior, or preparing context for a zero-knowledge subagent.

Quick-scan `CONTRIBUTING.md` only when it exists and only for workflow rules that affect the task: setup, test, lint, format, commit, CI, migrations, generated files, or release commands. Do not spend context on community contribution process unless the task involves PR/release workflow.

When working in a monorepo, read the closest relevant package instructions, not only the workspace root. If a task touches `services/<service>`, prefer `services/<service>/TESTING.md` over a generic root testing section when they conflict.

## Apply The Context

- Use project-provided test commands and scripts before generic framework commands.
- Follow documented setup, environment, database, fixture, and reset rules.
- Respect local agent instructions for review, implementation, and handoff.
- Preserve project-specific naming, API, data, and architecture conventions.
- If instructions conflict, prefer the most local and task-specific file, then ask only if the conflict blocks safe progress.

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

## Subagents

When spawning or briefing a subagent, include the relevant project context in the prompt or direct it to read the same instruction files first. A zero-knowledge subagent should not guess test commands, setup rules, or repo conventions from generic language knowledge.

## Handoff

When reporting results, mention the project-specific command or convention used, especially for tests. If you intentionally did not run a documented command, state why.
