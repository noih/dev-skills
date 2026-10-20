---
name: comments
description: "Use when: writing or reviewing source code for the no-comments rule, removing existing comments, or maintaining JSDoc consumed by an OpenAPI generator."
user-invocable: false
---

# No Comments in Source Code

Do not add comments to application or test source code except for the OpenAPI JSDoc below. This is a prohibition, not a preference for fewer comments. An existing comment style does not authorize new comments.

## Scope

- Prohibit inline and block comments, file headers, section dividers, TODO/FIXME notes, commented-out code, and documentation comments or docstrings, except for the OpenAPI exception below.
- Deployment files, operational scripts, configuration, and standalone documentation are outside this prohibition. Classify by purpose, not file extension; application code does not become exempt by calling it a script.
- Express behavior through clear names, straightforward structure, types, and meaningful tests. Do not add abstractions or split functions solely to replace comments.
- Most code needs no explanation. Record only exceptionally important constraints or decisions worth preserving long-term in the existing spec. Do not document every implementation detail or move every removed comment into the spec. Complexity or a useful explanation of "why" is not an exception.

## Existing Comments

- **Daily work:** follow the no-new-comments rule. Loading this skill or fixing a bug is not project-wide adoption; keep existing-comment cleanup within authorized task scope. An unadopted project does not acquire a new lint gate merely from skill loading.
- **Authorized adoption:** explicit user authorization or project instructions must enable the project-wide policy. Reuse existing authority without asking again. Remove all prohibited application/test comments across the project, not only changed files, without a legacy baseline; preserve OpenAPI JSDoc and move any executable coverage into ordinary tests. Removal alone does not require a spec entry; retain only exceptionally important long-term constraints/decisions there.
- **Already adopted:** preserve and maintain the project's cleanup/enforcement requirements; routine work does not reset or weaken them. Repair enforcement within authorized write scope. Read-only work reports violations and missing enforcement without edits; it neither adopts the policy nor invents a gate.

## OpenAPI Generation Exception

Allow JSDoc that the project's configured tool consumes to generate OpenAPI JSON. These blocks are API contract inputs: preserve and update endpoint declarations, summaries, tags, parameters, response definitions, typedefs, properties, and their descriptions as the API changes.

Confirm the generator actually consumes the annotations; JSDoc syntax alone does not qualify. Keep the blocks focused on the generated API contract, without unrelated implementation explanations. Do not introduce a generator merely to justify comments. When changing these annotations, use the project's generation or validation command to check the affected contract.

## Conflicts

For both existing comments and new code, inspect tool directives, required license notices, and comments/docstrings with runtime or generated-output effects before changing them. Prefer an equivalent implementation or external configuration that does not require source comments. If no equivalent is available or a notice must remain, preserve the affected material while resolving the conflict below; do not blindly delete it or invent another exception. Do not use inline suppression directives to bypass this policy.

Resolve conflicts with project requirements or other skills (such as a required `shortcut:` comment) using the instruction hierarchy and applicable explicit user decisions. Follow settled precedence without asking for confirmation again. If a concrete conflict remains unresolved, report it for user resolution and hold only the affected work. Existing comment usage alone is not permission.

## Lint Enforcement

During authorized adoption in a project with a lint tool (ESLint, Oxlint, or another language's tooling), add or configure a rule rejecting prohibited source comments as errors in the existing lint command and any CI lint gate. Maintain that enforcement in already-adopted projects, repairing gaps within authorized write scope or reporting them when writes are outside scope. Written guidance alone is insufficient for adoption. Scope the rule to application and test source, excluding deployment files, operational scripts, configuration, and standalone documentation.

Use a supported rule or installed plugin when available; otherwise implement the smallest project-local rule using the tool's supported extension mechanism. Inspect the installed version and configuration before choosing a rule; do not invent rule names or assume different linters support the same plugins. If the tool cannot enforce the policy, report the concrete limitation and remaining enforcement gap instead of claiming completion. Do not introduce a linter solely for projects without one.

Configure the OpenAPI exception from the actual generator inputs and annotation format. Allow only the JSDoc blocks it consumes, including their contract descriptions; other comments in the same files must still fail. Do not exempt an entire API directory, all JSDoc, or every block containing an OpenAPI-looking tag.

Use syntax-aware inspection so strings, URLs, regular expressions, and comment-like text in data do not trigger the rule. Do not enable automatic comment deletion: annotations and existing comments can affect behavior or required outputs.

Verify the configured rule with small passing and failing cases: ordinary comments fail, consumed OpenAPI JSDoc passes, unrelated JSDoc and explanatory comments beside valid annotations fail, comment-like string content passes, and excluded operational files remain unaffected. Check documentation comments/docstrings for the applicable language. Run the affected lint command and, when the OpenAPI exception is configured, the project's OpenAPI generation or validation command. Resolve existing prohibited comments as part of adoption. Report unresolved conflicts and the affected lint gate as pending, not passing; do not silently weaken the rule.
