# Skill structure discussion

## Scope and evidence

Discussion only, against `docs/skill-boundaries@dc02a14`. No skill edits, installation, runtime experiments, or product tests were authorized or performed in this round.

Two rounds: independent assessment followed by coordinator-mediated challenges of the competing proposals. The existing discussion threads were reused with explicit model/high overrides:

- Fable 5.1: `thr_ch5spqedjr`, `claude-fable-5-1`.
- Opus 5.5: `thr_y7abf4xri4`, `claude-opus-5-5`.
- GPT-6.1-Sol: `thr_hpx4h4ydii`, `gpt-6.1-sol`.

The provisional discussion budget was 5–10 minutes, without additional agents or experimental gates. Findings are source-based design judgments, not measurements of discovery, compliance, compaction, token cost, latency, or installer behavior.

## Agreement

- Preserve skill names, invocation metadata, personal policies, and standalone safety. No mandatory shared skill or new cross-skill dependency.
- Withdraw the proposed `project-context/references/resources.md` and `ui-migration.md`. The current entrypoint has 81 lines / 1,209 whitespace-delimited words; resource guidance is mostly necessary policy, and migration guidance is one paragraph. These counts describe this source, not universal splitting thresholds.
- Keep authority, safety prohibitions, evidence validity, stop conditions, cleanup ownership, and partial-install fallbacks in entrypoints.
- Preserve project-context's non-test resource contract and lead's embedded dispatch cleanup contract. Maintenance ownership does not imply a runtime dependency. Fable explicitly withdrew its earlier “one sentence each, details only in testing” recommendation.
- Defer lead restructuring and SDD table extraction. Existing CLI guidance includes decision timing; moving it would not necessarily improve it. The SDD tables have no demonstrated problem justifying this round.
- A small README task-to-skill selector could help humans. It must not duplicate the inventory, imply a mandatory workflow, or stand in for runtime skill discovery.
- Do not use arbitrary file/word limits or two passing cases as proof that a structural change is useful.

## Remaining disagreement

| Reviewer | Preferred first testing change | Reason and limitation |
| --- | --- | --- |
| Fable | Reorder the entrypoint, preserving content and headings | Avoids new reference dependencies; improvement from ordering remains unmeasured. |
| Opus | Extract execution examples, reporter choices, and optional calculation detail | Separates operational examples from decisions; exit-status and no-false-pass rules must remain in the entrypoint. |
| Sol | Extract browser configuration, commands, and diagnostic examples | Gives non-browser work a clear optional boundary; loses value if safe summaries duplicate most of the reference or agents miss it. |

There is no three-party agreement on which change should ship first. Each reviewer's proposed “common next step” was its own recommendation, not evidence of consensus.

## Coordinator adjudication

No implementation is dispatched. If a later implementation is requested, prefer a small testing execution-reference candidate first: reporter tables and shell examples only, retaining streaming progress, correct exit-status capture, launch-versus-success distinction, resource rules, and stress decisions in the entrypoint. This is a coordinator choice, not a unanimous conclusion or a proven optimization.

Do not bundle framework preference changes, browser policy extraction, UI cross-skill deduplication, SDD relocation, or description narrowing into that candidate. A README selector is independently useful; it is not evidence that references improve agent behavior.

Before accepting any later extraction, trace every moved rule and incoming section reference. Read conditions must occur before the relevant action, including a mid-task transition into browser, pipeline/background, or stress work. Keep adequate standalone safeguards.

If a required reference is missing, disclose it and continue independent work. An already-understood safe direct runner or foreground execution may remain usable. Hold only operations whose isolation, ownership, exit status, or other required safeguards cannot be established. Never claim the reference was read.

A later candidate needs a bounded comparison covering normal use, a mid-task transition, and an unavailable reference, observing actual reads and decisions. Safety or false-pass regressions reject the affected extraction. Static link checks alone do not demonstrate runtime compliance; no performance claim follows from shorter entrypoints.

## Closeout

