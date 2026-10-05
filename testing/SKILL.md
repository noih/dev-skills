---
name: testing
description: "Use when: writing tests, designing test strategy, choosing test scope, reviewing test quality, debugging test failures, running browser QA or temporary test processes, managing test data and artifacts, verifying their cleanup, or balancing fast feedback with confidence."
user-invocable: false
---

# Testing

## Testing Mindset

Coverage is a signal, not the goal. High-quality tests should increase confidence that the system can run safely in production under realistic, surprising, and failure-prone usage.

Do not stop at happy paths and basic parameter validation. Test the behavior users, clients, jobs, integrations, and future code changes are likely to stress after the service is deployed.

Good tests answer:

- What must always be true in production?
- What happens when this operation is repeated, retried, cancelled, or performed out of order?
- What if valid data appears in an unusual state combination?
- What if old data meets new code?
- What if two actors perform related actions at the same time?
- What if an external system is slow, stale, partial, duplicated, malformed, or unavailable?
- What if the user has permission for one resource but not a related resource?
- What side effects must happen exactly once?
- What state must remain consistent after partial failure?

## Writing Tests First

Write the test before the implementation when the expected behavior is already pinned down: a reproducible bug, a stated acceptance criterion, a protocol or schema you must match, or a refactor that must preserve current behavior. The failing test is the specification, and seeing it fail first is what proves it can fail at all.

Write the implementation first when the behavior itself is still being discovered — spikes, exploratory work, an API shape you are still choosing. Add the tests once the shape settles, before the change is considered done.

If a separate test-first workflow is in play for this task, follow it; this section only decides the default when nothing else specifies an order.

## Test Case Design

- **One assertion per concept** — Each test verifies one specific behavior or invariant.
- **Descriptive names** — Test names read as behavior specs (e.g., "should not create duplicate invoices when retrying after timeout").
- **Arrange-Act-Assert** — Follow AAA pattern consistently.
- **Independent tests** — No dependency on execution order or shared mutable state.
- **Clean up side effects** — Tests that create database records, temp files, queues, timers, or environment changes must restore original state.
- **Realistic data** — Use production-shaped data with meaningful IDs, relationships, timestamps, permissions, and statuses.
- **Parameterized tests** — Use table-driven / parameterized tests (`it.each`, `@pytest.mark.parametrize`) for multiple inputs testing the same rule.
- **Production-like boundaries** — Prefer exercising public APIs, service boundaries, database constraints, queue handlers, and integration seams over private helper details.

## Test Priority Order

1. **Core production behavior** — The main behavior users, clients, or downstream systems rely on.
2. **Business invariants** — Rules that must never be violated, even under unusual states or retries.
3. **Misuse and unexpected usage** — Repeated calls, wrong order, stale state, mixed ownership, and unusual but valid combinations.
4. **Failure and recovery paths** — External failures, partial writes, timeouts, retries, rollback, and idempotency.
5. **Boundary cases** — Empty, minimum, maximum, just below/above thresholds, large payloads, and concurrency.
6. **Basic validation** — Required fields and simple invalid input, when not already covered by higher-value behavior tests.

## What to Test

- Business logic and domain rules
- API request validation and response format
- Error handling paths and error types
- Authentication and authorization boundaries
- Data transformation and serialization
- State transitions and side effects
- Idempotency and duplicate request handling
- Concurrency and race-sensitive flows
- Partial failure and recovery behavior
- Tenant, owner, role, and permission boundaries
- Persistence behavior with existing, migrated, stale, or legacy data
- Integration assumptions at external service, cache, queue, file, and network boundaries

## High-Value Production Scenarios

Prioritize surprising but plausible usage that can happen after launch:

