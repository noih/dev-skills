---
name: ui-parity
description: "Compare an implementation's UI/UX against a reference, repair confirmed discrepancies, and deliver a leader-reviewed report with matched, current screenshots. Use for demo-versus-web parity or reference-based UI acceptance across related cases. Not for a standalone redesign, API-only testing, or an ordinary code review without a UI reference."
---

# UI parity

Compare a reference and an implementation, fix confirmed in-scope UI/UX differences, and deliver a verified report. Minimize human involvement by resolving routine findings from evidence and existing authority. A difference list or screenshot collection alone is not completion.

## Scope and authority

- Start with the user's scope, accepted exceptions, reference, implementation, and permission to repair. A request to compare **only** remains read-only. This skill does not authorize publication, external messages, production mutations, new tracking systems, or unrelated work.
- The reference is the default UI standard, not an infallible business specification. Current explicit decisions and applicable requirements take precedence. Consult backend contracts/implementation when they clarify meaning; unfinished backend code is evidence, not automatic authority. Do not introduce API functional testing unless requested or necessary for an authorized change.
- The leader owns the common quality standard, correctness judgments, ambiguous decisions, and final acceptance. Browser workers collect facts; repair workers implement confirmed changes. Routine repetitions can follow a leader's recorded standing decision. Do not send every color mismatch to the user.
- The leader investigates conflicts and decides within delegated authority. Missing grounds first trigger a question-directed evidence request to an existing appropriate operator; ask the user only for a remaining product decision that evidence and authority cannot settle. Hold that dependency and continue independent work; never bury it as a pass.
- For an unavailable backend capability, implement the achievable UI with an isolated contract-shaped mock when authorized. Label the evidence as mocked; do not add mock labels, autofill buttons, or fake-success paths to the product merely for testing convenience. Preserve actual security and business constraints.

## Read by responsibility

| When | Read |
| --- | --- |
| Leader: kickoff, grouping, dispatch, relay | [Orchestration](references/orchestration.md) |
| Leader: calibration and decisions; repair worker: assigned criteria | [Judgment criteria and examples](references/criteria.md) |
| Browser worker: before capture; repair worker: invalidation; report owner and leader: status, freshness and review | [Evidence and report contract](references/evidence.md) |
| Report owner: start the report | [Report outline](assets/report-outline.md) |

These files travel with the skill; no sibling installation is required. When available, `lead` supplies harness coordination and resource ownership, `testing` supplies proportionate execution rules, and `punch-list` handles later user findings. Share decisions and evidence rather than creating duplicate gates. The task-specific model profile in Orchestration applies to this workflow; explicit user overrides always win. Preserve applicable host safety and ownership rules.

## 1. Establish one acceptance contract

Discover existing project instructions and task records. Reuse them; record only missing information:

- Reference and implementation URLs/paths and immutable revisions; relevant dirty changes, build identity and environment. Record unavailable inputs honestly.
- In-scope cases, roles, organizations, entities, required UI states, normal and narrower supported viewport(s), and explicit exclusions. Keep existing case IDs. Derive a concise checklist if none exists; don't inherit a fixed case count from another project.
- Requirements and accepted deviations, including reference defects, mock boundaries, and controls intentionally retained.
- Runtime/browser/data ownership, isolation/reset method, report location, and current evidence index.
- A proportionate verification plan and provisional time/context budget. One coherent delivery phase owns acceptance; a worker change or fix round does not create a new full test campaign.

Calibrate on a few representative states: a dense table, form/detail dialog, and important transition when present. The leader applies the ordered decision standard in Judgment criteria, inspects actual reference and implementation evidence, and records concrete decisions before mass capture. Include a narrower-width check for dense layouts. Inspect relevant reference source when available to resolve meaning; distinguish observed behavior from inferred intent.

Audit shared primitives early: status wording, buttons, badges, tables, dialogs, messages, signatures, empty values, and exports. Inventory testing shortcuts in the implementation. These are **examples, not an exhaustive checklist**: inspect the whole relevant UI and workflow for other inconsistencies. Remove only confirmed out-of-scope controls; preserve accepted exceptions and legitimate actions. Resolve common causes before capturing the same defect dozens of times.

## 2. Assign coherent groups

