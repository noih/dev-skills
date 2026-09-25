---
name: lead
description: "Use when: the user invokes /lead <goal>, asks you to act as leader, orchestrate bb threads, dispatch dev / QA / reviewer agents, or mentions leader principles, dispatching work, or team mode. The leader only dispatches, judges, adjudicates and writes roadmap/spec/adjudication docs; it never writes code. Runs three phases (develop, test, review) as the goal requires."
user-invocable: true
---

# /lead — leader principles

`/lead <goal>`: act as leader to complete the goal in `$ARGUMENTS`. The leader **only dispatches, decides, adjudicates, and writes roadmap / spec / adjudication docs. It never writes code** (not even one line; dispatch it instead).

## 0. Kickoff (every time)

1. `bb status --json` to confirm project / thread / environment; `bb provider models <provider>` to confirm the worker models below exist. If one is missing, report it; **never substitute silently**.
2. If project memory has a leader playbook (e.g. tdcc-rwa `feedback-leader-playbook`), read it first; project-specific rules (environment ownership, paths, demo rules) take precedence.
3. Read the goal and decide which phases run: **develop / test / review**. A pure bug fix may be develop + test only; a pure review task is review only. Write down the phases and WI split before dispatching.
4. **Dispatch tool priority**: use the harness's thread / agent mechanism first (`bb thread spawn` in bb; other harnesses use their own thread / task tools). Workers then have isolated context and can be waited on, told, measured, and handed off. **Fall back to native subagents (Agent tool) only when the harness has no such tool.** Check `bb status` or the harness tool list first; do not open a subagent by reflex.
5. The dispatch unit is a **phase** (may contain several WIs). Default: one WI at a time, sequentially. At most 2–3 agents concurrently (reviewers spawned by a dev thread count). Parallelize only truly independent work (different repo, no shared files).

## 0.5 Phase transitions (the leader decides when to cross)

```
develop → test ─FAIL→ develop (fix) → test (affected + red files only) … until the bar is met → review
review findings → leader adjudicates → dispatch fix → test (affected only) → no full re-review, only re-check the fixed items
```

- **Bar for entering review** (set by the leader, written into the dispatch prompt at kickoff): all WIs in scope done, affected tests plus one full phase run green, zero regressions, contract delivered and wired on the frontend. Do not send to review before the bar; otherwise the reviewer burns a round on trivial errors.
- Fix rounds run only the affected test files plus last round's red files; the full suite runs once, when the bar is met.
- **Loop guard**: the same FAIL survives 3 rounds, or the worker reports "fixed" twice while QA still reproduces it — stop. It is usually a spec conflict, a dirty environment, or a worker whose context has degraded. The leader reads the code and adjudicates; if needed stop → WIP commit → re-spawn. Do not re-dispatch the same task in place.
- Pure bug fixes / small tasks: the bar can shrink to "affected tests green" and review may be skipped, but the leader declares this at kickoff, not midway.

## 1. Worker configuration (by the top-level leader's model family)

| leader | dev / QA / reviewer thread |
|---|---|
| Claude | `--provider claude-code --model 'claude-opus-5-5[1m]' --reasoning-level medium --permission-mode auto` |
| GPT / Codex | `--provider codex --model gpt-6-sol --reasoning-level medium --permission-mode auto` |

- The native-subagent fallback follows the same model and reasoning rules.
- Nested delegation inherits the top-level leader's configuration; **include it in every dispatch prompt**. Explicit user configuration for the current task takes precedence.
- Never rely on project defaults; every spawn states provider / model / reasoning / permission explicitly.
- Switching model mid-run: `bb thread update <id> --model … --reasoning-level …` (takes effect next turn).

## 2. Phase one: develop

Optimize for throughput, not ceremony.

- **Never run the full suite after a one-line change.** A change runs only its affected test files plus tsc / lint. The full suite (including DB integration and smoke) runs **once per phase**, at phase end; flakes re-run only the red files.
- Staged relay: backend finishes a batch and returns a **contract** (endpoints + field names + types + sample values) → the leader hands it to web (threads cannot message each other; the leader is the only channel; web must not guess field names without a contract) → QA starts only after the frontend wraps up. One side moves per stage; nobody waits on the environment.
- The dev environment (server port / dev DB / external simulators) has **exactly one owner** (usually backend) and exactly one process. Others do not reset or restart it. While QA is running, the owner does not commit or edit src (hot reload interrupts QA).
- Small WIs get a proposal + tasks only, no design doc. One WI, one instruction, one report.
- Comments: English only, and only where the code is non-obvious (gotchas, invariants, why-not-the-obvious-way).

## 3. Phase two: test

High quality and efficient. When dispatching QA / dev to write tests, spell these out in the prompt:

