# Evidence and report contract

## Capture setup

Use `playwright-cli` through PATH for ordinary UI operation, with a project-named session and group suffix, such as `-s=project-parity-company`. Read its installed skill/current help before use. Use project runners for regression tests and Playwright Test for repeatable browser tests. DevTools serves needed performance/memory diagnostics or missing capabilities; a failed first attempt alone isn't enough to switch. A user-selected tab follows the tool that can access it.

If the preferred tool or a required capability is actually unavailable, respect explicit tool constraints and use an available permitted host alternative, disclosing capability/evidence limits. Do not auto-install or silently substitute; if constraints prevent an adequate alternative, hold only the dependent checks and report the limit.

Record ownership before creating sessions, profiles, services, fixtures or reference copies. Use isolated automation state, not the personal browser. Verify independent authentication/storage and record-level data ownership before parallel work. Don't restart a shared server or mutate a build while another worker captures it.

Before each capture, assert observable state: role/org/entity, page/dialog title, phase/status, key data and controls. Wait for relevant rendering, fonts and transitions; don't rely solely on a fixed sleep. Assert transient success/error content while visible. Record actual assertion outcomes; failure means investigation, not a labeled image of the wrong state. For behavioral cases, record the action sequence and observed state changes as well: a matching still image cannot prove tab filtering or acknowledgement behavior.

For relevant tables/dialogs on **both sides**, determine horizontal/vertical overflow at the tested state/viewport. Traverse the relevant scroll containers to observe hidden required columns, actions and sections; capture labeled corresponding view segments when needed. Reuse unchanged known-layout checks rather than capturing every scroll offset or creating a full matrix.

Use the same business role as the reference; record permissions and any entry path affecting the screen, including persistent sidebar/scroll state. Test-suite shortcuts are allowed only when they reproduce an equivalent state. Align viewport, device scale, zoom/text size, sidebar, scroll container/offset or anchor, focus/hover, modal stack and tabs. Pixel-identical offsets aren't required for approved layout differences, but semantic content must correspond and the reason must be explicit. Store and link full screenshots; optional crops supplement them without hiding discrepancies. Never edit screenshots to manufacture parity.

Use realistic isolated data. A needed state missing from fixtures belongs in the project's existing fixture/seed with a small guarding test, not capture scripts. If the reference cannot reach a state, record why and any template/structure comparison; this does not establish behavior parity and the gap stays visible. Distinguish reference-unavailable states, verified single-side behavior and two-sided parity; a data-difference waiver never extends to behavior. Mock unavailable dependencies with contract/fixture versions and unverified backend behavior in metadata. Deliberate reference exception simulation requires a reversible task-owned copy/override and verified restoration. Never silently edit the reference into compliance or leave it altered. Respect explicitly excluded exceptions.

## One evidence index

Adapt the existing manifest format. If absent, use a small JSON/YAML index containing the following information. This is a data contract, not a requirement to build a new framework; common setup can be recorded once per run/group and referenced.

```yaml
run:
  id: parity-run-01
  scope: case IDs and explicit exclusions
  reference: URL/path and revision/build identity
  implementation: URL/path and integrated revision/build, relevant dirty fingerprint
  models: requested and effective leader/browser/repair models and reasoning
  setup: role mapping, fixture/mock versions, viewport/scale/zoom, environment ownership
  report: exact output path/URL and generation identifier
comparisons:
  - id: CASE-01.step-02.review-dialog.top
    case: CASE-01
    step: step-02
    phase: reopened review dialog before confirmation
    actions: cancel to list, reopen the same entity
    expected: agreed visible state and interaction checks
    reference_capture: ref-capture-001
    implementation_capture: impl-capture-004
    findings: [finding-001]
    disposition: fix
    work: open
    leader_review: null
pending_refresh:
  CASE-01.step-02.review-dialog.top:
    implementation: [{reason: shared badge change, revision: integrated change identity}]
    reference: []
captures:
  - id: impl-capture-004
    side: implementation
    comparison: CASE-01.step-02.review-dialog.top
    image: images/impl-capture-004.png
    captured_at: actual timestamp
    revision: actual capture revision, relevant dirty/build fingerprint
    dimensions: actual width and height
    state: role/org/entity, data status, modal stack, selected tab, scroll/focus
    assertions: observed state and interaction checks with outcomes
    dependencies: relevant components/styles/fixtures/configuration
    fixture_mode: real or mocked, with scope
    freshness: stale
    supersedes: impl-capture-002
    invalidation: change reason/revision, time, invalidated review ID
findings:
  - id: finding-001
    expected_actual: concrete difference with evidence
    decision: leader decision ID
    affected_comparisons: [CASE-01.step-02.review-dialog.top]
    repair: change/revision and verification evidence
```

These are incomplete illustrative records, never passing evidence. Every referenced capture/finding must exist in the real index. Use stable IDs composed of case, step, semantic phase and view segment. Bind each side explicitly; **never pair by array position, nearest timestamp, or filename guessing**. A user step may need multiple pairs: input → signing → success, or dialog top → history/footer. Don't fabricate clicks to equalize flows; label accepted differing stages and explain their relationship.

