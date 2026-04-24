---
name: sdd
description: Inserts three quality gates into any spec-driven-development workflow — HOOK 1 grill (before implementation), HOOK 2 test (before review), HOOK 3 review (before archive). Complements spec tools (openspec, superpowers, generic plan files), does NOT replace them. Activates automatically when the conversation shows spec-lifecycle signals — "spec written / ready for grill / ready to apply / done implementing / ready to review / archive this / ship it" — or on explicit `/sdd grill`, `/sdd test`, `/sdd review`. Works for human-driven work AND autonomous agent runs; agents self-grill and write a decision log to `sdd-reports/<slug>.md`.
---

# SDD Skill

Three quality gates for spec-driven-development. The skill **does not** own the design or implement phases — those belong to whatever spec tool the project uses. The skill only inserts grill / test / review at the points where spec tools typically have gaps.

## Gates

```
spec-tool: design / proposal / plan  →  layout check  →  HOOK 1  grill
spec-tool: implement / apply         →  HOOK 2  test   (blocking)
spec-tool: archive / merge-spec      →  HOOK 3  review
```

`layout check` is not a HOOK — it's a one-shot classification judgement that runs before a new spec file is created, so the file lands in the right directory (sub-project vs monorepo root). See "Project layout check" under HOOK 1.

HOOK 1 and HOOK 3 are **offered**. HOOK 2 is a **blocking gate** — no review on failing tests without explicit override. The skill prefers in-flow resolution and escalates only when agent options would drift from the spec's goal.

## Requirements (optional, skill degrades gracefully if missing)

| Skill | Purpose | Source | If missing |
|-------|---------|--------|------------|
| grill-me | HOOK 1 adversarial questioning | `mattpocock/skills/grill-me` (only this skill from that repo is required) | Human: prompt install or skip. Agent autonomous: skip HOOK 1, record `Status: skipped-no-grill-me` in `sdd-reports/<slug>.md`, continue |
| superpowers:requesting-code-review | HOOK 3 preferred review path | Part of the superpowers plugin | Fall back to built-in `/review` |

Built-in `/review` always available. Test framework (HOOK 2) is the only other external dependency; no framework → HOOK 2 skips with warning (see "HOOK 2 test").

## Commands

Manual trigger alongside auto-activation ("Automatic triggers" below). `<action>` ∈ {`grill`, `test`, `review`}.

| Command | Effect |
|---------|--------|
| `/sdd grill [spec-path]` | Fire HOOK 1 on the resolved spec; `[spec-path]` overrides resolution. See "HOOK 1 grill" |
| `/sdd test` | Fire HOOK 2 gate; verify test status, set `tests-green:<slug>` if green. See "HOOK 2 test" |
| `/sdd review` | Fire HOOK 3; require `tests-green:<slug>` (fires HOOK 2 if unset), then dispatch to `superpowers:requesting-code-review` or built-in `/review`. See "HOOK 3 review" |

## Automatic triggers

Auto-fires on natural-language signals (tool-neutral — openspec, superpowers, generic plan files).

| HOOK | Primary signal | Fallback signal + dedup |
|------|----------------|--------------------------|
| — layout check | Proposal-creation signals: "開 proposal", "新 spec", "let's plan X", "start a change", `openspec add`, `.superpowers/plans/<slug>` being created | Runs at most once per slug per session (dedup by `layout-checked:<slug>`) |
| 1 grill | "grill this", "審 spec", "spec 寫完了", "ready for spec review", explicit `/sdd grill` | Apply / implement signal ("let's apply", "openspec apply", "開工") with `grilled:<slug>` flag unset → offer grill first |
| 2 test | "done implementing", "ready to review", "ready to archive", explicit `/sdd test` | Triggered internally when HOOK 3 is about to fire and `tests-green:<slug>` is unset |
| 3 review | "archive this", "ship it", "merge this", "收工", archive command invoked, explicit `/sdd review` | Archive signal with `reviewed:<slug>` flag unset → offer review first |

### Session flags (infinite-loop prevention)

| Flag | Set when | Read when |
|------|----------|-----------|
| `layout-checked:<slug>` | Project-layout check completes (or user explicitly confirms layout) | Proposal-creation signal — if set, don't re-classify layout |
| `grilled:<slug>` | HOOK 1 completes OR user skips | Apply / implement signal — if set, don't re-offer grill |
| `tests-green:<slug>` | HOOK 2 exits 0 | HOOK 3 trigger — if unset, fire HOOK 2 first |
| `reviewed:<slug>` | HOOK 3 completes OR user skips | Archive signal — if set, don't re-offer review |

