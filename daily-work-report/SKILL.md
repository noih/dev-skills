---
name: daily-work-report
description: "Draft PM-facing daily progress and plans from work sessions or supplied notes, including coordination, design, implementation, testing, and deployment. Use for daily updates, next-morning reports, or rewriting progress notes. Not for implementation, code review, or time tracking."
---

# Daily Work Report

Write an accurate update the user can paste to a project manager. Report what
advanced, its meaningful scope, and the relevant result or blocker. Keep dates,
project names, and completion stages clear for readers without engineering context.

## 1. Establish Scope

- Follow the user's language; default to Traditional Chinese and `Asia/Taipei`.
  Resolve the sending date and reporting period before using yesterday/today.
  An evening draft for tomorrow reports today's work as yesterday's progress.
  Respect specified working days around weekends and holidays; ask only when
  ambiguity changes which work belongs, and collect independent evidence meanwhile.
- If supplied notes are sufficient, rewrite them without rescanning projects.
  Preserve accepted wording, project names, grouping, and corrections. Use the
  user's edited report as the reference for detail and format; an omission is
  not necessarily a factual correction or a universal ban on that detail.
  Apply corrections cumulatively; when asked for the full report, return both
  progress and plans with all accepted edits, not just the latest changed item.
- If plans for the target day are missing, ask once while collecting progress.
  Bundle any invitation to add meetings, communication, or other unrecorded work.
  Do not ask again for plans already explicitly provided for that day.

## 2. Collect Read-Only Evidence

In BB, use the CLI regardless of agent provider; consult `bb-cli` or live help.
Elsewhere, use accessible transcripts or supplied notes.

1. Inspect projects and both current and archived sessions:
   `bb status --json`, `bb project list --json`, `bb thread list --json`, and
   `bb thread list --archived --json`. Include accessible hidden sessions when
   relevant and account for listing limits.
2. Find activity in the reporting period, including standalone or unfinished
   discussions, requirements, coordination, documents, investigation, development,
   and QA. A leader, child session, commit, or file change is not required.
3. Read dated events with `bb thread log <id> --all --json` or sequence pagination.
   Prioritize the day's requests, corrections, analysis, and conclusions; read
   earlier context and tool results as needed. Metadata only aids discovery:
   `updatedAt`, opening, renaming, marking read, and archiving are not work evidence.
   `bb thread output <id>` may return an older result.
4. Group by the actual project and problem, not merely session title or parent.
   Sessions can span repositories. Consolidate discussion, implementation, and QA
   records without losing distinct advances or counting the same work twice.
5. Resolve conflicts chronologically using validation and decision records.
   A failed full run followed by focused fixes does not prove another full run
   passed. Accept the user's account of unrecorded work without requiring a
   matching session or claiming independent verification.

Keep brief private working notes per item: date, request source, work performed,
current stage, evidence, and remaining work. These support accuracy; they are not
an appendix to the report. Missing records mean no evidence was found, not that
no work happened.

Inspection and drafting only: do not wake or delegate agents, message people,
rerun tests, reset data, deploy, or retry operations to produce the report.

## 3. Determine Actual Progress

- Report advances during this period, not accumulated accomplishments restated
  as new work. Distinguish original plans, later external requests, and issues
  discovered during testing; attribute changed scope plainly, without blame.
- Requirements, design, investigation, coordination, and documents count even
  without code or a final decision. Name the subject, work performed, and outcome
  or open question. Separate active follow-up or reconciliation from passive
  waiting; neither inflate brief exchanges nor hide substantial communication.
- Preserve stage and certainty: proposal versus accepted decision, investigation
  versus confirmed cause, implementation versus deployment, and deployment versus
  integration verification. Include only stages actually performed. A planned
  workaround is not verified recovery; an accepted risk is not a resolved cause.
  If an operation may have succeeded despite an error, do not describe recovery
  as simply repeating it.
- User corrections override older records, but intent or expectation is not
  completion evidence. Do not infer effort or hours from commits, test counts,
  messages, tokens, tool calls, or session duration, or pad a report to imply a
  full working day.
- When a small visible result required substantial investigation or rework,
  explain the supported reason briefly: reproduction findings, compatibility
  constraints, revised requirements, environment recovery, or document revisions.
  Do not justify effort with adjectives or more bullets. Ask or omit the reason
  if unknown; mention recorded work duration only when relevant or requested.

