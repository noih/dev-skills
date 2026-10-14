# BDD executable contract — discussion and adjudication

## Current state

- Goal: update skills so OpenSpec records behavior evolution while `.feature` describes the current checkout's executable acceptance contract. Generation/execution must not reinterpret scenarios through an LLM.
- Agreed: AI may draft scenarios and bindings before review; deterministic tooling consumes versioned sources afterward. Historical snapshots are not a second current authority. Latest means per branch/commit, not globally newest.
- Scope: `bdd`, affected `sdd` / `testing` / `lead` / `code-review` instructions and references, README. Documentation only; no runner installation, custom converter, or unrelated policy changes.
- Baseline: `main` at `ad24bad47eae6ad31e48a15b7d87a69076869011`, clean working tree before this note. Leader owns this note; one delegated writer owns implementation.
- Phase: one workflow-policy delivery phase: three-model discussion → one implementation → scoped validation → one independent review → local commit. No push.
- Participants requested: Fable 5.1 high, Opus 5.5 high, GPT-6.1-Sol high. Leader model configured to GPT-6-Astra high for subsequent turns.
- Provisional budget: 15–25 minutes overall; 5–8 minutes combined scoped validation/review. No application builds or browser runs. Record overruns rather than lowering requirements.
- Verification: cross-file source-of-truth consistency, frontmatter/links/whitespace, and two small fresh-context policy cases compared with baseline behavior. Inspect execution transcripts. No runtime integration claim.
- Resources: shared project checkout; no services or fixtures needed. Worker threads are leader-owned and will be removed after evidence is retained and cleanup confirmed.
- Complete: implementation `5b932ca9e8505ed7606edf591b10bd878f1d6746` and fixes `0d9a53d0a7f4ffb327a5a6dca521916ea252bf64` on `docs/bdd-executable-contract`. Static checks, two policy evaluations, independent review, and targeted re-check are complete. No services/fixtures were created; all five workers were idle with zero background activity, then stopped/deleted successfully. This retained adjudication/evidence document is the leader-owned deliverable.

## Accepted constraints

1. Preserve discovery, decision reuse, complete assertion coverage, risk-based test layers, and evidence validity.
2. Keep proposals/history distinct from current executable behavior; update feature, bindings and product in the same coherent change.
3. Missing/ambiguous bindings and unexecuted required cases must not silently pass.
4. Do not claim custom schemas automatically replace OpenSpec's Markdown validation/merge/archive semantics. Establish the integration boundary before claiming it works.
5. Respect existing project conventions and scope migration explicitly; do not silently substitute AI translation for a required deterministic path.

## Discussion and decisions

Fable (`claude-fable-5-1`, high) and Opus (`claude-opus-5-5`, high) independently identified the current-spec/history distinction and runner adoption boundary. Sol (`gpt-6.1-sol`, high) reviewed their proposals and the leader's decisions, agreeing with the final source ownership and explicit no-runner gap. All three requested models participated; Sol is the sole implementation writer.

Leader adjudication of their proposals:

- Accept three roles: OpenSpec main specs hold current requirement intent; active changes hold proposed deltas and decisions; archives retain historical records. `.feature` is the scenario source for the current checkout in the deterministic workflow. Do not describe all OpenSpec artifacts as historical.
- Accept direct Gherkin execution and deterministic generation equally. Version tool/config inputs; generated files are read-only. Reviewed AI-written standalone tests are not proof that `.feature` is executed.
- Accept full outcome assertions in bindings and required scenario/Examples-row execution evidence. Compare scenario identity and example identity, not counts alone; counts can hide substitutions.
- Accept explicit migration boundaries and preserve valid legacy tests. Do not silently downgrade a requested deterministic gate when no runner exists. A feature draft may exist during authorized adoption, but is not executable/passed until connected. Do not require fresh approval for already-authorized setup or demand whole-project migration.
- Reject blanket new retry prohibitions, mandatory one-to-one Markdown/Gherkin names, and mandatory new rollback workflows: these exceed this policy update. Preserve failure/retry visibility and existing evidence rules; reconcile feature/bindings/product/current requirements across merges or rollback.
- Treat reference-only OpenSpec scenarios as an integration proposal, not proven support. Preserve installed-version requirements; check validate/sync/archive compatibility before adopting a representation. No custom schema/converter is implemented here. Existing history may contain snapshots; new snapshots must be mechanical, revision-bound, and non-authoritative.
- Do not auto-archive or require archive permission again where already authorized. While a change is active, main spec plus its delta expresses intended behavior; closeout makes pending sync/archive visible instead of claiming it happened.
- Edit caller instructions in `sdd`, `testing`, `lead`, and `code-review`, since their current spec-only wording otherwise conflicts with the new source ownership.

