# SDD Reference

Auxiliary material for the `sdd` skill. SKILL.md links here for the full template + examples; loading this file is only needed when the skill needs the verbatim layout.

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
**Code:** <revision plus relevant dirty/untracked content fingerprint or retained patch>
**Configuration / environment:** <inputs material to the checks>
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
**Status:** passed | skipped-by-user | skipped-no-grill-me | halted-severe
**Mode:** user-mode | agent-autonomous | agent-with-leader
**Spec examined:** <input reference>
**Project context inspected:** <specs / plans / docs / files / modules / tests checked before questions>
### Context-derived answers
- <question or assumption>. Answer: <what existing project context shows>. Evidence: <path / symbol / decision record / behavior>.
### Decisions
- <decision>. Reason: <why>.
### Open questions resolved
- <question>. Resolution: <how>. Reason: <why>.
### Escalations
- <issue>. Path: <asked user | asked leader | halted>. Outcome: <...>.

## HOOK 2 test
**Status:** passed | failed | blocked | failed-overridden | skipped-no-framework | halted-severe
**Inputs / scope:** <tested input reference and covered deliverables/checks>
**Command:** `<test command>`
**Result:** <exit status and evidence; baseline failures / unavailable checks separately>
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