## 4. Write for the PM

- Use a natural colleague's voice. Polish for clarity without inflating scope,
  certainty, impact, or effort. Follow the user's established project names and
  format rather than repository names or technical categories.
- Default to project names as top-level bullets and work items as nested bullets,
  even for a single project. Use the user's project names and assignments in both
  progress and plans; a shared organization or repository does not make two
  projects one. Write item text directly, without mini-titles such as "Testing:".
- Group by meaningful work item, usually one or two connected sentences. Lead
  with its purpose so preparation steps are understandable. Combine preparation,
  execution, and results for the same objective into one item, even when they
  happened in separate sessions. Fold supporting tests, logs, and routine
  documentation into that item; split distinct deliverables or substantive
  communication when they merit their own item.
- Match detail to substance, not a target bullet count. Describe small changes
  plainly; retain the meaningful scope and reason for larger changes. Avoid both
  dismissive wording and vague summaries such as "completed related adjustments."
- Keep details that change the PM's understanding of scope, outcome, availability,
  dependencies, or next action. This can include a concrete cause, validation,
  deployment milestone, or unresolved blocker. Simplify terminology, not substance.
  Name a known pending action instead of a vague "awaiting confirmation"; preserve
  unresolved causes and do not imply that the action will resolve every issue.
- Leave verification minutiae in working notes: hashes, paths, commands, test
  counts, payload fields, secondary test scenarios, implementation safeguards,
  and generic benefits already implied by the work. Exact identifiers belong
  only when they identify the work or explain a material issue.
- Commit/push status, seed application, and deployment reminders usually belong
  in notes, not a handoff checklist appended to each item. Omit irrelevant detail
  without implying deployment or integration success. State each useful result or
  limitation once; avoid repetitive testing/pending-work formulas and recaps.
- If a source link materially helps, use one recipient-accessible reference beside
  the item, subject to confidentiality. Do not dump evidence or searched-project
  inventories. Mention access gaps briefly outside the report only when they
  materially affect coverage.

## 5. Preserve the User's Plan

- Today's tasks come only from the user's explicit plan for that date. Preserve
  scope, order, and morning/afternoon arrangements. Edit wording, not decisions;
  do not promote unfinished work, blockers, older plans, or agent recommendations
  into scheduled tasks.
- Clarify a brief plan's subject and activity using supplied context, without
  turning communication or testing into promised delivery. Preserve meeting
  titles; a descriptive title needs no generic purpose or invented deliverables.
  Expand an agenda only when supplied or requested. Do not append generic
  deliverables such as "compile discovered issues" unless they add requested scope.
- For repeated testing or other repeated work, include the supplied reason for
  doing it again and preserve the requested method. Do not present regression
  testing after changes as first-time validation or invent a reason if none is known.
- If useful, present evidence-backed unfinished work separately as candidates
  pending the user's choice, without assigning dates, priority, or ownership.
  Do not repeat tasks already chosen in these suggestions.
- If plans remain missing, leave today's tasks pending input and continue drafting
  progress. Missing plans do not mean no work is planned.

## Confidentiality

- Reusable skill content, examples, supporting files, and commit messages must
  contain only generic procedures or invented scenarios, never private project
  facts from the conversation. Changing a company name alone is not sufficient:
  exclude identifying workflows, terminology, people, accounts, routes, internal
  paths, environments, incidents, and transcripts.
- Do not persist source records or internal reports unless the user requests an
  appropriate private destination; keep them out of distributable skill repos.
- In the actual report, use only details appropriate for the authorized audience;
  exclude credentials and unnecessary sensitive data. Drafting does not authorize
  sending or publishing.

## Output and Final Check

Return the paste-ready report, without an introduction, analysis, source appendix,
or closing offer unless requested. Use the user's established format; otherwise
use this project-grouped template. Translate labels only
when reporting in another language; leave a blank line before each list.

```text
昨日進度：

- 專案名稱
  - 工作目的、進度與結果或待處理事項。

今日待辦：

- 專案名稱
  - 預計工作；若是再次執行，簡述已知原因。
```

Before returning, check dates and coverage, remove duplicate or secondary detail,
confirm stage and uncertainty, and verify that every planned task was chosen by
the user. Preserve material scope and blockers while matching their accepted
level of detail.
