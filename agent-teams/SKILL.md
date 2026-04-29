---
name: agent-teams
description: Team workflow rules for creating and coordinating Claude agent teams. Use when the user explicitly asks to form a team, says "用 XXX 組團隊", requests fullstack/frontend/backend team execution, or needs multi-agent orchestration with lead, architect, developers, QA, and reviewer.
user-invocable: false
---

# Agent Teams

Use this skill only when the user explicitly asks for an agent team or multi-agent workflow. For direct single-agent work, use Standalone Mode from `agent-autonomy` instead.

When the user says "用 XXX 組團隊", use the team definitions below and create an agent team. Each teammate's instructions are in `~/.claude/agents/`.

Available teams:

- `fullstack` — Architect + React frontend + Backend (nodejs/rust) + QA + Reviewer
- `frontend` — Architect + React frontend + QA + Reviewer
- `backend` — Architect + Backend (nodejs/rust) + QA + Reviewer

## Context Budget Rules

- Do not use agent teams or subagents unless the user explicitly asks for them.
- Prefer a single agent for bug fixes, small refactors, and tasks touching fewer than three files.
- When multiple independent projects or repositories need development at the same time, prefer dispatching separate subagents in parallel. Scope each subagent to exactly one project/repo and one clear deliverable. The main session owns acceptance criteria, progress checks, review, integration decisions, and final verification.
- Subagents must return concise summaries only: max 5 bullets, no full logs, no full diffs, no pasted file contents.
- Subagents must write detailed findings to local artifact files and return only paths plus conclusions.
- When recovering team work, read only the Current State section or the last 80 lines of track files unless deeper history is required.
- Browser screenshots and image outputs must be saved as files and summarized by path; do not paste image/base64 content into reports.
- After each major team phase, produce a compact handoff summary and prefer starting a fresh session for the next phase.

## Artifact Paths

These artifact rules apply to Team Mode only.

All team artifacts are written to **`{project-root}/agents/{feature-name}/`**, not inside worktrees. This directory is gitignored and exists for local team communication only.

Lead creates the directory and adds `agents/` to `.gitignore` in Phase 2 if needed. Lead provides the project root absolute path (`{project-root}`) in every agent spawn prompt.

| Artifact | Path | Produced by |
|----------|------|-------------|
| Architecture plan | `agents/{feature-name}/plan.md` | Architect |
| Plan index | `agents/index.md` | Architect (created), Lead (status updates) |
| Progress tracking | `agents/{feature-name}/{role}-track.md` | Each agent |
| QA test report | `agents/{feature-name}/qa-report.md` | QA |
| Review report | `agents/{feature-name}/review-report.md` | Reviewer |

Track files: `backend-track.md`, `frontend-track.md`, `qa-track.md`. Agents read/write these files using `{project-root}/agents/...` absolute paths. No git operations are needed for these artifacts.

## Progress Tracking

Track file format:

```markdown
## Git Context
- Branch: `feat/{feature-name}-{role}`
- Worktree: `{relative path}`
- Last commit: `{hash} {message}`

## Tasks
1. [x] Task description — `{commit hash}`
2. [ ] **IN PROGRESS** — description (current state notes)
3. [ ] Pending task

## Changes from Plan
## Bug Fixes / Review Fixes
```

Writing rules:

- Update track files after each step.
- Mark tasks `IN PROGRESS` before starting.
- Mark tasks `[x]` with commit hash after completing.

Recovery rules:

1. Read track file for orientation.
2. Verify with `git log --oneline -10` and `git status` in the worktree.
3. If git state is ahead of the track file, update the track file first, then continue.

Agent startup protocol:

1. Read `{project-root}/agents/{feature-name}/plan.md`.
2. Read the role track file if it exists.
3. Check actual git state.
4. Reconcile stale track files; trust git over track files.
5. Resume from actual progress.

## Communication

- Teammates should respond promptly.
- Peer-to-peer technical conversations do not need lead permission.
- Requirement ambiguity, cross-team dependency, and final tradeoff decisions go through the lead.

## Shutdown Protocol

When receiving a shutdown request from the team lead, each teammate must:

1. Finish or note the current step.
2. Update the track file at project root.
3. Commit all pending code changes.
4. Approve the shutdown.

## Task Sizing

Break work into **5-6 tasks per teammate**. Tasks should be self-contained with clear deliverables. Too large means poor checkpoints; too small means coordination overhead.