Flags live in memory for the current session only. A new session starts fresh — by design, because spec / code may have changed. There is no cross-session persistence of skip decisions.

## Spec slug resolution

Commands that need a slug (for `sdd-reports/<slug>.md` and session flags) resolve via this priority:

1. **Session semantic** — inspect the ongoing conversation for the spec that is clearly the subject of current work. The conversation usually names it ("we're working on add-auth", "the iCard proposal"). This is the default.
2. **Git branch** — parse `git branch --show-current`; if it follows `feat/<slug>` / `fix/<slug>` conventions and matches a spec directory, use `<slug>`.
3. **Filesystem mtime** — the most-recently-edited file under any detected spec-tool directory (`openspec/changes/*`, `.superpowers/plans/*`, `docs/plans/*`). Surface the resolved slug so the user can override.
4. **Ask** — if none of the above disambiguates, ask the user.

`[spec-path]` arguments to individual commands always override resolution. The resolved slug is cached for the session; subsequent commands reuse it until the user explicitly switches or provides a different path.

**Assumption**: one spec in flight at a time. If multiple specs are actively being worked on in the same session, session semantic picks the one currently being discussed — same rule as single-spec flow.

## HOOK 1 grill

Goal: catch design / scope problems before implementation.

### Pre-check: project layout (runs on proposal-creation signals)

Before a spec file is created, sdd classifies the target _project directory_ — the parent under which the spec tool (openspec, superpowers, generic plan, issue link, …) writes its own artifact using its own convention: `<dir>/openspec/changes/<slug>/`, `<dir>/.superpowers/plans/<slug>/`, `<dir>/docs/plans/<slug>.md`, etc. sdd picks only the `<dir>`; it does not pick the tool, its in-project path, or run the spec-tool command. The classification is declarative — the AI uses its available filesystem tool-use to gather the needed info; sdd does not mandate a specific shell command. HOOK 1 grill then fires once the spec file exists.

### Heuristics (first match wins)

| Check | Layout | Target dir |
|-------|--------|------------|
| Spec tool's conventional dir already at cwd (`openspec/`, `.superpowers/plans/`, `docs/plans/`, …) | Respect existing | cwd |
| Same convention dir inside exactly one sub-project | Respect existing | That sub-project |
| Workspace manifest at cwd (`pnpm-workspace.yaml`, `turbo.json`, `nx.json`, `Cargo.toml [workspace]`, `go.work`) | Monorepo | cwd |
| Shared `core/` / `packages/` / `libs/` at cwd alongside app dirs | Monorepo | cwd |
| 2+ sibling dirs each with own manifest, no root manifest, no shared core | Multi-project | Relevant sub-project |
| Single manifest at cwd, no siblings | Single-project | cwd |
| None of the above | Ambiguous | Ask the user |

Existing-convention rows come first — if specs already live somewhere for this project, keep using that location.

Examples:

```
mukaoe/                          isle-apps/
  mukaoe-service/ (manifest)       core/
  mukaoe-web/     (manifest)       icard/
                                   liquidity/
                                   pnpm-workspace.yaml
→ multi-project                  → monorepo
  target: chosen sub-project       target: isle-apps/
```

### Action

Layout classification is a declarative input, not an exec step. The skill states what information is needed; the AI gathers it using whatever filesystem tool-use it has.

1. Determine cwd + relevant sibling directories well enough to classify per the heuristics table. No specific command is mandated.
2. **Multi-project**: ask "which sub-project — A / B / both?" → target dir = chosen sub-project. "Both" → two target dirs, matching-slug specs per sub-project, pairing logged.
3. **Monorepo / single-project / existing convention**: target dir per heuristics (no ask).
4. Hand target dir back to the spec tool; set `layout-checked:<slug>`; record `## Project layout` in `sdd-reports/<slug>.md`.

### Input to grill-me

sdd passes two things to the grill-me skill:

1. **Spec content** — full text of the detected spec artifact (proposal.md for openspec, plan.md for superpowers, the plan file for generic).
2. **Goal summary** — a one-to-two sentence summary of what this change delivers. If the spec tool has an explicit "delivers" / "goal" field, use it; otherwise derive from the spec's title + first paragraph and ask the user / agent to confirm before passing to grill-me. Goal anchors grill-me's questioning.