All three reviewers reported zero new resources and no modifications. All three threads were confirmed idle with zero background agents and queued messages; preserve them as discussion evidence. The coordinator removed the four owned prompt files and their temporary directory `/private/tmp/dev-skills-structure.j1o3luf0`, then verified its absence. This record is the only repository change from the discussion.

## Revised premise: one complete refactor, KISS

The user subsequently rejected incremental delivery and requested another discussion assuming a complete refactor is acceptable, while keeping the result simple. This changes the proposed delivery scope; it does not authorize implementation in this discussion turn. The following proposal supersedes the earlier narrow-candidate recommendation.

The same three threads, with the same explicit models/high reasoning, independently proposed whole-repository end states against `574aca5`, then responded to a consolidated proposal and concrete counterexamples. No additional agents, prototypes, or runtime experiments were used. All three accepted the integrated one-delivery plan, subject to the safeguards below; Fable retains a preference for one testing reference instead of two.

### Proposed end state

Keep the 21 existing flat skill directories, names, invocation metadata, personal defaults, and behavior contracts. No master/router/core skill, required sibling installation, generated policy system, new validation framework, or mandatory document template.

| Treatment | Skills |
| --- | --- |
| Rewrite organization and redundant wording around each skill's actual decisions | testing, lead, sdd, code-review |
| Consolidate within the existing package where necessary, preserving complete contracts | project-context, bdd, code-quality, comments |
| Check affected interfaces and references; edit only when the refactor actually changes what they consume | react, rust, commit-conventions, punch-list, issue-tracking, road, daily-work-report |
| Preserve the current body unless a concrete affected reference requires repair | api-design, backend-principles, nodejs, security, sql-conventions, typescript |

Comprehensive delivery means completing all necessary interfaces together, not producing a diff in every file.

- **project-context:** repository knowledge, commands and installed versions, UI-work classification, general resource ownership, and handoff. Consolidate repeated handoff wording in place. Remain self-contained for non-test work; no new resource or UI reference files.
- **testing:** organize around evidence selection, test design, execution/resources, and result interpretation/reuse/stop. Merge overlapping priority, risk, and what-to-test lists without losing distinct failure mechanisms. Retain evidence boundaries, deterministic acceptance fallback, resource safeguards, streaming progress, correct exit status, launch-versus-success distinction, and all stress decisions in the entrypoint.
- **testing references:** add `references/browser.md` for browser-specific binary/configuration, session commands, and diagnostic examples; add `references/execution.md` for general reporter, pipeline/background execution examples and optional repetition calculations. Browser-specific commands belong to the former; generic command execution belongs to the latter. Shared safety decisions stay in SKILL.md, so the references need not depend on each other. A background browser test may need both for separate operations. Read before the relevant action, including mid-task transitions; ordinary direct runner execution must not require reading both.
- **lead:** staffing/model choice, dispatch contracts, phase scheduling, adjudication, relay and closeout. Consolidate repeated phase/retry/handoff explanations. Preserve the exact model table/user overrides, review and loop limits, recordkeeping obligations, and embedded cleanup contract. Do not replace role-specific duties with a reference to testing.
- **sdd:** stage entry/readiness, authority, capability checks, evidence validity, skips/waivers, and progress. Consolidate repeated authority explanations. Use the existing REFERENCE for layout examples/marker tables/templates; ambiguity, missing-runner and missing-companion decisions remain understandable in the entrypoint.
- **bdd:** preserve deterministic source/execution/adoption/evidence rules, model-in-product exception, legacy and OpenSpec compatibility boundaries. Improve organization where useful without mechanically moving every normative detail out of REFERENCE or breaking its existing anchors. BDD is not the owner of all non-BDD authority decisions.
- **code-review:** consolidate review passes, risk lists and reporting into one coherent review method. Preserve independent resource safety, evidence scrutiny, read-only authority, and meaningful findings; it must work without sibling skills.
- **comments/code-quality:** retain scope, the consumed-OpenAPI-JSDoc exception, adoption authorization, established enforcement, conflict handling and read-only boundaries. Shorten genuine repetition without imposing a one-sentence limit.
- **adjacent skills:** preserve React-specific parity detail, Rust-specific runtime/Cargo guidance, standalone commit/WIP/handoff rules, punch-list routing, tracker authority and roadmap semantics. Inspect affected references; punch-list and issue-tracking default to no edit unless an actual interface changes.
- **README:** improve the existing skill table's grouping and descriptions to answer “when to use which skill,” retaining activation information. Do not create a second competing inventory or mandatory workflow.

