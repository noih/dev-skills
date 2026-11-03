# Common judgment criteria

## Leader decision standard

Use this order for every comparison pair and finding. Record the evidence and applicable rule, not just “looks acceptable.”

1. **Establish comparability.** Confirm role/org/entity, input data, action phase, viewport and relevant state. A stale/mispaired capture is an evidence defect first: correct it before declaring a product discrepancy. If behavior is disputed, reproduce the transition; a still image alone is insufficient.
2. **Establish authority.** Current explicit user decisions, applicable requirements and agreed contracts define intended behavior; the reference supplies the default UI/UX answer otherwise. An accepted exception must cite a user decision, requirement, contract or reference behavior for the specific UI behavior being accepted, and stay within its original scope: a wording exception does not authorize different feedback or extra steps, and a calculation rule or available field does not by itself prescribe its presentation. Artifacts that describe the implementation (existing code, tests and step definitions, design notes, prior acceptances, introducing commits) explain origin but never create authority; an approved executable specification is traced to its approval. An absence claim ("the contract has no field", "no requirement covers it") names the source, revision and search scope, and is checked against the same pattern elsewhere in the implementation and against superseding sources before delivery. An item still open in the requirement or clarification log that governs the behavior is `pending-decision`, not inference. Re-trace inherited exceptions with missing sources or changed premises; unfinished backend code never silently overrides requirements. Missing requirement text alone does not prohibit a behavior: distinguish an unsupported addition from insufficient evidence, and preserve real security/contract constraints. A material unresolved conflict goes to step 5, not a guessed rule.
3. **Check correctness and usability invariants below.** The implementation must satisfy them even when the reference is defective. Repair violations within the task's authority and document the intentional reference departure. If a proposed repair changes unresolved business policy, settle that dependency first.
4. **Check parity.** Compare observable content, structure, appearance and interaction against the authoritative result. An unexplained discrepancy is a fix, including small details; an implementation-only behavior with no support after investigation is a `fix` toward the reference. Reuse only source-backed exceptions within their original scope; don't invent a waiver because the difference is cosmetic or expensive.
5. **Choose and record one disposition.** Use the table below and canonical vocabulary in [Evidence](evidence.md#canonical-status-and-summary). When facts are insufficient, request narrow missing evidence through Orchestration before choosing a rule or asking the user. Ask the user only for a remaining unagreed product rule, waiver or material conflict outside delegated authority. The leader handles evidence-backed implementation choices without repeated permission.
6. **Accept only verified results.** Require the agreed behavioral assertions, current related images and visual review of the actual report. Record the review against steps 2–4: authority, invariants at each tested width, and parity. A review that answers only parity ("same as the reference") is incomplete. Repaired code alone leaves work open; missing evidence remains unknown, not accepted.

### Acceptance invariants

All applicable invariants must hold; these are a baseline, not the full set of project requirements.

| Standard | Required result |
| --- | --- |
| Observable state agrees with effective state | Inputs, active filters, rows/count, pagination, badges and available actions describe the actual current state. A retained keyword cannot silently stop filtering. |
| Actions have the intended consequences | Validation, state transitions, persistence/reset, confirmation, cancellation and subsequent screen match the agreed flow. Toast and modal are not interchangeable merely because text matches. |
| Required content and controls remain usable | No overlapping columns, obscured values/actions, unreadable layout or inaccessible required content at supported widths. Overflow is handled deliberately, including in dialogs and exports. |
| Presentation follows the reference or a justified exception | Wording, units, empty markers, button shape/variant, badges, spacing and alignment match the intended standard. Visible defects in the reference are not a mandate to reproduce unusable UI. |
| Evidence describes what was actually verified | Each pair shows its stated step/state and true version; mocks, missing sides, accepted differences and exclusions are explicit. |

### Disposition and authority

| Evidence/condition | Leader decision | Required next action |
| --- | --- | --- |
| Same state; unsupported wording/style/layout/interaction deviation | `fix` | Assign the responsible layer, affected IDs and expected result. No user question for ordinary alignment. |
| Proven state inconsistency or unusable layout; expected outcome established | `fix` | Correct the bug even if the reference also has it; document the intentional difference and verify behavior/usability. |
| Incorrect role/data/phase/scroll/build or swapped/stale report image | `evidence-mismatch` | Reproduce/recapture/rebind and reassess. Don't change product code merely to match the wrong image. |
| Valid evidence is insufficient to establish sequence, context or meaning | `evidence-needed` | Ask the existing operator for question-directed facts using Orchestration; if collection is unavailable, record why and leave work open. |
| Applicable explicit exception or requirement calls for a difference | `accepted-difference` | Cite its authority, rationale and exact scope; verify the implementation satisfies it. |
| Harmless rendering/fixture variation demonstrated below | `accepted-difference` | Record why meaning, appearance under the agreed tolerance and behavior are unaffected. |
| Facts investigated; product rules still conflict or a required policy/waiver lacks authority | `pending-decision` | Recommend an outcome with impact and ask the authorized decision-maker. Continue independent work. |
| No unexplained discrepancy; applicable invariants hold | `meets-standard` | Complete behavior checks and actual report review before acceptance. |

A claimed fix with stale/missing evidence or no valid leader review retains its decision but leaves work open. Decision is not verification progress; use the separate dimensions in Evidence.

Default visual tolerance is **no unexplained observable discrepancy**, not a global pixel-difference percentage. Rendering noise such as antialiasing or subpixel rasterization can be accepted when font, layout, readability and semantics are unchanged. Dynamic timestamps or equivalent fixture values can vary only when the case does not require exact values and their effect on width, state, totals and behavior has been checked. Different words, button variants, section placement, column alignment or interaction are not rendering noise. Use a project-defined tolerance if one exists; don't invent a broad threshold to turn failures green.

Prioritize broken behavior/inaccessible content before presentation defects, but both must be resolved when in scope. “Minor,” “Demo probably meant,” and “the user can inspect it later” are not acceptance reasons. Unrun or blocked checks cannot become passes. A decision record contains expected/actual, cited rule/source and evidence strength (explicit requirement/decision, contract, observed reference behavior/source, or inference), disposition, authority, affected IDs, verification needed and final evidence/reviewer; keep it in the existing index. A decision conditioned on a future fact or review ("accepted until the contract provides X", "deferred unless review shows Y") records that condition as an open check with an owner; deferral, handoff, repair cost or a pending review note is not acceptance. User-facing explanations of disputed differences label the basis as explicit (requirement/contract/decision), observed reference behavior, or inference. For disputes, investigate requirements → contracts → reference behavior/source → implementation history; this is a lookup order, not an authority ranking or a checklist for every style difference. Inference alone cannot establish a reference defect; use `evidence-needed` or `pending-decision` unless an acceptance invariant is violated.

### Responsive tables: an authorized usability correction

A reference that compresses columns into overlapping content does not define acceptable final behavior. The leader can authorize a bounded responsive-layout repair under the usability invariant without asking the user to approve the same principle for every table. Preserve information, actions and business meaning; this is not permission for an unrelated redesign.

- Assign content-aware minimum widths using suitable column/cell sizing, rather than giving every column equal width or a large global minimum. Index/period columns usually need little space; identifiers, dates, amounts and action controls need enough to remain distinguishable.
- Wrap prose and long text first; keep short key elements (status pills, buttons, short identifiers and amounts) on one line when room exists. Preserve meaning and readability; don't solve overflow by shrinking text indiscriminately, hiding columns, clipping actions or applying `nowrap` everywhere.
- When required minima exceed available width, use a discoverable horizontal scroll area around the table or the project's agreed responsive pattern. Keep overflow contained inside the intended page/dialog area. Header and body widths/alignment must stay synchronized; all required actions must remain reachable.
- Verify at the normal viewport and a narrower supported width that puts pressure on the layout. If widths aren't defined, select and record a reasonable narrower probe for this layout, rather than implying universal mobile support. Test representative longest realistic values and the action column. At each tested width, record the observed result: adjacent values distinguishable, wrapping preserves unambiguous names and identifiers, actions reachable, overflow contained. "All columns visible" or "same wrapping as the reference" is not that observation, and matching the reference never waives readability. Record it before accepting or deferring a layout difference, including a shared-component deferral. Geometry checks flag candidates; they don't prove readability. No fixed universal pixel widths or breakpoint values are prescribed.
- Reuse checks for identical shared layouts; inspect other materially different tables/dialog consumers. Exports/PDFs need their own readable, aligned layout rather than relying on browser scrolling.
- Capture both sides at the same tested width and record intentional implementation improvements as accepted differences after verification. A wider implementation screenshot cannot conceal a narrow-width failure.

## Inspection directions — examples, not limits

The following are recurring examples, **not an exhaustive acceptance checklist**. Inspect the whole relevant UI, transitions, and state consistency. Look for plausible problems outside these examples; passing this list alone never proves the case correct. Scale exploration to scope and risk rather than generating every possible combination.

| Dimension | Examples and useful questions |
| --- | --- |
| Content | Status wording, titles/subtitles, labels, punctuation/dash glyphs, unwanted identifiers, confirmation/result messages, currency prefixes, units, dates, empty values |
| Appearance | Button shape/radius/size and variants; badge foreground/background/padding; typography, label/value contrast, borders, spacing and wrapping |
| Tables and exports | Header/body alignment, numeric/text alignment, column width allocation, excessive width for index/period columns, clipped actions, overflow and PDF layout |
| Structure | Missing sections/columns, ordering, detail/history contents, modal stack, drawer versus centered dialog, footer actions |
| Feedback behavior | Toast versus modal, blocking versus nonblocking, required acknowledgement, auto-dismissal, focus and destination after dismissal |
| State and navigation | Tab changes, search keywords, filters, sorting, pagination, counts, selected rows, cancel/back/reopen, repeated submission, roles and available actions |
| Forms and transitions | Actual enabled/disabled behavior, validation timing, defaults, required fields, submit → pending → success/return, keyboard/focus behavior where applicable |
| Product boundary | Autofill/injection buttons, testing entrypoints, debug/txHash actions, simulation controls or bypasses; retain only legitimate or explicitly accepted controls |
| Evidence quality | Wrong case/step/phase, swapped images, stale version, mismatched role/entity/scroll, missing nested modal, transient result captured too late |

## Behavioral examples

**Toast versus modal.** Identical success text does not make these equivalent. Reproduce the same action and observe whether it blocks interaction, requires confirmation, auto-dismisses, moves focus, and leaves the user on the same page/dialog afterward. Inspect the full sequence: a screenshot may have caught a toast after a modal was dismissed, or before it opened. If the actual implementation substitutes a toast for a required acknowledgement modal, fix the interaction and downstream flow, not merely the color or wording. Capture the required feedback and evidence the resulting state.

**Tab changes invalidate search while keeping its keyword visible.** Start with a keyword that demonstrably filters results, switch tabs, then compare the input, active filter, displayed rows/count, and pagination. Visible keyword plus unfiltered results is inconsistent state, not a cosmetic difference. Requirements/reference determine whether switching tabs preserves and reapplies the keyword, resets both input and filtering, or restores separate search state per tab. Do not choose whichever is easiest. Check switching back and clearing the keyword, including any relevant delayed response, with focused UI evidence. A still image of the input alone cannot verify search behavior.

**Other state contradictions.** A stale selected row after filtering, a page index beyond the new result range, a count that doesn't match the active filter, or a submit button with the wrong availability can reveal an implementation bug even without a clear reference discrepancy. The leader judges correctness from the agreed behavior and evidence; matching a broken reference is not enough. Keep repairs within authorized scope and record deliberate departures from the reference.

For enabled/disabled claims, inspect the DOM/control state and actual interaction. A lighter button may be hover, focus, opacity, theme or timing; pixels alone don't establish disabled behavior. Use a defined stable state and consistent focus/pointer positioning.

## Layout and evidence traps

- A suspected missing column may be outside the horizontal viewport. Inspect overflow and scroll at the agreed viewport before declaring it absent. For narrow numeric/index columns, assess content needs rather than equal-width defaults. Keep header/body alignment and usable content width together.
- Long dialogs need inspection below the fold: history, rejection reason, signature/review details and footer. An absent screenshot section doesn't prove the UI lacks it. Use separately labeled top/bottom pairs with identity evidence.
- A history view may intentionally show **only changed values**. If the reference repeats unchanged fields, investigate semantics instead of automatically adding them. Record a justified accepted difference.
- Default to reference wording: describe what the user did and its result, without rewriting copy to expose internal system stages. Make only the smallest factual correction needed for the actual flow; retain information needed for the next step, safety or completion. Status names separately follow effective state. Apply this rule to the whole class of strings.
- A missing rejection reason differs from an empty reason. Compare the intended placement and exact empty marker; do not invent fallback text or a redundant banner. Check consistency between list and detail.
- Extra required fields may be web-only additions or valid requirements. Check requirements/contracts; remove unsupported additions, not real constraints just because the demo validates loosely.
- Signature UI can be feasible before signing infrastructure exists. Implement UI steps against a contract-shaped mock, preserving pending/failure/success distinctions. Never represent mocked signing as a real transaction or make the product a permanent “simulation” screen without a requirement.
- Different data can be harmless fixtures or can change wrapping, actions, badges, totals and states. A fixture waiver names the values that may differ, not added or missing structure, unreadable layout or changed behavior; each use states what differs. Use equivalent data where it matters, including populated states for conditional content. A blanket “data differs” note doesn't settle it.
- Defects in both sides can be fixed in the implementation when authorized. Preserve the reference and document the improvement. Unrequested improvements remain suggestions.
- Isolated fixtures/mocks can simplify setup without adding product quick-fill controls. Preserve explicitly accepted conveniences, such as a temporary account switcher, within their agreed scope.

## Decision reuse and impact

Record each decision once: ID, expected behavior, evidence/source, authority, scope and affected components/cases. On each decision, enumerate the same pattern across the scope in source and the index, including semantic and shared-component usages, not only identical strings; apply uniformly or record exceptions. Record when a later decision supersedes an earlier exception so the old exception no longer applies. Reuse decisions while their sources and premises remain valid. A standing decision that explains many accepted differences is itself a finding to re-examine: verify its premise across materially different data shapes, and reopen dependent comparisons if it fails. Sampling checks the shared premise; it never replaces per-pair review. Workers execute standing decisions but escalate exceptions rather than silently extending them.

Use the Evidence contract's impact and invalidation procedure. A dialog fix can affect several steps; a status fix also changes background lists; a currency label can appear in other detail reports. Use focused interaction checks for state bugs and avoid unrelated API suites for presentation-only repairs.
