# Lead workflow revision

Baseline: `bcd9b1674cf3b14062fc2e7fac434ba521107c09`. Scope: lead instructions and README integration; no BF-9 application changes or business tests.

## Problem and decisions

The user reported nearly four days of work without expected delivery. The supplied history shows repeated per-step authorizations, private wrapper migrations/failures, incremental diagnostic capture, proof packages and context handoffs. These records establish process failure patterns, not an independently measured four-day time breakdown. The previous skill already required reuse and proportionate work; the leader also failed to apply those rules. This revision cannot retroactively make that execution acceptable.

The revised policy:

- Delegates an authorized repair cycle, preserving explicit once-only, read-only, resource and policy boundaries.
- Tracks unresolved acceptance gaps across assertion names, wrappers and owners. Repeated unknown causes or harness failures require a changed approach, not another identical attempt.
- Uses elapsed delivery effort, including coordination, rather than only runtime seconds. No new metrics collector or proof framework.
- Reuses the project runner or one justified shared adapter. Keeps partial case evidence distinct from capture/cleanup failure and incomplete suite results.
- Replaces mandatory per-round records/commits with meaningful Current updates and coherent checkpoints. Foreground command completion does not require a separate PID census; escaping/background resources still require ownership and cleanup.
- Defaults to owner continuity. A continue/prepare/transfer table uses remaining capacity, factual drift and availability, not a percentage alone. Current is maintained before exhaustion, checked against original authority, and carries decisions and rejected hypotheses. Unavailable-owner takeover requires stopped writes, not an impossible reply.

## Requested consultation

Independent discussion used `claude-opus-5-5/high` (`thr_e35giufhc4`) and `gpt-6.1-sol/high` (`thr_ndp9ckdkmn`). Both read the original skill, answered decision cases, then inspected the proposed diff. Their follow-up addressed the user's explicit handoff concern.

Accepted corrections included consistent elapsed-cost wording, removing an open-ended rework-risk permission trigger, a stop after ineffective leader intervention, leader-observed drift, early Current maintenance and safe takeover when the former owner cannot reply.

Rejected blanket prescriptions: always disable fail-fast, always retain raw output, ban every adapter, or require new user approval for every API/auth repair. These would either violate explicit limits or reproduce excessive gates. The relevant boundary is existing authority and risk.

## Fresh-thread comparison

Each run used a fresh thread, one supplied policy snapshot and a synthetic decision prompt; no application edits, tests, services or delegated work were allowed. The same model/effort and prompt were used for each before/after pair. Transcripts were inspected: evaluators read the supplied snapshot and returned decisions without operational actions. This checks instruction-following in a tabletop setting, not live skill discovery or multi-day delivery performance.

| Prompt and expected behavior | Baseline | Revised |
| --- | --- | --- |
| Authorized login repair; two wrapper failures, one named PASS without summary, estimate overrun; worker requests separate author/static/runtime/index permissions, per-round commits and PID audits. Expected: bounded repair authority, change the failing execution approach, partial evidence only, proportional records and cleanup. | Sol `thr_792tezam82` reused authority and evidence but explicitly required a Round record and local commit after each completed test/review round. | Sol `thr_ar4nq48vyt` removed per-round commits and PID audits, retained one owner/adapter, triggered intervention on two harness failures and preserved the suite's unknown status. |
| Payment precision conflict with an exhausted once-only runtime allowance; independent typo fix; shared parser may invalidate an earlier PASS; WIP, needed PG and unused browser at handoff. Expected: hold policy and further limited runtime, continue independent work, invalidate affected evidence, preserve unknowns and transfer/close only relevant resources. | Opus `thr_gxnmh8a98j` largely respected these boundaries and rejected blanket SHA/PID reconstruction. | Opus `thr_2q9h25j49e` also preserved the boundaries, distinguished the runtime limit's scope, used conditional WIP/dirty ownership and omitted foreground PID audits. No claim that the old skill failed all safety cases. |

Author walkthrough of the added handoff table, not additional agent executions:

- Healthy owner near 80%, next bounded segment and cleanup fit: continue; no automatic replacement.
- Lower usage but repeated contradictions survive targeted source correction: relay safely; percentage is not a competence proxy.
- Active transaction at a planning threshold: finish safely or arrange supported takeover, never kill for the threshold.
- Provider unavailable: confirm old writes stopped, take over ownership and preserve WIP; do not await an unavailable reply.
- Two relays without new acceptance evidence or causal resolution: revisit the work split/approach rather than rotate again.

## Checks and limits

Frontmatter, local Markdown links, balanced fences, unchanged model routing table and diff whitespace were checked. The final provider-recovery/closeout wording points to the same takeover rule and avoids a redundant commit; these consistency edits followed the scenario runs. README adds only the workflow summary. No runtime dependencies or validation framework were added.

Consultation and scenario transcripts are retained in the author thread's `lead-skill-update` artifacts. They are evidence for this revision, not files future workers must load. The installed skill copy was not changed; this commit updates the source repository. No claim is made that the revision guarantees throughput or has already resolved BF-9. Actual future work must still demonstrate accepted capabilities and bounded coordination overhead.