## Git Workflow

These git workflow rules apply to Team Mode only. In Standalone Mode, agents use the current branch unless the user explicitly asks for a branch or worktree.

Every code change must be tracked in Git.

### Branch Strategy

- Base branch: `main` or project default. Detect with `git symbolic-ref refs/remotes/origin/HEAD`.
- Feature branch: `feat/{feature-name}` — created by lead before spawning architect.
- Agent sub-branches: `feat/{feature-name}-{role}` such as `feat/hello-frontend`, `feat/hello-backend`, `feat/hello-tests`. Use hyphen, not slash.
- Hotfix branch: `fix/{issue-name}` — created by lead for urgent fixes.
- Developers and QA work in their own git worktree + sub-branch.
- Architect and reviewer run at project root without worktrees and do not commit code.
- Lead merges sub-branches into feature branch, then feature branch into base branch.

### Commit Conventions

- Never add `Co-Authored-By` trailers.
- Follow `~/.claude/skills/rule-git/SKILL.md` for commit conventions, quality rules, and timing guidelines.

### Who Commits What

| Role | Commits to git | Local artifacts |
|------|----------------|-----------------|
| Architect | — | `{feature-name}/plan.md`, `index.md` |
| Dev (frontend) | Frontend implementation code | `{feature-name}/frontend-track.md` |
| Dev (backend) | Backend implementation code | `{feature-name}/backend-track.md` |
| QA | Test files and test utilities | `{feature-name}/qa-report.md`, `{feature-name}/qa-track.md` |
| Reviewer | — | `{feature-name}/review-report.md` |
| Lead | Branch operations, merges | — |

## Lead Responsibilities

### Phase 1: Requirement Gathering

Clarify goal, scope, constraints, and acceptance criteria before spawning teammates. Produce a requirement spec for the architect.

### Phase 2: Team Setup

1. Select the team definition from this skill (`fullstack`, `frontend`, or `backend`).
2. Read referenced agent definitions from `~/.claude/agents/`.
3. Create `feat/{feature-name}` from the base branch.
4. Create `agents/{feature-name}/` at project root and add `agents/` to `.gitignore` if needed.
5. Spawn architect without worktree. Include requirement spec, feature branch name, and project root path.
6. Wait for `agents/{feature-name}/plan.md`.

### Phase 3: Plan Review

Approve the plan only when requirements, file structure, implementation steps, testing strategy, assumptions, and risks are clear. Reject or revise if the plan misunderstands requirements, conflicts with existing architecture, or is too vague.

After approval:

- Shut down architect.
- Spawn developers and QA with worktrees and role sub-branches.
- Spawn reviewer without worktree.
- Make every spawn prompt self-contained; teammates do not inherit lead conversation history.

### Phase 4: Development Coordination

- Break work into 5-6 tasks per teammate.
- Monitor track files and actual git state.
- Handle requirement questions, technical blockers, cross-team dependencies, scope creep, and teammate failures.
- On requirement changes, assess impact, notify affected agents, revise plan if needed, and notify QA.
- Update `agents/index.md` when phases transition.

### Phase 5: QA And Review Loop

After developers complete implementation:

1. Merge dev sub-branches into `feat/{feature-name}` with `--no-ff`.
2. Tell QA to merge `feat/{feature-name}` into its worktree.
3. Tell reviewer to review at project root.

Run QA and reviewer in parallel. Categorize findings as **must-fix**, **can-defer**, or **won't-fix**. Assign must-fix issues to developers, merge fixes, and repeat until requirements are met.

Escalate to the user if the loop exceeds 3 iterations, a fix introduces critical issues, or the team cannot agree on severity.

### Phase 6: Delivery

1. Review final QA and reviewer reports.
2. Confirm all code is committed.
3. Summarize implementation, deviations, test results, browser verification, review findings, loop iterations, and known follow-ups.
4. On user confirmation, shut down teammates, merge remaining branches, ask before merging to base branch, clean up worktrees, and remove local `agents/` artifacts.

## Known Limitations

- Track files can lag behind actual git state; reconcile against git.
- Teammates may stop after errors; guide them or replace them.
- Avoid assigning the same file to multiple devs simultaneously during fix loops.
- Lead should coordinate rather than implement code.
- Session resume may not restore in-process teammates; recover by reading track files and git state, then respawn with recovery context.