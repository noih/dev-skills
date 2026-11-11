---
name: delegate
description: "Use when: about to spawn a subagent, thread or worker; deciding whether to hand work to another agent; the user asks to dispatch, parallelize, or use a reviewer / QA / browser agent; choosing a delegated agent's model; or acting on a delegated agent's report. The agent that holds the context does the work, code included, and delegates only what a separate context does better."
user-invocable: false
---

# Delegate

The agent that holds the goal and its context is the **driver**. It does the work itself, code included. Another agent is a tool for one bounded job. There is no leader that only dispatches and no team to staff.

## Why

- A brief drops detail on the way down and a report drops it on the way back. Neither side can see what the other missed.
- A delegated agent rebuilds context the driver already has, and the driver often has to check the result against the source anyway.
- Briefing, waiting, relays and proof packs can cost more elapsed time than the work they organize.

## When to delegate

Delegate only for one of these reasons. Otherwise do the work yourself, whatever its size.

| Reason | Example |
| --- | --- |
| Independent verification | A review or exploratory QA that must not be anchored on your reasoning. |
| Noise isolation | Browser capture, long logs, broad searches: you need the conclusion, not the output. |
| Disjoint parallel work | Separate repositories or files with no shared state, where wall-clock time matters. |
| A bounded hard problem | A security, concurrency or cross-system conflict that needs a stronger model or a second opinion. It returns analysis; the fix and the judgment stay with the driver. |

Do not delegate:

- A step inside a causal chain you are working. Diagnosis, the fix and judging whether it worked stay with one owner. Collecting evidence for that judgment, such as a capture or a test run, can be delegated.
- Work whose details you need afterwards. A summary will not carry them.
- Because an earlier task used agents. Size each request on its own.

An explicit user request wins, both on whether to delegate and on how many agents to use; the write-ownership rule below still holds. When the choice is not obvious, state it with the reason in one line and proceed.

Work larger than one context stays with the driver: keep a short current-state note in the existing task record (goal, decisions, rejected hypotheses, next action, valid evidence) and continue across compaction. Delegate a part of it only for a reason above. If the driver can no longer reconcile that note with the source (forgotten decisions, repeated rejected hypotheses, contradictory readings) and a targeted reread does not restore it, it stops starting new work, reaches a safe boundary and hands the goal to one successor session, telling the user. The successor is the new driver: it confirms the old writes have stopped and inherits the goal, authority, failure history, budget and valid evidence without restarting them.

## Rules

- **Only the driver delegates.** A delegated agent does not spawn further agents; it returns the need to the driver.
- **At most three delegated agents run at once.** Run dependent work sequentially.
- **One owner per write scope and per shared environment.** An agent that writes gets files or a checkout nobody else writes, the driver included, until it returns. Whoever started a shared service owns it. Nobody else resets or restarts it, or changes its source, data or runtime while a run depends on it.
- **Delegated agents are short-lived.** One that runs out of context returns what it has to the driver, who finishes the rest or briefs a fresh agent from its own record. A delegated agent never hands over directly to a successor.
- **The user's limits travel with the brief.** Read-only, once-only, resource and policy restrictions bind every delegated agent.

## Brief

Hand over a complete outcome, not a sequence of steps awaiting approval.

- Outcome and acceptance criteria; scope and protected boundaries.
- What is already known: existing evidence, agreed decisions and scenarios, hypotheses ruled out. Link stable project rules instead of restating them; the agent can load `testing`, `code-review` and `comments` itself.
- Authorized edits and checks, budget and stop condition. Routine edits, reruns and static checks inside that scope need no further permission.
- What to return: a concise result, evidence locations, remaining blockers, changed files, and live resources with their cleanup state.
- What to return instead of deciding: business policy, changed acceptance, new external or destructive effects, an exceeded budget.
- Model and reasoning level.
- That it is a delegated agent: it does not spawn agents, and it reports a need for one, or for a decision, in its result while continuing independent work.

A reviewer gets the original goal, the applicable requirements and decisions, the current diff, relevant call paths and test evidence. It does not get your reasoning about why the change is right.

Verify every path you give exists. Send multi-line prompts through a prompt file or a quoted heredoc; backticks inside double quotes are executed by the shell.

## Acting on results

