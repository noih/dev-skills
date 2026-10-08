---
name: lead
description: "Use when: the user invokes /lead <goal>, asks you to act as leader, orchestrate bb threads, dispatch dev / QA / reviewer agents, or mentions leader principles, dispatching work, or team mode. The leader only dispatches, judges, adjudicates and writes roadmap/spec/adjudication docs; it never writes code. Runs the develop, test, and review workflow stages as the goal requires."
user-invocable: true
---

# /lead — leader principles

In Claude Code, use `/lead <goal>`; in Codex, use `$lead <goal>`. Act as leader to complete the user-provided goal. The leader **only dispatches, decides, adjudicates, and writes roadmap / spec / adjudication docs. It never writes code** (not even one line; dispatch it instead).

`lead` owns team assignment, coordination, resource ownership, handoffs, and adjudication. `sdd`, when in use, manages development progress and stage readiness for either solo or team work. They can operate together: use the same task state and verification evidence, let `sdd` identify what remains, and let the leader assign who completes it. Neither requires enabling the other.

## 0. Kickoff (every time)

1. `bb status --json` to confirm project / thread / environment; `bb provider models <provider>` to confirm the selected leader and worker models below exist. Configure the leader's model / reasoning through supported harness controls; if the current session cannot change them, report the mismatch. If a model is missing, report it; **never substitute silently or claim an inactive configuration is active**.
2. If project memory has a leader playbook (e.g. tdcc-rwa `feedback-leader-playbook`), read it first; project-specific rules (environment ownership, paths, demo rules) take precedence.
3. Read the goal and split it into coherent **business delivery phases**: each is an implementable and acceptance-testable capability (for example, customer onboarding, then billing), not a page, module, checkbox, commit, worker, or handoff. Within each delivery phase, choose the needed workflow stages: **develop / test / review**. A small bug fix may need develop + test only; a pure review task is review only. Write down the delivery phases, their stage scope, and any context-sized WI batches before dispatching.
   - **Plan verification before the first dispatch.** Load `testing` yourself and apply Verification Cost And Risk. Give each delivery phase one acceptance scope, owner, and budget; select checks by required behavior, material risks, and missing evidence rather than assigning every test layer. Batch acceptance after the coherent implementation is ready, with explicit triggers for deferred checks and focused early checks where late discovery would cause costly rework. Whole-application verification and repetition / stress loops need a requirement or specific risk; completion alone does not require them.
   - **Estimate and tell the user.** Give a rough delivery estimate with planned expensive checks and their combined cost, including setup, execution, agent output/diagnosis, and likely rework. Reuse recorded timings and token usage when available; otherwise mark estimates provisional and measure during necessary work, not an extra full gate just for timing. Record the budget in existing task notes; it does not lower the acceptance bar.
4. **Dispatch tool priority**: use the harness's thread / agent mechanism first (`bb thread spawn` in bb; other harnesses use their own thread / task tools). Workers then have isolated context and can be waited on, told, measured, and handed off. **Fall back to native subagents (Agent tool) only when the harness has no such tool.** Check `bb status` or the harness tool list first; do not open a subagent by reflex.
5. The acceptance unit is the **business delivery phase**; a dispatch may cover the whole phase or a context-sized WI batch within it. Run dependent batches sequentially and carry the phase's remaining work and evidence across relays. A WI, page, worker, or handoff does not earn its own acceptance gate. At most 2–3 agents concurrently (reviewers spawned by a dev thread count). Parallelize only truly independent work (different repo, no shared files).

## 0.5 Delivery-phase transitions (the leader decides when to cross)

```
develop → test ─FAIL→ develop (fix) → test (affected + red files only) … until the bar is met → review
review findings → leader adjudicates → dispatch fix → verify affected behavior and review impact
```

