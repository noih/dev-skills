# BDD Examples

Read for deterministic contract/adoption boundaries or scenario wording, boundary examples, structured inputs, and shared context/metadata. Business policies and values below are illustrative, not default requirements; tags, tables, and Background are optional. Gherkin snippets illustrate wording and may need a Feature wrapper to form a complete `.feature` file.

## Deterministic contract and adoption

| Artifact | Role |
| --- | --- |
| OpenSpec main specs | Current requirement intent/index in the spec tool's supported format. |
| Active change/delta | Proposed behavior evolution and recorded decisions; read with main intent until sync/archive. Proposals alone are not agreed acceptance. |
| Archives | Historical requirements/decisions/evidence, not a second current scenario authority. |
| `.feature` | Agreed acceptance scenarios for the current branch/commit in the deterministic workflow. |
| Bindings and runner/generator configuration | Versioned executable setup/actions/assertions and deterministic tool inputs. |
| Generated tests, if used | Derived output; regenerate from source, never hand-edit. |

Link requirements and features using existing conventions; no mandatory one-to-one names or new registry. A direct Gherkin runner needs no generated test file. Either path must consume versioned feature/binding source without a model interpreting/translating scenarios or supplying bindings during generation/execution. This governs scenario-to-test plumbing; model calls in an AI product under test remain allowed. AI authoring before source review is allowed; AI-written standalone tests without this connection do not establish deterministic feature execution. Before running generated tests, regenerate from current inputs or verify that committed output matches them; read-only stale output is not current evidence.

For example, a feature branch changes the maximum reservation duration from 120 to 180 minutes. Record the delta/decision, update the branch's feature boundaries and affected bindings/product together, and retain the archive's 120-minute history. Before sync, main intent plus the agreed active delta explains the branch's intended behavior. After a merge or rollback, reconcile these artifacts; do not select the globally newest timestamp or overwrite history to resolve disagreement.

**OpenSpec integration is a compatibility boundary.** Preserve the installed version's required Markdown structure. A scenario containing only a feature link, or a custom artifact schema, does not by itself prove native validator, sync/merge, or archive compatibility. Check those operations against the installed version before adopting such a representation. Record unresolved integration honestly; this skill does not supply a converter or claim working support. Existing historical snapshots may remain; new scenario snapshots must be mechanical, tied to the source revision, and labelled historical rather than maintained as current copies. Sync/archive occurs only within the task's authorization; report pending lifecycle work without claiming completion.

**Adoption is scoped.** Legacy projects may keep their existing spec/test conventions until migration is in scope. When deterministic acceptance is requested but no feature runner/binding path exists, ordinary passing tests and Markdown scenarios remain useful evidence but do not satisfy that gate. During authorized adoption, draft `.feature` source and continue independent discovery while reporting the missing connection. Reuse compatible project tooling and tests; do not demand wholesale migration, install tools for discussion/editorial work, or ask again for already-authorized setup. Unresolved material choices follow the existing authority rules.

**Acceptance checks identities and assertions.** Enumerate the required scenarios and each Scenario Outline's Examples rows from current source, then reconcile them with actual runner results and binding assertions. Existing runner identities or feature path/scenario/row references are sufficient; do not invent an ID service. Counts alone can conceal omitted cases replaced by unrelated ones. Inspect every required outcome, including unchanged state and prohibited side effects. Required skipped, pending, filtered/excluded, undefined, ambiguous, or otherwise unexecuted cases are gaps; a successful exit or unrelated green suite cannot pass them. Preserve retry/failure evidence and explicit waivers under existing testing rules. Keep additional security and implementation-risk tests.

Evidence inputs include feature/binding content, tool/dependency versions, generator/runner configuration and filters, fixtures/environment, product revision, and requirement/change references. Reuse results only while their relevant inputs remain valid; no fresh run is needed solely for a handoff or commit with unchanged content.

## Business event versus UI choreography

For an illustrative rule that only members may reserve a room:

```gherkin
# Poor business wording: describes one screen implementation, not the rule.
Scenario: Reserve a room
  Given I click the Members tab
  When I click #room-7 and then the blue Reserve button
  Then I see a green toast

# Better: a test can prove these outcomes at the appropriate boundary.
Scenario: A member reserves an available room
  Given Mira is a member and room 7 is available for 15:00–16:00
  When Mira reserves room 7 for 15:00–16:00
  Then that interval is reserved for Mira
  And no additional reservation is created for any other room or interval
```

Selectors and navigation belong in test implementation unless the UI interaction is itself the specified contract. A journey may include several meaningful events when their sequence is what needs proof; do not split it into order-dependent scenarios merely to keep one When.

## Boundary Scenario Outline

Illustrative agreed rule: reservations last at least 30 and at most 120 whole minutes, inclusive. Boundary rows distinguish inclusion from an off-by-one error; they are not a required number of examples.

```gherkin
Scenario Outline: Enforce reservation duration limits
  Given Mira is a member and room 7 is available for the requested interval
  When Mira requests a reservation lasting <minutes> minutes
  Then the request is <result>
  And the number of reservations created is <created>

  Examples:
    | minutes | result   | created |
    | 29      | rejected | 0       |
    | 30      | accepted | 1       |
    | 120     | accepted | 1       |
    | 121     | rejected | 0       |
```

Each row has fresh initial context. Do not let an accepted row consume availability for a later row.

## Structured inputs

A data table supplies one scenario's structured input; it is not a set of independently executed Examples rows. Illustrative agreed rule: a reservation import containing overlapping intervals is rejected atomically.

```gherkin
Scenario: Reject an overlapping reservation import
  Given Mira is a member and room 7 has no reservations
  When Mira imports these reservations for room 7 on 2026-11-03
    | start | end   |
    | 09:00 | 10:00 |
    | 09:30 | 10:30 |
  Then the import is rejected because the intervals overlap
  And room 7 still has no reservations
```

Use a Doc String when exact multiline content is relevant. Technical detail is appropriate when the technical contract itself is being specified. Illustrative agreed API contract: an unknown room returns HTTP 404 with this JSON body and creates no reservation.

```gherkin
Scenario: Report an unknown room
  Given an authenticated member and no room with ID 99
  When the member submits a valid reservation request for room 99
  Then the response status is 404
  And the JSON response body is
    """json
    {"code": "ROOM_NOT_FOUND", "roomId": 99}
    """
  And no reservation is created
```

Assert JSON content semantically unless formatting is also part of the agreed contract.

## Background and metadata

Illustrative agreed rule: only a reservation's owner may cancel it. Share short, relevant context; keep scenario-specific state visible. The optional tag below is useful only if the project's existing tooling/report uses it; a stable scenario name can provide traceability on its own.

```gherkin
@reservation-ownership
Feature: Cancel a reservation
  Background:
    Given Mira and Sol are members
    And reservation R7 belongs to Mira and is active

  Scenario: The owner cancels
    When Mira cancels reservation R7
    Then reservation R7 is cancelled

  Scenario: Another member cannot cancel
    When Sol cancels reservation R7
    Then cancellation is rejected
    And reservation R7 remains active and belongs to Mira
```

Background describes fresh initial context for both scenarios. The second scenario does not inherit the first scenario's cancelled reservation. Keep agreement status, decision references, and spec version in existing spec/task metadata when useful; a tag never turns proposed policy into agreed acceptance.
