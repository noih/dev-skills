---
name: sdd
description: Inserts three quality gates into any spec-driven-development workflow — HOOK 1 grill (before implementation), HOOK 2 test (before review), HOOK 3 review (before archive). Complements spec tools (openspec, superpowers, generic plan files), does NOT replace them. Activates automatically when the conversation shows spec-lifecycle signals — "spec written / ready for grill / ready to apply / done implementing / ready to review / archive this / ship it" — or on explicit `/sdd grill`, `/sdd test`, `/sdd review`. Works for human-driven work AND autonomous agent runs; agents self-grill and write a decision log to `sdd-reports/<slug>.md`.
---

# SDD Skill

Three quality gates for spec-driven-development. The skill **does not** own the design or implement phases — those belong to whatever spec tool the project uses. The skill only inserts grill / test / review at the points where spec tools typically have gaps.

## Principle

- Complement, not replace. The spec tool drives design and implementation. This skill only hooks in quality gates around it.
- Works across tools. openspec, superpowers, generic plan files, and mixes of them are all supported. Detection is signal-based, not tool-specific.
- Works for humans AND autonomous agents. Agents self-grill, record decisions, and escalate only when a decision would derail the spec's goal.

## Gates

```
spec-tool: design / proposal / plan  →  HOOK 1  grill
spec-tool: implement / apply         →  HOOK 2  test       (blocking)
spec-tool: archive / merge-spec      →  HOOK 3  review
```

HOOK 1 and HOOK 3 are **offered**. HOOK 2 is a **blocking gate** — review should not proceed on failing tests without an explicit override. Interruption is treated as a worst-case outcome; the skill prefers to resolve issues in-flow and only escalates when the agent's available options would cause drift from the spec's original goal.

## Requirements (optional, skill degrades gracefully if missing)

| Skill | Purpose | Install |
|-------|---------|---------|
| grill-me | HOOK 1 — adversarial questioning against the spec | `npx skills@latest add mattpocock/skills/grill-me` |
| superpowers:requesting-code-review | HOOK 3 — preferred review path when available | Part of the superpowers plugin |

Behavior when each is missing:

- **grill-me missing**:
  - Human mode: prompt the install command, ask "install then retry" or "skip this time".
  - Agent autonomous mode: skip HOOK 1, record `Status: skipped-no-grill-me` in `sdd-reports/<slug>.md`, proceed to the next phase.
- **superpowers:requesting-code-review missing**:
  - Fall back to the built-in `/review` command.

Built-in `/review` is always available. The project's own test command (detected at HOOK 2) is the only other external dependency; when no test framework is detected, HOOK 2 skips with a warning (see "HOOK 2 test").

## Commands

The skill is triggered via `/sdd <action> [args...]` (plus natural language). `<action>` is one of: `grill`, `test`, `review`. The skill dispatches internally on the first argument. All three are thin entry points — they make the quality gate explicit and give a manual trigger point, but the skill also auto-activates on the signals listed in "Automatic triggers" below.

### `/sdd grill [spec-path]`

Invoke HOOK 1 against the resolved spec. Pass `[spec-path]` to override the resolution. See "HOOK 1 grill" for behavior.

### `/sdd test`

Invoke HOOK 2. Detects the project's test framework, runs it, updates the `tests-green:<slug>` session flag on pass. See "HOOK 2 test".

### `/sdd review`

Invoke HOOK 3. Enforces `tests-green:<slug>` first (fires HOOK 2 if not set), then delegates to `superpowers:requesting-code-review` (preferred) or built-in `/review` (fallback). See "HOOK 3 review".

## Automatic triggers

The skill watches the ongoing conversation for spec-lifecycle signals and offers the matching HOOK. Signals are natural-language based for tool neutrality — they work whether the user is driving openspec, superpowers, or a generic plan file.

| HOOK | Primary signal | Fallback signal + dedup |
|------|----------------|--------------------------|
| 1 grill | "grill this", "審 spec", "spec 寫完了", "ready for spec review", explicit `/sdd grill` | Apply / implement signal ("let's apply", "openspec apply", "開工") with `grilled:<slug>` flag unset → offer grill first |
| 2 test | "done implementing", "ready to review", "ready to archive", explicit `/sdd test` | Triggered internally when HOOK 3 is about to fire and `tests-green:<slug>` is unset |
| 3 review | "archive this", "ship it", "merge this", "收工", archive command invoked, explicit `/sdd review` | Archive signal with `reviewed:<slug>` flag unset → offer review first |

### Session flags (infinite-loop prevention)

| Flag | Set when | Read when |
|------|----------|-----------|
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

### Input to grill-me

sdd passes two things to the grill-me skill:

