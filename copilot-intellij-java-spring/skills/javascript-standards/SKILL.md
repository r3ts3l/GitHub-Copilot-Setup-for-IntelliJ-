| name | description |
| --- | --- |
| javascript-standards | Apply modern JavaScript best practices to the current file or selection, covering syntax, async patterns, error handling, and code quality. |

# JavaScript Standards

Your goal is to review the current code and apply modern JavaScript best practices, making targeted edits where the code deviates from established patterns.

## Modern Syntax — Fix Any Violations

- Replace `var` with `const` by default; use `let` only when the variable is reassigned — never `var`.
- Use arrow functions for callbacks and inline functions; use named function declarations for top-level functions.
- Use destructuring for objects and arrays instead of repeated property access: `const { id, name } = user`.
- Use template literals instead of string concatenation: `` `Hello ${name}` `` not `"Hello " + name`.
- Use optional chaining (`?.`) instead of manual null checks: `user?.address?.street`.
- Use nullish coalescing (`??`) instead of `||` when the fallback should only apply to `null` / `undefined`, not falsy values.
- Use spread syntax (`...`) for copying arrays and objects instead of `Object.assign` or `Array.concat`.
- Use shorthand property names: `{ name }` not `{ name: name }`.

## Async Patterns — Fix Any Violations

- Replace `.then()` / `.catch()` chains with `async` / `await`.
- Wrap `await` calls in `try/catch` — never leave async errors unhandled.
- Flag `await` inside loops where parallel execution is possible; replace with `Promise.all()`.
- Flag `new Promise()` wrapping an already-promise-based API — unnecessary and error-prone.
- Never mix `async/await` and `.then()` in the same function.

## Error Handling — Fix Any Violations

- Flag empty `catch` blocks — always log or rethrow the error.
- Flag `catch (e)` where `e` is never used — use `catch` without a binding if the error is intentionally ignored, and add a comment explaining why.
- Never swallow errors silently; at minimum log them with context: `console.error('Failed to load user', err)`.
- Use custom error classes that extend `Error` for domain-specific errors instead of throwing plain strings or generic objects.

## Code Quality — Fix Any Violations

- Remove all `console.log` statements not guarded by a debug flag.
- Extract magic numbers and strings into named constants at the top of the module.
- Keep functions under 20 lines; extract helpers for readability.
- Avoid deeply nested callbacks or conditionals — flatten with early returns.
- Remove commented-out code.
- Use `===` and `!==` exclusively — never `==` or `!=`.

## Modules — Fix Any Violations

- Use ES module syntax (`import` / `export`) — replace CommonJS `require()` / `module.exports` unless the project explicitly targets Node.js CommonJS.
- Prefer named exports over default exports for better refactoring and IDE support; default exports only for single-concept modules.
- Flag wildcard imports (`import * as X`) where specific named imports would be clearer.

## Objects and Arrays — Fix Any Violations

- Use `Array.isArray()` to check for arrays — never `typeof x === 'object'`.
- Prefer array methods (`map`, `filter`, `reduce`, `find`, `some`, `every`) over imperative `for` loops when transforming data.
- Flag mutation of function arguments — always work on a copy.
- Use `Object.freeze()` for constants that should never be mutated.

## Output

- Show each change made with a brief explanation of the rule it satisfies.
- List any remaining issues the developer must address manually.
