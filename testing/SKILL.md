---
name: testing
description: "Use when: writing tests, designing test strategy, choosing test scope, reviewing test quality, debugging test failures, running browser QA or temporary test processes, managing test data and artifacts, verifying their cleanup, or balancing fast feedback with confidence."
user-invocable: false
---

# Testing

Coverage is a signal, not the goal. Choose evidence that establishes required production behavior and material risks, including unusual states, misuse, partial failure, and recovery. Keep the agreed quality bar fixed.

## Verification Cost And Risk

Before adding or running a test, name the behavior or evidence gap, inspect existing coverage and still-valid results, then choose the lowest-total-cost sufficient check. Count authoring, setup, execution, agent output/diagnosis, maintenance, and likely rework from delayed feedback; do not invent precise token estimates.

- **Prevent failures first.** Reuse proven code, simplify branches/state, and use types and data constraints. Static checks and review answer appropriate questions, but cannot prove runtime behavior or replace required tests.
- **Test the responsible boundary.** Test pure calculations directly, persistence/transactions at the relevant integration, and complete journeys through E2E only where needed. Duplicate assertions across layers need distinct failure mechanisms. Prefer public APIs, service boundaries, database constraints, queue handlers, and integration seams over private helpers.
- **Limit claims to exercised behavior.** Faithful external fakes support executed logic and caller-interaction assertions, but cannot prove a replaced outcome-owning component's behavior. A mocked save does not prove durable state; a captured request proves shape/intent, not transport or remote delivery. Separate tests against independent doubles do not establish seam compatibility. Shared types/fixtures prove only contracts actually checked. Reuse sufficient valid contract/integration evidence, otherwise choose the cheapest sufficient seam check or report the gap.
- **Choose timing by late-discovery cost.** Check risky contracts, authorization, money, and data integrity before dependent work compounds uncertainty. Otherwise defer to a named trigger such as integration completion or phase acceptance. Deferred checks remain pending, with an owner in team work; deferral is not a waiver or pass.
- **Batch and reuse.** Share setup in one invocation and rerun only invalidated scope. Do not remove regression coverage to shrink a run. Select affected tests only when dependencies are understood; investigate uncertain impact and broaden where unresolved.
- **Bound agent overhead.** Retain concise streaming progress, commands, scope, and results; inspect failure details as needed. Share one evidence record across workers rather than copying logs or duplicating runs.
- **Stop with sufficient evidence.** Required behavior and material risks need current evidence and required checks must pass. Then stop adding tests, full-suite runs, or reassurance reviews. Whole-application verification needs a user/project requirement or unresolved cross-feature risk that narrower checks cannot cover. Cost limits never justify weaker assertions, a lower bar, or an incomplete check labelled passed; disclose gaps and reassess the plan.

## Scenario-Driven Business Tests