Group cases by reusable business context, roles/data, prerequisites, shared UI, and file ownership. A group can contain several cases or a bounded part of a large flow. Do not force one worker per case, per page, or for all browser work.

Use independent browser sessions and data ownership for parallel groups. Tabs alone do not isolate cookies, storage, accounts, or backend records. Serialize dependent transitions and shared writes. Give workers the short current contract and relevant entrypoints, not the whole conversation. Use the exact family-specific roles in Orchestration; don't split further merely to increase agent count.

## 3. Inspect, decide, repair, and recapture

1. Browser worker reproduces the same semantic state on both sides and asserts it before capturing. Record suspected differences and facts, not unearned PASS labels.
2. Leader independently compares each pair and supporting behavior/source using the ordered decision standard, even without worker findings. Use the Evidence contract's canonical dispositions; insufficient facts require narrow evidence collection before guessing or escalating product policy. Unsupported taste or “minor/cosmetic” is not grounds for accepting a deviation. Reuse standing decisions for repeated, unambiguous defects.
3. Repair worker fixes the responsible layer within assigned ownership and performs focused functional checks. Preserve user work. Return changed components/styles/fixtures, revision, validation and **all affected comparison IDs and sides**, including dependent cases and visible background content. Leader checks impact completeness.
4. On each known change, immediately persist affected-side invalidation, invalidate related review and union/deduplicate pending IDs in the existing index; surface **已過時 / 需要更新** in the draft report immediately. Follow the Evidence contract's bounded capture checkpoints: collect diagnostic evidence when needed, but formally recapture affected states once related repairs are stable, not after every small edit or only at project end.
5. Leader reviews replacement pairs in the actual report and closes findings only when current evidence meets the standard. Keep unaffected valid evidence with true capture versions. Repeated invalidation or failures require investigation of fixtures/state/spec/ownership or context degradation before another capture attempt; never loop blindly or weaken acceptance.

Read-only tasks stop at evidence and decisions and explicitly state repairs were not authorized. Repair tasks do not defer confirmed in-scope fixes merely because a report draft already exists. An explicit exclusion is not a pass.

## 4. Assemble and review the actual report

Build a reviewable report from explicit case/step/state IDs using the Evidence contract and Report outline. Prefer the project's existing report generator; otherwise create simple local HTML with paired images and stable case anchors. Keep dependencies minimal. Drafts remain visibly drafts.

The leader opens the **exact deliverable path/URL** and independently reviews every current pair, including actual linked images at sufficient resolution or aligned identified segments when the report view is inadequate. Inspect the rendered report too; bind review to its generation/presentation. Check instructions, content, appearance, state correspondence, accepted differences and true versions, even when workers reported no findings. Verify representative interactions and disputed states through application evidence or assigned reproduction. Structural checks, contact sheets, pixel diffs, worker claims and image counts cannot replace visual review.

After a shared change, reopen all affected pairs, not only the reported case. When older evidence is unaffected, preserve its capture revision and record why intervening changes do not invalidate it. Never claim all images were captured at final HEAD unless they were.

## Completion

- Complete delivery requires current matched evidence and valid leader review for every required state. No required stale/needs-update, missing or unverified images may remain under a completed status. Any unresolved item keeps the report partial; exclusions require existing authority and cannot silently shrink scope. Observed mocked UI can satisfy its declared UI scope, never an unverified backend claim.
- Confirmed in-scope differences are fixed and reverified with newly captured affected-side evidence; unchanged reference baselines and unaffected current-valid images may be reused with true capture versions and impact rationale. Current-valid does not mean newly captured; an explicit all-new requirement overrides reuse. Accepted differences cite the decision and rationale.
- Text, screenshots, source links, case anchors, and version labels agree. Long-dialog sections and final transient outcomes are evidenced when required.
- The final integrated build is identified; applicable checks pass or limitations are stated. Mocked UI evidence does not establish backend readiness.
- Owned temporary reference edits/fixtures are restored and verified. Sessions/processes are closed with ownership checks or explicitly transferred with owner and expiry. Preserve evidence, user state, and unrelated services.

Return the report link, implementation change/commit summary, scope/exclusions, leader review result, and material remaining limits. Commit, tracking, and publication follow existing authority. The user should judge the delivered result, not perform its first complete review.
