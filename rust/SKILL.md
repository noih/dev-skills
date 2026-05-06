---
name: rust
description: "Use when: writing, reviewing, debugging, refactoring, or testing Rust code, including Cargo projects, async/Tokio code, integration tests, error handling, ownership, traits, modules, and tooling."
user-invocable: false
---

# Rust Conventions

## Naming Conventions (RFC 430)

| Item | Style | Example |
|------|-------|---------|
| Crates | `snake_case` | `my_crate` (hyphens in Cargo.toml map to underscores) |
| Modules | `snake_case` | `mod auth_handler;` |
| Types (struct, enum, trait) | `PascalCase` | `HttpRequest`, `ParseError` |
| Functions, methods | `snake_case` | `fn read_file()` |
| Local variables | `snake_case` | `let user_count = 0;` |
| Constants, statics | `SCREAMING_SNAKE_CASE` | `const MAX_RETRIES: u32 = 3;` |
| Type parameters | Short `PascalCase` | `T`, `K`, `V`, `E` |
| Lifetimes | Short lowercase | `'a`, `'de` |

The compiler warns on violations — treat these warnings as errors.

### Conversion method prefixes

Follow the standard library conventions:

| Prefix | Meaning | Ownership | Example |
|--------|---------|-----------|---------|
| `as_` | Cheap reference-to-reference | Borrows `&self` | `as_str()`, `as_bytes()` |
| `to_` | Expensive conversion | Borrows `&self` | `to_string()`, `to_vec()` |
| `into_` | Ownership-consuming conversion | Takes `self` | `into_inner()`, `into_vec()` |
| `from_` | Constructor from another type | Associated fn | `String::from_utf8()` |
| `try_` | May fail (returns `Result`) | Varies | `try_from()`, `try_into()` |

## Ownership & Borrowing

- Prefer borrowing (`&T`, `&mut T`) over cloning. Only `clone()` when ownership transfer is genuinely needed
- Prefer `&str` over `&String`, `&[T]` over `&Vec<T>` in function parameters — accept the most general form
- Use `Cow<'_, str>` when a function sometimes needs to allocate and sometimes doesn't
- Avoid returning references to local data — return owned types from public API boundaries

## Error Handling

- **`Result<T, E>` everywhere**: Use `Result` for all operations that can fail. Reserve `panic!` for truly unrecoverable states (violated invariants, programmer bugs)
- **`thiserror`** for libraries: Define typed error enums with `#[derive(Error)]` so callers can match on variants
- **`anyhow`** for applications: Use `anyhow::Result` in binaries and top-level application code where error variant matching isn't needed
- **`?` operator**: Propagate errors with `?`. Avoid manual `match` on `Result` just to re-wrap or return
- **`unwrap()` / `expect()`**: Only in tests, examples, or when the invariant is provably guaranteed. Prefer `expect("reason")` over bare `unwrap()` so panics are self-documenting

## Type Safety

- **Newtype pattern**: Wrap primitive types to prevent misuse — `struct UserId(u64)` instead of raw `u64`
- **Enums over booleans**: Prefer `enum Visibility { Public, Private }` over `is_public: bool` when the meaning isn't obvious
- **Make invalid states unrepresentable**: Model states as enums so the compiler prevents illegal transitions
- **`#[must_use]`**: Add on functions whose return value should not be silently ignored

## Trait Design

- Use traits for abstraction boundaries; prefer generics (`impl Trait` / `<T: Trait>`) for static dispatch, trait objects (`dyn Trait`) for dynamic dispatch
- Implement standard traits when appropriate: `Display`, `Debug`, `Clone`, `PartialEq`, `Default`, `From`/`Into`
- Derive what you can: `#[derive(Debug, Clone, PartialEq, Eq, Hash)]` — only implement manually when custom behavior is needed

## Pattern Matching

- **Exhaustive matching**: Prefer `match` over `if let` chains when there are more than two variants — the compiler catches missing cases
- **Avoid wildcard catch-all** (`_`) on enums unless intentional — adding a new variant should trigger a compile error, not silently fall through
- **Destructure in `match` arms**: Extract fields directly — `Err(e) => ...` not `Err(err) => { let e = err; ... }`
- **`if let`**: Use for single-variant checks — `if let Some(v) = opt { ... }`. For two variants, `match` is clearer

## Iterators

- **Prefer iterator chains** over manual `for` loops with mutable accumulators: `.filter().map().collect()` is idiomatic and often optimized equally
- **Avoid `collect()` mid-chain**: Chain iterators lazily; only `collect()` at the end when you need the final collection
- **Use `iter()` / `iter_mut()` / `into_iter()`** intentionally — borrowing vs consuming the collection

## Smart Pointers

| Type | Use when |
|------|----------|
| `Box<T>` | Heap allocation needed: recursive types, large values, trait objects (`Box<dyn Trait>`) |
| `Rc<T>` | Multiple owners in single-threaded context (rare — question if the design needs it) |
| `Arc<T>` | Multiple owners across threads |
| `Cow<'a, T>` | Sometimes borrowed, sometimes owned — avoids unnecessary clones |

- Default to stack allocation. Only `Box` when the compiler requires it or the value is too large for the stack
- `Rc`/`Arc` often signal a design issue — consider if ownership can be restructured first

## Modules & Visibility

