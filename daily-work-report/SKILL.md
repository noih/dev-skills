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
- When available, compare the user's latest report from supplied context or a
  privately configured report location to preserve names and avoid repeating
  earlier progress. Use its stated coverage, not just its posting time; it does
  not override an explicit reporting window or automatically roll the date forward.
- If supplied notes are sufficient, rewrite them without rescanning projects.
  Preserve accepted wording, project names, grouping, and corrections. Use the
  user's edited report as the reference for detail and format; an omission is
  not necessarily a factual correction or a universal ban on that detail.
  Apply corrections cumulatively; when asked for the full report, return both
  progress and plans with all accepted edits, not just the latest changed item.
- Before discovery, establish the work-project allowlist and personal-project
  exclusions from the user's instructions or applicable private local context.
  Keep actual organization names, repository roots, and mappings outside this
  public skill. Do not assume every local project or BB thread is work-related.
  If scope is still unknown, ask once and collect only already-confirmed work.
  Apply the same scope to BB, native transcripts, Git, project issues, meetings,
  work conversations, and the final report.
- If plans for the target day are missing, ask once while collecting progress.
  Bundle any invitation to add meetings, communication, or other unrecorded work.
  Do not ask again for plans already explicitly provided for that day.

## 2. Collect Read-Only Evidence

Check BB, native Claude Code, and native Codex as complementary sources, regardless
of where this report is being drafted. Finding BB activity does not finish
discovery. Skip unavailable sources and record material coverage gaps; do not
install tools or resume sessions to inspect history. For native discovery, read
[Native session evidence](references/native-sessions.md).

1. When BB is available, use its CLI regardless of agent provider; consult
   `bb-cli` or live help. Inspect projects and both current and archived sessions:
   `bb status --json`, `bb project list --json`, `bb thread list --json`, and
   `bb thread list --archived --json`. Include accessible hidden sessions when
   relevant and account for listing limits. Also discover native Claude Code and
   Codex transcripts, using project metadata to filter before reading content.
2. Find activity in the reporting period, including standalone or unfinished
   discussions, requirements, coordination, documents, investigation, development,
   and QA. A leader, child session, commit, or file change is not required.
3. Read dated BB events with `bb thread log <id> --all --json` or sequence
   pagination, and dated native transcript events for the same reporting window.
   Prioritize the day's requests, corrections, analysis, and conclusions; read
   earlier context and tool results as needed. Metadata only aids discovery:
   `updatedAt`, opening, renaming, marking read, and archiving are not work evidence.
   `bb thread output <id>` may return an older result.
4. Scan commit history in the repositories those sessions touched. Take the
   paths from the project sources, and include in-scope worktrees and sibling
   repositories the sessions mention, such as an app, docs, or spec repo beside
   the server. A mention or shared parent directory does not expand the allowlist.
   Use a read-only listing over the reporting period, for example
   `git log --all --since=<start> --until=<end> --format='%h %ad %s' --date=iso`.
   Commit messages surface work no session recorded, confirm what was merged
   versus left on a branch, and date decisions. A commit proves only what its
   message and timestamp say, not that the change was tested or deployed.
   Include relevant uncommitted work with read-only status/diff inspection when
   useful; establish its date and authorship from sessions or the user's account,
   not file modification times. Do not infer completion percentages from a diff.
5. Group by the actual project and problem, not merely session title or parent.
   Sessions can span repositories. Consolidate discussion, implementation, and QA
   records without losing distinct advances or counting the same work twice.
   BB may mirror a native session: correlate provider session IDs when available,
   otherwise use project, timestamps, and matching content. Count mirrored events,
   resumed/forked history, and parent/child summaries only once; preserve distinct
   later work. In mixed-project sessions, include only in-scope work items.
6. Resolve conflicts chronologically using validation and decision records.
   A failed full run followed by focused fixes does not prove another full run
   passed. Accept the user's account of unrecorded work without requiring a
   matching session or claiming independent verification.

