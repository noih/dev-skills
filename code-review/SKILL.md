---
name: code-review
description: "Use when: reviewing code, checking current changes, assessing correctness/security/maintainability/performance risks, preparing review findings, or deciding whether an issue is worth raising."
user-invocable: false
---

# Code Review Guidelines

Use this personal review lens alongside official review guidance. Review for production failures: broken behavior, corrupted data, bypassed permissions, hidden errors, unsafe future changes, and realistic performance/resource problems. Avoid nitpicks without practical impact.

## Establish Scope And Authority

Ground findings in the actual code and stated requirements, not earlier conversation history. Inspect enough surrounding implementation, callers, tests, jobs, clients and integrations to understand the changed path; individually reasonable diff lines do not establish correctness.

Trace inputs, existing/persisted states, ownership/tenant/role/session assumptions, side effects and ordering, errors, and downstream callers. Preserve original behavior in suggestions unless the task authorizes changing it. Raise business-logic defects without waiting for permission; label ambiguous intent and ask before implementing an unauthorized behavior change. Suspicious existing behavior is a question/risk, not permission to fix it silently. A read-only assignment stays read-only.

## Resources Used By The Review

A solo/read-only review may still create files and processes within its permitted scope. Use project runners and compatible outputs. Before an isolated snapshot/worktree or build directory, record exact paths, owner, purpose, cleanup point and process/session identity. Preserve shared services, caches and uncommitted work; a location/name/age alone does not establish ownership.

At each completed review round, failure or cancellation, save findings/minimal reproduction evidence, stop owned processes/writers, release handles, then remove unused disposable data/build outputs. Revalidate surviving PID identities before signalling and confirm no consumer uses a path before deletion. Verify process-tree exit **and** path removal, not merely successful shutdown or an empty inventory. Report blocked inspection/removal with exact resource/error; never claim it clean. A finished report or deleted thread does not reclaim its cache.

During continuing work, compatible resources may transfer only when the recipient accepts ownership and can access them. Retained outputs need a named owner, reason and expiry; the current owner remains responsible until acceptance. Report closed/removed, retained, or cleanup blocked explicitly. Use `testing` teardown details when available and executing tests, without making the safeguards above depend on its installation.

## Required Review Passes

For every meaningful change, make these passes before concluding there are no issues. Use the failure mechanisms below to focus inspection rather than requesting unrelated cleanup.

| Pass | Trace and flag when supported by concrete evidence |
| --- | --- |
| Requirement fit and regression | Wrong business rules, thresholds/edge cases, state transitions or outputs; breakage for existing callers, persisted/legacy data, API contracts, background jobs and UI flows. Suggestions must preserve unrequested behavior, such as keeping `>= 10` rather than changing it to `> 10`. |
| Security and privacy | Authn/authz and user/role/tenant/ownership checks; boundary validation of user/external/storage/network data; secrets/PII/internal errors in logs or outputs; injection into SQL, shell, templates, URLs, imports or code execution; weakened security/auditability. |
| State and side effects | Writes/deletes/events/queues/emails/cache operations, concurrency and retries: duplicate, partial, or out-of-order effects; consistency and recovery after failure. |
| Errors and data boundaries | Silently swallowed failures, overly broad catches, missing diagnostic context or caller recovery; unnecessary internal defensive checks that obscure already-established guarantees. Keep useful error context and genuine boundary/invariant checks. |
| Evidence and quality gates | Focused coverage for important production, user-facing, permission-sensitive, stateful or regression-prone behavior. Claims must stay within exercised behavior/seam compatibility; accept sufficient valid contract/integration evidence. A skill mandate is not tool enforcement, and configured checks/flags are not execution or success. Flag unexplained lowered thresholds, disabled CI, suppressions, skipped/deleted tests or stripped assertions. Legitimate changes need rationale and adequate retained/replacement verification. Follow existing constraints, including `CONSTRAINTS.md`, without inventing gates. |
| Architecture and simplicity | Blurred module ownership, bypassed public boundaries, inverted/cyclic dependencies, misplaced business logic, domain dumping-ground utilities or unrelated coupling. Flag wrappers/factories/service layers and one-use abstractions that hide direct logic without benefit; unclear, repetitive or overly verbose names; unnamed domain constants/status/limit/time values. Keep stable abstractions that clarify responsibility, protect invariants or match established architecture; single-use alone does not prove waste. |
| Performance and resources | Realistic hot-path O(n²)/repeated scans, N+1 calls, unnecessary render/request work or serialization, large payloads, blocking I/O, and unbounded memory/caches/queues/listeners/subscriptions. Inspect fixtures/runners for temp DB/profile/snapshot/build leaks on partial setup, failure or cancellation, fixing the shared owner rather than relying on each agent/test. Do not flag theoretical concerns on small/cold paths. |

