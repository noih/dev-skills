---
name: nodejs
description: "Use when: writing, reviewing, debugging, or refactoring Node.js code, including modules, async flows, filesystem/process usage, runtime config, and server-side JavaScript."
user-invocable: false
---

# Node.js Conventions

## Module System

- Prefer ESM (`import`/`export`) — set `"type": "module"` in `package.json`
- Use `import.meta.url` and `import.meta.dirname` (Node 21.2+) instead of `__dirname` / `__filename`
- For CJS interop, use `createRequire(import.meta.url)` when importing CJS-only packages
- Follow project convention — if the project uses CJS, stay consistent

## Async Patterns

- **`async`/`await` over callbacks** — use promise-based APIs (`fs/promises`, `timers/promises`)
- **Handle rejections at the responsible boundary** — let awaited failures propagate to the existing caller or framework error handler. Catch locally for recovery, cleanup, or useful context, not just to rethrow. Detached promises need an explicit error handler.
- **`Promise.all` for concurrent work** — run independent async operations in parallel, not sequentially
- **`Promise.allSettled`** when all results are needed regardless of individual failures
- **Don't mix async paradigms** — don't mix callbacks with promises/async-await in the same flow. Using `.catch()` for inline error transformation with `await` is fine
- **Convert callback APIs** — use `util.promisify` to wrap callback-only packages into promises

## Error Handling

- **Operational vs programmer errors** — operational (network failure, invalid input) are expected and handled; programmer errors (TypeError, null ref) should crash and be fixed
- **`process.on('unhandledRejection')`** — log and exit; unhandled rejections indicate missing error handling
- **Custom error classes** — extend `Error` with meaningful names and properties (`statusCode`, `code`)
- **Error context** — include relevant context (userId, requestId) when logging; don't log just the message

## Event Loop

- **Never block the event loop** — no synchronous I/O (`readFileSync`, `execSync`) in request handlers or hot paths
- **Sync is acceptable at startup** — reading config, loading certs; only problematic in running server code
- **CPU-intensive work** → `worker_threads` or external process; keep main thread responsive

## File System & Paths

- **`node:fs/promises`** over callback-based `fs`
- **`node:path`** for all path operations — `path.join()`, `path.resolve()`, never string concatenation
- **Streams for large files** — use `createReadStream` / `createWriteStream` with `pipeline()`; don't read entire large files into memory

## Process & Signals

- **Graceful shutdown** — listen for `SIGTERM` and `SIGINT`, stop accepting new work, drain connections, then exit
- **Cleanup resources** — close database connections, flush logs, release file handles before exit
- **Exit codes** — `0` for success, `1` for errors

## Environment & Config

- **`process.env` for configuration** — never hardcode environment-specific values
- **Validate at startup** — check required environment variables exist and are valid before serving
- **No `.env` in production** — use the platform's secret management; `.env` is for local development only

## Date & Time

- Native `Date` and `Intl` are sufficient for timestamp comparisons, standard ISO serialization, and locale formatting.
- For calendar arithmetic, custom parsing, or timezone rules, follow the project's existing library. When a library is needed and none is chosen, I prefer **`date-fns`** for backend work. Do not add it just to replace a simple native operation.

## Built-in Modules

- **Prefer `node:` prefix** — `import fs from 'node:fs'` makes it explicit this is a built-in, not an npm package
- **Prefer built-in over npm** — use `node:crypto`, `node:util`, and other standard modules before reaching for third-party alternatives. Test framework selection belongs to the `testing` skill