When available, supplement these sources with in-scope project-management issue
activity. Resolve the project as in section 5, then query the user's activity
during the reporting period. Check the tool's timezone and date bounds. Read
individual issue comments/activity to identify the actual contribution;
update timestamps alone can miss comment-only activity. A current status or an
assignment does not prove the user performed work during this period.

Relevant work conversations and meeting records can fill gaps when accessible
and within the established scope. Use calendar events to locate
meetings and their summaries, and relevant Slack conversations for coordination
or commitments. A calendar entry proves scheduling, not attendance or a decision.
Treat AI summaries as fallible: verify unclear names, ownership, or conclusions
against available records rather than guessing. Consolidate a meeting and its
recording, and merge overlapping issue, chat, session, and meeting evidence.
Keep private report locations and channel mappings outside this skill.

Tool mapping: when using AI Desktop, use `project_list` to resolve projects,
`issue_query` for issues, `calendar_events` for meetings, and `calendar_context`
for meeting summaries. For issue activity, pass `project`, `activity_after`,
`activity_before`, and `activity_by="me"`; `updated_after` alone can miss comments.
For a single day's calendar, set both `start_date` and `end_date` to that date
(inclusive). With other providers, use equivalent read-only tools and verify
their filter semantics rather than assuming these parameter names apply.

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
- If a work item maps to a verified project-management issue, prefix its text
  with the exact display ID in brackets, for example `[DEMO-42] ...`. Apply this
  to progress, confirmed plans, and task suggestions. Verify the project and
  issue content rather than guessing from similar titles; leave unmatched work
  unnumbered. Keep the user's grouping and order. For a consolidated batch, use
  a relevant umbrella issue if one exists; do not expand a concise count into an
  exhaustive ticket list or imply one ticket covers unrelated work.
- Group by meaningful work item, usually one or two connected sentences. Lead
  with its purpose so preparation steps are understandable. Combine preparation,
  execution, and results for the same objective into one item, even when they
  happened in separate sessions. Fold supporting tests, logs, and routine
  documentation into that item; split distinct deliverables or substantive
  communication when they merit their own item.
- One item is its purpose or business rule plus its outcome. Spend the words on
  what the change means for the business, such as the rule now enforced or the
  data source now used, not on how it was delivered or how it behaves internally
  (UI sequence, endpoint added, test harness used). Add a second sentence only
  for an external blocker or a result the PM must act on.
- Listing an item under progress already says it is done. Do not append
  completion confirmations such as "deployed and verified online", "review
  passed", "merged to main", or "acceptance passed"; the PM reads them as noise.
  Only an unfinished item carries a stage, in one clause, and when the user's
  plan for today already names the remaining step, the progress line stays
  plain and the plan carries the stage.