- **Default to private**: Everything is private by default. Only expose what's needed
- **`pub(crate)`**: Prefer over `pub` for items used across modules within the crate but not exported to consumers
- **`pub(super)`**: For items shared with the parent module only
- **Module file layout**: Prefer `foo.rs` + `foo/bar.rs` (Rust 2018+) over the older `foo/mod.rs` style
- **Re-exports**: Use `pub use` in `lib.rs` to create a clean public API surface, even if the internal module structure is different

## Async & Concurrency

- **Don't block the runtime**: Never call blocking I/O or CPU-heavy code in an async context. Use `spawn_blocking` for these
- **Shared state**: Use `Arc<Mutex<T>>` or `Arc<RwLock<T>>` judiciously; prefer message passing (`mpsc`, `oneshot` channels) when data flows in one direction
- **Cancellation safety**: Futures can be dropped at any `.await` point. Don't hold locks across `.await` — acquire, use, and release within a synchronous block
- **`Send` + `Sync`**: Understand the bounds. Async tasks spawned with `tokio::spawn` require `Send`. If a type isn't `Send`, restructure or use `spawn_local`

## Testing

- **Unit tests**: Place in a `#[cfg(test)] mod tests` block at the bottom of the same file
- **Integration tests**: Place in the `tests/` directory at the crate root. Each file is compiled as a separate crate — they test the public API
- **Doc tests**: Code blocks in `///` doc comments are compiled and run as tests. Keep them simple and focused
- **`#[should_panic]`**: Use for tests that verify a `panic!` occurs — include `expected = "message"` to avoid false positives
- **Test naming**: `snake_case` describing the scenario — `fn rejects_empty_input()`, not `fn test_1()`
- **Assertions**: Prefer `assert_eq!` / `assert_ne!` over `assert!(a == b)` — the error message shows both values on failure

### Cargo test performance

- Each Rust integration test file in `tests/` is a separate test binary. After production code changes, Cargo may need to relink every affected integration test binary, even though shared dependencies are not rebuilt once cached.
- Prefer prebuilding selected integration tests once with `cargo test --no-run` (or the project's documented wrapper) before executing several binaries. This pays compile/relink cost once instead of invoking Cargo separately for each file.
- When tooling needs to execute test binaries directly, get executable paths from Cargo artifact metadata instead of guessing paths under `target/`. The `target/debug/deps` directory can contain stale, duplicate, example, bench, or platform-specific artifacts.
- Do not assume a long pause before `running N tests` is test logic. It can be Cargo planning, artifact locks, compilation, linking, or test binary startup before libtest begins timing.
- Avoid sharing Tokio-runtime-bound resources across separate `#[tokio::test]` cases via a static cache. Each `#[tokio::test]` creates its own runtime by default; cache only runtime-independent data unless the project has a custom single-runtime harness.
- Avoid adding extra Cargo targets solely as wrappers around existing scripts. Every bin/example/test target can participate in Cargo planning and compilation; wrappers should provide real Rust behavior, not just hide a shell command.

### Rust integration test architecture

- Treat each `tests/*.rs` file as its own crate and binary. Choose file boundaries intentionally around coherent behavior flows, API surfaces, or integration resources instead of dumping unrelated scenarios into one large integration file.
- Pick an isolation boundary that matches the shared resource cost. For external state such as databases, ports, files, queues, or service namespaces, per-binary or per-suite isolation is often a practical middle ground between one shared environment and rebuilding everything per test case.
- Avoid per-test rebuilds of expensive external state unless cheaper boundaries cannot prevent pollution. Rust integration suites can become dominated by setup time because each binary already has compile/link/startup overhead.
- Keep static caches limited to data that survives independent async runtimes and process boundaries: names, paths, immutable fixture bytes, fingerprints, or configuration. Build pools, clients, servers, routers, temporary transactions, and app state inside the current test runtime/process.
- Pass libtest arguments after `--`, and set thread counts intentionally when tests share external resources. Do not rely on default parallelism when tests mutate the same database, filesystem namespace, ports, or singleton service.
- Keep ignored, external, destructive, sandbox, or slow e2e tests behind explicit commands. Ordinary local Rust test runs should not accidentally call external systems or mutate non-local state.

## Money (Decimal)

- Use `rust_decimal` crate for all monetary calculations — never use `f32`/`f64` for money (floating-point precision issues)
- Use `rust_decimal_macros` with `dec!()` macro for literals: `dec!(19.99)`
- Serialize with `#[serde(with = "rust_decimal::serde::str")]` to ensure JSON transmits as string, avoiding precision loss

## JSON Serialization

- `Option<T>` serializes as `null` by default. Use `#[serde(skip_serializing_if = "Option::is_none")]` when an API contract requires omitted absent fields

## Tooling

- **`cargo fmt`**: Format all code. Don't override `rustfmt` defaults without team consensus
- **`cargo clippy`**: Run with `-- -D warnings` to treat lints as errors. Fix the lint, don't `#[allow]` it unless there's a documented reason
- **`cargo audit`**: Check dependencies for known vulnerabilities
- **`cargo doc --open`**: Verify documentation renders correctly

## Performance

- Leverage zero-cost abstractions — iterators, generics, and traits compile to the same code as hand-written loops
- Avoid unnecessary heap allocations: prefer `&str` over `String`, `&[T]` over `Vec<T>` when ownership isn't needed
- Use `serde` efficiently: `#[serde(skip)]` for derived fields, `#[serde(rename_all = "camelCase")]` instead of per-field renames
- Profile before optimizing: use `cargo bench`, `criterion`, or `flamegraph`. Don't guess where the bottleneck is