- **Delivery phases own scheduled verification.** Develop, test, and review are workflow stages within a delivery phase. Complete the coherent phase implementation before its scheduled acceptance run. A module, page, WI, fix round, re-check, or replacement worker does not create another gate, full build, screenshot campaign, or stress loop. Source-demo observation is implementation context, not acceptance evidence.
- Later phases verify their own scope. Preserve an earlier phase's passing evidence unless a specific shared API, authentication, style, dependency, fixture, or environment change invalidates named cases; then rerun only that affected earlier coverage alongside the current phase. At final integration, assess remaining cross-feature gaps using `testing`; do not automatically rerun the whole application.
- **Bar for entering closeout review** (set at kickoff): in-scope implementation complete, agreed test gate satisfied with current evidence, and required integration connected. Coordinate this with SDD stage readiness when used. An explicitly requested diagnostic review may inspect unfinished/failing work without claiming closeout readiness.
- Fix rounds reuse valid evidence and run affected checks, including transitive callers and shared behavior. Re-check fixes and their impact; expand testing/review when new changes or risks invalidate earlier evidence. Do not repeat a full gate solely because a round or worker changed.
- **Review rounds are bounded.** One review per delivery phase, then one re-check limited to the blocker / major fixes. Minor fixes are verified by their own tests, not by another reviewer pass. New minor findings from a re-check are logged for the end, not dispatched as another round. A new blocker / major found in a re-check gets a fix and a re-check of that item only. After two re-check rounds on the same batch, stop and bring the remaining list to the user instead of opening a third.
- **Loop guard**: the same FAIL survives 3 rounds, or the worker reports "fixed" twice while QA still reproduces it — stop. It is usually a spec conflict, a dirty environment, or a worker whose context has degraded. The leader reads the code and adjudicates; if needed stop → WIP commit → re-spawn. Do not re-dispatch the same task in place.
- Pure bug fixes / small tasks: the bar can shrink to "affected tests green" and review may be skipped, but the leader declares this at kickoff, not midway.

## 1. Leader and worker configuration

Choose the column by the top-level leader's model family. Each cell specifies **model / reasoning**.

| work | Claude | GPT / Codex |
|---|---|---|
| Leader: scope, dispatch, evidence assessment, adjudication | `claude-fable-5-1` / `high` | `gpt-6-astra` / `high` |
| Dev | `claude-opus-5-5[1m]` / `medium` | `gpt-6.1-sol` / `medium` |
| QA: execute existing tests / explicit browser cases, report failures | `claude-sonnet-5-5[1m]` / `medium` | `gpt-6-luna` / `medium` |
| QA: design tests, explore business-logic / authorization / state-transition / concurrency holes | `claude-opus-5-5[1m]` / `medium` | `gpt-6.1-sol` / `medium` |
| Reviewer: cross-file correctness, architecture, security, spec consistency | `claude-opus-5-5[1m]` / `medium` | `gpt-6.1-sol` / `high` |
| Escalation: a difficult, clearly bounded investigation or finding | `claude-fable-5-1` / `medium` | `gpt-6-astra` / `medium` |
| Escalation: deep security, concurrency, or cross-system spec conflicts | `claude-fable-5-1` / `high` | `gpt-6-astra` / `high` |

- **QA routing**: Sonnet / Luna execute defined cases; Opus / Sol design coverage and hunt holes. If one assignment includes execution and exploration, use Opus / Sol for the whole assignment; do not split merely to save model cost. A reviewer does not replace exploratory QA.
- **Escalation belongs to the leader**: choose it when the work warrants deeper analysis or the default worker's evidence is insufficient. Dispatch only the difficult item, with the available evidence and a clear completion condition; routine work keeps its default configuration. Model escalation does not reset the loop guard, reopen passed gates, or expand scope.
- For a bounded escalation, Fable / Astra at `medium` is a starting point; genuinely difficult work uses `high`. Do not assume Fable / medium beats Opus / high or Astra / medium beats Sol / high. Keep the lightest configuration that meets the task's quality bar based on actual results.
- Claude worker spawns use `--provider claude-code`; GPT worker spawns use `--provider codex`. Pass the table's `--model` and `--reasoning-level` explicitly, plus `--permission-mode auto`; never rely on project defaults. Quote model IDs containing `[1m]` in shell commands.
- The native-subagent fallback follows the same model and reasoning rules.
- Nested delegation follows the top-level leader's **model family and task routing**, not the parent worker's model or the leader's `high` effort for every task; **include the routing and escalation rules in every dispatch prompt**. Explicit user configuration for the current task takes precedence.
- Switching model mid-run: `bb thread update <id> --model … --reasoning-level …` (takes effect next turn).