- **State transitions** — Invalid order, repeated operation, retry, cancellation, rollback, and status regression.
- **Authorization boundaries** — Right role wrong owner, right owner wrong tenant, expired session, stale permission, and cross-resource access.
- **Idempotency** — Duplicate requests, double-submit, retry after timeout, repeated webhook delivery, and job replay.
- **Concurrency** — Two users update the same resource, a scheduled job overlaps with manual action, and parallel requests race on the same invariant.
- **Partial failure** — Database succeeds but external API fails, email fails after transaction, cache write fails, queue publish fails, or downstream returns partial success.
- **Data shape drift** — Old records, missing optional fields, unknown enum values, legacy formats, reordered results, duplicate items, and stale caches.
- **Boundary rules** — Exactly at limits, just below/above thresholds, empty but valid collections, maximum payloads, and timezone/date boundaries.
- **Invariant protection** — Totals never negative, ownership never crosses tenants, money is not rounded incorrectly, and status cannot regress illegally.
- **Recovery behavior** — Retries do not duplicate side effects, failed operations leave consistent state, and compensating actions are safe to run more than once.

## What NOT to Test

- Framework internals (router matching, middleware ordering)
- Third-party library behavior
- Trivial getters/setters with no logic
- Private implementation details that may change
- Exact error message wording (test error types instead)
- Coverage-only cases that execute lines without proving useful behavior
- Mock behavior so heavily that the test no longer exercises production-relevant boundaries

## Key Principles

- **Follow existing conventions** — Match the project's test style exactly: file naming, directory structure, assertion library, mock patterns
- **Test behavior, not implementation** — Tests should survive refactoring if behavior is preserved
- **Production confidence over coverage** — Prefer fewer tests that prove meaningful production behavior over many shallow tests that only raise coverage numbers
- **Minimal mocking** — Mock only true external boundaries or hard-to-control infrastructure; over-mocking hides integration bugs and makes tests brittle
- **Fast feedback** — Unit tests should be fast; reserve slow tests for integration suite
- **Deterministic** — No flaky tests; avoid timing-dependent assertions, use deterministic test data
- **Readable** — Tests are documentation; someone reading a test should understand the expected behavior
- **Observable outcomes** — Assert durable outcomes visible at the service boundary: returned data, persisted state, emitted events, queued jobs, audit records, or side effects
- **Failure realism** — Simulate realistic production failures: timeouts, retries, duplicate delivery, stale data, partial responses, and permission drift
- **Snapshot tests sparingly** — Only for stable, serializable output (e.g., config files, API responses). Avoid for large UI components — they break on every style change and reviewers stop reading diffs

## Test Infrastructure Design

Design the test suite so each layer earns its cost. Fast unit tests can be broad and numerous; expensive integration, browser, database, container, or external-service tests should be fewer, scenario-driven, and focused on behavior that unit tests cannot prove.

- **Separate by feedback cost** — Keep cheap deterministic tests close to the code. Put expensive setup behind explicit integration/e2e commands so agents and developers choose them deliberately.
- **Batch shared setup** — If several affected tests need the same compile, container, database, browser, emulator, migration, or fixture setup, run them in one invocation so the setup is paid once.
- **Choose stable isolation boundaries** — Prefer isolation by file, flow, suite, worker, schema, database, temp directory, tenant, or namespace when per-test isolation is too expensive. The boundary should prevent cross-test pollution without rebuilding the world for every assertion.
- **Cache only environment-independent work** — Cache compiled artifacts, migrated templates, static fixtures, downloaded dependencies, or generated assets. Do not cache runtime-owned clients, connections, event loops, request contexts, transactions, or mutable test state across incompatible runtimes or workers.
- **Make reset explicit and deterministic** — Provide a documented reset switch for stale or suspicious state. Tie reusable test environments to a schema, migration, fixture, or code fingerprint so they rebuild when assumptions change.
- **Prefer canonical project runners** — If a project has a test script that encodes batching, setup reuse, database isolation, service startup, or output defaults, use it instead of raw framework commands unless diagnosing the runner itself.
- **Document the runner contract** — Future agents should know the normal fast command, how to pass test files/names, when to force reset, which suites are ignored/external/destructive, and when the full suite is expected.
- **Optimize for long-term stability** — A slightly slower runner that isolates state predictably is better than a fragile fast path that leaks data, depends on order, or fails under parallel execution.

## Writing Tests

Optimize tests for focused reading and targeted execution. An agent should be able to inspect one test file and understand the behavior, setup, and assertions without loading unrelated scenarios or huge data blobs.

