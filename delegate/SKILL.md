---
name: delegate
description: "Use when planning or starting a bounded implementation from a spec, a multi-step browser or release check, or a regression run that needs baseline comparison. Also use for independent review, parallel work, a second opinion, an explicit delegation request, or acting on an agent's report."
user-invocable: false
---

# Delegate

The agent that holds the goal is the **driver**. It discusses requirements, writes the plan, implements work that needs that context, reviews diffs and owns acceptance.

## When to delegate

When planning, and again when the next phase becomes clear, look for a whole job another agent can finish from existing inputs. For example, once the release contents are settled, one agent can build the package, run the documented installation checks and return the artifact identity and any failures.

If the agent can start from the spec, scripts or agreed cases, and the driver can judge the result from a diff or focused evidence, compare the cost of doing the work locally with briefing, execution and verification together. **Delegate the whole job when it saves driver context and the handoff pays for itself**, even when the driver must wait. Choose the model for the job after making that decision. Keep a quick command with short output local.

| Reason | Example |
| --- | --- |
| Independent verification | A review or exploratory QA that must not be anchored on your reasoning. |
| Bounded execution | Run the browser cases or compare a failing suite with its baseline. Give one agent the environment for the whole run and ask for reproducible failures. |
| Noise isolation | Browser capture, long logs, broad searches: you need the conclusion, not the output. |
| Disjoint parallel work | Separate repositories or files with no shared state, where wall-clock time matters. |
| A bounded hard problem | A security, concurrency or cross-system conflict that needs a stronger model or a second opinion. It returns analysis. The fix and the judgment stay with the driver, unless the driver is below the strongest model and they cannot be separated from the analysis. |
| Specified implementation | A whole deliverable whose behaviour, boundaries and acceptance checks are already recorded. See [Specified implementation](#specified-implementation). |

Do not delegate:

- A step inside a causal chain you are working. Diagnosis, the fix and judging whether it worked stay with one owner. Collecting evidence for that judgment, such as a capture or a test run, can be delegated.
- Work whose details you need afterwards and could only get from a report. A diff you read carries them; a summary does not.
- Because an earlier task used agents. Size each request on its own.

An explicit user request wins, both on whether to delegate and on how many agents to use; the write-ownership rule below still holds. When the choice is not obvious, state it with the reason in one line and proceed.

Work larger than one context stays with the driver: keep a short current-state note in the existing task record (goal, decisions, rejected hypotheses, next action, valid evidence) and continue across compaction. Delegate a part of it only for a reason above. If the driver can no longer reconcile that note with the source (forgotten decisions, repeated rejected hypotheses, contradictory readings) and a targeted reread does not restore it, it stops starting new work, reaches a safe boundary and hands the goal to one successor session, telling the user. The successor is the new driver: it confirms the old writes have stopped and inherits the goal, authority, failure history, budget and valid evidence without restarting them.

### Specified implementation

Hand a specified deliverable to one implementation-capable agent when the cost comparison above favors delegation and these conditions hold:

- The spec or task record settles the expected behavior, boundaries and acceptance checks. The agent can start by reading it. Keep implementation with the driver if it depends on unrecorded discussion.
- The agent can own its write scope. Changes to behavior at a security, money, concurrency, data-integrity or cross-system contract boundary stay with the driver. A separable lower-risk part can still be assigned.

The agent owns the deliverable through diagnosis, change and repair until its acceptance checks pass. It stops and returns its state instead of guessing when the record is ambiguous or contradicts the code, the work needs a file outside its scope, a decision appears, or the same gap has failed two repairs.

The driver is the escalation path and needs no user. On a return, and at acceptance, name the cause first:

1. Something the record already answers, or work left out: the same agent continues.
2. A gap in the record: the driver decides, records it, and the same agent continues.
3. Evidence that overturns the plan, or a second return on the same item: the driver takes the deliverable to acceptance itself.

Only a decision that is the user's waits for the user, and only the dependent work waits.

Accept against the original requirement, not the report: read the diff, read the evidence for each acceptance item, and rerun the check that decides. After parallel deliverables merge, rerun the checks whose call paths they share.

When a decision exceeds the driver’s capability, it asks the strongest model for a ruling; when the ruling and the fix cannot be separated, the strongest model takes that item to acceptance.

## Rules

- **Only the driver delegates.** A delegated agent does not spawn further agents; it returns the need to the driver.
- **At most three delegated agents run at once.** Run dependent work sequentially.
- **One owner per write scope and per shared environment.** An agent that writes gets files or a checkout nobody else writes, the driver included, until it returns. Whoever started a shared service owns it. Nobody else resets or restarts it, or changes its source, data or runtime while a run depends on it.
- **Delegated agents are short-lived.** One that runs out of context returns what it has to the driver, who finishes the rest or briefs a fresh agent from its own record. A delegated agent never hands over directly to a successor.
- **The user's limits travel with the brief.** Read-only, once-only, resource and policy restrictions bind every delegated agent.

## Brief

Point to the existing spec, script or cases. Give the agent its outcome, scope, acceptance check and what evidence to return. Add only the context it cannot get there, such as a ruled-out diagnosis, a sensitive boundary, a budget or a live resource it must clean up. Pass on the user's limits and tell the agent to return unresolved decisions to the driver. It owns routine execution and repair within scope.

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

A **tier** is a model's position in its provider's lineup. "Stronger" and "lighter" in this skill always mean tier; reasoning effort does not change it. Within a tier, use the newest version the host lists.

The table gives the tiers for two families and the work that belongs on each, lightest first. It holds the defaults as of November 2026: a routing preference that follows the providers' own positioning, not a benchmark.

| Tier | Claude | GPT / Codex | Work | Effort |
| --- | --- | --- | --- | --- |
| Light | `claude-haiku-5-5` | `gpt-6-luna` | A command with a determinate result: run existing tests or a build, read logs, search, report results | `high` |
| Standard | `claude-sonnet-5-5` | `gpt-6.1-terra` | Interactive execution of explicit cases: browser, capture | `high` |
| Strong | `claude-opus-5-5` | `gpt-6.2-sol` | Specified or parallel implementation, designing tests, exploratory QA, independent review of cross-file correctness, architecture and spec consistency | `medium`; `high` for a review or a genuinely difficult job |
| Strongest | `claude-fable-5-2` | `gpt-6.2-astra` | Planning and architecture, high-risk implementation and review, a bounded hard problem: deep security, concurrency, cross-system spec conflicts | `medium`; `high` for a review or a genuinely difficult job |

Three rules hold whatever the table says:

- **A high-risk review is not lighter than the driver.** A review of security, concurrency, money or data-integrity changes uses the driver's tier or a stronger one, at `high`.
- **A hard problem never goes to a lighter tier.** It goes to a stronger model of the same family. When the driver already runs the strongest, it is a fresh-context second opinion on the driver's model at `high`.
- **Tier before effort.** Choose the tier by the judgment the job needs, then take the effort from the table. Do not go above `high`: when `high` on a tier is not enough, go up one tier at `medium`. More effort buys a model time, not the judgment of the tier above.

Then:

- **A capability failure moves the job up.** A false pass, or a stall on the same step twice, moves that kind of job up one tier for the rest of the task; record the change. A failure caused by a missing input does not count.
- An assignment that mixes execution with exploration uses the Strong tier for the whole assignment; do not split it to save model cost. A reviewer does not replace exploratory QA.
- **Outside the table** (the driver's family is not listed, or a listed model is unavailable or out of quota): a delegated agent uses the driver's model and reasoning level, mechanical execution and capture use a lighter model of the same family, and the three rules above still hold. Use the models the host lists and say which was used. If none fits, the driver does optional work itself; a required independent review stays pending and is reported.
- Keep one provider family per task unless the user asks for a mix. Never switch family silently.
- Pass the model and reasoning level explicitly and record requested and effective values; a requested name does not prove the active configuration.

## Spawning and cleanup

- Use the harness's thread or agent mechanism, so the delegated agent has an isolated context and can be waited on and messaged; use native subagents when it has none. Follow the harness's own guide for commands. In bb, pass `--provider`, `--model`, `--reasoning-level` and `--permission-mode auto` explicitly.
- Keep working on whatever does not depend on the delegated agent. Prefer completion notifications and bounded waits over polling. Silence or age alone is not a stall: inspect recent output, steer first, and replace the agent only when recovery requires it.
- After a provider failure, inspect thread state, saved work and resource ownership before retrying. Do not replay an operation whose outcome is unknown.
- Resources a delegated agent starts follow the lifecycle rules in `testing` and `project-context` when available: an owned handle, a cleanup method, a verified exit. Before removing an agent, confirm its writes have stopped and its resources are closed or accepted by the driver. Removing a thread does not clean up its processes or files.
