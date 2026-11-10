# dev-skills repository rules

This repository is public, and other people install its skills.

- Never write a real client, organization, project, repository, workspace, channel or person name, an internal URL, a tracker key or a local path into a tracked file or a commit message. This covers examples, QA notes under `docs/` and quoted transcripts. Use neutral placeholders such as `<project>` or `DEMO-42`.
- Keep skills tool-neutral. Do not name the issue tracker, company system or private configuration one person happens to use; write "the project's tracker" and leave the mapping to private project instructions.
- `.githooks/commit-msg` rejects a commit whose staged files or message contain a configured private term. Enable it once per clone with `git config core.hooksPath .githooks` and keep the terms in `.git/info/private-terms`, which is never committed. When the hook fails, reword the content; do not bypass the hook.
