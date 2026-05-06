---
name: code-review
description: "Use when: reviewing code, checking current changes, assessing correctness/security/maintainability/performance risks, preparing review findings, or deciding whether an issue is worth raising."
user-invocable: false
---

# Code Review Guidelines

Use this skill as a personal review lens alongside any official code review guidance. Focus on issues that materially affect correctness, security, architecture, maintainability, simplicity, or performance. Avoid nitpicks that do not meaningfully improve the code.

## Review Posture

Review with production failure in mind. Do not only check whether the code looks clean; check whether it can break real users, corrupt data, bypass permissions, hide failures, or make future changes unsafe.

For every non-trivial change, actively trace:

- What inputs enter the changed code path?
- What state can already exist in production?
- What permissions, ownership, tenant, role, or session assumptions are being made?
- What side effects happen, and can they happen twice, partially, or out of order?
- What errors can occur, and who can observe or recover from them?
- What callers, jobs, API clients, UI flows, or integrations depend on the changed behavior?

Do not assume the diff is locally correct just because each changed line looks reasonable.

## Review Scope

- Ground findings in the code being reviewed.
- Inspect enough surrounding code to understand the affected behavior.
- Do not rely on previous conversation history as evidence.
- Prefer concrete, actionable findings over broad style commentary.
- Preserve behavior unless the user explicitly asks for a behavior change.
- If a suggested change might alter business logic, ask before treating it as a fix.
- Trace call sites, data boundaries, and side effects when reviewing behavior changes.
- Treat missing tests as a finding when the change affects production behavior, security, permissions, state transitions, or regression-prone logic.
- Raise uncertain but plausible production risks as questions or risks instead of silently ignoring them.

## Required Review Passes

For every meaningful change, make these passes before concluding there are no issues:

1. **Requirement fit** — Does the implementation actually satisfy the stated task, including edge cases and non-happy paths?
2. **Regression risk** — Could existing callers, persisted data, API contracts, background jobs, or UI flows break?
3. **Security boundary** — Are authentication, authorization, tenant ownership, input validation, secrets, and sensitive outputs still protected?
4. **State and side effects** — Are writes, deletes, events, queues, emails, cache updates, and retries safe and consistent?
5. **Error behavior** — Are failures observable, actionable, and not silently swallowed?
6. **Test evidence** — Are there focused tests for the behavior most likely to break?
7. **Simplicity** — Is the solution as direct as the problem allows, without premature abstraction or hidden coupling?

## What To Flag

### Correctness And Behavior

Flag changes that can cause incorrect behavior, regressions, data loss, broken edge cases, or mismatches with the stated requirement.

All review suggestions must preserve original behavior unless the requested task is to change behavior.

```text
Original: if value >= 10
Wrong:    if value > 10
```

If existing behavior looks suspicious, raise it as a question or risk instead of silently changing it.

### Security And Privacy

Flag changes that weaken authentication, authorization, input handling, secret handling, sensitive data protection, or auditability.

Watch for:

- Trusting user input or external API responses without boundary validation.
- Missing authorization checks for user-, role-, or tenant-scoped data.
- Leaking tokens, credentials, personal data, or internal errors in logs or responses.
- Injection risks in SQL, shell commands, templates, URLs, dynamic imports, or code execution paths.
- Weakening existing security controls without a clear reason.

### Architecture Boundaries

Flag changes that blur ownership, bypass established layers, invert dependencies, or place logic in the wrong module.

Watch for:

- Business rules leaking into UI, controllers, route handlers, or persistence code when the project has a clearer layer for them.
- Direct cross-module access that bypasses existing public APIs or ownership boundaries.
- New dependencies that point against the intended dependency direction.
- Shared utilities becoming dumping grounds for domain-specific behavior.
- Changes that couple unrelated features or make future changes harder.

### Simplicity And Readability

Flag code that is harder to understand than the problem requires.

Watch for:

- Names that repeat obvious context, encode too many implementation details, or make simple code harder to scan.
- Abstractions created for one-time use, hiding simple code without reducing real complexity.
- Factories, strategies, managers, wrappers, or service layers that do not match an established project pattern or solve a real problem.
- Magic numbers, strings, status codes, limits, and time values that encode domain meaning without names.

```text
Bad:  userAuthenticationValidationResultStatus
Good: isValid

Bad:  isCurrentUserLoggedIntoTheSystem
Good: isLoggedIn
```

### Data Boundaries And Error Handling

Flag unclear or unsafe handling of data at boundaries, and errors that hide failure or remove useful context.

Watch for:

- Missing validation at system boundaries such as user input, external APIs, storage, network responses, and authentication boundaries.
- Paranoid internal checks that obscure logic inside trusted code paths.
- Errors swallowed silently or logged without giving callers a way to react.
- Broad catches that mask unrelated failures.
- Rethrowing errors without enough context to diagnose the failed operation.

### Performance And Resource Use

Flag performance issues when they are likely to matter in realistic usage.

Watch for:

- Avoidable O(n^2) behavior, repeated scans, or missing hash-based lookups in hot paths.
- N+1 database or API calls.
- Unbounded memory growth, caches, queues, subscriptions, listeners, or retained references.
- Expensive work repeated instead of batched, cached, memoized, or moved out of render/request loops.
- Large payloads, unnecessary serialization, or blocking I/O in latency-sensitive paths.

Do not flag theoretical performance concerns when the code path is small, cold, or clearly not performance-sensitive.

## What To Keep

- Meaningful error handling with useful context.
- Necessary validation at system boundaries.
- Direct code that is easier to read than an abstraction.
- Useful abstractions reused multiple times, reducing real complexity, or matching established architecture.
- Performance optimizations backed by realistic needs.
- Idiomatic language and framework patterns.
- Code that matches local conventions even if another style is also valid.

## Findings Quality

- Lead with bugs, regressions, security issues, data integrity risks, and correctness risks.
- Include architecture, maintainability, and performance findings when they have clear practical impact or increase future change risk.
- Treat missing or weak tests as review findings when the changed behavior is important, user-facing, permission-sensitive, stateful, or historically easy to regress.
- Skip issues that a formatter, linter, typechecker, or compiler would trivially catch unless they indicate a deeper problem.
- Do not over-fix; some patterns are acceptable in local context.
- Leave comment-specific writing guidance to the `comments` skill unless comments create a real review risk.

Raise a finding only when you can explain the concrete risk and a practical fix.

A finding should include:

- The concrete risk.
- The condition that triggers it.
- The likely impact.
- A practical fix or direction.
- The evidence in code that supports the concern.

If you cannot prove a problem but see a realistic production risk, raise it as an open question with the specific scenario that worries you.

## No-Issue Reviews

If no findings are found, do not stop at "looks good." State what was checked and what residual risk remains.

Mention:

- The main behavior reviewed.
- Any tests or validation observed.
- Any important paths not verified.
- Whether full-suite testing, integration testing, browser testing, or manual verification is still missing.

## Output Guidance

When presenting review findings:

1. List findings first, ordered by severity.
2. Include file and line references when possible.
3. Explain the concrete risk and a practical fix.
4. Add open questions only after findings.
5. Keep summary secondary and brief.

If no issues are found, say so clearly and mention any residual risk or test gap.