- **A report is evidence to check, not a result to forward.** Before relying on a claim, read the source or evidence behind the part that matters. When a claim proves wrong, get the command, inputs and output, then fix the responsible implementation, test or environment. Do not answer distrust by adding blanket repetition to later briefs.
- **Findings are input; the driver decides.** Judge each finding against the user's original request: would leaving it unfixed keep the goal unmet, violate a requirement, or break existing behavior the request did not ask to change? Yes: fix it. No: log it for the user at the end. That includes "it would be nicer" and "a cleaner approach" suggestions. Record required fix, optional deferred work or decision needed in the existing task record.
- **Decide within existing authority only.** Reuse decisions, delegation and waivers the user already gave. Business policy, changed acceptance and waivers beyond that authority go to the user, batched with evidence, a recommendation and the impact. Hold only the dependent work.
- **Review when it is required, and bound the rounds.** Get an independent review when the user, the project or the spec workflow requires one, or when the change carries security, money, data-integrity or concurrency risk. An explicit requirement and that risk outrank the size of the change; otherwise a small fix relies on its affected tests. Run at most one review per deliverable (one coherent, acceptance-testable capability), then one re-check limited to the required fixes. A new required fix found in a re-check gets a fix and a re-check of that item only; other new findings are logged, not dispatched. After two re-check rounds on the same change, bring the remaining list to the user.
- When the user agreed a demo or prototype as the requirement, read its source yourself before deciding what it means.

Repeated failed repairs follow the loop guard in `testing` when available; a new agent does not reset that history.

## Model and reasoning

The driver runs on the model and reasoning level the user chose for the session. Explicit user or project configuration overrides everything below.

Two rules hold whatever the table says:

- **A high-risk review is not weaker than the driver.** A review of security, concurrency, money or data-integrity changes uses the driver's model or a stronger one, at the driver's reasoning level or `high`, whichever is higher.
- **A hard problem never goes to a weaker model.** It goes to a stronger model of the same family. When the driver already runs the strongest, it is a fresh-context second opinion on the driver's model at the driver's reasoning level.

Otherwise use the table, chosen by the driver's model family. It holds current defaults for two families: a dated routing preference, not a benchmark.

| Delegated job | Claude | GPT / Codex |
| --- | --- | --- |
| Independent review: cross-file correctness, architecture, security, spec consistency | `claude-opus-5-5` / `high` | `gpt-6.1-sol` / `high` |
| Mechanical execution: run existing tests or explicit browser cases, capture, searches and log reading, report results | `claude-sonnet-5-5` / `medium`; `high` for multi-tab coordination or difficult interactive controls | `gpt-6.1-terra` / `high`; `max` for the same cases |
| Parallel implementation, designing tests, exploratory QA | `claude-opus-5-5` / `medium` | `gpt-6.1-sol` / `medium` |
| Bounded hard problem: deep security, concurrency, cross-system spec conflicts | `claude-fable-5-2` / `medium`; `high` when genuinely difficult | `gpt-6-astra` / `medium`; `high` when genuinely difficult |

- Routing order within a family, lightest to strongest: Sonnet, Opus, Fable; Terra, Sol, Astra.
- An assignment that mixes execution with exploration uses the third row for the whole assignment; do not split it to save model cost. A reviewer does not replace exploratory QA.
- **Outside the table** (the driver's family is not listed, or a listed model is unavailable or out of quota): a delegated agent uses the driver's model and reasoning level, mechanical execution and capture use a lighter model of the same family, and the two rules above still hold. Use the models the host lists and say which was used. If none fits, the driver does optional work itself; a required independent review stays pending and is reported.
- Keep one provider family per task unless the user asks for a mix. Never switch family silently.
- Pass the model and reasoning level explicitly and record requested and effective values; a requested name does not prove the active configuration.
- Keep the lightest configuration that meets the quality bar on actual results. Do not assume a stronger model at lower reasoning beats a lighter model at higher reasoning.

## Spawning and cleanup

- Use the harness's thread or agent mechanism, so the delegated agent has an isolated context and can be waited on and messaged; use native subagents when it has none. Follow the harness's own guide for commands. In bb, pass `--provider`, `--model`, `--reasoning-level` and `--permission-mode auto` explicitly.
- Keep working on whatever does not depend on the delegated agent. Prefer completion notifications and bounded waits over polling. Silence or age alone is not a stall: inspect recent output, steer first, and replace the agent only when recovery requires it.
- After a provider failure, inspect thread state, saved work and resource ownership before retrying. Do not replay an operation whose outcome is unknown.
- Resources a delegated agent starts follow the lifecycle rules in `testing` and `project-context` when available: an owned handle, a cleanup method, a verified exit. Before removing an agent, confirm its writes have stopped and its resources are closed or accepted by the driver. Removing a thread does not clean up its processes or files.
