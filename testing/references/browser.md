# Browser Recipes

Read the relevant section before starting, taking over, reconfiguring, or diagnosing an automation browser, even when browser work begins midway through a task. These recipes cover binary/configuration, session commands and process diagnosis; the testing entrypoint retains tool selection, ownership, shared-resource protection, handoff and cleanup decisions. Generic reporter/pipeline/background recipes are separate and need not be read for ordinary browser operations.

## Screenshot Evidence

Capture only images needed to establish a visual finding or key state. Prefer a relevant region or viewport over full-page captures unless the finding concerns the whole page. Keep enough context to identify the state and defect; avoid near-duplicate images.

Test at the required viewport, then downscale the saved evidence proportionally to the smallest size that keeps relevant text and details legible. Bounding boxes such as 1024×768 or 1280×960 are examples, not required dimensions or a forced aspect ratio; do not stretch or upscale. Inspect the reduced image before discarding a disposable original. Preserve original-resolution inputs/baselines when pixel comparison, fine detail, or a project/user requirement needs them; a reduced review image does not replace those inputs. Use deterministic resizing/cropping, never generative alteration of test evidence.

## Separate Automation Binary

Do not automate the user's everyday Chrome app. Automation launched from `/Applications/Google Chrome.app` (Playwright's `chrome` channel, chrome-devtools MCP's default `stable` channel) can receive OS link-open events even when headless. Prefer a separate binary:

- Playwright CLI / MCP: bundled Chromium via `PLAYWRIGHT_MCP_BROWSER=chromium`, or `"browser": { "browserName": "chromium" }` in `.playwright/cli.config.json`.
- chrome-devtools MCP: `--executablePath` pointing at Chrome for Testing / Chromium, or `--channel=canary`.
- If configuration cannot be changed within the task, disclose the constraint instead of silently falling back to system Chrome.

## Close The Owned Session

Run `playwright-cli -s=<session> close` from its recorded workspace, then check both session inventory and the recorded daemon/browser process tree. Exit code 0 or an empty `playwright-cli list` alone is insufficient: missing registry entries, different workspace scope, or an unreachable socket can leave a live daemon undiscovered. Retention requires the entrypoint's named owner, reason, expiry and accepted handoff.

`playwright-cli close-all` and `kill-all` stop every session, including concurrent work. Use them only on explicit user request or after confirming no other session is in use.

chrome-devtools MCP owns a browser for the MCP server's lifetime; its last page cannot be closed through the tools. Close only pages you opened with `close_page`; do not navigate or close user-selected pages. Use `--isolated` when a throwaway profile is enough.

## Diagnose Before Signalling

Debugging flags locate candidates; they do not prove ownership or that a browser is unused. PPID 1 is normal for a detached daemon. Compare candidates with the session/PID, command, start time and cwd recorded at launch:

```bash
pgrep -fl 'remote-debugging-pipe|remote-debugging-port'  # candidates only
ps -o pid=,ppid=,lstart=,command= -p <pid>               # compare with recorded identity
```

Stop only the verified task-owned daemon (for example, a leftover `@playwright/cli` node daemon) together with its browser descendants. Otherwise the daemon may relaunch the browser. Recheck survivors; never match `Google Chrome` alone, which also kills the user's browser. Inspection/cleanup errors remain unresolved rather than proof of exit. Also verify removal of disposable profiles/data under the task's resource contract.
