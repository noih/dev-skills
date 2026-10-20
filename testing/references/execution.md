# Execution Recipes

Read the relevant section before selecting reporter overrides, piped/background execution, or repetition calculations. An ordinary direct foreground project runner needs no additional read. Project runners and the entrypoint's evidence, exit-status, ownership, stress and stop rules take precedence. These recipes do not require the browser reference.

## Concise Streaming Output

Keep file/binary boundaries, per-test progress and final summaries visible during long runs. Avoid:

- `command | tail -N`: output is buffered until EOF, hiding progress versus a hang/crash.
- `command &> /dev/null`: no diagnostic trail; failure forces another run.
- Background work without a reachable log.
- Counting through `grep -c` / `wc -l`, which hides names, progress and timing distribution.
- Suppressing `Running <file>` boundaries in multi-binary suites.

If a run is silent, inspect buffering, process activity and timeout before calling it stalled or stopping it.

### Default Reporter

Keep the project's output defaults when suitable; otherwise these commands provide concise progress:

| Framework | Command |
| --- | --- |
| Vitest | `vitest run --reporter=dot` |
| pytest | `pytest -q --tb=short` |
| cargo nextest | `cargo nextest run` |
| cargo test | `cargo test -q` |

### Investigate A Failure

Only rerun with verbose output when the failure needs more context. Target the failing test by name:

| Framework | Command |
| --- | --- |
| Vitest | `vitest run --reporter=verbose -t "<test name>"` |
| pytest | `pytest -v --tb=long -k "<test name>"` |
| cargo nextest | `cargo nextest run --no-capture "<test name>"` |
| cargo test | `cargo test "<test name>" -- --nocapture` |

## Retain Output And Capture The Real Result

Choose a unique task-owned log path before launch. For Bash/Zsh, stream and retain with:

```bash
(set -o pipefail; command 2>&1 | tee "$run_log")
```

Preserve the pipeline status immediately; without `pipefail`, successful `tee` can hide test failure. Other shells need their supported equivalent for the actual test status.

Background execution needs a runner/tool that retains a process handle and final exit status. In one owning shell:

```bash
command > "$run_log" 2>&1 & run_pid=$!
# Inspect progress, then collect the child's result in this owning shell.
if wait "$run_pid"; then run_status=0; else run_status=$?; fi
```

Report/propagate `run_status`. Another tool-call shell cannot necessarily `wait` for this child; use the tool's session handle instead. Logs and successful launch alone do not prove success. Keep the process identity and teardown ownership required by the entrypoint.

For multi-binary runners (Rust integration scripts, E2E harnesses), surface each current binary/file as it starts, not just an end summary.

## Optional Repetition Calculation

First apply the entrypoint's risk, scope, count, budget, owner and stop decisions; no probability formula authorizes a loop. For independent runs at a constant assumed failure rate `p`, `n` runs detect at least one failure with probability `1 - (1 - p)^n`. State those assumptions; do not claim that confidence for correlated runs or an unknown rate. There is no default count, including 100. A run cut short by budget remains incomplete evidence, not a pass.