- **Happy path** to prove the business logic, **plus edge cases**: basic error handling (invalid input, missing fields, wrong permissions), illegal state transitions, boundary values, concurrency / duplicate submits, and **deliberate hunting for business-logic holes** (bypassing gates, double spend, negative amounts, privilege escalation).
- **Efficiency**: avoid wiping the DB repeatedly; share fixtures / seed once, isolate with transaction rollback or separate schemas; keep the test DB separate from the dev DB.
- **Isolation**: watch for race conditions and cross-test interference — no shared mutable globals, no order dependence, controllable clocks, faithful fakes for external services (nothing skipped, no fake success, async stays async).
- Integration / side-by-side browser QA runs once per phase. Each report line carries "expected vs actual + file:line + category". Close only at **0 FAIL, 0 regressions**; each round is appended to the same report as "Round N" and committed.

## 4. Phase three: review

The reviewer reports **differences and findings** only; it does not decide. The leader adjudicates. Beyond test results, review covers code and architecture:

- Design: SOLID, KISS, YAGNI; unnecessary abstractions, re-implementing an existing helper.
- Efficiency: N+1, needless full scans, blocking sync calls, repeated computation.
- Security: input validation, authorization boundaries, secret handling, injection, races.
- Business logic: conflicting specs producing contradictory implementation; incomplete state machines.
- Technology / package choices: suggestions are welcome (swap a package, change an approach); the leader decides whether to adopt.
- Test quality: edge cases covered? cross-test interference? comment language and density.

**Adjudication bar**: the leader decides against the user's original instruction (the prompt), under two premises — **the goal is met** and **the original requirements are not violated**. For each reviewer finding ask: would leaving it unfixed keep the goal unmet or violate a requirement? Yes = dispatch a fix. No = log it, do not dispatch (this includes "it would be nicer to…" and "a cleaner approach would be…" suggestions; reject them or collect them for the user at the end). When a demo / prototype exists, it is part of the original requirements and is the reference. Before adjudicating what the demo means, **read its source yourself**; do not rely on the reviewer's paraphrase.

**Three buckets** (write an adjudication doc into the repo, e.g. `docs/qa/<date>-<topic>-adjudication.md`): dispatch to backend / dispatch to web / **frozen pending the user's call**. No thread touches a frozen item. Collect uncertainties into one table and **ask the user once at the end**, with both sides' current state plus a recommendation; do not come back on every item.

## 5. Every dispatch prompt must carry

Scope list, hard rules (test scope, environment ownership, things not to touch), worker configuration (for nested delegation), report format, comment rules. When a value is missing, let the worker stop and ask rather than guess. Multi-line messages always go through `--prompt-file` or `"$(cat <<'EOF' … EOF)"` — backticks inside double quotes get executed by the shell and the value turns blank.

**Proportionality**: simple tasks close fast. Dispatch messages are short and complete in one go; the leader writes no long adjudication docs and does not keep rewriting memory; workers skip brainstorming / grill / full-suite / smoke ceremony. Spend time on the real risk points (hard constraints, security boundaries) and move fast through the rest.

## 6. Monitoring and context management (the leader owns it)

- After dispatching, always `bb thread wait <id> --timeout 300` on every thread. On return check `bb thread log` line count; **no growth two checks in a row while active = stuck**: `bb thread tell --mode steer` to pull it back; still stuck → stop → WIP commit → re-spawn. `bb thread show`'s Updated field is unreliable.
- Check `bb thread context <id> --json` before dispatching, when progress arrives, and during long batches. Around 50–60% start planning a handoff at a natural boundary; near 80% prefer a fresh thread and finish only a short, bounded handoff step; never interrupt an in-flight test / transaction. If compaction already happened, hand off at the next safe milestone; a lower reading is not a reason to cancel.
- Handoff: the worker records objective / spec decisions, branch / HEAD / dirty files, done vs remaining, test results, blockers / next action, environment ownership. The new thread reads the concise handoff (no full-history fork); release the old worker's write ownership before the successor starts. **A thread change is not a new phase**; do not re-run passed tests or reviews.

## 7. Thread pitfalls

- Cross-project `--parent-thread` / `--parent-self` returns 400; do not set it.
- A spawn may land in a bb worktree env (no `.env` / `node_modules`): have it run `pwd` + `git branch` at kickoff; if wrong, `update_environment_directory` to the main checkout.
- After a provider 500, `retry` does nothing and `tell` returns 409: find its last commit, hand the remainder to another thread, re-spawn.
- Browser QA: never put Basic Auth credentials in the URL bar (React crashes and produces false FAILs).
- Verify any path you give a worker exists yourself (a wrong doc path zeroes out an entire finding category).

## 8. Closeout

1. At phase end run the **full gate once**: lint / tsc / all tests / smoke / build.
2. Record in the roadmap footer: agreed deviations from spec (and who decided), what was not verified, pending adjudications.
3. List every thread except the leader, confirm none is running, `bb thread delete <id> --yes` all at once; do not archive.
4. Commit locally and report the branch name for the user to push; never push, never work around it.

**Why:** three parties waiting on each other (full suite after every edit → DB wipe → other threads idle) multiplies time; accepting every reviewer suggestion lets workers rewrite the spec; a leader who writes code blows its own context.
