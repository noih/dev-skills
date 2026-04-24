---
description: Manually re-sync all roadmap WI statuses with their spec tools (fallback — auto-sync usually happens on spec changes)
---

Invoke the `roadmap` skill's Sync intent (fallback manual trigger).

Optional scope: $ARGUMENTS (specific roadmap slug, or empty for all active roadmaps)

Scan every WI's `Spec:` field, apply the skill's sync rules (promote-only, respect manual Skipped, don't auto-demote or auto-skip), and report:

- Promotions made (Pending → Done based on archive existence)
- Warnings (unresolved Spec paths, dangling `Branched from` references, format inconsistencies)
- WI that need user decision (e.g. Spec missing but not sure if should be Skipped)

Use this when:

- First adoption (bring an existing roadmap in line with current spec state)
- After batch operations done outside the AI (manual file moves, external tool archive)
- Periodic audit

Under normal interactive use, the skill auto-syncs when the user archives / creates / modifies specs, so explicit sync is rarely needed.
