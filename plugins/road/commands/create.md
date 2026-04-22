---
description: Create a new roadmap
---

Invoke the `road` skill to create a new roadmap.

Input: $ARGUMENTS

If the input looks like a kebab-case slug (e.g. `integration`), create `roadmaps/<slug>.md` with the skeleton defined in the skill. If no slug is given, ask the user what to name it. Self-bootstrap `roadmaps/` if missing.
