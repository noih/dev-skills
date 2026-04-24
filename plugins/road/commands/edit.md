---
description: Open a dialog to modify a roadmap — add/skip work items, edit fields, adjust phases, or any other changes
---

Invoke the `road` skill in edit mode for conversational modifications.

Target: $ARGUMENTS (optional roadmap slug; if omitted, ask which roadmap)

Open a dialog. Ask the user what they want to do, then apply the skill's rules:

- **Add a work item** — auto-assign next ID, Status `[ ] Pending`, elicit Title / Delivers / Spec
- **Skip a work item** — set `[~] Skipped`, strip to stub, ask for reason → Notes
- **Edit a field** — change Delivers / Spec / Phase / Notes / Title of a specific WI
- **Change status manually** — override sync (mark Done / revert Done / etc.); confirm and write reason to Notes for any reversal
- **Adjust Phase / Overview** — add a phase row, change a WI's phase, etc.

All changes follow the skill's schema and safety rules (required fields, Skipped stub, Done-revert confirmation, etc.).
