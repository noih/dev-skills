# SDD Reference

Auxiliary material for the `sdd` skill. SKILL.md links here for the full template + examples; loading this file is only needed when the skill needs the verbatim layout.

## Project layout examples

```text
multi-project-root/                          workspace-root/
  service-api/ (manifest)       core/
  web-client/     (manifest)       icard/
                                   liquidity/
                                   pnpm-workspace.yaml
→ multi-project                  → monorepo
  target: chosen sub-project       target: workspace-root/
```

Existing-convention rows (rows 1-2 in the heuristics table) come first — if specs already live somewhere for this project, keep using that location.

## Decision log full template (`.sdd/logs/<slug>.md`)

One file per spec. Overwritten section-by-section by each HOOK run — file always represents latest state. Sections absent if that HOOK didn't run. Each HOOK section has bounded structure; attempts list grows within a run, frozen on completion.

```markdown
# SDD Log — <slug>
_Goal: <one-to-two-sentence summary of what this spec delivers>_

## Project layout
**Layout:** single-project | multi-project | monorepo
**Target dir(s):** `<dir relative to cwd>`
**Spec tool:** openspec | superpowers | generic | issue link | other
**Spec artifact path(s):** `<final path(s) spec tool wrote>`
**Reason:** <heuristic that matched>

## HOOK 1 grill
**Status:** passed | skipped-by-user | skipped-no-grill-me | halted-severe
**Mode:** user-mode | agent-autonomous | agent-with-leader
### Decisions
- <decision>. Reason: <why>.
### Open questions resolved
- <question>. Resolution: <how>. Reason: <why>.
### Escalations
- <issue>. Path: <asked user | asked leader | halted>. Outcome: <...>.

## HOOK 2 test
**Status:** passed | failed-overridden | skipped-no-framework | halted-severe
**Command:** `<test command>`
**Attempts:** <N>
### Attempts
1. <failure + fix>
N. <final state>
### Overrides
- Reason: <why>

## HOOK 3 review
**Status:** passed | skipped-by-user | halted-severe
**Dispatched to:** superpowers:requesting-code-review | /review
### Findings addressed
- <finding>. Fix: <...>.
### Findings deferred
- <finding>. Reason: <why>.
### Escalations
- <...>
```
