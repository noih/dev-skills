# BDD verification boundaries

## Scope and acceptance

Baseline: `26f4309` on `main`. User requested discussion with Fable 5.1/high, Opus 5.5/high, and GPT-6.1-Sol/high, then updates informed by [the article](https://ithelp.ithome.com.tw/articles/10399526). Its historical [v0.2.0 change](https://github.com/class83108/insight-to-quality/pull/1) is context, not an authority overriding this repository.

One delivery phase: discuss, implement minimal skill wording, validate, independently review, commit locally. Provisional budget: 15–20 minutes overall, 4–6 minutes for static checks and two focused policy evaluations. Parent owns this note; one worker owns skill edits. No push, dependency installation, application runtime, new runner, mandatory case matrix, or additional verification ledger.

Preserve the accepted deterministic feature/OpenSpec policy. Candidate improvement: connect claimed behavior to the actual exercised production boundary and assertions; distinguish written skill instructions from tool-enforced checks. Existing risk-based tests and standalone skill installation must remain usable.

Policy evaluation expectations, fixed before implementation:

1. A mocked repository call or mocked target method cannot prove durable persistence. Identify the unexercised required boundary and choose a relevant low-cost integration check; report gaps without treating green unit tests or a ledger as proof.
2. Pure logic with a faithful external fake can retain cheap tests. Do not demand universal E2E, a new ledger, or repeat approval. A skill saying a check is mandatory does not prove a tool gate exists or that it ran; reuse actual tool evidence and report unavailable checks honestly.

## Discussion and adjudication

- Fable and Opus independently identified the same concrete gap: an external dependency may own the very outcome being claimed, so external/internal classification alone cannot justify a mock as acceptance evidence. Their recommendations also cover separately tested components whose doubles hide an incompatible seam.
- Retain cheap unit tests and composed evidence from caller, adapter, and contract/integration checks when they cover the actual claim. A captured outgoing request can prove caller intent/arguments; it does not prove transport or remote delivery. A non-invocation assertion can legitimately prove a prohibited interaction at that boundary.
- Reject blanket claims that fakes prove no real logic, that shared types/fixtures alone prove runtime interoperability, or that every scenario needs fresh integration/E2E execution. Fidelity needs only the aspects relevant to the tested behavior; existing sufficient evidence remains reusable.
- Opus verified `sdd` already distinguishes workflow instructions from installed runtime hooks and readiness flags from proof. Leave `sdd` and `lead` unchanged. Clarify evidence honesty in standalone `testing` without adding enforcement infrastructure.
- Sol agreed after challenging ports/adapters, pure-logic fakes, request-vs-delivery claims, and unchanged-input reuse. Accepted four wording edits in exactly `testing/SKILL.md`, `bdd/SKILL.md`, `code-review/SKILL.md`; preserve frontmatter and existing ownership/adoption rules. No additional policy was imported from the article.

## Current handoff

Complete on `docs/bdd-verification-boundaries`: skill changes at `88b77da`, both fresh-context Sol/high policy cases passed, and Fable independent review found no blocking issues. This note records the final adjudication. No runtime integration evidence. All workers reported no retained resources; Fable's temporary PR diff was removed and verified. No push or merge; branch is ready for the user.

## Round 1 evidence

- Writer's single static batch passed: `git diff --check 26f4309`, exact three-skill-file scope and unchanged frontmatter assertions. The diff preserves deterministic/OpenSpec ownership, standalone fallback, and unchanged-input evidence reuse. No new links or executable code.
- Local implementation commit: `88b77da0b99b9a45eb76be685e28544aecf2d39f` (`docs: clarify verification evidence boundaries`). No push or merge.
- **Policy case 1 — PASS (parent adjudication):** real service plus mocked `save` supported caller assertions, not restart durability. Independent producer/consumer fixtures hid `text`/`translated_text` incompatibility. The agent rejected closeout, invalidated the affected older schema/serializer evidence, and proposed focused persistence and real-output-to-consumer checks without full browser/broker verification. Fresh Sol/high run: about 62 seconds.
- **Policy case 2 — PASS (parent adjudication):** the agent retained faithful-fake unit evidence, prohibited-call assertions, and sufficient existing adapter/contract evidence with unchanged inputs across commits. It rejected new E2E/live-provider/ledger/approval ceremony, but required actual evidence for never-run lint. Fresh Sol/high run: about 47 seconds, overlapping case 1.
- Parent inspected answers and execution transcripts: relevant skill reads and baseline text/diff comparisons; no implementation, hypothetical test execution, or additional resources. The cases are hypothetical policy applications, not production runtime tests or measured before/after agent benchmarks. Existing risk-based tests and evidence reuse predate this change; the new wording makes mock/seam and mandate/enforcement/execution boundaries explicit.

## Review and final adjudication

Fable reviewed `26f4309..88b77da`: no blocker; requirement met. The parent accepted the review and did not dispatch optional changes:

- Fake fidelity still requires judgment against relevant dependency behavior; no mandatory anchoring method or automated fidelity check was introduced. This is a known limit, not evidence that every fake is trustworthy.
- The existing external-boundary mocking rule controls isolation; the new outcome rule controls what evidence can support. These are compatible.
- The expanded boundary bullet is long; splitting it is stylistic and unnecessary for this scope.

No acceptance requirements waived, no unresolved implementation decisions. Static validation, two policy cases, and one independent review suffice for this documentation-only change. Actual runner/OpenSpec/application integrations and measured baseline-agent improvement were not tested. Existing global skill installations were not updated.

## Follow-up: actionable runner instructions

User requested tool selection, installation, generation and execution instructions in the skills. Baseline `1d6d6be`; continue on the existing branch. Scope: `bdd/SKILL.md` conditional reference routing and a same-directory `bdd/TOOLING.md` guide for playwright-bdd, Cucumber.js, pytest-bdd, Behave and Reqnroll. Sol/high owns implementation; parent owns this record. Preserve existing policy and installed-project conventions.

Acceptance: official-source verification of commands and prerequisites; generated-wrapper versus editable-stub distinction; failure-short-circuit generation/run sequence; no all-runners installation, browser-only mandate or repeated approval. One scoped static batch and independent command/setup walkthrough review. No runner installation/runtime tests in the skills repository; no new trigger or acceptance policy, so no repeat policy-evaluation campaign. Provisional total budget 5–10 minutes including 2–3 minutes review/checks. No services or disposable resources planned.

Implementation committed at `88c9b94`: only `bdd/SKILL.md` and `bdd/TOOLING.md`, +188 lines. Writer verified current official documentation and passed one static batch: diff whitespace/scope, unchanged frontmatter, local links/anchors, shell/Python snippet syntax. Runtime setup was not executed. Opus/high independently reviewed commands/prerequisites and walked through existing Playwright and pytest integration cases.

Opus review verified Cucumber module loaders, Playwright generation/fixtures, pytest bindings/scaffolds and Reqnroll package selection; existing adoption and artifact policies remain consistent. Parent accepted two medium findings: preserve a top-level-only Playwright suite explicitly when adding projects, and avoid duplicating existing pytest scenario bindings. Also accepted the applicable Yarn PnP setup reminder. TUnit is outside the guide's package examples; do not treat the listed integrations as an exhaustive compatibility matrix. No new test framework or acceptance requirement is introduced. Targeted follow-up took about two minutes rather than another full gate. Reviewer did not independently check every upstream page or release-specific compatibility; writer's source checks remain applicable, and runtime integration is still untested.

Fix commit `bffcc35` changed only `bdd/TOOLING.md`. Focused prose and whitespace checks passed. Parent read the exact diff and confirmed all three accepted findings resolved: explicit ordinary Playwright projects with non-overlapping discovery, only unbound pytest scenarios/new scaffold sources with visible shared steps, and version-matched Yarn PnP prerequisites. No further review round was needed. Reviewer removed `/private/tmp/claude-501/bddrev` and verified its absence; writers created no temporary resources. Follow-up complete, committed locally on the same branch, no push/merge/global install. All command examples still require the target project's compatible versions and actual execution before claiming integration passes.

## User-requested additional discussion

At baseline `13ab010`, user requested other agents' opinions on the tooling guide. Fable 5.1/high and Opus 5.5/high independently discussed practicality and alternatives, without repeating the earlier static/runtime gate. Both supported the existing five-tool guidance, setup prerequisites, fixture/binding guidance and generated/scaffold distinction.

Parent accepted two small clarifications: persist runner paths/loaders and required generation+execution in a reusable project command consumed locally and by existing CI; label the table non-exhaustive and direct unlisted stacks to compatible version-specific official setup plus actual scenario execution evidence. The former strengthens the existing integrate-into-runner instruction, rather than introducing CI infrastructure. Sol/high implements only these guide edits, with focused whitespace/prose checks and parent diff review.

Parent initially did not impose a universal JS/TS runner or generated-output ignore policy: compatible existing runners and committed-output freshness remain valid. Additional ecosystem recipes, turnkey demos, pytest fixture examples, and runner enumeration helpers were optional scope expansion at that point. No runtime integration or benchmark is claimed. Fable created no resources; Opus used two primary-document fetches without retained files and disclosed a few incidental QA-note search hits, so its review context was not completely isolated. Sol committed the two clarifications at `604a28a`; focused whitespace/prose checks and parent diff inspection passed.

User then explicitly authorized defaults for the company's common JS/TS, Java, Rust and Go stacks. This supersedes the earlier choice against language defaults and authorizes the three additional ecosystem recipes. Existing compatible runners still take priority; for new adoption use Cucumber.js for general JS/TS, playwright-bdd for an existing Playwright Test project or browser-first testing, Cucumber-JVM/JUnit Platform for Java, `cucumber` for Rust, and `godog` for Go. No forced migration, generated-output VCS default, or all-stack installation. Sol extends only the tooling guide with official-source-checked dependency/discovery/run instructions; one affected static batch and a focused review of the new recipes, no repeat review of unchanged tools. Provisional additional budget 4–6 minutes plus 2 minutes review. No live runner installation/integration is claimed.

Implementation `5ccde9b` adds the company defaults and three recipes. Java covers test dependencies, suite/glue/resource discovery and Maven/Gradle execution; Rust covers `harness = false`, World registration/executor and fail-on-skipped propagation; Go covers `TestFeatures`, strict step status, nonzero suite result propagation and preserving ordinary tests. Official-source/config reasoning, whitespace, single-guide scope, same-directory links/anchors and unchanged frontmatter checks passed. Parent inspected the diff. Focused Opus review found no material defect; it used API knowledge without fresh upstream fetches, so source verification comes from the writer and parent's primary-source checks, not a second independent source audit. Parent also checked the Java engine's suite pattern and Rust/Go failure APIs directly. No installed dependencies, compiled snippets or runtime integration evidence.

Final decision: accepted language defaults and both discussion clarifications are complete; no forced existing-runner migration or generated-file VCS change. All three workers reported no resources. Branch remains `docs/bdd-verification-boundaries`; no push, merge or global skill installation. No remaining blocking findings; practical adoption still needs target-version compatibility and actual execution evidence.