1. **Spec content** — full text of the detected spec artifact (proposal.md for openspec, plan.md for superpowers, the plan file for generic).
2. **Goal summary** — a one-to-two sentence summary of what this change delivers. If the spec tool has an explicit "delivers" / "goal" field, use it; otherwise derive from the spec's title + first paragraph and ask the user / agent to confirm before passing to grill-me. Goal anchors grill-me's questioning.

### Human mode

Interactive. sdd invokes grill-me with the two inputs above. grill-me asks questions one at a time; user answers. Conversation goes until user is satisfied. On completion, sdd sets `grilled:<slug>` and appends a summary to `sdd-reports/<slug>.md` under `## HOOK 1 grill`.

### Agent autonomous mode

Agent plays both roles — answers grill-me's questions against the spec + goal, records every non-trivial decision, and escalates only on goal-drift risk (see "Escalation" below). On completion:

- `sdd-reports/<slug>.md` HOOK 1 section records every decision, open question, resolution, and any escalations.
- `grilled:<slug>` is set.
- Agent continues to phase 2 (implement) unless it escalated.

## HOOK 2 test

Goal: block review on failing tests. Prevents wasting reviewer time and shipping broken code.

### Test framework detection

Resolve per this precedence:

1. **Explicit user statement.** "Tests run via `pnpm test`" wins. Remember for the session.
2. **Auto-detect from markers** (first match wins):
   - `package.json` with `scripts.test` → `<pm> test` (infer pm from lockfile: `pnpm-lock.yaml` → pnpm, `yarn.lock` → yarn, `bun.lockb` → bun, else npm)
   - `Cargo.toml` → `cargo test`
   - `pyproject.toml` / `pytest.ini` / `setup.cfg [tool:pytest]` → `pytest`
   - `go.mod` → `go test ./...`
   - `Makefile` with `test` target → `make test`
   - `justfile` with `test` recipe → `just test`
3. **Ask the user** on ambiguity.
4. **No test framework detected** — warn "this project has no test framework", record `Status: skipped-no-framework` in the HOOK 2 section, skip the gate. HOOK 3 will still run but with a banner warning "this spec was not verified by automated tests".

### Execution

1. Run the resolved command; capture exit code + output.
2. **Exit 0** — set `tests-green:<slug>`; record `Status: passed` in HOOK 2 section; return.
3. **Non-zero** — do not set the flag. Enter the fix loop:
   - Classify severity per "Severity classification" below.
   - Minor → agent fixes, re-runs, records attempts.
   - Moderate → agent fixes or asks leader if available; records attempts.
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

### Severity guidelines (not fixed rules — use judgment + tie-breakers)

| HOOK | Minor | Moderate | Severe |
|------|-------|----------|--------|
| 1 grill | Question's answer is directly inferrable from spec | Answer requires product-intent assumption but either choice still delivers the goal | Question exposes that spec's goal is ambiguous / self-contradictory / technically infeasible |
| 2 test | Clear bug, mechanical fix (typo / off-by-one / missing import) | Test vs impl ambiguity; needs product judgment to resolve | Failure suggests the spec's declared behavior cannot be delivered as specified |
| 3 review | Style, naming, small refactor | Architectural suggestion that doesn't block the delivered goal | Security hole, data-correctness bug, architectural flaw, or spec-drift in the implementation |

### Tie-breakers

1. **Uncertain between two levels → pick the more severe one.** Conservative bias; prefer a short interruption to a wrong shipped decision.
2. **Drift risk dominates complexity.** A "hard to fix" issue that doesn't endanger the goal stays moderate. A "one-line fix" that reveals goal-level confusion is severe.
3. **Repeated failure = drift signal.** If the same issue resists resolution across multiple attempts, treat the resistance itself as a drift signal and escalate.

## Decision log format (`sdd-reports/<slug>.md`)

One file per spec. Overwritten section-by-section by each HOOK run — the file always represents the latest state, but each section internally records the attempts that led to that state.

```markdown
# SDD Report — <slug>

_Goal: <one-to-two-sentence summary of what this spec delivers>_

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

- HOOK 1 and HOOK 3 — user can skip any invocation with "skip this time" / "no". Records `Status: skipped-by-user` in the section. `grilled:<slug>` / `reviewed:<slug>` session flag is set to prevent re-prompting this session.
- HOOK 2 — cannot be skipped silently. Overriding requires the phrase "skip tests, I know they fail" (or equivalent explicit opt-out). Records `Status: failed-overridden`.
- No cross-session skip persistence. A new session starts fresh. If the user wants to skip again next session, they decline again — cost is one utterance.

## Out of scope

- Writing specs (spec tool's job)
- Running `openspec apply` / executing plans (spec tool's job)
- Archiving specs, merging specs into canonical living specs (spec tool's job)
- Git commits (user's git workflow owns this)
- Pushing, creating PRs (not this skill's concern)
- Enforcing specific test frameworks, code style, or architecture
