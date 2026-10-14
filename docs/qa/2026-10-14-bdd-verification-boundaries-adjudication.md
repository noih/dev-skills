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
