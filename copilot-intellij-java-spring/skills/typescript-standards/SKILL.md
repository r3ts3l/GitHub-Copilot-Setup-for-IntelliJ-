| name | description |
| --- | --- |
| typescript-standards | Apply TypeScript best practices to the current file or selection, covering type safety, generics, interfaces, and compiler strictness. |

# TypeScript Standards

Your goal is to review the current code and apply TypeScript best practices, making targeted edits where the code weakens type safety or deviates from modern patterns.

## Type Safety — Fix Any Violations

- Ban `any` — replace with a specific type, `unknown`, or a generic. If `any` is truly necessary, add a comment explaining why.
- Replace `as SomeType` type assertions with proper type narrowing using `typeof`, `instanceof`, or a type guard function.
- Never use non-null assertion (`!`) without a preceding null check — replace with optional chaining or an explicit guard.
- Flag `object` and `{}` as types — replace with a specific interface or `Record<string, unknown>`.
- Enable and respect `strict` mode in `tsconfig.json` — flag any settings that disable strictness (`noImplicitAny: false`, `strictNullChecks: false`).

## Interfaces and Types — Fix Any Violations

- Use `interface` for object shapes that may be extended; use `type` for unions, intersections, and aliases of primitives.
- Never use `I` prefix for interfaces (e.g., `IUser`) — just `User`.
- Prefer `readonly` on properties that should not be mutated after construction.
- Use `Partial<T>`, `Required<T>`, `Pick<T, K>`, `Omit<T, K>` utility types instead of redefining subsets of existing types.
- Flag duplicate type definitions — extract shared shapes into a shared types file.

## Generics — Fix Any Violations

- Replace repeated overloads that differ only in type with a single generic function.
- Constrain generics when the type must satisfy a shape: `<T extends User>` not unconstrained `<T>`.
- Avoid unnecessary generic parameters that always resolve to the same type at call sites — simplify to a concrete type.

## Enums — Fix Any Violations

- Prefer `const enum` for purely numeric or string constants used only at compile time.
- Replace string literal unions (`'admin' | 'user' | 'guest'`) with a `const` object + `typeof` pattern when the values need to be iterated at runtime.
- Flag numeric enums with no explicit values — always assign explicit string or numeric values to prevent accidental reordering bugs.

## Functions — Fix Any Violations

- All function parameters and return types must be explicitly typed — flag implicit `any` returns.
- Use function overloads for functions that accept meaningfully different argument shapes rather than a union of all possible types in a single signature.
- Prefer `unknown` over `any` for catch clause bindings: `catch (err: unknown)`.

## Async Patterns — Fix Any Violations

- All `async` functions must have a typed return: `Promise<User>` not `Promise<any>`.
- Flag untyped `fetch` responses — always assert or validate the response shape before use.
- Use `Promise.allSettled()` when multiple promises should run in parallel and individual failures should be handled separately.

## Imports — Fix Any Violations

- Use `import type` for type-only imports to avoid runtime overhead: `import type { User } from './types'`.
- Flag `require()` calls — replace with ES module `import`.
- Flag unused imports — remove them.

## Output

- Show each change made with a brief explanation of the rule it satisfies.
- List any remaining issues the developer must address manually.