Capture IDs and filenames are immutable. New images get new IDs/paths; update current references in the index. This also prevents stale browser caches from serving overwritten PNGs. Retain historical before-images only as needed and clearly separate them from current verified implementation images.

## Canonical status and summary

Keep these dimensions canonical in the index; derive concise reader-language report statuses below rather than displaying raw enum fields together:

| Dimension | Values and meaning |
| --- | --- |
| Leader disposition | `meets-standard`, `fix`, `accepted-difference`, `evidence-mismatch`, `evidence-needed`, `pending-decision`, `excluded`; unset means not yet assessed. Criteria defines when to choose them. `excluded` requires cited scope authority. |
| Comparison work | `open` → `ready-for-review` → `leader-accepted`. Ready requires current matched captures and required behavior checks; a fix also requires implementation and focused verification. Acceptance requires valid leader image/report review. New invalidation returns work to `open`; presentation-only review invalidation returns it to `ready-for-review` if evidence remains valid. Excluded comparisons leave work unset. |
| Capture freshness | `current` = valid for the assessed scope/build; `stale` = invalidated by changed or unresolved inputs; `historical` = retained only as prior evidence. Missing captures are missing, never current. |
| Report status | `draft`, `partial`, `leader-reviewed complete`. Required unresolved evidence, repair, review or policy keeps delivery partial. |

`fix` and `accepted-difference` are decisions, not proof of completion. `evidence-needed`, `evidence-mismatch`, `pending-decision` and an unset disposition are non-pass and cannot become `leader-accepted`. Any required check unavailable or blocked stays `open` with its reason/owner rather than adding another progress status. A read-only assessment can document a fix without claiming repaired acceptance. On authorized exclusion, including for feature removal, unbind current captures, move old images/steps to history, clear work and pending refresh, and count the comparison only under exclusions. Superseded evidence within retained comparisons is historical and cannot support current acceptance.

Summarize comparison IDs, not image or finding counts: required scope = `leader-accepted` + pending (`open` or `ready-for-review`); list authorized exclusions separately. Accepted differences are a subset of accepted comparisons only after verification/review, not a second pass count. Show pending refresh IDs/count as a subset of pending work, deduplicated across sides and changes. An assessment/report review alone does not make unresolved comparisons accepted.

Render one short status in the reader's language per pair: e.g. **已確認** for accepted work, **已接受差異** for a verified exception, **待補證據 / 證據待校正 / 待決策** for the corresponding unresolved disposition, or **待修正 / 待驗證** according to the open next action. Stale evidence overrides acceptance with prominent **已過時 / 需要更新**; current replacements awaiting review show **需要更新：待 leader 審閱**, not stale-image wording. Authorized exclusions show **範圍外** with the reason. Keep raw dimensions and review/provenance details accessible through the index or collapsed details; do not repeat them beside every successful pair.

## Immediate invalidation and capture checkpoints

1. **Inspect impact on each change.** Repair worker supplies changed components/styles/tokens/text keys/fixtures, known usages and comparison IDs/sides. Leader checks existing index dependencies against actual usages, including transitive consumers and visible dialog backgrounds. Global styles/fonts require checking potential consumers across groups. Inspect every current capture reference: primary sides, supporting/segment pairs, supporting captures and behavior-evidence/image links. For fixture/seed changes, compare affected served data before and after rather than relying on keyword search; no fixed role×endpoint matrix is required. Existing mappings are a starting point, not a complete dependency graph; inspect uncertain usages and keep potentially affected evidence pending/stale until impact is resolved. No static dependency framework is required.
2. **Persist and surface immediately.** As soon as impact is known or cannot be excluded, mark affected captures stale, invalidate related reviews, return comparison work to `open`, and union affected IDs/sides into `pending_refresh`. Record change reason/revision and invalidated review; repeated changes append causes without duplicating the capture task. Update the visible report immediately, not only on final regeneration: downgrade completed status to partial; affected figures/steps prominently say **已過時 / 需要更新**, with IDs and overview pending count. If regeneration is costly, persist a visible status patch/banner tied to those IDs beside the affected content that overrides prior acceptance; index and displayed status must agree. A commit or caption edit never clears invalidation.
3. **Repair and verify in bounded batches.** Group related workflow/shared-component fixes. Perform necessary focused interaction, control-state and narrow-width checks while repairing. Changes to feedback modality, blocking, focus or navigation run affected existing end-to-end/acceptance scenarios, including the next step after dismissal. Compare failures against a baseline revision only when attribution is unclear. Only the same failure reproduced at baseline is reported as pre-existing, never as a pass. Defer formal report recapture until the batch is stable, not until the whole project ends. Diagnostic images may be collected earlier to enable decisions; they become final evidence only if their IDs, state, build, checks and freshness satisfy this contract.
4. **Capture checkpoint.** Leader confirms an identified integrated build and fixture baseline, passing focused checks and no known pending change affecting the group. Pin these inputs for capture; allow required UI transitions and reset from the recorded recipe. Capture each pending state/side once for that stable checkpoint. Reference images need refreshing only when their own inputs change. A new change re-invalidates only affected evidence; frequent unexpected changes pause that group's formal capture for diagnosis of shared writes, requirements or fixtures, and possibly a smaller batch. Do not blindly repeat or impose a fixed retry cap.
5. **Verify and close per ID.** Bind new immutable capture IDs, record actual results and move eligible comparisons to `ready-for-review`. Record replacement IDs in pending entries so current replacements awaiting review are not recaptured again. Replace their stale-image label with **需要更新：待 leader 審閱**; capture freshness is current but acceptance is pending. After leader image/report review, set `leader-accepted` and clear only those resolved pending IDs/markers. Other IDs remain visibly pending. Pending refresh records retire only after valid replacement/review, a documented impact investigation proves the old evidence unaffected, or authorized exclusion under Canonical status; never relabel an affected old image as refreshed.