## 2. Stage one: develop

Optimize for throughput, not ceremony.

- During implementation, use `testing` to decide whether a focused early check prevents costly rework, proves a regression, or resolves a failure. Honor user/project requirements; do not automatically run checks after each page or WI. Write focused tests when behavior is known and batch broader suites at the planned checkpoint. Pass the evidence gaps, deferred-check triggers, and existing valid results to workers so they do not independently duplicate verification.
- Before dispatching UI work with an existing reference, classify it as a new feature, behavior migration, or exact UI/UX parity. New features do not inherit demo-observation or source-copy steps. For migrations, inspect reuse permission/licensing, framework compatibility, dependencies, security, and maintenance constraints; choose full migration, partial reuse, or necessary reimplementation. When exact parity is required and the source is confirmed reusable and compatible, prefer moving its components/UI and required assets/dependencies, preserving DOM/JSX, control flow, labels, defaults, fields, styles, interactions, accessibility, responsive behavior, and states. For relevant UI work, pass along tokens, fonts, global CSS, import order, class merging, and shared primitives. Discuss a material strategy choice only when it changes requirements, risk, or cost; do not seek approval again after the approach is confirmed. Generic conventions, native controls, or shorter code do not justify UX changes; security and data-precision fixes remain mandatory.
- Staged relay: backend finishes a batch and returns a **contract** (endpoints + field names + types + sample values) → the leader hands it to web (threads cannot message each other; the leader is the only channel; web must not guess field names without a contract). Relays carry the delivery phase's remaining work and evidence; scheduled acceptance QA starts after the whole phase implementation wraps up. Focused earlier checks follow `testing`'s cost-of-late-discovery rule and explicit user/project requirements. One side moves per stage; nobody waits on the environment.
- Each shared dev environment has **exactly one owner** (usually backend) and one managed instance of each required service. Others do not reset or restart it. While QA uses it, do not change its source, data, or runtime in ways that invalidate the run; coordinate updates at a safe boundary.
- Small WIs get a proposal + tasks only, no design doc. One WI, one instruction, one report.
- Comments: English only, and only where the code is non-obvious (gotchas, invariants, why-not-the-obvious-way).

## 3. Stage two: test

High quality and efficient. When dispatching QA / dev to write tests, spell these out in the prompt:

- **Coverage by risk**: prove the required business behavior and select relevant error, boundary, state-transition, authorization, concurrency, and recovery scenarios using `testing`. Deliberately look for business-logic holes where plausible; this is not a mandatory case matrix for every feature. Reuse existing tests and add only cases that fill material gaps.
- **Efficiency**: avoid wiping the DB repeatedly; share fixtures / seed once, isolate with transaction rollback or separate schemas; keep the test DB separate from the dev DB.
- **Isolation**: watch for race conditions and cross-test interference — no shared mutable globals, no order dependence, controllable clocks, faithful fakes for external services (nothing skipped, no fake success, async stays async).
- **Repetition / stress loops** follow `testing` → Repetition And Stress Runs. The leader assigns a justified loop to one owner after the affected implementation settles, or for a focused flake investigation. A normal fix round runs affected tests once; a worker does not inherit or independently restart a previous loop.
- Plan integration / side-by-side browser QA at delivery-phase boundaries; reuse valid results and rerun affected flows when their inputs change. Each finding carries "expected vs actual + file:line + category". Record each completed test/review round's executed verification, findings, and decisions as historical evidence under "Round N" in the existing report, then commit it. The living current-state handoff links those records and updates current state without duplicating their narration. Close only when the agreed gate is satisfied, with baseline failures, blocked checks, and explicit waivers reported honestly.

## 4. Stage three: review

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

**Write the test scope per delivery phase, not from a template.** Implementation-batch prompts name only justified diagnostics; the phase acceptance prompt names the planned checks and earlier evidence to reuse. Full gates and stress loops require a separate, explicit assignment naming the owner, scope, reason, expected cost, and stop condition from `testing`; never copy "run 100 times" into successive prompts. Workers report newly discovered verification needs to the leader, who checks existing evidence and in-flight runs before assigning more work. A focused diagnostic loop may happen earlier when a concrete failure requires it.

