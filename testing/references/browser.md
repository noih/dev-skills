# Browser Recipes

Read the relevant section before starting, taking over, reconfiguring, or diagnosing an automation browser, even when browser work begins midway through a task. These recipes cover binary/configuration, session commands and process diagnosis; the testing entrypoint retains tool selection, ownership, shared-resource protection, handoff and cleanup decisions. Generic reporter/pipeline/background recipes are separate and need not be read for ordinary browser operations.

## Screenshot Evidence

Capture only images needed to establish a visual finding or key state. Prefer a relevant region or viewport over full-page captures unless the finding concerns the whole page. Keep enough context to identify the state and defect; avoid near-duplicate images.

Test at the required viewport, then downscale the saved evidence proportionally to the smallest size that keeps relevant text and details legible. Bounding boxes such as 1024×768 or 1280×960 are examples, not required dimensions or a forced aspect ratio; do not stretch or upscale. Inspect the reduced image before discarding a disposable original. Preserve original-resolution inputs/baselines when pixel comparison, fine detail, or a project/user requirement needs them; a reduced review image does not replace those inputs. Use deterministic resizing/cropping, never generative alteration of test evidence.

## Separate Automation Binary

Do not automate the user's everyday Chrome app. Automation launched from `/Applications/Google Chrome.app` (Playwright's `chrome` channel, which Playwright CLI uses when nothing is selected, and chrome-devtools MCP's default `stable` channel) can receive OS link-open events even when headless. Prefer a separate binary:

- Playwright CLI / MCP: bundled Chromium via `--browser=chromium` on `open`, `PLAYWRIGHT_MCP_BROWSER=chromium`, or `"browser": { "browserName": "chromium" }` in `.playwright/cli.config.json`. The flag is accepted although `--help` lists only channels, and it needs no configuration change, so a missing config file is not a reason to use system Chrome.
- Bundled browsers are not installed with the CLI, and their revision is tied to the CLI version. A "not installed" error names the fix (`playwright-cli install-browser chromium`): install it, and expect to repeat that after a CLI upgrade. Do not drop the selection to make the error go away.
- Choose the engine by who uses the surface: `--browser=webkit` with `--device` for pages reached mainly from mobile Safari, bundled Chromium otherwise. WebKit is a separate binary too. Device emulation approximates layout and engine behavior; it is not a real-device check.
- chrome-devtools MCP: `--executablePath` pointing at Chrome for Testing / Chromium, or `--channel=canary`, set in the server configuration.
- If no separate binary can be installed or selected within the task, disclose the constraint instead of silently falling back to system Chrome.

## Shared Browser Cache

Playwright keeps browser builds in one per-user cache shared by the CLI and every project's own Playwright install. A build the current tool no longer uses may still be the pinned build of another project. Before removing builds, list which installations reference them with `playwright-cli install-browser --list`, and remove only builds no installation references.

## Close The Owned Session

Run `playwright-cli -s=<session> close` from its recorded workspace, then check both session inventory and the recorded daemon/browser process tree. Exit code 0 or an empty `playwright-cli list` alone is insufficient: missing registry entries, different workspace scope, or an unreachable socket can leave a live daemon undiscovered. Retention requires the entrypoint's named owner, reason, expiry and accepted handoff.

`playwright-cli close-all` and `kill-all` stop every session, including concurrent work. Use them only on explicit user request or after confirming no other session is in use.

chrome-devtools MCP launches a browser on its first tool call and keeps it for the MCP server's lifetime, which is the whole agent session. No tool closes it, its last page cannot be closed, and there is no idle timeout, so one call leaves a browser running until the session ends. Close only pages you opened with `close_page`; do not navigate or close user-selected pages.

When the DevTools work is done, stop the browser the server launched for this session: the process carrying `--remote-debugging-pipe` whose parent chain leads to the MCP server started by the current agent process, not another session's server. Verify that ancestry as below, stop that one process, and confirm it is gone. The server stays up and launches a fresh browser on the next call. Never stop a browser the server attached to (`--browserUrl`, `--wsEndpoint`, `--autoConnect`); that one is the user's.

In the server configuration, `--isolated` gives each session a throwaway profile, so concurrent sessions do not contend for one profile directory, and `--headless` keeps a browser that outlives its use from holding a window open.

## Diagnose Before Signalling

Debugging flags locate candidates; they do not prove ownership or that a browser is unused. PPID 1 is normal for a detached daemon. Compare candidates with the session/PID, command, start time and cwd recorded at launch:

```bash
pgrep -fl 'remote-debugging-pipe|remote-debugging-port'  # candidates only
ps -o pid=,ppid=,lstart=,command= -p <pid>               # compare with recorded identity
```

A browser launched by an MCP server has no PID recorded at launch. Establish ownership by ancestry instead: repeat the `ps` call on each `ppid`, from the candidate up to the MCP server and on to the current agent process. A candidate whose chain reaches a different agent process belongs to another session.

Stop only the verified task-owned daemon (for example, a leftover `@playwright/cli` node daemon) together with its browser descendants. Otherwise the daemon may relaunch the browser. Recheck survivors; never match `Google Chrome` alone, which also kills the user's browser. Inspection/cleanup errors remain unresolved rather than proof of exit. Also verify removal of disposable profiles/data under the task's resource contract.