- **Plan file boundaries before adding tests** — Each test file covers one module, service method group, route, domain concept, or user-visible behavior. If a file needs more than one `describe` block for unrelated behaviors, split it.
- **Avoid catch-all files** — Do not create broad files such as `service.test.ts`, `api.test.ts`, `fixtures.ts`, or `test-data.ts` when they mix unrelated behaviors.
- **Keep behavior-specific data inline** — Small input/output examples and meaningful data differences should stay close to the assertion.
- **Hide noisy defaults in builders** — Use builders, factories, or fixtures for repeated setup fields that do not matter to the behavior under test.
- **Split large fixtures by scenario** — Large payloads belong in small, scenario-specific fixture files, not one shared mega-fixture.
- **Prefer local context over clever reuse** — Avoid shared setup that forces future readers or agents to open many files before understanding one test.

## Preferred Frameworks

Use the project's existing test framework. If none exists:

- **Node.js / TypeScript** → Vitest (native ESM, fast HMR, built-in coverage)
- **React** → Vitest + @testing-library/react (queries by role/text/label, user-perspective testing)
- **Rust** → cargo test + nextest (parallel execution, better output)
- **Python** → pytest (concise syntax, powerful fixtures)

## Browser Tool Selection

- **Playwright CLI is the default** for everyday UI verification, bug reproduction, form flows, responsive checks, console/network inspection, and request mocking. Use the `playwright-cli` skill when available. Invoke the CLI through PATH with a project-named session (`-s=<project>`); add a task suffix for concurrent work.
- **Use agent-browser when its built-in `read`, `batch`, or `diff` commands materially simplify the task** — page content extraction, one-off browser workflows, or snapshot/screenshot/URL comparisons. Keep a flow in one tool where practical; a failed first attempt alone is not a reason to switch tools. Diagnose the failure first.
- **Use Playwright Test for repeatable browser regression tests**, including assertions for critical flows and cross-browser coverage. Run existing tests through the project's runner. A successful CLI interaction verifies the current run; it does not replace a persistent regression test.
- **Use Chrome DevTools for performance, CPU/heap analysis, or diagnostics missing from the current tool.** For a user-selected existing tab, use the tool that can access that tab.

These are workflow preferences, not exclusive capabilities: both CLIs support snapshots, element refs, sessions, and debugging. Do not assume agent-browser is more token-efficient than Playwright CLI without measurements for the actual workflow.

## Test Resource Lifecycle

Apply this to solo work as well as delegated work, and to filesystem resources as well as processes. Cleanup belongs to each fixture/suite or completed experiment/review round, not only the final project closeout.

- **The creator owns cleanup.** For resources you start, record the owner, purpose, session name or process identity (PID, command, start time), working directory, and stop method in the existing task notes. Keep browser sessions, test stubs, runners, and shared services separate; "preserve the environment" is not a blanket browser-retention instruction. Do not stop pre-existing or other agents' resources.
- **Cleanup is part of completion.** Close task-owned resources after success, failure, cancellation, or an invalid attempt, at a safe boundary with no in-flight transaction. Save evidence, then retire an invalid browser before opening its replacement. A passing test with leftover resources is not a completed task.
- **Retention requires a handoff.** When continuation or user review needs a live resource, record the named next owner, reason, and cleanup deadline or event. The current owner remains responsible until the recipient accepts; under a leader, unresolved ownership returns to the leader. User-requested retention takes precedence and must be recorded. Do not retain resources merely for possible future debugging.
- **Use runner-owned teardown for repeatable tests.** Prefer fixtures or `try/finally`; shell experiments need a trap installed before launch and a bounded timeout with descendant cleanup. A trap in one shell does not manage a session used across multiple tool calls. Capture child handles/PIDs at launch; keep each attempt's PID record separate until that attempt is verified gone. Never overwrite a failed attempt's record with a retry's.
- **Verify exit, not just the stop command.** Check the owned process tree after graceful shutdown. Revalidate command and start time before signalling surviving PIDs; never kill by a broad name or assume a reused PID is yours. Escalate only for verified task-owned leftovers. Keep cleanup errors visible; permission-denied process inspection or `kill -0` failure is not proof of exit. If verification is blocked, report cleanup as unresolved with the exact resource and error, retaining its owner and record.
- **Report disposition before handoff or completion:** `closed/removed (verified)` / `retained (owner, reason, expiry)` / `cleanup blocked (owner, resource, error)`. Include exact paths and sizes for substantial retained outputs. Thread stop, archive, deletion, and context replacement prove neither process exit nor disk reclamation.