**When a worker's claim proves wrong** (it reported "stable" or "passing" and a reviewer reproduces failures), get the command, inputs, count, and output; diagnose and fix the implementation, test, or environment responsible. Verify the affected behavior with evidence. Do not answer distrust by adding blanket repetition to later dispatches.

For work that starts processes or creates temporary data, source snapshots/worktrees, or isolated build outputs, include the cleanup contract in the dispatch itself; do not rely on the worker discovering a skill:

> Own and record resources you create (capture session/PID identity and exact paths at launch, plus cwd and cleanup method). Reuse compatible task-owned browser/server/build state while the same work or round continues when the recipient explicitly accepts ownership and can access it; the leader may accept interim ownership between sequential workers, and shared phase services keep one stable owner. Note tool-local handles that cannot transfer and close or recreate them only when needed. Close unused resources now and retained resources at the actual end or cancellation of their work, round, or phase unless explicitly transferred for continued work. For resources scheduled for disposal, stop owned writers, remove disposable data/build copies, and verify both exit and path removal. Preserve other owners' resources and uncommitted work. Report `closed/removed (verified)`, `retained (named owner, reason, expiry; paths/sizes for large outputs)`, or `cleanup blocked (owner, resource, error)`. If identity was not captured, make a bounded current-state inspection and report uncertainty instead of mining unbounded historical logs. Until retention is accepted, the current owner remains responsible, with unresolved cleanup returning to the leader.

Do not issue blanket "preserve all sessions/services" instructions. List the exact resources needed for continuation, separate shared services from disposable browsers, and give each retained resource an owner and cleanup deadline or event. Honor explicit user retention, recording who will own it. Artifacts should preserve completed test evidence without keeping its browser alive.

Budget disk and heavy-work concurrency at phase boundaries: inspect free space and substantial retained outputs, reuse a bounded set of compatible build directories, and retire superseded snapshots/targets before creating replacements. Do not overlap heavy compilation with timing-sensitive QA when contention would invalidate results. Keep necessary shared caches under explicit ownership; do not ask workers to globally purge caches or wait until project completion to reclaim disposable outputs.

**Proportionality**: simple tasks close fast. Keep dispatches and adjudication notes concise; avoid redundant brainstorming, repeated records, and checks whose evidence remains valid. This does not waive agreed SDD stages, required independent review, or project quality gates. Spend time on the real risk points and move fast through the rest.

## 6. Monitoring and context management (the leader owns it)

