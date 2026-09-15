---
name: daily-work-report
description: "Prepare daily progress reports for project managers from work sessions across projects, including coordination, documentation, requirements, design, implementation, testing, and deployment. Use for daily updates, next-morning reports, or rewriting progress notes into yesterday's progress and today's tasks. Not for implementing changes, code reviews, or time tracking."
---

# Daily Work Report

Write a natural, accurate update that the user can paste to a project manager.
Readers may not be engineers and may use AI to summarize reports over time.
Keep project names, reporting dates, and completion stages clear enough to avoid
double-counting work or confusing a plan with a delivered result.

## Scope and Dates

- Follow the user's reporting language; default to Traditional Chinese. The
  skill instructions are in English, but the report should match its audience.
- Resolve the intended sending date and reporting period before using
  "yesterday" and "today". Default to `Asia/Taipei` unless instructed otherwise.
  A report prepared in the evening for the next morning describes the current
  day's work under yesterday and the next day's plans under today.
- Follow the specified working day around weekends and holidays. Ask one short
  question only when ambiguity would change which work is included; continue
  collecting independent evidence while waiting.
- When rewriting sufficient notes already supplied by the user, do not rescan
  every project. Preserve accepted wording, names, and corrections across edits.
- If the developer has not supplied plans for the target day, ask for them
  while collecting the previous day's progress. Do not ask again when those
  plans have already been explicitly provided for that day.

## Find Work by Activity, Not Agent Role

Use the available read-only session history. In BB, use its CLI regardless of
the agent provider; consult the available `bb-cli` skill or live command help.
Outside BB, use accessible session transcripts or user-supplied notes. State
coverage gaps briefly instead of implying access to unavailable histories.

1. Inspect projects and both current and archived sessions. In BB, start with
   `bb status --json`, `bb project list --json`, `bb thread list --json`, and
   `bb thread list --archived --json`. Check accessible hidden sessions when
   relevant and account for bounded listings. Archived work is part of normal
   discovery, not a fallback; archiving itself is not a work event.
2. Find sessions that may have activity in the reporting period, including
   standalone discussions, coordination, documentation, planning, investigation,
   development, and QA.
   A leader, child session, commit, or file change is not required.
3. Check event timestamps and substantive conversation. In BB, use
   `bb thread log <id> --all --json` or sequence pagination. Read the day's user
   requests, corrections, agent analysis, and conclusions first. Read earlier
   context or tool results only where needed. An unfinished discussion without
   a final response can still contain reportable progress.
4. Treat metadata as a discovery aid, not evidence of work. Opening, marking
   read, or renaming a session is not progress. `updatedAt` alone is insufficient;
   `bb thread output <id>` may return an older result.
5. Extract work from each session, then group by the actual project and problem.
   Parent relationships and leader summaries help connect related work; they
   are not entry requirements. A standalone session is a valid primary source.
6. Merge discussion, implementation, and QA for the same work without counting
   them separately. Include distinct progress from child sessions. Sessions
   can span repositories; their project label or title may be misleading.
7. Resolve conflicting reports chronologically using the relevant validation
   or decision record. Initial QA failure followed by focused fixes does not
   establish that a second full test run passed.
8. Session history is not a complete work log. Meetings, direct communication,
   document work, and manual operations may be absent. When coverage is unclear,
   invite the developer to supply missing work in one short question, together
   with any request for today's plans. Accept their explicit account without
   demanding a matching session; do not imply independent verification.

This task is inspection and drafting. Do not wake agents, delegate work, message
people, rerun tests, reset data, deploy, or retry operations just to produce a
report. Such actions require a separate user request. Missing records mean no
evidence was found, not that no work happened.

## Identify Actual Progress

Before drafting, keep brief working notes for each item: reporting date,
request source, work performed, current stage, evidence, and remaining work.
Do not include this evidence ledger in the report unless requested.

- Distinguish planned work, additional external requests, and problems found
  during testing. Attribute a late request plainly without blame or portraying
  it as an originally planned deliverable.
- For ongoing work, report what advanced that day: new findings, revised scope,
  completed checks, or remaining blockers. Do not recount the accumulated result
  or rephrase yesterday's status as new progress. If nothing advanced, say so
  only when relevant; do not manufacture a daily achievement.
- Requirements, design, investigation, coordination, and documentation count
  as work. Identify the subject, activity, and current outcome or open question.
  A code change, final decision, or new artifact is not required.
- Distinguish passive waiting from active follow-up, reconciling conflicting
  requirements, preparing meeting material, or revising documents. Communication
  may be the day's main work even if agreement is still pending. Describe that
  effort and the unresolved dependency separately, without inflating brief
  exchanges or dismissing substantial coordination as merely waiting.
- Do not turn an agent's proposal into an accepted decision. Distinguish a
  completed plan from implementation, and analysis from a confirmed root cause.
- Describe implementation, testing, test-data preparation, database rebuilding,
  deployment, and integration testing as stages of the relevant work. Include
  stages actually performed; do not invent a full delivery cycle for every item.
- Keep local completion, commits, deployment, and integration verification
  distinct. Preparation for an action is not evidence it was executed.
- A planned workaround is not a verified recovery. An accepted risk is not a
  resolved cause. If an operation may already have succeeded despite an error,
  do not summarize recovery as blindly repeating it.
- User corrections about actual work and preferred names supersede older
  notes. A statement of intent or expectation is still not proof of completion.
- Do not infer effort from commits, test counts, message volume, tokens, tool
  calls, or elapsed time between messages. Do not pad a report to imply a full
  working day; equally, do not dismiss substantial analysis because it produced
  no code.