### Files, Fixtures, And Build Outputs

- **Register cleanup during setup.** Use the existing fixture/runner teardown, `try/finally`, or scoped temporary-directory ownership, including partial setup failure. Closing a database connection does not remove its database, WAL, or temporary directory. Stop owned children and release file handles before removing their data; surface cleanup failures without hiding the original test failure.
- **Contain disposable outputs.** Prefer a unique run-owned root for temporary databases, browser profiles, snapshots, and isolated build outputs; explicitly direct tools into it. Record paths created outside it too. Preserve only the logs, traces, or minimal reproduction needed for a finding; retain larger data only with an owner, reason, and expiry. A global temp directory is not an automatic cleanup policy.
- **Handle abnormal exits.** Traps and destructors cannot guarantee cleanup after a crash or forced kill. Record the run root before launch and reconcile interrupted runs at the next safe checkpoint. Verify ownership and that no writer/consumer is using each path before removal; never sweep by prefix or age alone, or delete shared caches, user data, or another agent's work.
- **Bound repeated runs.** Reuse compatible compilation and setup, keeping test-state isolation. Check fixture cleanup and disk growth on a short run before a long repetition loop. Preserve required repeat counts and concurrency coverage; scope repetitions to the relevant cases. Avoid overlapping heavy compilation and timing-sensitive suites when contention would invalidate results.
- **Treat caches separately.** Disposable review/experiment build directories end with that round unless explicitly retained. Reusable project caches may survive, but need an owner and a size/expiry review point. Inspect their growth at phase boundaries and prune only while unused; do not run blanket clean commands after every test.
- **Verify filesystem cleanup.** Check that disposable paths are gone, with inspection errors reported as unresolved; a successful teardown command or zero remaining processes is insufficient. When fixing a recurring fixture/runner leak, add one focused check that success and failure leave no owned data or children, including interruption if the runner handles it.

## Browser Session Hygiene

Automation browsers outlive the agent that started them unless they are closed explicitly. A leaked instance of the system Google Chrome can silently capture links the user clicks elsewhere, so the user's everyday browser appears broken.

- **Do not automate the user's everyday Chrome app.** Any automation launched from `/Applications/Google Chrome.app` (Playwright's `chrome` channel, chrome-devtools MCP's default `stable` channel) registers with the OS as Chrome and can receive link-open events, even when headless. Prefer a separate binary:
  - Playwright CLI / MCP: bundled Chromium via `PLAYWRIGHT_MCP_BROWSER=chromium`, or `"browser": { "browserName": "chromium" }` in `.playwright/cli.config.json`.
  - chrome-devtools MCP: `--executablePath` pointing at Chrome for Testing / Chromium, or `--channel=canary`.
  - If the config cannot be changed in the current task, say so to the user instead of silently falling back to the system Chrome.
