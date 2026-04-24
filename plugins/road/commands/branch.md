---
description: Branch an existing roadmap into a new one that diverges at a specific WI — one-way, no merge
---

Invoke the `road` skill's Branch intent.

Input: $ARGUMENTS — expected form `<src-slug> <dst-slug> [--at WI-XX]`

Behavior:

1. Verify `roadmaps/<src-slug>.md` exists. Fail if not.
2. Resolve the anchor WI:
   - If `--at WI-XX` is given, use it.
   - Otherwise use the src roadmap's last `[v] Done` WI.
   - If src has no Done WI and `--at` is omitted, ask the user which WI to branch at.
3. Verify the anchor WI exists in src. Fail if not.
4. Create `roadmaps/<dst-slug>.md` with the branched skeleton defined in the skill:
   - `# <Dst Title> Roadmap` (title-case of the slug; user may override)
   - `**Branched from:** <src-slug> @ WI-XX`
   - Standard Legend + empty `## Work Items`
5. Do not copy any WI from src. dst starts empty; shared history is referenced via the header.
6. dst's WI numbering starts fresh at WI-01 in its own namespace.

Branches are one-way. No merge command exists. If the user later wants to reconcile two roadmaps, they copy WI content between files manually.
