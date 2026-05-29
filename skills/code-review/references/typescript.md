# TypeScript and frontend

Read this when the code under review is TypeScript, especially React frontends. Apply it on top of the architecture and code lenses in SKILL.md.

## Type discipline

The whole value of TypeScript is the compiler stopping bugs before runtime. Most quality problems here are places where someone quietly turned that off.

- **Strict mode on.** `strict: true` in tsconfig. Without it, half the guarantees evaporate and you have JavaScript with extra steps.
- **`any` is a hole in the type system.** It disables checking for everything it touches and spreads. When you don't know a type, reach for `unknown` and narrow it, not `any`.
- **Casts (`as`) are you lying to the compiler.** Sometimes necessary at a boundary, but a casual `as SomeType` to make an error go away just moves the failure to runtime. Each one is a small finding.
- **Make illegal states unrepresentable.** The frontend version of primitive obsession is a component with `isLoading`, `isError`, `data`, and `error` all optional, which allows "loading and error at the same time" and a dozen other impossible combinations. Model it as a discriminated union: a state that is *either* loading *or* error-with-message *or* success-with-data. The compiler then forces you to handle each, and the impossible cases can't compile.

## Boundaries in the frontend

- **Separate data-fetching from presentation.** A component that fetches, transforms, and renders all at once can't be tested or reused. Push fetching into hooks or a data layer; keep components about rendering.
- **Validate data at the edge.** TypeScript types are erased at runtime. The compiler believing an API returns `User` does nothing if the backend returns something else. Parse and validate responses at the boundary with a runtime validator (Zod is the common choice). Types alone do not protect you from a service that lies or a schema that drifted.
- **Server state is not client state.** Data fetched from an API (cached, refetched, invalidated) is a different animal from UI state (is this menu open). Conflating them, hand-rolling fetch-and-cache in `useState` and `useEffect`, is a frequent architecture smell. A query library (TanStack Query, SWR) exists for the server half.

## React-specific traps

- **`useEffect` as a hammer.** Effects are for synchronizing with something outside React (the DOM, a subscription, a network request). They are not for computing values from props and state (do that during render or with `useMemo`) and not for responding to user events (do that in the handler). React's own guidance is "you might not need an effect," and most overuse traces back to ignoring it.
- **Dependency arrays.** A wrong or missing dependency array gives you stale closures: the effect captures old values and quietly uses them. The lint rule is right far more often than the developer overriding it.
- **Index as `key`.** Using array index as a list key breaks reconciliation when the list reorders or items are inserted, producing wrong state attached to wrong rows. Use a stable id.
- **Unstable references as props.** A new object, array, or function created inline every render defeats memoization and triggers re-renders down the tree. Matters when it's measurable, not as a blanket rule.
- **Async effects without cleanup.** Fire a fetch in an effect, navigate away fast, the response lands on an unmounted component or a stale request wins a race. Use an abort signal or an ignore flag in the cleanup.

## Language footguns

- **`==` vs `===`.** Use `===`. Loose equality coerces and surprises (`0 == ''`, `null == undefined`).
- **Money in floats.** Same as everywhere. `0.1 + 0.2 !== 0.3`. Use integer cents or a decimal library.
- **Mutating props or state directly.** React state is immutable by contract; mutating it skips re-renders and causes ghosts. Build new objects and arrays.
- **Unhandled promise rejections.** An `async` function whose rejection nobody catches fails silently or crashes depending on the runtime.

## Accessibility is correctness, in proportion

Frontend quality is not only the code. A `<div onClick>` styled to look like a button is not a button: no keyboard, no focus, no screen-reader role. That is a correctness bug, not a nicety. Flag the egregious ones (clickable divs, missing labels on inputs, focus traps), and keep it proportional. Not every review is a full a11y audit, but a button that isn't a button always counts.

## Testing

- Test what the user experiences. The Testing Library philosophy is to query by role and text, the way a person finds things, not by component internals or test ids that couple the test to the implementation.
- Mock at the network boundary (MSW), not by stubbing the modules under test. Stubbing internals tests the mock.
- Snapshot-everything tests rot into noise that everyone clicks past. Snapshot deliberately, not reflexively.

## Performance

- **Bundle size.** `import _ from 'lodash'` pulls the whole library; import the function. Watch what ships to the browser.
- **Big lists** need virtualization once they're long; rendering ten thousand DOM nodes is its own bug.
- **Memoization, judged not reflexed.** `useMemo` and `useCallback` everywhere is its own smell: it adds cost and complexity for re-renders that were never a problem. Measure, then memoize the hot path.
- **Fetch waterfalls.** Requests that could run in parallel chained one after another make pages feel slow. Look for the sequential await that didn't need to be sequential.

## Frontend smells, quick list

- `any` creeping in, habitual `as` casts, giant components, `useEffect` doing work that belongs in render or a handler, deriving state into more state instead of computing it, and untyped or unvalidated API boundaries.
