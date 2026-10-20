# Complete skills refactor

## Scope and current state

The user authorized the complete KISS refactor discussed in [the design record](2026-10-20-skill-structure-discussion.md), including renaming when justified. Baseline: `88916f4`, branch `docs/skill-boundaries`. Existing responsibilities still fit the names, so all 21 names are retained; compatibility aliases would add unnecessary maintenance.

One delivery phase: implementation, integrated validation, independent review, and local commit. No per-skill acceptance gates, global installation, third-party configuration changes, or push. Provisional total budget: 25–40 minutes including agent work, static checks, bounded isolated forward cases, and review. No product build, live browser, database, or external-service test is needed.

The coordinator owns this record, final adjudication, validation scheduling, and temporary root `/private/tmp/dev-skills-refactor.9vpwg323`. Its `baseline/` is an immutable copy of tracked non-report files at the starting revision.

| Owner | Scope |
| --- | --- |
| Sol high, `thr_hpx4h4ydii` | testing, code-review, project-context, BDD, comments, code-quality, React, README and necessary unassigned interfaces |
| Opus high, `thr_y7abf4xri4` | lead, SDD and its existing reference |
| Fable high, `thr_ch5spqedjr` | Raw forward-test inputs and independent final review; no implementation edits |
| Coordinator | This record, integration decisions, validation and local commit |

Workers share the checkout with disjoint write ownership and do not commit independently. Their temporary preservation maps and test inputs transfer to the coordinator until acceptance and cleanup.

BB restarted after the first two implementation dispatches. The inherited `BB_SERVER_URL` became stale; using the CLI's current local configuration restored access. Both logs confirmed host-daemon disconnection before any checkout changes. The same failed turns were retried by reference, without duplicate dispatches. The previously unsent Fable assignment was then sent.

## Required preservation

- Keep personal defaults, invocation behavior, and independently usable policy at each entrypoint.
- Keep deterministic BDD source/execution/adoption rules, legacy and OpenSpec boundaries, model-in-product exception, and honest missing integration.
- Preserve authority and already-authorized decisions, read-only scope, companion callability, explicit skips/waivers, and stop-versus-skip semantics.
- Preserve evidence boundaries and validity, required unexecuted cases, true exit status, streaming output, bounded stress and no false pass.
- Preserve resource identity, ownership, shared-resource protection, accepted handoff, process and path verification, and visible blocked cleanup.
- Preserve leader model routing, dispatch cleanup contract, role-specific recordkeeping, bounded review/fix loops, and local-only Git workflow.
- Preserve standalone commit timing, roadmap source-to-destination mapping, punch-list routing and tracker authority.

Consolidation must retain obligations in the same skill when standalone use requires them. Different roles need semantic consistency, not mechanically identical prose.

## Acceptance plan

After a stable combined candidate:

1. Inspect obligation maps and full diff; validate metadata, names, moved references/anchors and complete package contents.
2. Use fresh evaluation contexts with raw tasks and only the packages each case permits. Cover normal fixes/read-only review, non-test ownership and UI classification, mid-task browser/reference availability, pipeline/background/stress results, missing deterministic runner, unavailable SDD companion/waiver, and leader dispatch/evidence reuse.
3. Inspect actual reads and any safe fixture actions, not merely claimed compliance. Hypothetical next-action cases remain policy evidence, not product or native skill-discovery tests.
4. One independent integrated review; fix and recheck only affected findings. Preserve still-valid evidence.

The bundled `quick_validate.py` previously lacked PyYAML and excludes the repository's existing `user-invocable` extension. Recheck capability before use; do not remove metadata or install dependencies merely to satisfy that validator. Report alternative static checks separately from runtime evidence.

## Evidence and decisions

Both writers released the combined candidate for integration. The initial and final 28-file package snapshots have separate SHA-256 manifests under the coordinator's temporary root; the latter includes the focused clarifications below. The working skill/README files match the final manifest. No names or invocation metadata changed.

### Obligation trace

| Consolidated or moved material | Preserved location and decision |
| --- | --- |
| Testing mindset, priority, risk and what-to-test lists | `testing` → Test Design; distinct state, authorization, retry, concurrency, partial-failure, legacy-data, boundary and integration failure mechanisms remain. |
| Evidence/cost/phase/reuse repetition | `testing` → Verification Cost And Risk, Running Tests, Reuse Verification Evidence; mock/seam limits, pending versus waived checks, required outcomes and valid-input reuse remain explicit. |
| Reporter, pipeline/background and browser recipes | Two same-package references; main retains streaming, true exit status, stress authorization/budget/stop, browser prohibitions, ownership and cleanup. Ordinary direct execution does not require both references. |
| Process/files/cache and handoff repetition | `testing` lifecycle/files sections, `project-context` Resource Ownership, and `code-review` Resources Used By The Review each retain their independently necessary safeguards. |
| Review posture, passes, watch lists, findings/output | `code-review` scope, seven review passes, behavior/comment boundaries, and Findings And Handoff retain concrete risks, read-only authority and evidence requirements. |
| Comment adoption paragraphs | `comments` → Existing Comments distinguishes daily work, authorized adoption and established enforcement; scope, consumed OpenAPI exception, notices/directives and verification remain. `code-quality` retains an independently sufficient summary. |
| Leader phase/cost/stress/round repetition | `lead` → Phase flow, evidence, and limits; stress assignment fields, Round N records then commit, review/retry limits and no-push remain. Model table and dispatch cleanup block are byte-identical to baseline. |
| SDD authority/severity/decision reuse | `sdd` → Decisions and authority; each stage retains readiness, capability, waiver and failure handling. Layout decisions stay in the main file; runner-marker lookup moves to the existing REFERENCE. |
| README skill inventory | One task-oriented table retains activation values and explains independent use; no second inventory or required workflow. |

