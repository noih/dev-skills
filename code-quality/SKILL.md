---
name: code-quality
description: "Use when: writing, reviewing, refactoring, or designing code in any language, especially to evaluate simplicity, readability, naming, abstractions, error handling, maintainability, and code comments."
user-invocable: false
---

# Code Quality

These are personal defaults. Follow explicit task constraints and existing project conventions; apply guidance to the requested change, not as a reason for unrelated cleanup.

## Quality Principles

Correctness comes first. When efficiency differences are minor or theoretical, prefer readability and simplicity over clever or compressed code. When efficiency differences are large enough to matter in realistic usage, prioritize the more efficient approach while keeping the code as understandable as the constraints allow.

### Avoid

- **Poor naming** — Vague names (`data`, `info`, `temp`, `result`), excessive abbreviations (`usr`, `mgr`), verbose names that add noise, or names that don't match actual behavior. Names should convey purpose without reading the implementation
- **Unnecessary defensive code** — Paranoid null checks inside system boundaries. Only validate at system boundaries (user input, external APIs)
- **Over-abstraction** — Wrappers, helpers, or factories that add indirection without clarifying a responsibility or protecting an invariant. Similar-looking code alone does not justify a shared abstraction
- **Template patterns** — Design patterns (Factory, Strategy, etc.) where simple direct code suffices
- **Inefficient algorithms** — O(n²) when O(n) is achievable, unnecessary iterations, missing hash-based lookups
- **Poor error handling** — Silently swallowed errors, overly broad catch blocks, missing error context
- **Floating-point money** — Never use native floating-point for monetary calculations (IEEE 754 precision issues). Always use an arbitrary-precision decimal library. See language-specific skills for package choices
- **Date/time handling** — Native APIs are enough for timestamps, standard ISO serialization, comparisons, and locale formatting. For calendar arithmetic, custom parsing, or timezone rules, use the project's date library; runtime/framework skills specify my preferred packages when a library is needed. Avoid handwritten calendar and timezone logic.
- **Magic values** — Unexplained numbers and strings; use named constants
- **Dead code** — Unused imports, variables, functions, unreachable code. Don't comment out and keep — delete it (version control has history)
- **Deep nesting** — Nested control flow that obscures the behavior. Use early returns, guard clauses, or extraction when they make the flow easier to follow
- **Long functions** — Mixed responsibilities that are hard to understand together. Length and section comments are signals to inspect, not automatic reasons to split
- **Hidden side effects** — Functions that modify external state, send requests, or write files without the name indicating it. Side effects should be explicit, predictable, and concentrated at clear boundary layers

### Acceptable when

- Meaningful error handling with useful context
- Necessary validation at system boundaries
- Abstractions that clarify a stable responsibility or protect an invariant, even with one caller
- Performance optimizations backed by actual needs
- Idiomatic patterns for the language/framework

## Before Simplifying

Before removing a structure, inspect callers, tests, and relevant history to understand why it exists. Preserve outputs, side effects and their ordering, errors, and edge cases. Counts and nesting depth are signals to inspect, not refactoring gates.

## Comment Conventions

Comments preserve intent, constraints, and non-obvious behavior. Prefer clearer code to syntax narration; keep explanations that a rename cannot replace. Use the `comments` skill when comment conventions need closer attention.
