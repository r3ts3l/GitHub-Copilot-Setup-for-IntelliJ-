| name | description |
| --- | --- |
| java-standards | Apply Java 21 coding standards and modern language features to the current file or selection. |

# Java 21 Coding Standards

Your goal is to review the current code and apply Java 21 standards, making targeted edits where the code deviates from best practices.

## Modern Java 21 Features — Use These

- **Records** for DTOs and immutable data carriers — replace classes that are pure data holders with no behaviour.
- **Sealed classes** for restricted type hierarchies — replace open class hierarchies where all subtypes are known at compile time.
- **Pattern matching** for `instanceof` and `switch` expressions — replace verbose `instanceof` + cast patterns.
- **Text blocks** for multi-line strings (SQL, JSON templates) — replace string concatenation.
- **Virtual threads** for I/O-bound concurrent operations — use `Executors.newVirtualThreadPerTaskExecutor()`.

## Best Practices — Fix Any Violations

- Use try-with-resources for all `AutoCloseable` resources.
- Use `.equals()` or `Objects.equals()` for object comparison — never `==` for non-primitives.
- Extract repeated literals into named constants.
- Keep method parameters to 3 or fewer; group extras into a value object or record.
- Remove dead code — never leave it commented out.
- Reduce cognitive complexity: extract methods, prefer polymorphism over deeply nested conditionals.

## Build Verification

After making changes, confirm the code would pass `mvn clean verify`. Flag any issues you cannot resolve automatically.

## Static Analysis

Flag any SonarLint/SonarQube, Checkstyle, or PMD violations detected and explain how to fix them.

## Output

- Show each change made with a brief explanation of the rule it satisfies.
- List any remaining issues the developer should address manually.