### Design decisions and rejected suggestions

The coordinator selects two testing references for distinct action triggers. Sol supports this; Opus accepted it after cross-discussion. Fable prefers a single REFERENCE with two sections to reduce file-boundary ambiguity, but accepts two when ownership is explicit as above. This is a design tradeoff, not measured performance evidence.

Withdrawn or rejected:

- Collapsing cleanup/comment obligations into a bare sibling link: breaks standalone use.
- Deleting commit-conventions' timing rules because lead/SDD discuss handoffs: SDD explicitly excludes Git workflow, and commit-conventions has standalone users.
- Narrowing code-quality's description during structural work: changes activation behavior.
- Adding roadmap footer rules or fixing assumed dated archive-path failure: current lead already uses existing task/adjudication records, and road already follows observed source-to-destination mappings.
- Assigning every decision-reuse rule to BDD or requiring all summaries to be byte-identical: different roles retain different obligations.
- Changing roadmap location-based synchronization into a test-gate system.

Four writing principles suffice: organize by decisions; consolidate same-file repetition; retain independently usable safeguards at each relevant entrypoint; extract only cohesive conditional detail. Do not add rules merely to meet file-count, word-count, or formatting targets.

### One delivery and one bounded acceptance scope

Edit the policy owners and their consumers as one coherent change, reconcile all affected references, then validate and independently review the complete diff. Internal editing order is not staged user delivery; no per-skill gate or pilot approval cycle.

Keep a concise before/after obligation map in this existing adjudication record. For removed obligations needed independently, identify where the same skill still preserves them. Include role-specific duties such as leader stress assignment and historical round records; a matching sentence in another skill is insufficient.

Static checks cover unchanged invocation metadata, moved/removed obligations, all affected links and anchors, and complete skill-package contents. Bounded fresh-context cases cover:

1. A normal narrow fix and read-only review, including comment adoption/enforcement boundaries.
2. Standalone project-context handling a non-test build, owned/shared resources, handoff and UI-work classification.
3. A mid-task browser transition with available versus unavailable browser reference.
4. Background/pipeline result handling, stress authorization/budget/stop, and unavailable execution detail.
5. Required deterministic BDD acceptance without an executable connection.
6. Standalone SDD with unavailable companions, preserved skips/waivers, and honest closeout readiness.
7. A leader's single-phase dispatch, exact model routing, cleanup contract, and evidence reuse.

Combine compatible cases where useful; do not multiply gates by skill. Partial-install evaluations must actually omit sibling content; missing-reference cases must not leak the missing instructions through the prompt. Give raw task facts rather than expected adjudications, and inspect actual file reads/actions/result claims. Static grep does not establish runtime compliance. Reuse valid prior evidence where inputs match; repair and recheck only affected failures before one integrated delivery.

Missing references must be disclosed without stopping unrelated work. Continue a known safe direct runner or foreground alternative; hold only operations whose required isolation, ownership, exit-status or other safeguards cannot be established. No claim of having read unavailable content.

### Revised-discussion closeout

This remains a proposal, with no skill changes or global synchronization. All three reviewers reported no created resources or repository writes. All three threads were confirmed idle with no background agents or queued messages. The coordinator removed the four owned prompt files and directory `/private/tmp/dev-skills-full-structure.7gs8nmvz`, then verified absence. Keep the original threads as evidence.