### Execution modes

- **Human mode** — sdd invokes grill-me; interactive Q&A until user satisfied. sdd sets `grilled:<slug>` and appends a summary under `## HOOK 1 grill` in `sdd-reports/<slug>.md`.
- **Agent autonomous mode** — agent plays both roles against spec + goal, records every decision / open question / resolution / escalation in the HOOK 1 section, escalates only on goal-drift risk (see "Escalation"). On completion, sets `grilled:<slug>` and proceeds to implement unless escalated.

## HOOK 2 test

Goal: block review on failing tests. Prevents wasting reviewer time and shipping broken code.

### Test framework signals

The skill does not execute tests. It states the gate; the AI (or user) verifies status. When verification is needed, typical project markers can hint at the command — first match wins:

1. **Explicit user statement.** "Tests run via `pnpm test`" wins. Remember for the session.
2. **Common markers** (informational hints only):
   - `package.json` with `scripts.test` → `<pm> test` (infer pm from lockfile: `pnpm-lock.yaml` → pnpm, `yarn.lock` → yarn, `bun.lockb` → bun, else npm)
   - `Cargo.toml` → `cargo test`
   - `pyproject.toml` / `pytest.ini` / `setup.cfg [tool:pytest]` → `pytest`
   - `go.mod` → `go test ./...`
   - `Makefile` with `test` target → `make test`
   - `justfile` with `test` recipe → `just test`
3. **Ask the user** on ambiguity.
4. **No test framework detected** — warn "this project has no test framework", record `Status: skipped-no-framework` in the HOOK 2 section, skip the gate. HOOK 3 will still run but with a banner warning "this spec was not verified by automated tests".

### Gate

HOOK 2 is a status gate, not an executor. The skill does not run shell commands. Verifying test status is the AI's or user's responsibility, using whatever tool-use capability is available.