- **Close and verify your session.** Run `playwright-cli -s=<session> close` from its recorded workspace, then check both session inventory and the recorded daemon/browser process tree. Exit code 0 or an empty `playwright-cli list` alone is insufficient: a missing registry entry, different workspace scope, or unreachable socket can leave a live daemon undiscovered. A retained session must meet the lifecycle handoff rule above.
- **Do not clean up other agents' browsers.** `playwright-cli close-all` and `kill-all` stop every session, including concurrent agents' work. Use them only when the user asks or when you have confirmed that no other session is in use.
- **chrome-devtools MCP owns one long-lived browser.** It stays open for the lifetime of the MCP server, and its last page cannot be closed through the tools. Close only the pages you opened (`close_page`), and do not navigate or close pages the user selected. Use `--isolated` when a throwaway profile is enough.
- **Diagnose leaks before killing.** Debugging flags help locate candidates; they do not prove ownership or that a browser is unused. PPID 1 is normal for a detached daemon, not proof that it can be killed.

  ```bash
  pgrep -fl 'remote-debugging-pipe|remote-debugging-port'  # candidates only
  ps -o pid=,ppid=,lstart=,command= -p <pid>               # compare with recorded identity
  ```

  Stop the verified task-owned daemon (for example, a leftover `@playwright/cli` node daemon) together with its browser descendants. Otherwise, the daemon may relaunch the browser. Recheck survivors; never match on `Google Chrome` alone, because that also kills the user's browser.

## Running Tests

Run the smallest meaningful scope first; expand only after the focused test passes or the local failure is understood. Finish a meaningful unit of work — a feature path, bug-fix attempt, refactor step, or focused behavior change — before running. Run earlier only when feedback resolves uncertainty or diagnoses a failure. Reserve the full suite for the end of the feature, before handoff or merge.

Tests should provide feedback at checkpoints, not become a background reaction to every file save. The cost of reflexive runs grows with setup overhead: compiling test binaries, building containers, migrating databases, starting services, seeding fixtures, bundling assets, or provisioning emulators. Pay that cost once on a complete affected slice, not after every save.

When multiple affected tests share the same setup, pass them all to **one** runner invocation. Many ecosystems reuse work within a single invocation — compiled artifacts, database setup, browser startup, containers, caches, worker pools — but splitting equivalent tests across repeated shell commands repeats up-to-date checks and setup phases even when no code changed.

Recommended scope order:

1. The specific test by name when changing one behavior.
2. The affected test file.
3. The package, module, or crate suite.
4. The full suite only when the change could affect shared behavior or before final verification.

Default to the concise reporter (table below). Only rerun with verbose output when a failure needs more context.

### Progress visibility during runs

Minimal output is not silent output. A long-running suite must emit streaming progress — file/binary boundaries, per-test pass/fail marks, final summary — so a stalled run is distinguishable from a slow one. Pick a reporter that streams (`dot`, `-q`, libtest's per-binary header), not one that buffers until the end.

Anti-patterns:

- **`command | tail -N`** — `tail` does not flush until EOF; the run looks frozen until completion. Cannot tell progressing vs. hung vs. crashed.
- **`command &> /dev/null`** — status-only runs leave no diagnostic trail; failures force a re-run to investigate.
- **Background without a log file** — output goes nowhere reachable.
- **Counting through `grep -c` or `wc -l`** — collapses progress to a final number; loses pass/fail names and timing distribution.
- **Suppressing per-file boundaries** in multi-binary suites — losing "Running &lt;file&gt;" headers hides where time is being spent.

Patterns:

- **Stream + retain** (foreground): `command 2>&1 | tee /tmp/run.log` — live output, full log preserved for grep/replay.
- **Background + log + tail** (long suites the agent should not block on): `command > /tmp/run.log 2>&1 &`, then periodic `tail -n 80 /tmp/run.log` or `tail -f` while watching.
- **Per-binary header** for multi-binary runners (Rust integration scripts, e2e harnesses): surface the current binary/file as it starts, not only at the end.

Rule of thumb: a 30-minute run that produces zero bytes until completion has a wrong invocation. Fix the pipeline, not the patience.

### Default reporter (concise progress)

| Framework | Command |
|-----------|---------|
| Vitest | `vitest run --reporter=dot` |
| pytest | `pytest -q --tb=short` |
| cargo nextest | `cargo nextest run` |
| cargo test | `cargo test -q` |

### Verbose output (investigating a failure)

Run only the failing test by name with full output:

| Framework | Command |
|-----------|---------|
| Vitest | `vitest run --reporter=verbose -t "<test name>"` |
| pytest | `pytest -v --tb=long -k "<test name>"` |
| cargo nextest | `cargo nextest run --no-capture "<test name>"` |
| cargo test | `cargo test "<test name>" -- --nocapture` |