Sources considered: [OpenSpec concepts](https://github.com/Fission-AI/OpenSpec/blob/main/docs/concepts.md), [OpenSpec customization](https://github.com/Fission-AI/OpenSpec/blob/main/docs/customization.md), [Playwright-BDD](https://github.com/vitalets/playwright-bdd), [Cucumber configuration](https://github.com/cucumber/cucumber-js/blob/main/docs/configuration.md). Runner-specific default behavior is not generalized into universal claims.

## Verification rounds

Expected policy cases, fixed before implementation:

1. Existing executable project: change a reservation limit from 120 to 180 minutes on a feature branch while archive describes 120. Expect current `.feature` and bindings/product updated coherently, historical record preserved, deterministic runner inputs, complete boundary assertions, undefined/ambiguous/pending/excluded required cases not accepted, and no AI regeneration or hand edits to generated tests. Compare with baseline OpenSpec-first ownership.
2. Adoption boundary: existing OpenSpec Markdown requirements, ordinary tests, no Gherkin runner; request deterministic acceptance. Expect migration/integration gap exposed, no claim custom schema automatically fixes validation/archive, no silent LLM converter or unnecessary wholesale migration/installation, and independent discovery work may continue. A discussion-only or editorial task must not force a runner. Compare with baseline non-executable fallback.

### Round 1 — implementation checks

Sol checked implementation `5b932ca`: `git diff --check` and cached diff check passed; scope exactly eight files; five skill frontmatters unchanged from baseline; six relative links/anchors resolved; stale spec-only policy patterns had zero matches. A stdlib Python assertion check inspected these properties without leaving a script or fixture. After leader requested narrower no-model wording, binding-failure exclusion from red proof, and generated-artifact freshness, affected static checks were rerun before commit. No runtime integration was tested.

### Round 1 — fresh-context policy evaluations

Both evaluations passed the predeclared expectations against `5b932ca`. Each answered a hypothetical request using the new skill, then compared the previous skill text via `git show`; this is a behavioral evaluation of the revision plus a static baseline comparison, not a measured baseline agent run. The leader inspected transcripts as well as final answers. Neither read this answer key or earlier discussion; both loaded only current/baseline `bdd/SKILL.md` and `bdd/REFERENCE.md`, without mutations, external actions, or resources.

- Existing runner case (`thr_jkgyi588bm`, GPT-6.1-Sol/high): refused completion despite green CI; preserved 120-minute archive; used main intent plus 180-minute active delta; updated features/bindings/product; required meaningful red assertions, all Examples identities/results, generated freshness, and visible retry evidence; allowed an LLM in the product under test.
- Adoption case (`thr_hetvrjgacn`, GPT-6.1-Sol/high): ordinary Vitest evidence did not satisfy disconnected deterministic acceptance; rejected AI translation in CI; allowed already-authorized reservation-only setup and feature drafts; preserved OpenSpec Markdown until compatibility checks; exempted independent README wording changes from runner/discovery ceremony.

### Round 1 — independent review

Assigned to Fable/high after static and policy checks passed. Includes standalone skill installation: sibling BDD reference links must not make the documented missing-BDD fallback unusable. No runtime integration/build/browser checks requested.

Review findings and leader adjudication:

- M1 (major), standalone callers depend on an optional sibling file: accept. Dispatch self-contained legacy/deterministic fallback in `sdd`, `testing`, `lead`, `code-review`; make the reference optional and fix SDD's missing-BDD table entry. No automatic skill installation.
- M2 (moderate), no duplicate detail versus retained required Markdown during migration: clarify the transition. Required legacy content may remain during incomplete adoption, with explicit status/gap; it must not become a second ongoing manually authored current scenario authority. Do not take the suggested permanent duplication exception.
- M3 (moderate), activation boundary: clarify default new executable BDD acceptance, existing feature-driven projects, legacy conventions, and discussion/editorial exemption. Reject narrowing the new default to tasks that repeat the word deterministic; this would dilute the user's long-term choice.
- m1 (minor), `regenerate current inputs`: fix to `regenerate from current inputs`.
- m2 (minor), SDD framework setup respects existing authorization/explicit skip: accepted intentional change; no fix.

Dispatch: Sol owns the targeted documentation fixes. Frozen pending user decision: none. One focused re-check will cover M1 and changed transition/activation wording; earlier policy evidence remains valid unless those decisions change. No repeated broad gate.

### Round 2 — affected fixes

Sol committed `0d9a53d0a7f4ffb327a5a6dca521916ea252bf64` (seven files, +18/-10): self-contained caller fallback/optional reference, transitional Markdown boundary, explicit executable-workflow default, and wording correction. One affected static batch passed whitespace, scope, five unchanged frontmatters, six relative links/anchors, and stale wording checks. No services/resources created. The two earlier policy cases remain valid: the changed wording clarifies their existing decisions without changing their expected outcomes.

Fable's focused re-check found no remaining blocker/major. M1 standalone callers, M3 activation, and m1 wording were resolved; M2 was resolved under the accepted transitional-content interpretation. No further review round is justified. The remaining limitation is architectural: if an installed OpenSpec version cannot support the chosen representation, migration stays incomplete until a compatible integration is supplied; this policy update cannot prove that integration. Transitional Markdown must not be presented as a second completed current scenario authority.

## Closeout

Delivered eight updated skill/document files plus this decision/evidence record. Three requested models participated at high reasoning. Discussion proposals that were not adopted and their reasons are recorded above; no user decision is pending and no acceptance requirement was waived.

Validation limits: no BDD runner, generator, OpenSpec integration, browser, or application suite was installed/executed. The policy evaluations are hypothetical instruction tests with static baseline comparison, not runtime integration tests or measured baseline agent runs. Scenario-to-test plumbing is deterministic by policy; correctness of bindings and stability of the product still require project evidence.

Resources: no services, worktrees, test data, or temporary files created; all five worker threads stopped/deleted after their findings were preserved here. Bounded thread-wait commands exited; no command remains active. Only source documents and this retained report remain. Local commits only; branch is for the user to push.