1. Confirm test status for this spec's code:
   - Recent test run known green in this session → proceed
   - Unknown → verify (AI may run the project's tests if capable; otherwise ask the user)
   - Known failing → enter fix loop
2. **Tests green** — set `tests-green:<slug>`; record `Status: passed` + how it was verified.
3. **Tests failing** — do not set the flag. Fix loop:
   - Classify severity per "Severity classification" below.
   - Minor → fix, re-verify, record attempts.
   - Moderate → fix or ask leader if available; record attempts.
   - Severe → escalate (human) / halt (agent autonomous, no leader).
4. **Override** — user (or agent with explicit authority) may override with phrase "skip tests, I know they fail" / "override HOOK 2". Record `Status: failed-overridden` + override reason. Set `tests-green:<slug>=overridden` so HOOK 3 proceeds but with a warning banner.

HOOK 2 is the only blocking gate. HOOK 3 will not auto-fire without `tests-green:<slug>` set to passed or overridden.

## HOOK 3 review

Goal: human / senior review before archive.

### Preconditions

- `tests-green:<slug>` must be set (passed, overridden, or skipped-no-framework).
  - If unset, fire HOOK 2 first.
  - If HOOK 2 cannot be satisfied (severe fail, not overridden), halt — do not invoke review.

### Dispatch

- `superpowers:requesting-code-review` installed → invoke it.
- Otherwise → invoke built-in `/review`.

Both review the current branch diff. sdd does not touch review logic; it only decides which to invoke and prepends context (warning banners for `tests-green:<slug>=overridden` or `tests-green:<slug>=skipped-no-framework`).

### After review

Remind the user of the spec tool's archive step (`openspec archive <name>` for openspec, the archive move for superpowers, manual mv for generic). Do not auto-archive. sdd's job ends here — archive and merge-spec belong to the spec tool.

Set `reviewed:<slug>` on completion.

## Escalation

Three-level severity applies uniformly to HOOK 1, HOOK 2, and HOOK 3. The core question: **does the decision risk drifting from the spec's original goal?**

- **Minor** — the answer is either obvious from the spec or the fix is mechanical; agent decides + records.
- **Moderate** — the answer needs judgment (e.g. picking between equally valid implementations) but does not change what the spec delivers; agent decides, records decision + reason, proceeds.
- **Severe** — the decision would change what the spec delivers, contradict its stated goal, or reveal that the goal itself is unachievable; escalate.

### Escalation paths

| Context | Severe action |
|---------|---------------|
| Human in the loop | Pause and ask the user |
| Agent with leader / controller | Interrupt and ask the leader |
| Agent fully autonomous, no leader | Halt workflow; write `Status: halted-severe` in the relevant HOOK section of `sdd-reports/<slug>.md` with problem summary + recommended next steps for human review |

### Severity guidelines (use judgment; see tie-breakers)

| HOOK | Minor | Moderate | Severe |
|------|-------|----------|--------|
| 1 grill | Question's answer is directly inferrable from spec | Answer requires product-intent assumption but either choice still delivers the goal | Question exposes that spec's goal is ambiguous / self-contradictory / technically infeasible |
| 2 test | Clear bug, mechanical fix (typo / off-by-one / missing import) | Test vs impl ambiguity; needs product judgment to resolve | Failure suggests the spec's declared behavior cannot be delivered as specified |
| 3 review | Style, naming, small refactor | Architectural suggestion that doesn't block the delivered goal | Security hole, data-correctness bug, architectural flaw, or spec-drift in the implementation |

### Tie-breakers

1. **Uncertain between levels → pick the more severe.** Short interruption beats wrong ship.
2. **Drift risk > complexity.** Hard-to-fix but goal-safe stays moderate; one-line fix exposing goal confusion is severe.
3. **Repeated resistance = drift signal.** Same issue failing multiple attempts → escalate.

## Decision log format (`sdd-reports/<slug>.md`)

One file per spec. Overwritten section-by-section by each HOOK run — the file always represents the latest state, but each section internally records the attempts that led to that state.

```markdown
# SDD Report — <slug>

_Goal: <one-to-two-sentence summary of what this spec delivers>_

## Project layout
**Layout:** single-project | multi-project | monorepo
**Target dir(s):** `<project directory chosen as spec parent, relative to cwd>`
**Spec tool:** openspec | superpowers | generic plan file | issue link | other
**Spec artifact path(s):** `<final path(s) the spec tool wrote — e.g. mukaoe-service/openspec/changes/<slug>/proposal.md>`
**Reason:** <one-line heuristic that matched, e.g. "root pnpm-workspace.yaml → monorepo">

## HOOK 1 grill
**Status:** passed | skipped-by-user | skipped-no-grill-me | halted-severe
**Mode:** human | agent-autonomous | agent-with-leader

### Decisions
- <decision>. Reason: <why>.

### Open questions resolved
- <question>. Resolution: <how>. Reason: <why>.

### Escalations
- <issue>. Path: <asked user | asked leader | halted>. Outcome: <...>.

## HOOK 2 test
**Status:** passed | failed-overridden | skipped-no-framework | halted-severe
**Command:** `<resolved test command>`
**Attempts:** <N>

### Attempts
1. <summary of first failure + fix attempted>
2. <...>
N. <final state — pass / override / halt>

### Overrides
- Reason: <why tests were bypassed>

## HOOK 3 review
**Status:** passed | skipped-by-user | halted-severe
**Dispatched to:** superpowers:requesting-code-review | /review

### Findings addressed
- <finding>. Fix: <...>.

### Findings deferred
- <finding>. Reason: <why deferred>.

### Escalations
- <...>
```

Sections absent if that HOOK didn't run. File never grows unbounded — each HOOK section has a bounded structure (attempts list may grow within a run but is frozen on HOOK completion).

### Storage

Default location: `sdd-reports/<slug>.md` at the project root. The directory is **local artifact, not deliverable** — recommend gitignoring.

On first write in a project, sdd prints a one-line reminder: "`sdd-reports/` is a local artifact; consider adding to .gitignore." The skill does not modify `.gitignore` automatically — user decides whether to ignore, commit, or do something else.

## Skip semantics

- HOOK 1 / HOOK 3 — skip with "skip this time" / "no" → `Status: skipped-by-user`; `grilled:<slug>` / `reviewed:<slug>` set to suppress re-prompts this session.
- HOOK 2 — no silent skip. Explicit override phrase required ("skip tests, I know they fail" or equivalent) → `Status: failed-overridden`.
- No cross-session persistence. New session → decide again (cost: one utterance).

## Out of scope

- Spec writing, implementation, archiving, merging into living specs — spec tool's job.
- Git commits, pushes, PRs — user's git workflow.
- Enforcing specific test frameworks, code style, or architecture.
