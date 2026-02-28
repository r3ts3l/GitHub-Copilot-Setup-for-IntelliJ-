| name | description |
| --- | --- |
| react-standards | Apply React best practices and modern patterns to the current file or selection, including hooks, component design, performance, and accessibility. |

# React Standards

Your goal is to review the current code and apply React best practices, making targeted edits where the code deviates from modern patterns.

## Component Design — Fix Any Violations

- Use functional components only — replace any class components with functional equivalents.
- Keep components small and focused on a single responsibility; extract sub-components when JSX exceeds ~50 lines.
- Co-locate state as close to where it is used as possible — avoid lifting state higher than necessary.
- Prefer composition over prop drilling; if props are passed through more than 2 levels, flag for Context or component restructuring.
- Use named exports for components; default exports only for page/route-level components.

## Hooks — Fix Any Violations

- Replace any class lifecycle methods (`componentDidMount`, `componentDidUpdate`, `componentWillUnmount`) with `useEffect`.
- Ensure every `useEffect` has a correct dependency array — flag missing dependencies or unnecessary ones.
- Extract repeated stateful logic into custom hooks (`use` prefix); flag any duplicated hook logic across components.
- Never call hooks inside loops, conditions, or nested functions.
- Use `useCallback` for functions passed as props to memoized children; use `useMemo` for expensive derived values — but only when there is a measurable performance reason, not by default.
- Replace `useEffect` + `useState` data fetching patterns with a dedicated fetching hook or library (e.g., `useSWR`, React Query) if data fetching is the primary concern.

## State Management — Flag Any Violations

- Use `useState` for local UI state, `useReducer` for state with multiple sub-values or complex transitions.
- Flag any direct state mutation (e.g., `state.items.push(...)`) — state must be treated as immutable.
- Flag global state stored in `useState` at the root and passed down as props across many levels — recommend Context or a state library.

## Performance — Flag Any Violations

- Flag components that re-render unnecessarily; recommend `React.memo` where a component receives stable props.
- Flag large lists rendered without virtualization (e.g., `react-window`, `react-virtual`).
- Flag images missing `loading="lazy"` or explicit `width`/`height` attributes.
- Flag inline object or array literals passed as props (creates new reference on every render): `<Comp style={{ color: 'red' }} />` — extract to a constant or `useMemo`.

## Props — Fix Any Violations

- Define prop types with TypeScript interfaces or PropTypes — flag any untyped props.
- Destructure props at the top of the component; avoid accessing `props.x` directly.
- Provide default values for optional props.
- Flag boolean props with non-boolean defaults (e.g., `isOpen={undefined}`).

## Accessibility — Fix Any Violations

- Every `<img>` must have a meaningful `alt` attribute.
- Interactive elements (`div`, `span`) used as buttons must have `role="button"` and `onKeyDown` handlers, or be replaced with `<button>`.
- Form inputs must be associated with a `<label>` via `htmlFor` / `id`.
- Flag missing `aria-*` attributes on custom interactive components.
- Ensure focus management is handled when modals or dialogs open.

## Code Quality — Fix Any Violations

- Remove `console.log` statements.
- Replace string concatenation in `className` with a utility like `clsx` or `classnames` when conditions are involved.
- Flag `useEffect` blocks with async functions defined inline — extract to a named async function inside the effect.
- Keys in lists must be stable and unique — flag use of array index as key when the list can reorder or filter.

## Output

- Show each change made with a brief explanation of the rule it satisfies.
- List any remaining issues the developer must address manually.