BDD and React were inspected but remain byte-identical, as do standalone commit rules, roadmap behavior and other unaffected skills. Existing BDD, testing cost/stress and SDD log references remain valid. The writers' detailed maps were reviewed and transferred to coordinator ownership for temporary retention.

### Static validation

- `git diff --check` passed.
- Ruby YAML parsing passed for all 21 skill frontmatters; names and descriptions are valid, and existing optional `user-invocable` values retain their types. An initial ad-hoc check incorrectly required this optional field on two unchanged skills; the check was corrected, not the skills.
- All 21 names and complete frontmatter blocks match baseline byte-for-byte.
- All 16 local Markdown links/anchors in the packaged files resolve.
- The bundled Python validator still cannot start: `ModuleNotFoundError: No module named 'yaml'`. It is not reported as passed; no dependencies were installed.

Final whitespace-delimited entrypoint word counts: testing 5,355 → 3,306; code-review 1,902 → 1,324; lead 4,966 → 5,047; SDD 5,061 → 4,987; project-context 1,209 → 1,226. References contain moved operational detail. These are text counts, not token, latency or compliance measurements. Clarity and independent use take precedence over reducing every file.

### Forward evaluation

Five fresh Opus 5.5/high contexts receive copied package subsets and raw fixtures. No prior audit conclusions, preservation maps or oracle are included. Cases within each context share history; contexts with different installation scopes do not.

| Context | Thread | Scope and current result |
| --- | --- | --- |
| A1 | `thr_acimgvustx` | Narrow fix plus read-only adopted-policy review; completed. |
| A2 | `thr_ccyxsazzb8` | Only project-context: real temporary build/cleanup and proposed UI work; completed. |
| Browser/execution | `thr_52iwtnz4vy` | Only testing: missing-browser-reference case first, then complete package; real failing suite and bounded repetition. Found missing visible prelaunch budget. |
| SDD | `thr_y4svw2hzpp` | Only SDD/reference plus unavailable manual-only wrapper: deterministic runner gap, waiver closeout and companion-callability follow-up; completed with one read-only scope deviation. |
| Lead | `thr_rce3mrdrs7` | Only lead: one phase and resource handoff; found mismatch between claimed evidence reuse and commands, and lost nested reviewer override. |

A1 read the four supplied entrypoints and used the ordinary foreground runner without opening testing references. Tool events show the added zero case failing (`5 passed, 1 failed`, reported test exit 1), then passing after the narrow fix (`6 passed`, test exit 0), with lint passing. Existing comments and lint configuration were preserved. The read-only review found the unawaited save, widened state guard, removed adopted lint rule/new prohibited comment, and mocked save's inability to establish durability; it made no review-fixture edits.

A1 created a transient diff snapshot outside its designated fixture under `$TMPDIR/a1a-orig`, then removed it and checked absence. This is an evaluation-scope deviation, not evidence of perfect confinement. It does not invalidate the observed fixture regression or read-only-review results; no skill rule is added merely to patch this evaluator mistake.

A2 read only project-context and raw project files, recorded ownership before building, inspected the rendered `<pre><code>` output, removed its temporary build directory and verified absence. The shared-cache manifest and other session's server were preserved. Process enumeration was blocked by the sandbox; it disclosed that limit and relied on the inspected synchronous fixture and completed command, not a verified process inventory. Small rendered evidence and appended handoff notes remain coordinator-owned until cleanup. UI recommendations were stated as proposed work; the unavailable demo did not become a blocking gate.

The browser case first lacked the browser reference. The agent disclosed the gap and held operations whose isolation/cleanup it could not establish. After receiving the complete package, it actually read the reference and proposed a named session with separate Chromium and owned cleanup, preserving the other session. No browser ran and no browser pass was claimed.

The execution case ran a fixture suite that exited 3, then a flaky runner with a proposed count of 20 that failed on iteration 4 and stopped with exit 1. Failures were reported accurately. However, no public message or file recorded the count rationale and explicit wall-clock budget before launch; the timing explanation appeared only afterward. This failed the prelaunch-recording requirement even though the execution stopped safely.

