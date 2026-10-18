# Issue status reminder discussion

## Scope and evidence

- Reviewed commit: `7361c338ab9e173351873e289516cae73146044f`.
- User intent: remind agents to assess whether a linked issue needs updating according to the actual situation; do not require a state transition at every checkpoint.
- One static review round covering `issue-tracking`, `project-context`, `sdd`, and `lead`, followed by a focused ordering fix. Provisional discussion budget: 5–10 minutes; no runtime tests or live tracker writes.
- Reviewers: Fable 5.1 / high (`thr_edse9f49cm`), Opus 5.5 / high (`thr_ui7t436vsv`), GPT-6.1-Sol / high (`thr_4jrb7vqm7j`). All three reviewed the patch; the leader relayed Opus's ordering concern to Fable and Sol for assessment.

## Round 1: findings and adjudication

All three reviewers agreed that the patch preserves situational judgment: accurate states may remain unchanged, updates stay within existing authority, unavailable synchronization is disclosed, and phase completion does not automatically complete the issue. They found no mandatory state transition or additional acceptance gate.

| Finding | Decision |
| --- | --- |
| `lead` checked linked issue status after deleting workers and committing. A designated tracking owner could already have been deleted, and a resulting local handoff update would miss the commit. All three reviewers identified this execution gap. | Accept. Sol moved the check before worker cleanup and commit, with the leader handling it when the designated owner is unavailable. |
| Add completion/handoff terms to the `project-context` description. | Optional discovery improvement; not required to correct this patch. Keep the existing description. |
| Define every source that makes work issue-linked. | Do not add a taxonomy. Existing destination resolution and project conventions remain authoritative; incidental references should not select a tracker automatically. |
| Consolidate repeated reminders, expand authority wording, or update the README table wording. | Optional maintenance suggestions. The entrypoints serve different workflows, and the current text preserves authorization and does not contradict the README. No additional changes. |
| Installed copies lack the new reminders; `issue-tracking` is absent from the inspected global skill directories. | Distribution limitation, not a reason to add workflow gates. Updating repository files does not establish that other sessions have loaded them. Installation changes remain outside this discussion. |

No product decision is frozen pending user input. No implementation work was dispatched beyond the accepted ordering fix.

## Focused fix verification

- Sol changed only `lead/SKILL.md`: the assessment is now Closeout step 3, before cleanup, worker deletion, and local commit; the subsequent steps were renumbered.
- The leader inspected the resulting diff and ordering. Situational judgment, existing authority, accurate unchanged states, pending-update disclosure, and phase-versus-issue scope remain intact.
- `git diff --check` passed. Reused the original Ruby YAML/name/description checks; the follow-up changes no frontmatter.
- The original Python validator remains unavailable with `ModuleNotFoundError: No module named 'yaml'`. No dependencies were installed for this discussion.
- This is static instruction review, not evidence that agents reliably remember the check at runtime or that any external tracker integration works.

## Resource handoff

Reviewers reported no temporary resources, services, or installed-copy changes. The leader owns this retained discussion record. All three workers were confirmed idle, then deleted after their findings were preserved here; the child-thread list was verified empty.
