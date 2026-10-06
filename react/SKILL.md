---
name: react
description: "Use when: writing, reviewing, debugging, or refactoring React components, hooks, state management, effects, frontend behavior, styling, forms, or component tests."
user-invocable: false
---

# React Conventions

## UI Reference And Migration Scope

First identify whether the task is a new feature, a behavior migration, or exact UI/UX parity with an existing reference. Ordinary new features follow the project's normal design and do not require demo observation or source copying.

For a migration, inspect the relevant source and runnable demo, then assess permission/licensing, framework compatibility, dependencies, security, and maintenance constraints. Choose full migration, partial reuse, or necessary reimplementation. When exact parity is required and reuse is confirmed feasible, prefer moving the source components/UI and required assets/dependencies before adapting API, state, backend, or framework boundaries. Preserve the required DOM/JSX structure, control flow, labels, defaults, fields, styles, interactions, accessibility, responsive behavior, and loading/error/empty/disabled states; carry over relevant tokens, fonts, global CSS, import order, class merging, and shared primitives.

Exact parity is an outcome, not a demand for literal copying across incompatible stacks. Generic component/prop/styling preferences, native controls, shorter code, or local conventions do not justify UX drift, but unsafe backend or fixture architecture must not be copied and security/data-precision adaptations remain mandatory. Discuss a material strategy choice when it changes requirements, risk, or cost; once confirmed feasible, proceed without repeated approval.

## Naming Conventions

- **Components**: PascalCase — `UserProfile`, `OrderList`
- **Component files**: PascalCase matching the component name — `UserProfile.tsx`, `OrderList.tsx`
- **Hook files**: `useXxx.ts` — `useAuth.ts`, `useCart.ts`
- **Constants**: `UPPER_SNAKE_CASE` — `MAX_RETRY_COUNT`, `API_BASE_URL` (avoid PascalCase to prevent confusion with components)
- **Event handler props**: `onXxx` — `onClick`, `onSubmit`, `onStatusChange`
- **Event handler implementations**: `handleXxx` — `handleClick`, `handleSubmit`

## Component Design

- **One component per file** (default): Prefer one exported component per file. If the project's existing convention allows co-located internal sub-components in the same file, follow that convention — but never put multiple reusable public components in the same file
- **Function Components by default**: Use functions for ordinary components. A custom Error Boundary may require a class; reuse existing boundary support first.
- **Hook priority**: Prioritize using hooks to manage state and side effects
- **Single Responsibility**: Each component focuses on a single responsibility

## Props Design

- **Interface naming**: `{ComponentName}Props` — `UserProfileProps`, `OrderListProps`
- **Destructure in body, not in parameters**: Destructure props inside the component body, not in the function signature

```tsx
// ✅ Destructure in body
function UserProfile(props: UserProfileProps) {
  const { name, avatar, onEdit } = props
  // ...
}

// ❌ Destructure in parameters
function UserProfile({ name, avatar, onEdit }: UserProfileProps) {
  // ...
}
```

- **Children**: Declare `children: ReactNode` explicitly in the Props interface, not `PropsWithChildren`
- **Avoid prop drilling**: Use composition (children pattern) to pass components through instead of threading data through intermediate layers. Use Context or state management for truly shared state

## Conditional Rendering

- **Guard clause early return**: Keep the main render path flat, but call Hooks unconditionally before conditional returns so their order remains stable. Alternatively, move the conditional branch into a child component with its own Hooks

```tsx
function UserProfile(props: UserProfileProps) {
  const { user } = props

  if (!user) return null

  return <div>{user.name}</div>
}
```

- **Binary choice (A or B)**: Use ternary — `condition ? <A /> : <B />`
- **Show or hide**: Use `&&` with boolean confirmation — avoid the falsy trap (`0 && <Component />` renders `"0"`)

```tsx
// ✅ Explicit boolean
{count > 0 && <Badge count={count} />}
{!!items.length && <List items={items} />}

// ❌ Falsy trap
{count && <Badge count={count} />}
```

- **Multiple states / complex conditions**: Never nest ternaries. Extract to a sub render function or a separate component

## Data Fetching / Server State

- **Client state vs Server state**: Keep them separate. Client state (UI state like modal open/close, form input) uses `useState` or Zustand. Server state (API data with cache/sync needs) uses a dedicated library
- **Library preference**: Use the framework/project's existing data-loading facilities first. If client-side cache/sync needs warrant an additional library, prefer SWR for simpler cases or TanStack Query for advanced caching and mutation flows; do not add either just for a one-off fetch
- **Three-state handling**: Every data fetching component should handle loading, error, and empty states

## State Management

- **Store separation**: Separate stores by functional domains
- **Immutable updates**: Use Immer or the project's existing approach
- **Prefer Zustand + Immer**: When shared client state warrants a store and none is chosen; local state or composition is enough for simpler cases. Otherwise follow what the project uses
- **Do not update state during render**: Render must stay pure. Never mutate local state, global state, external stores, or observable state from the component body, JSX expressions, selector callbacks, or any render-time derived calculation. This applies regardless of state library. Trigger state changes from event handlers, effects, async callbacks, or explicit actions instead

## Styling

Follow the project's existing styling system — design tokens, theme, breakpoints, responsive strategy, and class naming conventions. Only choose a new approach when no existing system is in place.

- **CSS Modules `.d.ts`**: When the project has `typed:scss` or `typed:css` scripts in `package.json`, run those commands to auto-generate style type declarations — never manually create or edit `.d.ts` files for styles

## Hook Patterns

- **Custom Hooks**: Extract complex logic into custom hooks
- **Dependency Arrays**: Properly manage useEffect dependency arrays
- **Cleanup Logic**: Properly implement cleanup functions in useEffect
- **Memoization — don't wrap by default**:
  - `useMemo`: Only for computationally expensive derived values, not for simple string concatenation or object creation
  - `useCallback`: Only when passing callbacks to memoized child components or when used as a dependency in other hooks
  - If there's no measurable performance problem, don't add it

## Performance

- **`React.memo` — don't add by default**: It has comparison cost. Only use when a component receives stable props but its parent re-renders frequently, confirmed by DevTools Profiler
- **List keys**: Use stable, unique IDs from data (`id`, `uuid`). Only use array index for static, never-reordered lists (e.g., fixed nav items)
- **Code splitting**: Use `React.lazy` + `Suspense` at the route level. Only split component-level for large, non-critical blocks (heavy chart libraries, modal content)

## Date & Time

- Native `Date` and `Intl` are sufficient for timestamp comparisons, standard ISO serialization, and locale formatting.
- For calendar arithmetic, custom parsing, or timezone rules, follow the project's existing library. When a library is needed and none is chosen, I prefer **`dayjs`** for frontend work. Add only the plugins needed for the task.

## Error Handling

- **Error Boundary scope**: Wrap at route/page level so one page crashing doesn't take down the entire app. Add separate boundaries around critical isolated blocks (third-party widgets, charts, rich editors)
- **Don't over-wrap**: Not every component needs an Error Boundary — only pages and independently failing blocks
- **Implementation**: Reuse the framework's or project's existing Error Boundary support. If none exists, a small class boundary is appropriate; do not add a dependency just for a few boundaries.
