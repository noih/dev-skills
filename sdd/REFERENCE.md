# SDD Reference

Auxiliary material for the `sdd` skill: layout examples, test-framework markers, and the log template. SKILL.md keeps every decision rule (layout ambiguity, runner precedence, missing runner, skips/waivers); read the matching section here before classifying an unfamiliar layout, locating a runner the project docs do not name, or creating/substantially updating a log. If this file is unavailable, follow SKILL.md alone and record the fields it names.

## Project layout examples

```text
multi-project-root/              workspace-root/
  service-api/   (manifest)        packages/core/
  web-client/    (manifest)        packages/app/
                                   services/worker/
                                   package-manager-workspace.yaml
→ multi-project                  → monorepo
  target: chosen sub-project       target: workspace-root/
```

Existing-convention rows (rows 1-2 in the heuristics table) come first — if specs already live somewhere for this project, keep using that location.

## Test framework markers

Markers only locate likely tooling; an explicit user statement and project operating docs/custom runners take precedence, and SKILL.md's ambiguity and no-framework rules still apply.

- `package.json` with `scripts.test` → the package manager's test script
- `Cargo.toml` → cargo test
- `pyproject.toml` / `pytest.ini` / `setup.cfg [tool:pytest]` → pytest
- `go.mod` → go test
- `Makefile` with a `test` target → make test
- `justfile` with a `test` recipe → just test

## Decision log full template (`.sdd/logs/<slug>.md`)

One file per spec. Update the progress summary and affected evidence as work changes; preserve valid results and scoped waivers across handoffs. Link to existing task/test reports instead of duplicating their contents. Omit sections for gates that have not run.

```markdown
# SDD Log — <slug>
_Goal: <one-to-two-sentence summary of what this spec delivers>_

## Progress
**Stage:** <spec / implementation / test / review / ready for closeout>
**Task source:** <existing spec/task artifact>
**Completed / remaining:** <concise deliverables or task references>
**Blockers / next action:** <what remains and who owns it, if assigned>

## Evidence inputs
**Spec:** <path and version/content fingerprint>
**Executable contract (when applicable):** <feature/binding fingerprints, runner/generator version/configuration/filters; link existing evidence>
**Code:** <revision plus relevant dirty/untracked content fingerprint or retained patch>
**Configuration / fixtures / environment:** <inputs material to the checks>
**Evidence:** <accessible test/review report or handoff location>

Gate-specific results below identify their input snapshot when it differs; do not
overwrite an input reference and make an old result appear current.

## Project layout
**Layout:** single-project | multi-project | monorepo
**Target dir(s):** `<dir relative to cwd>`
**Spec tool:** openspec | superpowers | generic | issue link | other
**Spec artifact path(s):** `<final path(s) spec tool wrote>`
**Reason:** <heuristic that matched>

## HOOK 1 grill
**Status:** passed | blocked | skipped-by-user | skipped-no-grill-me | halted-severe
**Mode:** user-mode | agent-autonomous | agent-with-leader
**Spec examined:** <input reference>
**Companion:** <actual invocation method and outcome, or missing/unavailable/incompatible reason; skipped-no-grill-me records a skipped companion pass, not proof it ran>
**Project context inspected:** <specs / plans / docs / files / modules / tests checked before questions>
**Scenario readiness (behavior work):** <requirement/change and current feature references, agreed vs proposed outcomes, unresolved policy/integration gaps; link existing artifacts>
### Context-derived answers
- <question or assumption>. Answer: <what existing project context shows>. Evidence: <path / symbol / decision record / behavior>.
### Decisions
- <authorized decision or provisional assumption>. Authority/status: <source of delegation or pending decision-maker>. Reason/impact: <why, affected dependencies and next decision needed>.
### Derived examples / reopened decisions (when applicable)
- <new example>. Derivation: <agreed rule/decision reference>; scenario/test: <location>.
- Previous decision: <reference and agreed behavior>. New evidence/change: <counterexample, policy gap, assumption or requirement>. Impact/recommendation: <affected decisions/dependencies and proposed action>.
### Open questions resolved
- <question>. Resolution: <how>. Reason: <why>.
### Escalations
- <issue>. Path: <asked user | asked leader | affected dependency held>. Outcome: <...>.

## HOOK 2 test
**Status:** passed | failed | blocked | failed-overridden | skipped-no-framework | halted-severe
**Inputs / scope:** <tested input reference and covered deliverables/checks>
**Command:** `<test command>`
**Result:** <exit status and evidence; baseline failures / unavailable checks separately>
**Scenario coverage (behavior work):** <link required scenario/Examples identities to bindings/tests, outcome assertions and actual execution results; no duplicate ledger or counts-only proof>
**Scenario gaps:** <uncovered outcomes, required unexecuted cases, missing deterministic integration, manual verification, deferred automation, blocked checks; explicit waivers under Overrides>
**Attempts:** <N>
### Attempts
1. <failure + fix>
N. <final state>
### Overrides
- Reason: <why>. Authority: <who waived>. Scope/conditions: <what the waiver covers>.

## HOOK 3 review
**Status:** passed | findings-open | blocked | skipped-by-user | halted-severe
**Reviewed inputs / base:** <input reference and intended comparison base>
**Reviewer / method:** <actual reviewer or clearly labelled self-review; independence requirement/result>
### Findings addressed
- <finding>. Fix: <...>.
### Findings deferred
- <finding>. Reason: <why>.
### Escalations
- <...>
```
