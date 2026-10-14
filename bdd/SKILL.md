---
name: bdd
description: "Discover and specify business behavior through concrete examples, then drive test design and acceptance from agreed scenarios. Use during feature or behavior-change planning, spec writing, implementation, and acceptance, or when explicitly asked for BDD/Gherkin. Pure refactors and editorial changes reuse existing behavior evidence without a new discovery cycle."
user-invocable: true
---

# Behavior-Driven Development

Use agreed business scenarios as the backbone of test design and acceptance. Carry the same behavior from discussion into the spec, implementation tests, and verification evidence. Work one coherent business capability at a time; do not specify the entire project before delivering anything.

## Ownership And Activation

Use this skill directly (`/bdd` in Claude Code, `$bdd` in Codex) or through the relevant planning, development, or acceptance workflow. Start at the stage the task needs; do not repeat completed discovery just because a new worker or skill takes over.

- `bdd` owns behavior discovery, scenario formulation, and scenario-to-test correspondence.
- The project's spec tool (such as OpenSpec) owns requirement artifacts and their lifecycle. Store scenarios there using its existing format.
- `testing`, when available, owns test layers, fixtures, execution, cost, and evidence validity. Otherwise use the project's runner and testing conventions.
- `sdd`, when used, tracks progress and readiness; `lead`, when used, assigns work and adjudicates. BDD does not start another dispatch tree, gate, or progress log. Neither is required for solo use.

For a small behavior fix, one regression scenario may be sufficient. For a pure refactor, reuse the affected scenarios and preservation tests. Do not introduce Gherkin ceremony for editorial changes or technical work with no changed business behavior. In a read-only task, report scenario or coverage gaps without editing specs or code.

## Discover Before Specifying

Grill is the primary up-front clarification of known policy, boundaries, and exceptions, not proof that every unknown has been exhausted. Default to trusting recorded agreed decisions; a handoff, new worker, or newly loaded skill alone does not justify another discovery cycle or new questions.

When a new example follows from agreed rules, proceed autonomously within authorized scope: record the derivation from those rules and add the scenario and needed test. Reopen only affected decisions when new evidence or a counterexample, a newly exposed policy gap, a changed assumption, or a changed user requirement warrants it. Report the previous decision, new evidence, and impact/recommendation; keep unaffected decisions and independent work moving.

Read the user goal, current spec, recorded decisions, and relevant behavior/tests before asking questions. Collect the business rule, concrete examples, and unresolved questions in the existing discussion or spec notes. Consider developer feasibility and tester failure perspectives; these do not require separate agents.

- Identify the actor, relevant starting state, triggering event, and observable outcome. Explore important exceptions, boundaries, permissions, retries, or state transitions where the capability warrants them; do not fill a mandatory matrix for every feature.
- Reuse settled decisions and ask only questions whose answers change expected behavior or scope. Do not invent business policy to complete a scenario. Label proposals and provisional assumptions; they are not agreed policy or acceptance. Resolve authority and material dependency/rework risk before dependent implementation or acceptance; a leader may decide only within explicit delegated authority. Continue independent work and reversible local implementation choices. Clearly labelled, isolated prototypes with bounded scope/cost may gather decision evidence, but their outputs/tests must not establish acceptance or propagate unresolved policy into production-dependent work. Batch remaining questions by phase with recommendation and impact; if no decision-maker is available, hold only the affected dependency and record the next decision needed. Honor prior explicit decisions, delegation, and scoped waivers.
- Use examples to resolve ambiguity before polishing Given/When/Then. A discovered out-of-scope capability remains a separate proposal, not an automatic expansion of this change.

## Formulate In The Existing Spec

Turn agreed examples into scenarios in the current OpenSpec/spec artifact, preserving its required headings and syntax. Use stable scenario names or existing IDs so tests and reports can refer to the same behavior. No ID registry or separate BDD document is required.

If no spec tool is in use, keep the scenarios in the existing task document or agreed requirements location; standalone BDD does not require installing OpenSpec. A discussion-only request can keep proposed scenarios in the conversation until saving them is in scope.

