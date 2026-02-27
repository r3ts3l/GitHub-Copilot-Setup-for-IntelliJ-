| name | description |
| --- | --- |
| java-code-review | Run a structured, prioritized code review on Java/Spring Boot changes with CRITICAL, IMPORTANT, and SUGGESTION findings. |

# Java/Spring Boot Code Review

Your goal is to perform a thorough code review on the current changes and produce structured, actionable feedback.

## Priority Levels — Assign One to Every Finding

- **CRITICAL** (block merge): Security vulnerabilities, logic errors, data loss risks, breaking API changes.
- **IMPORTANT** (must discuss): Missing tests, performance issues, architecture deviations, significant code quality problems.
- **SUGGESTION** (non-blocking): Readability improvements, best practices that aren't violations, documentation gaps.

## Review Principles

- Reference the exact file and line number for every finding.
- Explain the impact — why it matters, not just what is wrong.
- Provide a corrected code snippet for every finding.
- Acknowledge good patterns when you see them.
- Group related findings together under a shared heading.

## Checklist — Check Every Item

- Methods under 30 lines, focused on single responsibility.
- No magic numbers — named constants used throughout.
- DRY — no duplicated logic across the changeset.
- No silent catches or empty catch blocks; error messages are meaningful.
- Input validation present on all external inputs.
- No string concatenation into SQL/JPQL — parameterized queries only.
- Auth/authz checks present on all relevant endpoints.
- Unit tests exist for all new or changed logic.
- Test names follow `methodName_shouldExpectedBehavior_whenCondition`; AAA structure present.
- No N+1 queries — JOIN FETCH or DTO projections used where applicable.
- All `AutoCloseable` resources closed with try-with-resources.
- HTTP status codes are correct (201 for create, 204 for delete, 400 for validation errors, etc.).
- No secrets, tokens, or credentials in code or comments.
- Public APIs documented with Javadoc.

## Output Format

For each finding:

```
**[PRIORITY] Category: Short Title**
File: `path/to/File.java`, line X
Issue: what is wrong and why it matters
Fix:
// corrected code here
```

End with a brief summary: total findings by priority level, and an overall merge recommendation.