- Prefer completion notifications and bounded waits/status checks sized to the command while preserving user responsiveness; do not impose one polling interval, rapidly poll unchanged state, or dump full logs. Inspect focused recent output and process/session state only when there is evidence of a problem. Silence, age, or an unchanged timestamp alone does not prove a stall. If evidence does show one, steer the worker; stop/checkpoint/re-spawn only when recovery requires it, preserving resource ownership.
- **Check cost before each expensive dispatch, not just at delivery-phase end.** Compare cumulative verification time plus the proposed run with the budget. If it exceeds the budget, runs are duplicating evidence, or the user questions duration, pause new expensive runs and revise the plan: consolidate pending fixes, reuse valid results, narrow invalidated checks, or remove optional loops / review rounds. Tell the user the revised cost and remaining work. Required gates remain required; report a delivery overrun rather than silently lowering the quality bar. Verification taking longer than coding is a signal to inspect, not by itself a reason to skip checks.
- Use `bb thread context <id> --json` at useful decision points. Around 50–60% is a cue to assess remaining work plus realistic handoff and cleanup reserve, not a mandatory switch. Near 80%, prefer a coherent checkpoint when the worker cannot safely finish within that reserve. Actual runtime limits bind; never interrupt an in-flight test or transaction to satisfy a percentage, and do not start a new broad task when reserve is low. After compaction, reassess continuity and reserve instead of automatically replacing the worker.
- Maintain **one concise current-state handoff section** in existing task notes, updating it at meaningful changes rather than after every tool call or by appending repetitive round narratives. It contains the goal, constraints, accepted decisions; cwd, branch, HEAD, and dirty-file ownership; done and pending work; the precise next action and source entrypoints; valid, invalidated, and pending evidence; and active commands/session handles plus resources needed. Link Git or original evidence and preserve required audit records and unique decisions/evidence; do not create a new tracking system or template artifact.
- A worker relay continues the **same business phase** and may be honestly unverified WIP. Preserve coherent changes with a WIP commit when useful or explicit dirty-file ownership; do not manufacture green checks, completion labels, a new gate/review/test run/screenshot campaign, or environment teardown/rebuild merely because the worker changes. Release the old writer before the successor starts. The successor reads the short state plus only the relevant spec and source entrypoints, verifies worktree/resource access, then resumes the exact next action without rereading the full transcript, replanning, or revalidating by default.
- Resource handoff is separate from write ownership. Transfer only compatible resources the successor needs and explicitly accepts; the leader may bridge sequential ownership, while shared phase services retain one stable owner. Record resource IDs at launch, access/health evidence, and expiry; perform only minimal access/health verification on transfer, not a fresh acceptance run. Note and safely close non-transferable tool-local handles, close unused resources immediately, and clean retained resources at the actual work/round/phase end. On a crash or stop, inspect the recorded resources and assign cleanup; the leader owns unresolved entries.
- The leader owns cross-worker decisions, acceptance bookkeeping, and the same current-state discipline for its own context. At handoff or closeout, check effectiveness qualitatively: relay alone must not have caused redundant verification, environment rebuilds, or historical rereading. Do not add a metric collector or reporting framework for this check.

## 7. Thread pitfalls

- Cross-project `--parent-thread` / `--parent-self` returns 400; do not set it.
- A spawn may land in a bb worktree env (no `.env` / `node_modules`): have it run `pwd` + `git branch` at kickoff; if wrong, `update_environment_directory` to the main checkout.
- After a provider 500, `retry` does nothing and `tell` returns 409: find its last commit, hand the remainder to another thread, re-spawn.
- Browser QA: never put Basic Auth credentials in the URL bar (React crashes and produces false FAILs).
- Verify any path you give a worker exists yourself (a wrong doc path zeroes out an entire finding category).

## 8. Closeout

These steps close a delivered phase or task, not a worker relay during an ongoing phase. For a relay, follow section 6: release the old writer and dispose of its resources through verified cleanup or accepted transfer to the leader or successor.

1. At each delivery-phase end, satisfy that phase's agreed acceptance scope, reusing matching evidence and rerunning affected failures as needed. At closeout, resolve pending checks and cross-feature evidence gaps, including any required whole-application gate. Stop when the agreed bar is met; a handoff or closeout does not itself justify another run or review.
2. Record in the roadmap footer: agreed deviations from spec (and who decided), what was not verified, pending adjudications.
3. Before deleting or archiving workers at closeout, reconcile this task's resource records. For resources scheduled for disposal, have the responsible worker verify daemon/browser and test-process exit **and removal of disposable files/directories**; an empty CLI list alone is not enough. A continued resource explicitly transferred to the leader or successor may remain live under its accepted owner, reason, and expiry, with paths/sizes recorded for substantial retained outputs. Clean actual obsolete resources; keep cleanup failures visible and assigned to their current owner. Do not declare closeout complete or delete a worker that still owns unresolved cleanup. Do not stop unrelated agents, personal browsers, or shared services, or remove their data/caches.
4. List this task's worker threads and confirm none is running. Delete only workers whose resources are closed/removed or accepted as retained; leave any worker with unresolved cleanup in place as its owner. Run `bb thread delete <id> --yes` for eligible workers; do not archive unless the user requests it. Thread removal does not replace process or filesystem cleanup.
5. Commit locally and report the branch name for the user to push; never push, never work around it.

**Why:** three parties waiting on each other (full suite after every edit → DB wipe → other threads idle) multiplies time; treating every batch and fix round as its own phase repeats the full gate, a 20–40 minute stress loop, and a review dozens of times for little added evidence; accepting every reviewer suggestion lets workers rewrite the spec; a leader who writes code blows its own context.
