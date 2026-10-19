---
name: punch-list
description: "Use when: the user has tried delivered work (smoke testing, dogfooding, UAT) and reports one or more findings (spacing, copy, wrong default or toggle, minor logic slips) to fix with checkpointed verification; also called a polish pass or snag list. Not for findings during an active spec's implementation (use sdd), refining code quality, polishing prose, reviewing code, or building new behavior."
user-invocable: true
---

# Punch List

Close out deviations from agreed behavior after delivery, while the user keeps testing. Items restore what was already agreed; new scope leaves this skill. In Claude Code, use `/punch-list`; in Codex, use `$punch-list`.

This skill arranges timing, scope, and records. `testing` decides what evidence each item needs and `code-review` decides how the batch is reviewed; this skill never grants a waiver, lowers the agreed bar, or adds a second completion standard. "Quick" and "cosmetic" are descriptions, not evidence policies.

## Entry

Start after the feature's development workflow has closed out, or when the user is testing delivered work and reporting what they see. Do not use it to bypass an unfinished `sdd` gate. If the current agent is a leader under `lead`, it does not run this skill itself: it hands the batch to one worker and keeps ownership and adjudication. If another leader is managing the work, report findings to it.

Inherit the delivered feature's requirements, agreed scenarios, valid evidence, known gaps, and linked issue. Do not re-grill, re-plan, or re-verify delivered work by default. If a fix reveals that the delivered feature never actually worked, mark the affected delivery evidence invalid in the primary record, report it at the next checkpoint, and let the user decide whether the item stays here or returns to the development workflow as unfinished scope.

The user collects findings by hand. The agent may drive the app with `playwright-cli` to reproduce a reported finding or confirm a fix when `testing` judges that the cheapest sufficient check; keep such runs small and focused. Do not start a standing smoke-test campaign or a full browser suite because the skill is active.

## Intake

Log every reported finding before fixing any of them, including several reported in one message. Keep the intake list in the conversation; synchronize it to the primary record at each checkpoint using the same ids. Use the first primary record that exists:

1. A `## Punch list` section in the active `.sdd/logs/<slug>.md`.
2. One comment per checkpoint on the delivered feature's issue, under `issue-tracking` rules.
3. The user's existing private handoff location; if none is known, ask once per `issue-tracking`. If nothing is writable, report the list as not persisted at each checkpoint and in the final report. Do not create a repo file or tracking folder for it.

Before adding an item, compare it with existing ids. A repeat of a fixed item reopens that id and counts as a failed attempt; a repeat of an open item adds evidence only. A finding reported while another fix is in progress is logged and queued; interrupt the current fix only when the new finding changes its safety, scope, or dependencies. A finding outside the delivered feature's scope is logged, asked about, and recorded on its own issue rather than fixed in passing.

Minimum fields per item: short id, observed vs expected in one line, route, state, evidence, and commit SHA once committed.

- Route: `stay`, `escalated`, `wontfix`.
- State: `open`, `fixed`. `fixed` never implies verified.
- Evidence: `verified` (reference), `user-checked` (who, revision, what they saw; valid only where `testing` accepts a manual check for that item, otherwise `gap`), `not-needed` (reason from `testing`), `pending` (named check and trigger), `gap` (unverified, reason), `waived` (authorized scope).

## Route each finding

Each finding either stays or escalates. First resolve the expected behavior: check the spec, scenarios, and the user's statements; if the expectation is still unclear, ask the user one question and park the item while waiting. Then apply the rules. Escalate when any applies:

1. The expected behavior is a product rule the agent would have to decide: spec, scenarios, and the user's answer do not settle it, or the user defers.
2. It changes a data schema, persisted format, stored records, migration, or backfill.
3. It changes a contract consumed outside this repository or deployable, or by persisted data or third-party clients: API request/response, event, CLI flag, or exported shared type. In-repo callers updated in the same fix stay.
4. It changes the substance of authorization, authentication, tenancy, secrets, money, transactions, data integrity, or an irreversible write. Copy and layout inside those screens stay.
5. It restructures module responsibilities, adds a capability, replaces a dependency, or refactors beyond what the repair needs.
6. After focused investigation the root cause, affected callers, or verification boundary is still unclear. Two failed fix attempts mean stop and re-diagnose; escalate if it is still unclear afterward.

