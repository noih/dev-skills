---
description: Show roadmap status — all roadmaps if no arg, specific roadmap if slug given; pass --full to stitch branched ancestry
---

Invoke the `roadmap` skill's Show / summarize intent.

Scope: $ARGUMENTS

- If `$ARGUMENTS` is empty: scan `roadmaps/*.md` (excluding `archived/`) and output a summary table of all active roadmaps with Status + Progress
- If `$ARGUMENTS` is a roadmap slug: output that roadmap's WI list with statuses, highlighting the first `[ ]` Pending WI as "currently in progress". If the roadmap has a `Branched from` header, note the source + anchor at the top
- If `$ARGUMENTS` contains `--full` alongside a slug: additionally stitch in the src roadmap's WI-01 through the anchor as read-only "inherited" history before the branched roadmap's own WI

Follow the skill's computation rules (Progress format `{done}/{total}[, {skipped} skipped]`, roadmap aggregate `[v]` when all WI are Done/Skipped). Branched roadmaps compute progress over their own WI only.
