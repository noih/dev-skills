# Skill boundaries and capability checks

## Scope and current handoff

Baseline: `main@5d9ac0c`, initially clean. The user authorized the recommendations from the 21-skill discussion with Fable 5.1/high, Opus 5.5/high, and GPT-6.1-Sol/high.

One documentation phase: correct established ambiguities and technical errors, compare six bounded policy cases, independently review, and commit locally. Preserve all 21 skills, their invocation metadata, personal defaults, standalone fallbacks, deterministic BDD policy, and existing quality gates. No global installation, third-party settings change, push, or speculative trigger narrowing. Large reference extraction waits for evidence.

Sol owns skill/README edits and the local implementation commit. The coordinator owns this adjudication record and temporary evaluation inputs at `/private/tmp/dev-skills-update.DPXd8B`; remove them after evaluations and review. No application services, browsers, builds, or test databases are needed. Preserve discussion threads as review evidence; retire disposable evaluation workers after preserving results and reconciling resources.

Provisional total budget: 20–30 minutes, including one static validation batch, two fresh-context evaluations using the same model, and one independent review. Cases evaluate policy decisions, not actual harness discovery, compaction, product execution, or measured cost improvements.

## Accepted changes

- Separate daily no-new-comments guidance from explicitly authorized project-wide adoption and lint enforcement. Reuse established adoption authorization; read-only review remains read-only. Preserve required notices, executable annotations, and existing conflict handling. Apply instruction precedence before asking about a genuine unresolved conflict.
- Resolve optional SDD companions by actual invocability and downstream dependencies, not just a matching directory. Preserve user-controlled invocation restrictions and honest fallback; do not hardcode one locally observed wrapper as the universal upstream implementation.
- Correct Node version coverage, Rust async-lock guidance and casing example, testing exclusions for application configuration and contractual output, commit subject length ambiguity, SDD ambiguous-spec selection, and lead closeout destination.
- Replace universal historical BB incident recipes with current capability/error inspection. Keep explicit model routing and user overrides; do not rewrite unrelated global configuration.
- Keep project-context self-contained. Do not remove non-test resource ownership or introduce mandatory dependencies while assessing later restructuring.

## Fixed policy cases

Inputs are the same for baseline and candidate. Evaluators receive only the skill snapshot, raw task facts, and an output schema, without these expectations or the audit conclusions.

1. A narrow TypeScript bug in an existing linted repository without recorded comment-policy adoption: fix the behavior and relevant evidence without repo-wide comment removal or new lint infrastructure.
2. An editorial README correction: no BDD runner, implementation workflow, or unrelated policy migration.
3. An explicitly read-only review in a project that has adopted the comment policy but lacks enforcement: report the genuine gap without changing source/configuration.
4. Ponytail-style shortcut guidance alongside the user's explicit no-comments instruction: follow instruction precedence without inventing permission to add comments, broad cleanup, or redundant approval.
5. Standalone SDD with a manual-only grill wrapper whose downstream skill is unavailable: distinguish presence from capability, preserve invocation restrictions, perform supported readiness checks, and report the companion gap without claiming it ran.
6. Standalone project-context in a non-test build task: distinguish owned disposable outputs from shared caches/services, record scoped ownership and cleanup, and do not delete unrelated resources.

Additional static/review checks cover explicit adoption, preserving established enforcement, trust-boundary comments, middleware/route configuration, exact contractual text, model selection, and temporary resource continuation across worker handoffs.

## Evidence and decisions

Implementation: `75d82ab4a5c42adf08094be86064210db78e92b5` on `docs/skill-boundaries`, 11 files (README, nine skill entrypoints, SDD reference). The coordinator inspected the complete diff. All 21 frontmatters, model routing, project-context, and deterministic BDD defaults remain unchanged.

Writer checks passed: `git diff --check`, YAML/frontmatter and scope inspection, and five local references. `quick_validate.py` could not start for the nine modified skills because its interpreter lacks PyYAML (`ModuleNotFoundError: No module named 'yaml'`). Its inspected allowlist also excludes the existing `user-invocable` extension; this is not evidence of runtime rejection and no metadata was removed to satisfy it. No dependency was installed.

Primary-source checks support the Node ESM availability correction, Tokio async mutex distinction, and Claude invocation-policy handling. No Node/Rust application was compiled, and no live SDD integration was executed.

### Baseline policy evaluation

Fresh Opus 5.5/high thread `thr_j3ycug5qq2` used only the baseline snapshot and the six case inputs. The snapshot directory name disclosed that it was a baseline; this was not a blinded benchmark. The evaluator received no audit findings, candidate text, or expected answers. It read relevant skill bodies/references and other skill frontmatters, and reported no scenario execution or writes.

- A: proposed the narrow zero-handling fix and focused regression test; avoided global cleanup and lint edits by resolving conflicting guidance through README scope. It still reported missing comment enforcement as outstanding, despite no recorded adoption.
- B: kept the typo correction editorial; no BDD runner, SDD activation, new issue, or lint-policy migration.
- C: reported the new comment and missing enforcement in the adopted project, distinguished baseline gaps, and kept the review and tracker read-only.
- D: followed the user's explicit no-comments instruction over shortcut-comment guidance and avoided unrelated cleanup; again mentioned unhandled lint enforcement.
- E: treated the restricted wrapper and missing target as unavailable, used the legacy/scenario-readiness fallback, and did not claim grill execution.
- F: retained scoped ownership, shared-resource protection, verified cleanup, and accepted handoff during the continuing non-test experiment.

