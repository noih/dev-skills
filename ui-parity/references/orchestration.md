# Orchestration

## Roles and model profiles

Choose the column from the top-level leader's family, not a worker's family. These are requested model labels and reasoning levels; resolve actual provider identifiers through the host's inventory before dispatch.

| Responsibility | GPT family | Claude family |
| --- | --- | --- |
| Leader: standard, correctness, differences, decisions, final review | GPT 6 Astra / high | Fable 5.2 / high |
| Browser: operate, reproduce, assert state, capture, collect evidence | GPT 6.1 Terra / max | Sonnet 5.5 / medium |
| Repair: implement and verify confirmed fixes | GPT 6.1 Sol / high | Opus 5.5 / medium |

Explicit user configuration overrides this profile. If a general orchestration skill supplies different defaults, use this workflow's specialized profile for these three roles and include it in briefs. Don't modify global model preferences. Record requested and effective models/efforts; a requested name does not prove active configuration. If unavailable, disclose the exact mismatch and obtain a task-level replacement decision before dependent dispatch. Continue independent work. Never silently reduce effort, cross families, or invent an identifier.

Use the host's native thread/agent controls; the workflow must not depend on BB. In BB, follow its current orchestration guide/`lead` skill when available: inspect `bb status --json`, provider inventory and spawn help; set explicit provider/model/reasoning and `--permission-mode auto`. Otherwise use supported equivalents. Verify results. If parallelism is unavailable, run coherent groups sequentially without pretending multiple agents ran. If the required separate roles cannot run, disclose the limitation and settle a reduced-role plan rather than silently having the leader do everything.

The leader reads source and reviews evidence to adjudicate; it does not implement repairs or perform every capture. Browser workers can flag suspected differences, investigate state mismatches, and prepare report entries; they don't decide business correctness or final acceptance. Repair workers resolve implementation details under approved decisions; changed business meaning returns to the leader.

## Grouping to minimize total cost

Optimize context reuse, handoffs, repeated setup, waiting, and accumulated context together. Fewer workers is not always cheaper; more workers is not always faster.

- Keep cases together when they share roles, entities, setup, transitions, UI primitives or source entrypoints. A related create → review → history sequence benefits from continuity.
- Separate independent workflows when setup/concepts differ enough that explaining them to the existing worker costs as much as briefing a new one.
- Prefer the same browser worker for recapture while its context/resources remain useful. Don't keep it responsible indefinitely or assign it every case.
- Give a shared primitive one repair owner and distribute its impact list through the leader. Independent repair workers need disjoint file ownership or isolated changes with an integration owner. No permanent shared-component role by default.
- Report assembly belongs to an existing suitable worker; no mandatory fourth agent. The leader still reviews the assembled artifact.
- Parallel captures require independent authentication/storage **and** non-conflicting backend/fixture state. Separate tabs suffice only when independence is demonstrated. Serialize shared-record mutations, service restarts, and capture against changing builds.

For example, company list/detail/edit/history can be one related browser group; issuance/signing another; settlement/export another. These are examples, not fixed categories or sizes. A shared dialog fix can have one repair owner across groups. Capture on pinned builds, then refresh affected evidence after integration.

## Minimal dispatch brief

Send one concise current brief per group; update it when decisions or state change:

```text
Goal/group: case/step/state IDs; deliverable; repair/read-only scope
Authority: criteria/deviations; standing decisions; unresolved dependency
Role/effective profile: leader/browser/repair, model + effort
Locations: cwd, branch/build, reference, implementation, report/index
Setup: role/permissions, org/entity, entry path (see Evidence capture setup), fixture, prerequisites/reset, relevant source entrypoints
Behavior probes: bounded actions and expected outcomes derived from this group's controls/risks; relevant overflow, horizontal scroll and below-fold dialog contents
Ownership: files, data, sessions/services; forbidden shared mutations
Evidence: current/stale IDs and sides, unknown impact, pending capture checkpoint; exact next action
Escalation: facts + evidence + question to leader; no product decision
Cleanup: resource identities, retention/transfer owner and expiry
```

Browser workers read the evidence contract and assigned criteria; repair workers read its invalidation rules, confirmed findings, relevant criteria and source context. Don't make everyone reread the entire report/transcript. Return a compact delta: completed IDs, evidence paths, factual findings, changed files/impact, unresolved questions and cleanup/resources. Keep one shared decision/evidence index, not competing checklists.

## Communication and context

Batch ordinary findings at coherent checkpoints. Escalate early when a decision blocks dependent work or a shared defect would invalidate many captures. The leader can approve a standing rule such as “return-confirmation buttons in this component use the reference primary style”; the repair worker applies it consistently and reports scope. Avoid a round trip per instance without transferring acceptance authority.

For `evidence-needed`, the leader asks an existing appropriate browser/operator worker: **unknown question; exact role/data/context and action/state sequence; facts/screenshots needed; decision the answer enables**. Collect obtainable facts before guessing or escalating a product question. Return observations or the specific collection limit; refine requests when new information warrants it, not through unchanged repeated requests. No full rerun or additional permanent role is implied.

The leader schedules capture checkpoints by related workflow or shared-component repair batch using the Evidence contract. Repair workers supply impact; browser workers capture related groups. Focused functional verification continues during repairs; deduplicated report capture waits for that batch's stability, not the whole project. Diagnostic captures remain available for decisions.

Assess context at meaningful checkpoints. Around 50–60% usage is a cue to plan remaining work and handoff reserve; near 80%, avoid starting a broad unrelated group. These are planning cues, not reasons to interrupt transactions or force replacement. Repeated compaction, lost state, or excessive rereading favors a new worker. A coherent small remaining task may favor finishing with the current one.

Maintain a short current-state handoff: decisions, locations/revisions and dirty ownership, done/pending IDs, exact next action, valid/stale evidence, and active resources/access. Transfer ownership explicitly; tool-local handles may not transfer. Relays don't reset acceptance scope, require full recapture, or justify rebuilding. Apply the same discipline to the leader; its successor inherits review coverage and unresolved decisions, not thousands of images loaded again.

Reconcile worker status and resource ownership at closeout. Close only owned sessions and verify owned browser/daemon processes, not just an empty tool registry. Retained services need an accepted owner, reason and expiry. No global browser cleanup or reference reset commands.
