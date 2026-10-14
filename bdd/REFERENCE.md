# BDD Examples

Read when choosing scenario wording, boundary examples, structured inputs, or shared context/metadata. All policies and values below are illustrative, not default business requirements. Use only the forms that clarify the actual agreed contract; tags, tables, and Background are optional.

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