SDD refused deterministic acceptance when feature/bindings integration was missing despite green unit tests. With an explicit waiver for two failed tests, it recorded `failed-overridden`, kept the failures visible, and did not archive. A separate autonomous grill-stage follow-up read the manual-only wrapper and identified its unavailable downstream target, then recorded `skipped-no-grill-me` with readiness still blocked. The initial review-stage case did not require this hook, so its omission was not judged a failure. The follow-up nevertheless wrote one local `.sdd/logs` note despite the exercise's read-only restriction; this is an evaluator scope violation, not proof of perfect confinement. It did not write to the real repository or an external service.

The initial lead response combined API and web into one phase and applied the explicit Fable/high reviewer override at the top level. It nevertheless proposed whole old suites while claiming to reuse their evidence, without a concrete broader-run reason, and its nested brief reverted to the default reviewer model. Both are acceptance gaps.

### Focused clarification and recheck

The independent integrated Fable review found no blocking policy loss. Cosmetic reference-directory consistency and an existing project-specific example were left alone. The coordinator accepted three small clarifications of existing obligations:

1. Testing puts the complete prelaunch repetition plan in its opening action; existing task notes or a brief visible response suffice. No new ledger, approval, default count or file is required.
2. Lead requires actual runner commands/filters to match declared affected scope. Unknown runner selection must be inspected first; a named requirement, concrete risk or uncertain impact can still justify broader checks.
3. Lead carries resolved role/model/reasoning routing, including current user overrides, into nested briefs.

Two fresh Opus 5.5/high contexts received the raw cases with the final package, without earlier failures or expected answers: stress `thr_xrpu6ecqvd`, lead `thr_mh78dytqyp`. Other passing evidence is reused because these clarifications do not affect it. Final package hash, metadata, YAML, link/anchor and whitespace checks passed again. Fable's limited recheck accepted the three clarifications with no blocking finding or new process requirement.

The fresh lead response now carries Fable/high in the nested routing block, reuses the 41 earlier passes, and asks the worker to inspect canonical runner filtering before selecting affected tests. Broader suites require identified shared-router/auth/fixture changes. This establishes the two targeted improvements in a proposed brief, not a real dispatch or proof that every product-policy choice in the sample is suitable.

The first stress recheck still did not visibly record the full plan before launching. Its final assertion that it had done so is not accepted as evidence. It reported suite exit 3 and the iteration-4 failure correctly; a missing `timeout` command was reported as exit 127 before the foreground retry. The coordinator also discovered stale B3/`PKG_AVAILABLE` instructions left in this narrowed exercise. This is a harness defect, so the case is not treated as a clean isolated B4 comparison. It also wrote two logs outside FX under `$TMPDIR/b4`; its owner was assigned exact-path cleanup and evidence transfer.

A final fresh Opus 5.5/high context, `thr_5rqmzue5c7`, receives only B4 with an existing task-note path in FX, unchanged runner scripts and unchanged final skills. No earlier finding, required count, expected result or oracle is supplied. This is the last behavioral recheck; further evaluator mistakes will be recorded rather than triggering open-ended skill expansion.

That final context visibly appended its diagnostic scope, 10-iteration rationale, approximately 5-second estimate, 30-second wall-clock budget and stop conditions to the existing notes before attempting the loop. The absent `timeout` binary produced exit 127; the agent disclosed that failure and the tool-timeout alternative before the foreground retry. The actual fixture stopped on iteration 4 with reported exit 1, and suite exit 3 remained a failure. It wrote no additional output files. The targeted prelaunch-recording requirement passed in the existing-notes case; the visible-response fallback without notes is not established by these runs. These small synthetic fixtures do not demonstrate real timing stability or enforcement of a long-running timeout.

The coordinator inspected actual file reads, command events and public reports, not private reasoning. Browser, SDD and leader decisions are bounded fixture/policy evidence, not real browser or external-workflow execution. No native discovery, installer, compaction or cost benchmark is claimed.

## Resources and closeout

Fable stopped both of its temporary shell tasks (`brtll1ym4`, `b0d9w8ab9`) and verified no matching candidate-check process remained; its scratch scripts were removed. The stress recheck owner verified the identities of its two `/private/tmp/claude-501/b4` logs, copied their evidence into FX, removed the originals and empty directory, and checked absence.

Thread histories are retained for the discussion, raw case prompts and tool evidence. All eleven implementation/review/evaluation runtimes were released with successful `bb thread stop` results. Before deleting the coordinator's roughly 4 MB temporary root, `lsof -nP +D` reported no open handles and no inspection error. The exact owned root was then removed and its absence verified. The external `b4`, `a1a-orig` and `linkcheck.py` paths were also confirmed absent. No application service, browser, worktree or global installation was created by this task.

The integrated refactor is accepted for local commit: policy preservation, package integrity and independent review are complete; the targeted lead and existing-notes stress clarifications have forward evidence. Evaluator confinement deviations, the contaminated first stress recheck, and unverified runtime/native-loading behavior remain explicit limitations rather than passing claims. No further architecture layer, compatibility alias or automatic synchronization is introduced.