- When a count already summarizes the members ("fixed the 5 issues", "found 6
  new issues"), stop at the count. Do not enumerate the members, single out one
  member's root cause, or restate the count as a parenthetical list elsewhere.
- Match detail to substance, not a target bullet count. Describe small changes
  plainly; retain the meaningful scope and reason for larger changes. Avoid both
  dismissive wording and vague summaries such as "completed related adjustments."
- Keep details that change the PM's understanding of scope, outcome, availability,
  dependencies, or next action: a concrete cause, an unresolved blocker, a
  residual risk, a decision taken, or a count that sizes the work. A deployment
  or validation milestone belongs only when it is itself the news, such as a
  previously blocked item finally going live. Simplify terminology, not substance.
  Name a known pending action instead of a vague "awaiting confirmation"; preserve
  unresolved causes and do not imply that the action will resolve every issue.
- Leave verification minutiae in working notes: hashes, paths, commands, test
  counts, payload fields, secondary test scenarios, implementation safeguards,
  observed sample values, and generic benefits already implied by the work. Exact
  identifiers belong only when they identify the work or explain a material issue.
- Delivery pipeline minutiae belong in notes, not report text: "developed, tested and
  reviewed", "merged", "branch pushed", "awaiting deploy or migration", and
  similar internal next steps. Do not narrate how a decision was reached ("as
  agreed with X", "per discussion"); state the rule or outcome. Do not append
  watch items or "still to confirm" tails unless the item is blocked by them.
  An unfinished stage that changes availability or the next decision (for
  example, implemented but not yet deployed) goes in today's plan when the user
  scheduled the remaining step, otherwise as one clause on the progress item.
  Omit irrelevant detail without implying deployment or integration success.
  State each useful result or limitation once; avoid repetitive
  testing/pending-work formulas and recaps.
- Housekeeping is not a work item: committing or tidying work already reported
  on an earlier day, reorganizing report folders, cleaning branches or
  worktrees. Fold it silently or drop it.
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
- Expand a plan item by naming its subject and purpose, not by enumerating the
  members behind a count. "Fix the 6 issues found in the retest" is complete;
  a parenthetical list of the issues is not more informative to the PM. A
  concrete follow-up already known from the evidence (for example, chasing a
  counterparty on a pending item) may be attached in a few words.
- For repeated testing or other repeated work, include the supplied reason for
  doing it again and preserve the requested method. Do not present regression
  testing after changes as first-time validation or invent a reason if none is known.
- If useful, present evidence-backed unfinished work separately as candidates
  pending the user's choice, without inventing dates, priority, or ownership.
  Do not repeat tasks already chosen in these suggestions.
- When suggesting today's tasks, also consult the corresponding work project's
  issues in the available project-management tool. Resolve the project mapping
  when needed, then query issues scoped to that project. Use read-only operations.
  Keep real project IDs and mappings in private context; a visible project is
  not automatically in scope.
- Start with relevant open issues, especially those assigned to the user or
  connected to recent work. Read individual issue details when needed to check
  status, next action, blockers, and recorded priority or due date. Do not require
  recent activity: an older open issue can still be a useful candidate. Account
  for query limits before claiming coverage; an unavailable source is not an
  empty backlog.
- Select a short, useful subset rather than copying the backlog. Favor actionable
  follow-ups supported by recent work, recorded urgency, or deadlines; explain
  each suggestion briefly. Exclude completed/canceled items, duplicates, and
  tasks already in the user's plan. Do not imply someone else's assignment is
  the user's task or that a blocked issue is ready to implement.
- Issue candidates are suggestions, not commitments or proof of work completed.
  Present them separately as `今日待辦建議（待確認）`, outside the paste-ready
  report, until the user selects them. Preserve any existing plan. Read issues
  only; do not change their status, assignee, priority, or comments for a report.
- Also consult the available calendar for the report's target day, limiting the
  query to that date (including when drafting tomorrow's report tonight).
  Use the reporting timezone and retain in-scope work meetings
  involving the user; exclude personal, canceled, or declined events. Preserve
  meeting titles and scheduled times, deduplicate against confirmed plans, and
  present additional meetings as `今日待辦建議（待確認）`. Do not invent agendas
  or deliverables, modify events, or treat unavailable calendar data as no meetings.
- Apply the same candidate rules to unfinished items from the previous report,
  work-chat commitments, and meeting action items assigned to the user. Check
  later records for completion or changed ownership before suggesting them;
  a commit alone does not prove the whole task is done. Neither an older promise
  nor a meeting assignment establishes the user's plan for the target day.
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
or closing offer unless requested. Task suggestions from section 5 may follow in
a clearly separate block; never blend them into confirmed plans.
Use the user's established format; otherwise
use this project-grouped template. Translate labels only
when reporting in another language; leave a blank line before each list.
Render the report as Markdown, not inside a code fence. Keep each bullet's text
on one source line so it can be copied without manual line-break artifacts.

```text
昨日進度：

- 專案名稱
  - 工作目的、進度與結果或待處理事項。

今日待辦：

- 專案名稱
  - 預計工作；若是再次執行，簡述已知原因。
```

Before returning, check dates and coverage, remove duplicate or secondary detail,
strip completion confirmations and internal mechanics from progress items,
confirm stage and uncertainty only where work is unfinished, verify issue IDs
against the matched work, and verify that every planned task was chosen by the user.
Preserve material scope and blockers while matching their accepted
level of detail.