**Current-valid is not newly captured.** Affected states require new captures of affected sides. Unchanged reference baselines and unaffected older evidence may remain current after a recorded impact check against the final build. Preserve actual capture revisions/times and reuse rationale; never claim reused images were newly captured at final HEAD. An explicit all-new task requirement overrides reuse. Fixture, role, reference, viewport, font or shared-style changes can invalidate evidence without source changes. Matching image bytes alone cannot establish behavior or context validity.

Complete delivery has no required stale/needs-update, missing or unverified images, unresolved impact or invalid review. Keep partial status while any remain; exclusions cannot silently shrink agreed scope.

## Render the report

Use [the outline](../assets/report-outline.md) with the existing generator or simple local HTML. Render explicit index links, escape labels/captions, and preserve relative image links. No external service or new reporting dependency is necessary.

- Keep the report concise: short overview of scope/status/remaining items; each case retains who/role, initial condition and goal, plus relevant entity/data/prerequisites. Follow with concise reproducible actions, expected results, paired evidence and precise status. Matches need no explanatory paragraph; add at most a short necessary finding/result/decision sentence per step.
- Stable case anchors and sequential explicit step headings. Concision reduces repeated methodology/metadata, not scenario meaning, evidence coverage or leader review. Keep blockers and material caveats visible.
- Consistent implementation/reference sides, image aspect ratios, display scales and column widths. Top-align images and explain intentional viewport/layout differences.
- Separate labeled pairs for long-dialog sections. Don't stretch/crop one side, omit a modal, or substitute a different step.
- Show minimal per-side identity/version where needed to prevent ambiguity and link the actual full image. Keep complete revision, role/entity, phase, viewport, mock mode and time accessible in collapsed details or evidence-index links; share common metadata once. Avoid giant repeated captions. A global “latest revision” banner cannot relabel old images.
- Historical before-images and retired steps collapsed separately. Excluded comparisons show the removal/out-of-scope reason in both image slots instead of “not captured”; old evidence is historical. Accepted differences and unresolved items remain adjacent to the relevant pairs, not only in an appendix.
- Derive visible status and summary counts from the canonical mapping above, with prominent stale/needs-update markers adjacent to affected evidence. Common generation/presentation/reviewer metadata, methodology, history and diagnostics belong in links/collapsed details only when useful.

## Validate structure, then inspect visually

Use project tooling or a small task-local check to verify:

1. Each required state maps to exactly one current comparison ID; view segments have distinct IDs. No duplicate IDs or undefined references, including primary sides, supporting/segment pairs, supporting captures and behavior-evidence/image links.
2. Every required pair has both sides or an explicit missing-side reason and non-pass status; a missing-side pair is never `meets-standard`. Excluded comparisons follow the retirement rules above. Required actions/results aren't omitted merely because counts look plausible.
3. Images exist, decode, have recorded dimensions and agree with capture metadata; links and anchors resolve. Flag unexplained reuse across different states for inspection.
4. No stale image, placeholder, unresolved finding/impact or invalid/missing review is accepted. Disposition, work, freshness, pending IDs, visible markers and counts agree; excluded comparisons have no current captures or open work. Every current capture reference is valid for the claimed state/build, not superseded evidence supporting current acceptance. Historical images remain separate.
5. Captions/text reflect current records/decisions; the integrated build and evidence reuse are identified.

Integrity is not UI correctness. The leader independently compares **every current pair**, even without worker findings, against relevant criteria and surrounding instructions. Open actual linked images at sufficient readable resolution or use aligned, identified segments when thumbnails/long full-page images hide detail; don't force massive original-image rereads when the view is already adequate. Inspect the exact rendered deliverable too. Contact sheets can triage but cannot replace this review.

Record comparison/capture IDs, reviewer, disposition and report generation/presentation reviewed so handoff preserves coverage. Changed evidence, scenario/result text or decisions invalidate related review; status-only updates recording that same review do not. Report CSS, scaling, cropping or pairing changes invalidate affected presentation review even with unchanged images; equivalent regenerated content/presentation can reuse recorded review after an equivalence check. Image equality alone is insufficient.

Verify representative interactions and disputed states via browser evidence or assigned reproduction, not screenshots alone. Resolve mismatched phases, missing sections, wrong roles/data, unreadable reductions, scroll and report layout errors before release. If the user sees something different, inspect their exact report path, anchor and image URLs first; don't assume failure to refresh.