## Write for a Project Manager

- Use a straightforward colleague's voice. Light editing for clarity and flow
  is welcome; it must not inflate scope, certainty, impact, or effort. Avoid
  promotional language and repetitive templates.
- Group by project. Each sub-item should describe one meaningful requirement,
  problem, or outcome, usually in one or two sentences. Add the reason, evidence,
  or next step when useful; do not force all three into every bullet.
- For small items, describe the change and relevant result plainly. Neither
  turn routine work into a major achievement nor dismiss it as "just a quick
  change". Fold routine supporting logs, documentation, and tests into the
  parent item. Substantive coordination or document work can merit its own item;
  do not hide it as incidental support or duplicate it under implementation.
- For large items, retain the purpose, meaningful scope, current result, and
  important remaining work as relevant. Summarize the work rather than listing
  implementation steps, but do not compress substantial progress into a vague
  phrase such as "worked on the feature". Detail should reflect useful context,
  not a target word count.
- Include technical details only when they explain impact, status, or the next
  action. Normally omit hashes, file paths, configuration names, raw errors,
  payloads, and test-by-test counts. Preserve meaningful affected scope when
  appropriate for the recipient.
- Replace vague "discussed requirements" with the subject and progress. For
  example: "Compared sign-in options and documented the constraints; the final
  choice is pending." This is an invented example, not a project record.
- The developer owns the work plan and priorities. Today's tasks must come
  only from plans the developer explicitly provided for the target day.
  Preserve their scope, order, and morning/afternoon arrangements; edit wording,
  not decisions. Do not allocate work or turn unfinished items, blockers,
  agent recommendations, or older plans into today's tasks without the
  developer's decision.
- The agent may surface evidence-backed unfinished work as candidates for the
  developer to consider. Keep these suggestions separate from the paste-ready
  report, clearly pending the developer's selection and scheduling. Do not
  assign dates, priority, or ownership as settled facts. Once the developer
  chooses and schedules an item, include it in today's tasks as appropriate.
- If plans are missing, keep today's tasks pending developer input. Continue
  preparing yesterday's progress, but do not present a complete report with
  invented tasks. Explicitly stating that no tasks are planned is different
  from not having supplied a plan.
- Describe unresolved issues concretely without dramatizing or concealing
  them. Do not add an hours estimate or workload judgment unless asked.

### Small Changes with Substantial Effort

A small visible result or diff can involve substantial work. When the developer
reports unusually high effort, or records show significant investigation or
rework, explain the actual reason instead of adding adjectives or more bullets.

- Look for concrete support: reproduction attempts and findings, compatibility
  constraints, affected dependencies, revised requirements, environment recovery,
  failed checks, meeting summaries, or document revisions. Explain the actual
  findings or rework, not the volume of messages or edits. Do not invent a
  complexity justification for every small item.
- State the outcome and the main effort driver briefly. Keep a traceable source
  in the working notes. Where useful, include one recipient-accessible reference
  to an existing issue, test result, or decision record beside the item, subject
  to confidentiality; do not dump logs or create a separate evidence appendix.
- Evidence can explain the work performed without proving a number of hours.
  Use explicit developer-provided or recorded work durations only when relevant,
  never session length or activity counts as substitutes. If the reason for
  reported effort is unclear, ask the developer or omit that explanation rather
  than fabricate it. Routine items need no extra justification.

## Confidentiality

- This reusable skill must contain only generic procedures and invented
  examples. Never embed real project facts from the source conversation in the
  skill, README, evaluation cases, scripts, or commit messages.
- This includes business terminology that reveals a private workflow, as well
  as organization or person names, account data, API routes, environments,
  internal paths, incident details, and copied transcripts. Replacing a company
  name alone is not sufficient anonymization.
- Keep source records and generated internal reports out of a distributable
  skill repository. Do not persist them unless the user requests an appropriate
  private destination.
- For the actual report, use the details authorized for its intended audience.
  Never include credentials or unnecessary sensitive data. Do not send or
  publish the report merely because it has been drafted.

## Output

Return the paste-ready report without an introduction, writing analysis, source
appendix, or closing offer unless requested. When proposing unfinished work,
present the candidates separately for the developer's decision; never mix them
into today's tasks before acceptance and scheduling. Translate these headings and
placeholders into the user's language and preserve their established format.
Leave a blank line before each list.

```text
Yesterday's progress:

- Project A
  - Work performed and its current result or remaining stage.
  - Another independently meaningful item.
- Project B
  - Work performed and its result.

Today's tasks:

- Project A
  - Planned testing or follow-up.
- Project C
  - Afternoon kickoff, followed by organizing requirements and open questions.
```

Use only the projects and items warranted by the evidence. If access limitations
materially affect coverage, add one brief note outside the paste-ready report.

## Final Check

- Do the periods match the intended sending date and timezone?
- Were archived and standalone sessions considered, with room for developer
  additions outside session history?
- Is each item new progress for this period and counted only once?
- Are external changes and actual engineering/environment work represented?
- Is substantive communication and document work represented, with active
  effort distinguished from passive waiting even when no decision is final?
- Are proposals, implementation, deployment, verification, and recovery distinct?
- Can the intended audience understand the status without inflated claims or
  unnecessary implementation details?
- Are small items described proportionately and large items given meaningful
  scope? If unusual effort is explained, is the reason supported and traceable?
- Does every task come from the developer's explicit plan for the target day,
  with unaccepted suggestions kept outside the report?
- Is the content appropriate for the intended recipient?