For features and behavior changes, use `bdd` when available to discover or reuse agreed scenarios before designing business acceptance tests. The deterministic workflow starts from current `.feature` scenarios, supplemented by security and implementation risks. When available, follow [BDD contract/adoption boundaries](../bdd/REFERENCE.md#deterministic-contract-and-adoption), including legacy and OpenSpec compatibility limits. Do not derive acceptance from implementation or treat disconnected legacy tests as deterministic execution.

Without `bdd` or its reference, retain agreed spec scenarios in legacy projects. For new executable BDD acceptance and existing feature-driven projects, default to versioned features/bindings consumed directly or through deterministic generation. No model interprets/translates scenarios or supplies bindings during generation/execution; AI authoring reviewed source beforehand and model calls in the product under test are outside that restriction. Generated tests are derived output: fix source and regenerate, or verify committed output matches current inputs before execution. A missing path remains an integration gap; do not auto-install the skill or silently downgrade required deterministic acceptance.

Map each required scenario and Examples row's preconditions, action, and every outcome to setup/actions and meaningful assertions, including unchanged state and prohibited side effects. Use existing runner/test metadata or the acceptance report. One scenario may need several tests; parameterized tests may cover several examples. Neither one-to-one mapping nor browser E2E is mandatory; reuse sufficient coverage.

Before claiming acceptance, inspect assertions and reconcile required identities with actual results. Counts, matching IDs, a test's existence, or a green suite alone are insufficient. Required skipped, pending, filtered/excluded, undefined, ambiguous, or otherwise unexecuted cases remain gaps. Report integration/coverage gaps, manual checks, deferred automation, and explicit waivers separately; waivers are not passes. New business scenarios update feature, affected requirement/change records, and tests within authority; unresolved policy goes to the user/leader, never into expectations rewritten to fit code. Pure implementation checks need not become business scenarios; pure refactors reuse preservation evidence without a new discovery cycle.

## Writing Tests First

When behavior is pinned down by a reproducible bug, acceptance criterion, protocol/schema, or refactor contract, write the test before implementation. For new/changed behavior, observe an assertion failure for the expected missing behavior; broken fixtures, unavailable services, or missing/pending bindings do not establish regression detection. A refactor instead establishes a passing preservation baseline. Tests check agreed behavior, not override requirements.

For spikes, exploratory work, or an unsettled API shape, implement first and add tests once behavior settles, before calling the change done. Run the focused original-behavior test when needed for regression proof or a preservation baseline; batch broader checks at the planned checkpoint. Early test authoring does not require every related suite early. Follow an explicit test-first workflow when one applies.

## Test Design

Prioritize core behavior and business invariants, then misuse/unusual states, failure/recovery, boundaries, and basic validation not already covered. Use the relevant risks below, not a mandatory matrix for every change:

| Risk | Production-shaped cases and outcomes |
| --- | --- |
| Rules and contracts | Business calculations, request validation, response/error types, transformations and serialization; exact thresholds, empty/min/max/large payloads, and timezone/date boundaries. |
| State and idempotency | Wrong order, repeated calls, retries after timeout, cancellation, rollback, status regression, duplicate submit/webhook/job replay; exactly-once intended effects and no duplicate writes or refunds. |
| Authorization | Right role/wrong owner, right owner/wrong tenant, related-resource permissions, expired session, stale permission; ownership never crosses tenants. |
| Concurrency | Two actors updating one resource, scheduled/manual overlap, races on invariants, timing and ordering interactions. |
| Partial failure and recovery | DB succeeds/external API fails; failed email, cache update or queue publish; slow, stale, partial, duplicated, malformed or unavailable dependencies. Assert consistent state and repeat-safe compensation. |
| Data evolution | Existing/migrated/old records, unusual valid state combinations, missing optional fields, unknown enum values, legacy formats, reordered/duplicate results and stale caches. |
| Integration seams | External service/cache/queue/file/network assumptions and real producer/consumer compatibility; distinguish caller intent from durable or delivered outcomes. |

Assert observable outcomes: returns, durable state, emitted events, queued jobs, audit records, or prohibited effects. Protect domain invariants such as nonnegative totals, exact money, ownership, and legal state transitions.

- One concept/invariant per test; names describe behavior, such as rejecting a duplicate invoice after timeout. Use Arrange-Act-Assert and table-driven cases for variations of one rule.
- Match project file naming, directories, assertions, and mock conventions. Tests should survive behavior-preserving refactors. Keep fast unit tests cheap; put slow integration/E2E setup behind explicit commands.
- Keep tests independent of execution order and shared mutable state; restore DB records, files, queues, timers, and environment changes. Use deterministic production-shaped IDs, relationships, timestamps, permissions and statuses; avoid timing-dependent/flaky assertions.
- Mock only real external boundaries or hard-to-control infrastructure, with fidelity to the relevant behavior. Do not mock away the behavior claimed as proven.
- Group files by a coherent module, route, domain concept, or user-visible flow. Split unrelated `describe` groups; avoid catch-all service/API/fixture files. Keep small distinguishing data inline, noisy repeated defaults in builders, and large fixtures split by scenario. Prefer local readable setup over reuse that requires many unrelated files.
- Use snapshots sparingly for stable serializable output, such as config or API responses, not large UI snapshots that reviewers cannot meaningfully assess.

Do not test framework/library internals, trivial getters/setters, private implementation details, or coverage-only execution. Application route configuration and auth middleware ordering remain application behavior. Prefer error types/codes over incidental wording, except where exact text is an agreed API/UI/acceptance contract. Do not over-mock production-relevant boundaries.

## Running Tests

Use the documented project runner, inspecting its setup, scope, isolation/reset rules, ignored/external/destructive suites, CI-only checks, and filters before execution. Use raw framework commands only when diagnosing that runner or explicitly requested. A stable isolated runner is worth more than a fragile fast path. Document the normal fast command, file/name filters, reset behavior, and full-suite expectations when maintaining it.

For multi-phase work, one coherent business delivery phase owns one acceptance scope, owner, and budget in existing notes. Batch scheduled acceptance after implementation is ready; allow focused early checks for risky assumptions, regression proof, failures, or explicit requirements. A page, module, WI, commit, save, or worker relay does not automatically trigger checks. Small fixes and review-only tasks keep proportionate affected verification.

Choose the scope matching the purpose: named test, affected file, package/module/crate, or whole application only when required or narrower checks cannot resolve cross-feature risk. These are choices, not an escalation ladder. Pass affected tests sharing compilation, containers, DB, browser, fixtures, or migrations to one invocation. Phase B verifies its scope plus named Phase A cases invalidated by shared APIs, auth, styles, dependencies, fixtures, or environment changes. A broad build/lint/mocked-and-real/screenshot campaign per page is not an affected-check exception. Failure fixes rerun affected checks until they pass; phase scheduling is not a once-only quota.

### Preferred Frameworks

Keep the existing framework. If none exists, prefer Node.js/TypeScript: Vitest; React: Vitest + @testing-library/react with role/text/label queries; Rust: cargo test + nextest; Python: pytest.

### Output And Exit Status

Default to concise output; use verbose failure output only when more context is needed. Long runs must stream progress at file/binary/test boundaries and retain diagnostics, so slow and stalled runs can be distinguished. Do not hide progress behind `tail`, counting filters, discarded output, or background execution without an accessible log. Silence alone does not prove a stall or justify killing the run; inspect buffering, process activity, and timeout first.

Capture the actual test exit status: successful launch, logs, and a successful output consumer do not prove test success. In Bash/Zsh pipelines, preserve `pipefail` status immediately so `tee` cannot mask failure; other shells need an equivalent supported method. Background work needs an owned process/tool session handle and collected final status; a later shell may not be able to `wait` for that child.

Before choosing reporter overrides, piped/background execution, or repetition calculations, read the relevant section of [execution recipes](references/execution.md). An ordinary direct foreground project runner needs no extra reference read. If required detail is missing/unreadable, disclose it and continue known-safe direct/foreground execution or independent work; hold only operations whose output, ownership, exit status, or other safeguards cannot be established. Never claim an unavailable reference was read.

## Test Resource Lifecycle

These rules apply without sibling skills, to solo/delegated work, processes and filesystem data. Each fixture/suite or completed experiment/review round owns cleanup, including failures/cancellation; do not wait for final project closeout.

- **Record at launch.** Capture owner, purpose, session/PID plus command and start time, cwd, exact data/build paths, and cleanup method/lifetime in existing notes. Separate browsers, stubs, runners, shared services, disposable state, and retained caches. Never stop pre-existing or others' resources. Missing identity calls for bounded current-state inspection and disclosed uncertainty, not unbounded historical-log mining.
- **Register teardown during setup.** Use fixtures, `try/finally`, or scoped directory ownership, including partial setup failure. Shell experiments need a preinstalled trap, bounded timeout, and descendant cleanup; a trap in one shell does not own a cross-tool-call session. Capture every attempt's child handles/PIDs and keep its record until verified gone; retries must not overwrite them.
- **Reuse during the active round.** Reuse compatible task-owned browser/server/build state through failed assertions, compaction, and worker relays. A relay alone does not end the work. Shared phase services keep one stable owner; transfer only needed compatible resources when the recipient accepts and can access them, checking health/access rather than repeating acceptance. Close unused/non-transferable handles; note tool-local handles needing recreation. Save evidence and retire an invalid browser before its replacement. A passing test with unowned leftovers is unfinished.
- **Retain deliberately.** Continuation/user review needs a named next owner, reason, and expiry/deadline event. Honor and record explicit user retention. Until accepted, the creator remains responsible; in teams unresolved ownership returns to the leader. Do not retain merely for possible debugging or treat "preserve the environment" as blanket browser retention.
- **Dispose and verify.** At the actual round/work end, failure or cancellation, choose a safe boundary without in-flight transactions. Save minimal evidence, stop owned writers/children, release handles, then remove disposable data. A closed DB connection does not remove its database/WAL/directory. Verify both process-tree exit and path removal; cleanup errors must not hide the original failure.
- **Revalidate before signalling/deleting.** Compare PID command/start time before signalling survivors; never kill by broad name or assume reused PIDs are yours. Escalate only for verified owned leftovers. Confirm exact path ownership and no active writer/consumer; never sweep by prefix, age, or temp location, delete others' work, or globally wipe shared caches. Permission-denied inspection or `kill -0` failure is not proof of exit. Keep blocked verification assigned and report the resource/error.
- **Reconcile abnormal exits.** Traps/destructors cannot guarantee cleanup after crashes/forced kills. Record the run root before launch and reconcile interrupted attempts at the next safe checkpoint before replacements.
- **Report disposition.** Before handoff/completion report `closed/removed (verified)`, `retained (owner, reason, expiry)`, or `cleanup blocked (owner, resource, error)`; give exact paths/sizes for substantial retained outputs. Thread stop/archive/deletion or context replacement proves neither process exit nor disk reclamation.

### Files, Fixtures, And Build Outputs

Prefer a unique run-owned root for temporary DBs, profiles, snapshots, and isolated builds; direct tools into it and record outside paths too. Save selected evidence outside disposable data under the retention rules below; logs, traces and screenshots are not automatically permanent artifacts. A global temp directory is not a cleanup policy.

Share expensive setup at stable file/flow/suite/worker/schema/DB/tenant/namespace boundaries that prevent pollution without per-test rebuilding. Cache only environment-independent compilation, templates, static fixtures, dependencies, and generated assets, not runtime-owned clients, connections, event loops, request contexts, transactions, or mutable state across incompatible runtimes/workers. Provide deterministic reset and tie reuse to schema/migration/fixture/code fingerprints.

Reuse compatible build caches without a new large copy per retry. Disposable review/experiment builds end with the round unless retained; reusable project caches need an owner and size/expiry review. Inspect free space, cache/output growth before large runs and at phase boundaries; prune only unused owned outputs. If space is insufficient, resolve owned leftovers or report the constraint before more heavy work. Avoid overlapping heavy compilation with timing-sensitive QA. For recurring fixture/runner leaks, fix the responsible owner in scope and add a focused success/failure cleanup check, including interruption when the runner handles it; manual cleanup alone is not prevention.

## Browser Tool Selection

- **Playwright CLI defaults** to everyday UI checks, bug reproduction, forms, responsiveness, console/network inspection and mocking. Use `playwright-cli` when available, through PATH, with `-s=<project>` and a task suffix for concurrency.
- **agent-browser** is useful when its `read`, `batch`, or `diff` materially simplifies extraction, one-off flows, or snapshot/screenshot/URL comparisons. Keep a flow in one tool; diagnose a failed first attempt before switching. Neither CLI is assumed more token-efficient without measurements.
- **Playwright Test** supplies repeatable browser regressions, critical-flow assertions and cross-browser coverage through the project runner. A successful CLI interaction verifies that run, not a persistent regression test.
- **Chrome DevTools** serves performance, CPU/heap, or diagnostics unavailable in the current tool. For a user-selected existing tab, use the tool that can access it.

These are workflow preferences, not exclusive capabilities: both CLIs support snapshots, element refs, sessions and debugging.

### Browser Session Hygiene

Before starting, taking over, reconfiguring, or diagnosing an automation browser, read the relevant [browser recipes](references/browser.md), including a mid-task transition to browser work. Routine non-browser tests do not need it. Missing/unreadable recipes must be disclosed; continue independent work or already-understood safe actions, holding only operations whose isolation, ownership, or cleanup cannot be established. Do not silently fall back to a personal browser or claim an unread reference was read.

- Do not automate the user's everyday Chrome app; use a separate automation binary. If required configuration cannot be changed, report that constraint rather than silently using system Chrome. Automation browsers can outlive the agent and affect the user's normal link handling.
- Close only task-owned sessions/pages and verify session inventory **and** the recorded daemon/browser process tree; a successful close or empty registry alone is insufficient. Retained sessions need the accepted ownership contract above.
- `close-all` / `kill-all` affect other sessions: use only on explicit user request or after confirming none other is in use. Do not navigate or close user-selected pages. With chrome-devtools MCP, close only pages you opened; its shared long-lived browser/last page is not disposed of by page cleanup.
- Debug flags and PPID 1 locate candidates, not proof of ownership or disuse. Revalidate PID identities before stopping the owned daemon and browser descendants, recheck survivors, and never match `Google Chrome` alone. Stopping the browser without its owning daemon can allow relaunch.

## Repetition And Stress Runs

Default to one affected run. A loop needs an explicit requirement or specific nondeterminism/stress risk, not phase completion.

Before starting the loop, record its scope, configuration, count and rationale, estimated total time, and an explicit wall-clock budget in existing task notes. If no task note is available, state this short plan in the current response before execution; do not create a ledger or require a new file.

- Settle affected implementation first unless a focused diagnostic loop is justified. In a team the leader assigns one owner; workers report the need instead of starting duplicate loops. A fix round, new worker, relay, or phase does not automatically restart one.
- Name the mechanism and scope: concurrency, timing, children, shared resources, or other nondeterminism. Preserve reproducing order/interacting tests; looping only the failing test may hide an order bug. Avoid unrelated deterministic tests or extra configurations without a coverage reason.
- Preserve explicit repeat counts and concurrency coverage; do not inherit an earlier agent's invented count. There is no default count, including 100. Otherwise state the risk and count rationale. Read execution recipes before using probability calculations; do not claim confidence for correlated runs or unknown failure rates.
- Bound cost using recorded iteration time, or count the first necessary measured iteration toward the total. Check cleanup/disk growth on a short run before a long loop; reuse compatible compilation/setup without sacrificing state isolation.
- Stop scheduling at the count or budget limit and normally at the first failure. Continuing a diagnostic run after failures needs a separate bounded reason; allow safe teardown. A budget-limited run is incomplete, not passed; reassess before extension.
- Diagnose implementation, test, and environment without assuming the test is wrong. After a fix, rerun only invalidated scope, including interacting tests when relevant. Consolidate fixes before costly repeats unless diagnosis needs earlier feedback, and preserve unaffected evidence.

## Reuse Verification Evidence

Record tested revision including relevant dirty/untracked content, command/scope, configuration/environment, fixtures, result, and evidence location in existing notes. Deterministic BDD also includes feature/binding content, requirements, runner/generator versions, config and filters. Compare those inputs before reuse: relevant source, requirement, tool/dependency, configuration, fixture, or environment changes invalidate affected evidence; an unchanged-input commit or new thread does not. Inspect named dependencies and broaden if impact remains uncertain. Retain unaffected passing results.

Keep the durable record proportional: a passing check normally needs only a short result with its command/scope and relevant inputs; record shared setup once and link existing runner/CI evidence instead of copying it per test or round. Keep failure excerpts, reproduction details or traces only when needed to explain an unresolved finding or substantiate a material result. Live diagnostic output need not all be saved permanently; remove task-owned duplicate and intermediate artifacts once useful evidence is retained, preserving required project/user records and regression baselines.

Screenshots are optional evidence for visual findings or key UI states, not a record of every click, page or successful assertion. Keep the smallest set that establishes the relevant distinction; text or runner results suffice for nonvisual outcomes. Store readable, proportionally reduced images when adequate; use the browser reference's sizing guidance without changing the tested viewport.

Distinguish regressions, unrelated baseline failures, and unavailable checks; a failing/unrun gate is not passed. Skill mandates do not prove tool enforcement, and configured CI/flags do not prove execution or success. Actual execution evidence remains reusable under the rules above.

## QA Follow-ups

When tracking is requested/agreed and `issue-tracking` is available, hand unresolved QA findings to it with reproduction, expected/actual behavior, revision/environment, impact, and evidence for authorized deduplication/follow-up. Running tests alone never authorizes tracker writes. Keep fixed-and-verified results in the existing test record; without the skill, retain unresolved findings in the handoff rather than claiming they were filed.