File count and module count are not criteria: one defect may require touching a component, its caller, and its test. Uncertain between routes → escalate.

An explicit user statement of expected behavior during the session is a decision when no spec, scenario, or acceptance condition contradicts it and no rule above applies. Fix it and record it as a user decision; if a spec covers that feature, flag it at the checkpoint for back-fill through the spec tool.

Escalation parks only that item and anything depending on it; the rest continue. Present escalated items together at the checkpoint with reproduction, expected behavior, known cause or open hypothesis with the investigation boundary, and a recommendation. The user chooses an issue via `issue-tracking`, a proposal via the spec tool and `sdd`, or `wontfix`. The agent opens tickets or proposals only under creation authority already agreed.

## Fix

One finding, one fix. Anything else noticed along the way becomes a new log entry, never an edit made in passing. Work solo: the current agent writes the code; do not dispatch subagents or `lead`.

Do not run scheduled batch verification or review after individual fixes; the checkpoint does that. Focused checks under `testing` remain allowed at any time: a red-then-green run of a regression test written first, a small `playwright-cli` reproduction or confirmation, a diagnostic run for a failure, a check the user asks for, or a check before a later fix builds on this one. For each item, answer `testing`'s evidence-gap question once and record the result; items sharing one logical change may be answered together. Shared styles, reordering that changes keyboard or accessibility behavior, and copy with legal meaning need their own answer.

During the batch, preserve work with explicitly labelled WIP commits. Final `fix:` commits come after the checkpoint, under `commit-conventions`: one commit per item, or one commit for items whose changes share lines in the same file, listing their ids in the body. Rewrite only unpushed WIP, with a soft reset and per-file staging; do not claim intermediate WIP commits were individually verified.

## Checkpoint

Run a checkpoint when any applies:

- The user asks to verify, wrap up, or hand off.
- The user says an area or flow is done, or the agent is about to start fixing a different one. Moving between screens while reporting is not a trigger.
- Before claiming completion, splitting final commits, returning to the development workflow, or ending the session.

"Stop here" is a stop instruction: record open items as `pending` with their named check and trigger, note the next action, and stop. This is the only case where `pending` survives a session end; it never counts as a pass.

A checkpoint consists of:

1. Reconcile the intake list against the actual diff since the last checkpoint: missing entries, unlogged edits, affected shared behavior and callers. Synchronize the primary record.
2. One `code-review` pass over the diff not yet reviewed or whose review was invalidated, which also re-checks each item's route against the escalation rules. Review findings become new log entries and are routed like any other; land those that would leave the delivered goal unmet, log the rest. Re-check only the affected scope after blocker and major fixes land; do not re-review unchanged scope.
3. One batched run of the missing or invalidated checks per `testing`, after review fixes have landed, reusing valid evidence. Failures rerun the affected scope until the agreed bar is met or the gap is recorded honestly.
4. Update each item's evidence state. Split WIP into final commits for items that met their gate.

A checkpoint with no new or invalidated scope since the last one records that and runs nothing.

Before a completion claim, every item is `verified`, `user-checked`, `not-needed`, `gap`, `waived`, `escalated`, or `wontfix`, and the report lists gaps, escalations, items not persisted, and remaining risk.

## Hand off

- **`sdd`**: starts after closeout and does not re-fire its hooks. A fix that changes spec'd behavior returns to the applicable decision or implementation stage through the spec tool; the spec is not edited first to justify the fix. Affected `tests-green` evidence is invalidated under `testing`'s reuse rules and refreshed at the checkpoint.
- **`lead`**: not used for dispatch. A leader hands the batch to one worker; an escalated item may later become a `/lead` goal.
- **`issue-tracking`**: fixed items stay on the delivered feature's issue as checkpoint summaries; escalations become follow-ups under its triage and authorization rules. On a closed issue, comment; reopen or open a new issue only per project policy and the user's choice.
- **`commit-conventions`**, **`testing`**, **`code-review`**: unchanged authority.

## Out of scope

- New behavior, feature development, architecture changes.
- Team dispatch, worker scheduling, model selection.
- A second completion standard, evidence policy, or tracking system.
- Standalone smoke-test execution or full browser suites.