Keep direct readable code, meaningful error handling, necessary validation, justified optimizations, idiomatic patterns and local conventions. Do not replace them merely to match another valid style. Skip defects trivially caught by formatter/linter/typechecker/compiler unless they reveal a deeper issue.

### Behavior Contracts And Acceptance Evidence

For behavior work, use `bdd` when available and follow [BDD contract/adoption boundaries](../bdd/REFERENCE.md#deterministic-contract-and-adoption) when that reference exists. Review requirement intent plus active deltas, current feature/bindings/product, and actual results together; archives do not override the branch's contract. Newly discovered business scenarios feed authorized source updates; unresolved policy returns to the user/leader rather than expectations being changed to fit implementation. Keep security and implementation risks that scenarios omit. Pure refactors reuse behavior-preservation evidence without new discovery.

Without BDD or its reference, legacy projects use agreed existing spec scenarios. New executable BDD acceptance and existing feature-driven projects default to versioned features/bindings consumed directly or through deterministic generation. No model may interpret/translate scenarios or supply bindings during generation/execution; model calls in the product under test and AI authoring reviewed source beforehand remain allowed. Generated tests are derived, read-only output: flag hand-edited/stale output and require current source/generation correspondence. Standalone tests without the feature/binding connection cannot satisfy that path. Do not auto-install the skill or silently downgrade required deterministic acceptance. Unsupported OpenSpec representation/validate/sync/archive compatibility claims remain gaps, not assumed support.

Inspect every required scenario/Examples identity, assertions for every outcome, and actual execution. Include unchanged state and prohibited side effects. Counts, IDs, test existence or green suites alone cannot prove coverage. Required skipped/pending/filtered/undefined/ambiguous or otherwise unexecuted cases, missing integration and outcomes beyond a replaced component/seam's evidence remain gaps. Preserve valid contract/integration evidence and distinguish manual checks, unavailable checks and explicit waivers from passes.

### Source Comment Policy

Do not request explanatory source comments as a fix: use clear code/tests, with only exceptionally important long-term constraints/decisions in the existing spec. Prohibit new application/test source comments, including docstrings, except JSDoc actually consumed by the configured OpenAPI generator. Deployment files, operational scripts, configuration and standalone docs are outside the prohibition; an existing comment style does not authorize additions.

Project-wide cleanup and new lint enforcement require explicit user/project adoption authority, not review or skill loading alone. Preserve already-adopted cleanup/enforcement; report violations or missing lint enforcement and overly broad OpenAPI exceptions. Read-only review reports gaps without editing. Inspect directives, license notices and runtime/generated-output effects before suggesting removal; preserve affected material while resolving conflicts by instruction hierarchy and explicit user decisions, asking only about unresolved conflicts. Apply `comments` details when available; do not require it to recognize these boundaries.

## Findings And Handoff

Raise findings only with a concrete risk and practical fix. Lead with correctness, regression, security and data-integrity issues, then architecture/maintainability/performance with demonstrated practical impact. Missing or weak tests merit findings for important or regression-prone changed behavior. Do not over-fix acceptable local patterns.

For each finding provide the location (file:line when possible), triggering condition, likely impact, practical fix/direction, and supporting code or reproduction evidence. Plausible but unproven production risks become specific questions, not confirmed defects and not silent omissions.

When tracking is requested/agreed and `issue-tracking` is available, hand it unresolved findings with reviewed revision, evidence, impact, delivery-blocking disposition and fix verification. It handles authorized deduplication/recording; read-only review grants no external write authority. Otherwise retain findings in the review handoff without claiming they were filed.

Present findings first by severity, open questions after them, then a brief scope/evidence summary. With no findings, say so clearly and state the main behavior inspected, validation observed, important unverified paths, and residual risk (including missing full-suite, integration, browser or manual checks where relevant). Do not substitute "looks good" for that account.
