---
description: Show roadmap status — all roadmaps if no arg, specific roadmap if slug given
---

Invoke the `roadmap` skill's Show / summarize intent.

Scope: $ARGUMENTS

- If `$ARGUMENTS` is empty: scan `roadmaps/*.md` (excluding `archived/`) and output a summary table of all active roadmaps with Status + Progress
- If `$ARGUMENTS` is a roadmap slug: output that roadmap's WI list with statuses, highlighting the first `[ ]` Pending WI as "currently in progress"

Follow the skill's computation rules (Progress format `{done}/{total}[, {skipped} skipped]`, roadmap aggregate `[v]` when all WI are Done/Skipped).