- **Given** states the relevant initial context independently for each scenario, **When** the meaningful business event, and **Then** its observable results, including side effects that must not occur. No scenario depends on a previous scenario having run. A Background shares context description applied afresh to each scenario, not mutable state carried between scenarios.
- Prefer one business rule/event per scenario and business language over selectors, clicks, private helpers, or database columns. Include technical detail when it is itself the contract under discussion.
- Use concrete values when they distinguish rules or boundaries. Use example tables, outlines, or shared backgrounds only when they improve understanding. Journey scenarios are appropriate when cross-capability behavior needs proof; they do not require a special user request. Prefer a focused event without enforcing a one-When grammar that excludes necessary journeys or technical-contract specs.
- Keep scenario outcomes specific enough to assert. Resolve contradictions or missing policy before treating a scenario as ready for implementation; do not seek approval again for already agreed behavior.
- Keep the spec as the requirement source. Do not create duplicate `.feature` files by default. If the project already executes Gherkin, follow its source-of-truth convention and link the spec and executable scenarios rather than maintaining conflicting copies. Neither Cucumber nor a new dependency is required.

For wording, boundary outlines, structured inputs, or shared context/metadata examples, read [REFERENCE.md](REFERENCE.md). These are illustrative policies, not default requirements or mandatory scenario ceremony.

## Drive Tests From Scenarios

Before implementing agreed behavior, map each in-scope scenario to existing tests and missing assertions. Design business acceptance tests around these scenarios, not around whatever implementation happens to exist. Reuse sufficient coverage; a new scenario reference alone does not justify a duplicate test.

1. Translate the scenario's preconditions and event into test setup and action. Assert every required outcome, including unchanged state and prohibited side effects. A matching name/ID or generic success/error assertion does not establish coverage.
2. Use `testing` to select the lowest-cost boundary that proves the behavior. One scenario may need several tests or layers, and a parameterized test may prove several examples. Do not turn every scenario into a browser E2E test or require a one-to-one mapping.
3. For new or changed behavior, add a focused test before implementation and observe its failure for the expected missing behavior, not a broken fixture or unavailable service. Existing satisfied behavior can reuse valid passing evidence; refactors establish a passing preservation baseline. Run broader acceptance at the planned checkpoint, not after each scenario or handoff.
4. Supplement scenario tests with relevant security, algorithmic, integration, and implementation-risk tests. Agreed scenarios are the acceptance backbone, not a ceiling on risk coverage.
5. When development or exploratory QA discovers a missing business scenario, reconcile it with the existing rules and update the spec and tests within authorized scope. Escalate unresolved policy or scope changes; do not silently rewrite expected behavior to fit the code. Pure implementation checks need not become business scenarios.

For example, an agreed scenario might be:

```gherkin
Scenario: Reject cancellation after shipment
  Given the order has shipped
  When the customer requests cancellation
  Then cancellation is rejected
  And the order remains shipped
  And no refund is issued
```

A test that only checks an error response leaves the state and refund outcomes unproven. Use the relevant service/integration evidence for those outcomes; a UI test is needed only if it proves an additional required behavior.

## Verify And Hand Off

Keep traceability in existing test names/metadata and the task's acceptance report or SDD log. For each required scenario, make its spec reference, test location/name (or explicit verification method), result/evidence, and any gap easy to find. Use a compact mapping when existing records do not already provide one; do not copy the scenario text or full logs into another ledger.

- Check assertion coverage as well as execution results. A green suite cannot pass an untested scenario, and an automated scenario is not passed merely because its test exists.
- Manual verification, deferred automation, unavailable checks, and explicit waivers must remain visible. A manual check cannot replace required automation without an authorized change to acceptance; a waiver is never a passed result.
- Reuse current evidence under `testing` rules. Without `testing`, record at minimum the revision including relevant dirty/untracked changes (content fingerprint or retained patch), spec/version, command or manual method and scope, relevant configuration/fixtures/environment, and actual result in existing task notes/report. Compare those inputs before reuse: relevant code, dependency, spec, configuration, fixture, or environment changes invalidate affected evidence; unchanged scope remains usable. If impact is ambiguous, inspect dependencies and broaden verification as needed, never guess a pass. Changed scenarios also invalidate affected test design; recheck those outcomes without rerunning unrelated acceptance.
- Hand off scenario references, unresolved decisions, coverage gaps, and valid evidence. `sdd` records readiness and `lead` assigns remaining work when present; standalone work reports the same facts without introducing another workflow.