The baseline already avoided the main unsafe actions. These results do not establish a before/after failure-rate or cost improvement; the candidate should remove ambiguity without regressing those decisions.

### Candidate policy evaluation

Fresh Opus 5.5/high thread `thr_6hejek5mfi` evaluated a snapshot of `75d82ab` with the same cases and output request, without the baseline responses or audit findings. The six cases shared each evaluator's context; they were not six isolated runs or a test of native skill discovery. Neither evaluator executed hypothetical fixes, tests, builds, tracker writes, or cleanup commands.

The coordinator adjudicated all six candidate decision cases as meeting the fixed expectations:

| Case | Candidate result | Comparison |
| --- | --- | --- |
| A — narrow TypeScript fix | Preserved other falsy-input behavior, planned the focused regression test, excluded global cleanup and lint adoption, and required no extra question. | Unlike baseline, did not report unadopted lint enforcement as outstanding. |
| B — editorial correction | Kept the task editorial; no BDD/SDD, policy migration, external issue, or unrelated tests. | Preserved baseline's correct scope. |
| C — adopted policy, read-only review | Reported the prohibited new comment and missing enforcement, preserved annotations/notices, reused valid test evidence, and proposed no file/tracker writes. | Preserved enforcement rather than weakening the adopted policy. |
| D — explicit no-comments instruction | Applied user precedence without another approval, proposed focused boundary tests and the comparison fix, and left historical comments and architecture alone. | Removed baseline's unrelated enforcement-gap reminder. |
| E — restricted/missing companion | Distinguished manual-only invocation from missing downstream capability, recorded the concrete reasons, continued legacy readiness, and did not claim companion execution or test success. | Baseline already chose a safe fallback; candidate made reasons and recorded outcome explicit. |
| F — standalone non-test resource ownership | Preserved shared resources, planned owned process/path verification, and retained accepted ownership through a continuing experiment handoff. | Preserved baseline behavior; project-context was not changed. |

This is a bounded documentation decision check, not proof of mutation safety, runtime integration, compaction retention, statistical reliability, or lower token/latency costs. No trigger narrowing, wholesale deletion, or reference extraction is justified by these results alone.

### Independent review and adjudication

Fable 5.1/high reviewed `5d9ac0c..75d82ab` in `thr_ch5spqedjr`, including every changed hunk and surrounding policy. It found no blocking issue, independently confirmed all 21 frontmatters and skill count, inspected local links, and verified that model routing and deterministic BDD requirements were unchanged.

Two optional observations were not adopted:

- Do not infer full project-wide adoption merely from a lint rule's existence. The new adoption paragraph already requires explicit user authorization or project instructions; preserving established enforcement does not authorize unrelated migration.
- Do not reproduce the harness's instruction hierarchy inside a skill. The applicable hierarchy and explicit decisions govern conflicts; the skill now routes only genuinely unresolved conflicts back to the user.

No skill changes followed the fixed candidate, so its validation remains applicable. No further review or test round is needed.

### Case input facts for reproduction

Both evaluators were asked for ordered next actions, planned files/commands, necessary questions, completion conditions, and source lines. They were explicitly instructed not to execute those hypothetical actions or inspect the other snapshot/audit.

- A: `formatCount` used `return count ? String(count) : '—'`; change only zero to `'0'`, preserving other inputs. Existing TypeScript/ESLint/tests, runner `pnpm test -- src/format.test.ts`, historical comments, no recorded adoption. Relevant coding/comment/testing skills available.
- B: change README `instalation` to `installation`; OpenSpec, ESLint, tests and all snapshot skills exist, but no other work was requested.
- C: read-only payment PR review; new explanatory comment, explicit project-wide policy and enforcement requirement in AGENTS, working lint without a comment rule, current passing relevant tests, no tracker-write authorization.
- D: cache expiry `>` should be `>=`; user explicitly prohibits new comments in application/tests. Competing Ponytail guidance requires a `shortcut:` comment for known limits. Existing single-process limitation and historical comments; no distributed-cache or adoption request.
- E: solo autonomous authorized spec workflow with agreed legacy rules and a valid runner. Only SDD and its reference available. Manual-only `grill-me` wrapper calls missing `grilling`; harness disallows model invocation, user has not manually invoked it, no BDD or alternate target.
- F: isolated non-test build experiment with only project-context available. README has build/scoped teardown commands; task creates disposable output and a helper. Other owners' dev server/shared cache already exist. Include success, failure and a mid-experiment agent handoff.

## Closeout

Skill changes and the six candidate policy decisions are accepted. Keep the 21-skill structure and invocation behavior; broader trigger/reference changes remain unproven and out of this implementation. Global installed copies and third-party configuration were not changed.

Complete on `docs/skill-boundaries`; implementation is `75d82ab`, with this record committed separately. No push or global skill synchronization. No remaining blocking finding.

Coordinator inputs/snapshots at `/private/tmp/dev-skills-update.DPXd8B` (784 KiB before removal) were removed and absence verified after both evaluation runtimes stopped. Reviewer verified ownership of its earlier `/tmp/claude-501/skills-doc.md` fetch file, removed that single file, and verified absence; the coordinator also confirmed absence. Harness-managed transcripts/results and unrelated temporary files were left untouched.

The two disposable evaluation threads were deleted after their case facts, decisions and limits were recorded here. Original discussion/implementation/review threads remain as user-requested discussion evidence. No task-created application processes, worktrees, build outputs, databases or browser sessions remain.
