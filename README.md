# GitHub Copilot Setup for IntelliJ — Java 21 + Spring Boot API Development

> A curated selection from [github/awesome-copilot](https://github.com/github/awesome-copilot) tailored for your stack: **Java 21, Spring Boot, IntelliJ IDEA, API development, and PR reviews.**

---

## Quick Reference — What Goes Where

```
your-project/
├── .github/
│   ├── copilot-instructions.md          ← Global custom instructions (always active)
│   ├── instructions/
│   │   ├── java.instructions.md         ← Java coding standards
│   │   ├── springboot.instructions.md   ← Spring Boot conventions
│   │   ├── code-review.instructions.md  ← PR review standards
│   │   └── security.instructions.md     ← OWASP security rules
│   ├── prompts/
│   │   ├── java-springboot.prompt.md    ← Spring Boot best practices prompt
│   │   ├── java-junit.prompt.md         ← JUnit 5 testing prompt
│   │   ├── review-and-refactor.prompt.md ← Code review + refactor prompt
│   │   ├── java-refactoring.prompt.md   ← Extract method refactoring
│   │   └── conventional-commit.prompt.md ← Commit message helper
│   └── agents/
│       ├── reviewer.agent.md            ← PR/code review agent
│       ├── security-reviewer.agent.md   ← Security-focused reviewer
│       ├── debug.agent.md               ← Systematic debugging agent
│       └── principal-engineer.agent.md  ← Senior engineering guidance
```

---

## Part 1: Instructions (Always-On Context)

Instructions are **always active** — Copilot reads them automatically whenever you're working in matching files. They set the coding standards Copilot follows.

### 1A. Global Custom Instructions

**File:** `.github/copilot-instructions.md`

This is the single most important file. It applies to ALL Copilot interactions in your project.

**Recommended content for your stack:**

```markdown
# Project Custom Instructions

## Stack
- Java 21 (use records, sealed classes, pattern matching, virtual threads where appropriate)
- Spring Boot 3.x with Maven
- Spring Data JPA + PostgreSQL
- JUnit 5 + Mockito + Testcontainers for testing
- SLF4J for logging

## Code Style
- Constructor injection only (no field injection, no setter injection)
- All dependency fields are `private final`
- Organize packages by feature/domain, not by layer
- Use Java Records for DTOs and value objects
- Use `Optional<T>` instead of returning null
- Use `var` only when the type is obvious from the right-hand side
- Use `List.of()`, `Map.of()`, `Stream.toList()` for immutable collections
- Keep methods under 20 lines; extract helpers for readability
- Follow Google Java Style Guide naming conventions

## API Design
- Use DTOs for request/response — never expose JPA entities
- Validate inputs with JSR-380 annotations (@Valid, @NotNull, @Size)
- Global exception handling via @ControllerAdvice
- Return proper HTTP status codes (201 for creation, 204 for delete, etc.)
- Use `ResponseEntity<T>` for explicit control

## Testing
- Every service method needs a unit test
- Use @WebMvcTest for controller tests, @DataJpaTest for repository tests
- Follow Arrange-Act-Assert pattern
- Descriptive test names: `methodName_shouldExpectedBehavior_whenCondition`
- Use AssertJ for fluent assertions

## Security
- Parameterized queries only (Spring Data JPA handles this)
- Never hardcode secrets — use environment variables or Spring profiles
- Validate all user inputs
- Use BCrypt for password encoding

## Logging
- Use SLF4J with parameterized messages: `log.info("User {} created", userId)`
- Never use System.out.println()
- Log at appropriate levels: ERROR for failures, WARN for recoverable issues, INFO for business events, DEBUG for technical details

## Comments
- Write self-documenting code; comment only the WHY, never the WHAT
- Use Javadoc for public APIs
- Use TODO/FIXME annotations for tracked issues
```

### 1B. Java Instructions

**File:** `.github/instructions/java.instructions.md`

```markdown
---
description: "Java 21 coding standards and best practices"
globs: "**/*.java"
---

# Java Development Standards

## Modern Java 21 Features — Use These
- **Records** for DTOs and immutable data carriers
- **Sealed classes** for restricted type hierarchies
- **Pattern matching** for instanceof and switch expressions
- **Text blocks** for multi-line strings (SQL, JSON templates)
- **Virtual threads** for I/O-bound concurrent operations (via `Executors.newVirtualThreadPerTaskExecutor()`)

## Best Practices
- Use try-with-resources for all `AutoCloseable` resources
- Use `.equals()` or `Objects.equals()` for object comparison, never `==`
- Extract repeated literals into named constants
- Keep method parameters to 3 or fewer; group into value objects if more
- Remove dead code — don't comment it out
- Reduce cognitive complexity: extract methods, use polymorphism over nested conditionals

## Build Verification
- Run `mvn clean verify` after changes
- All tests must pass before committing

## Static Analysis
- Address SonarLint/SonarQube warnings proactively
- Treat Checkstyle/PMD violations as compile errors in CI
```

### 1C. Spring Boot Instructions

**File:** `.github/instructions/springboot.instructions.md`

```markdown
---
description: "Spring Boot API development guidelines"
globs: "**/*.java,**/*.yml,**/*.yaml,**/*.properties"
---

# Spring Boot Standards

## Dependency Injection
- Constructor injection for required dependencies
- Fields are `private final`
- No `@Autowired` annotation on constructors (implicit with single constructor)

## Configuration
- Use `application.yml` over `.properties`
- Use Spring Profiles: `application-dev.yml`, `application-prod.yml`
- Use `@ConfigurationProperties` for type-safe config binding
- Never hardcode secrets

## Code Organization
- Package by feature: `com.example.app.order`, `com.example.app.user`
- Thin controllers → delegate to services
- Business logic in `@Service` classes only
- Services must be stateless
- Use `@Transactional` on service methods

## API Layer
- Use `@RestController` + `@RequestMapping`
- DTOs for input/output — never expose entities
- `@Valid` on request body parameters
- Global error handling with `@ControllerAdvice`
- Use `ResponseEntity<T>` for HTTP status control

## Data Layer
- Extend `JpaRepository` for standard CRUD
- Use DTO projections for read queries (fetch only what you need)
- Use `@Query` with JPQL for complex queries
- Paginate large result sets with `Pageable`

## Logging
- SLF4J only: `private static final Logger log = LoggerFactory.getLogger(MyClass.class);`
- Parameterized messages: `log.info("Processing order {}", orderId);`

## Testing
- `@SpringBootTest` for full integration tests
- `@WebMvcTest` for controller slice tests
- `@DataJpaTest` for repository tests
- Testcontainers for database integration tests
```

### 1D. Code Review Instructions

**File:** `.github/instructions/code-review.instructions.md`

```markdown
---
description: "Code review standards and PR review guidelines"
globs: "**/*"
---

# Code Review Standards

## Priority Levels
- **CRITICAL** (block merge): Security vulnerabilities, logic errors, data loss risks, breaking changes
- **IMPORTANT** (discuss): Missing tests, performance issues, architecture deviations, code quality
- **SUGGESTION** (non-blocking): Readability improvements, best practices, documentation gaps

## Review Principles
- Be specific — reference exact lines and provide examples
- Explain the WHY and the impact of each issue
- Suggest fixes with corrected code
- Acknowledge good patterns when you see them
- Group related comments together

## Quality Checklist
- [ ] Methods under 30 lines, focused on single responsibility
- [ ] No magic numbers — use named constants
- [ ] DRY — no duplicated logic
- [ ] Proper error handling (no silent catches, meaningful messages)
- [ ] Input validation on all external inputs
- [ ] Parameterized queries (no string concatenation in SQL)
- [ ] Auth/authz checks where needed
- [ ] Unit tests for new/changed logic
- [ ] Descriptive test names, Arrange-Act-Assert pattern
- [ ] No N+1 queries — use JOIN FETCH or projections
- [ ] Resources properly closed (try-with-resources)
- [ ] Proper use of HTTP status codes
- [ ] No secrets or credentials in code
- [ ] Public APIs documented with Javadoc
```

### 1E. Security Instructions

**File:** `.github/instructions/security.instructions.md`

```markdown
---
description: "OWASP-based security coding standards"
globs: "**/*"
---

# Security Standards (OWASP Top 10)

## A01: Broken Access Control
- Enforce least privilege — deny by default
- Validate authorization on every endpoint
- Never rely on client-side access control

## A02: Cryptographic Failures
- Use BCrypt/Argon2 for password hashing (never MD5/SHA-1)
- HTTPS everywhere
- Encrypt sensitive data at rest (AES-256)
- Load secrets from environment variables or vault, never hardcode

## A03: Injection
- Use Spring Data JPA or `NamedParameterJdbcTemplate` (parameterized queries)
- Never concatenate user input into SQL, JPQL, or OS commands
- Validate and sanitize all inputs

## A05: Security Misconfiguration
- Disable verbose error messages in production
- Set security headers (CSP, HSTS, X-Content-Type-Options)
- Keep dependencies updated; run `mvn dependency-check:check`

## A07: Authentication Failures
- Regenerate session IDs after login
- Use HttpOnly, Secure, SameSite=Strict cookies
- Implement rate limiting on auth endpoints

## General
- Document security measures in code comments
- Flag security issues as CRITICAL in code reviews
- Use `@PreAuthorize` / `@Secured` for method-level security
```

---

## Part 2: Prompts (On-Demand Tasks)

Prompts are **manually triggered** — you invoke them from Copilot Chat when you need to perform a specific task. In IntelliJ, you type `#` in Copilot Chat and select the prompt.

### 2A. Spring Boot Best Practices

**File:** `.github/prompts/java-springboot.prompt.md`

```markdown
---
description: "Get best practices for developing Spring Boot APIs"
mode: "agent"
tools: ["changes", "codebase", "editFiles", "problems", "search"]
---

# Spring Boot Best Practices

Your goal is to help me write high-quality Spring Boot applications.

## Review Focus Areas
- **Project Structure**: Feature-based packages, proper use of starters
- **DI**: Constructor injection, immutable fields
- **Configuration**: YAML, profiles, @ConfigurationProperties, no hardcoded secrets
- **Controllers**: RESTful design, DTOs, @Valid, @ControllerAdvice error handling
- **Services**: Stateless, @Transactional, business logic encapsulation
- **Repositories**: Spring Data JPA, DTO projections, custom @Query
- **Logging**: SLF4J with parameterized messages
- **Testing**: JUnit 5, Mockito, @WebMvcTest, @DataJpaTest, Testcontainers
- **Security**: Spring Security, BCrypt, input sanitization
```

### 2B. JUnit 5 Testing

**File:** `.github/prompts/java-junit.prompt.md`

```markdown
---
description: "Generate JUnit 5 tests following best practices"
mode: "agent"
tools: ["changes", "codebase", "editFiles", "problems", "search"]
---

# JUnit 5 Testing Best Practices

Help me write effective unit tests with JUnit 5.

## Standards
- Test class suffix: `Test` (e.g., `OrderServiceTest`)
- Naming: `methodName_shouldExpectedBehavior_whenCondition`
- Follow Arrange-Act-Assert pattern
- Use `@DisplayName` for readable test names
- Use `@BeforeEach` / `@AfterEach` for setup/teardown
- One behavior per test method
- Tests must be independent and idempotent

## Data-Driven Tests
- `@ParameterizedTest` with `@ValueSource`, `@MethodSource`, `@CsvSource`
- `@EnumSource` for enum-based tests

## Assertions
- Prefer AssertJ (`assertThat(...)`) for fluent assertions
- Use `assertThrows` for exception testing
- Group related assertions with `assertAll`

## Mocking
- Use Mockito with `@Mock` and `@InjectMocks`
- Mock only external dependencies, not the class under test
- Use `@ExtendWith(MockitoExtension.class)`

## Test Organization
- `@Tag("unit")` vs `@Tag("integration")` for filtering
- `@Nested` inner classes for grouping related tests
```

### 2C. Review and Refactor

**File:** `.github/prompts/review-and-refactor.prompt.md`

```markdown
---
description: "Review and refactor code according to project standards"
mode: "agent"
tools: ["changes", "codebase", "editFiles", "problems", "search"]
---

# Code Review and Refactor

You're a senior Java/Spring Boot engineer. Your task:

1. Read all coding guidelines in `.github/instructions/*.md` and `.github/copilot-instructions.md`
2. Review the current code carefully against those standards
3. Refactor where needed to align with the guidelines
4. Keep existing file structure intact
5. Ensure all tests still pass after changes
```

### 2D. Java Refactoring — Extract Method

**File:** `.github/prompts/java-refactoring.prompt.md`

```markdown
---
description: "Refactor Java methods using Extract Method pattern"
mode: "agent"
tools: ["changes", "codebase", "editFiles", "problems", "search"]
---

# Extract Method Refactoring

You are an expert in refactoring Java methods.

## Thresholds for Refactoring
- LOC (Lines of Code) > 15
- Number of Statements > 10
- Cyclomatic Complexity > 10

## Task
1. Analyze methods and identify those exceeding thresholds
2. Identify extractable code blocks
3. Extract into new methods with descriptive names
4. Add a one-line comment above each new method describing its purpose
5. Do not remove any functionality
6. Output complete, compilable Java 21 code
```

### 2E. Conventional Commits

**File:** `.github/prompts/conventional-commit.prompt.md`

```markdown
---
description: "Generate conventional commit messages"
mode: "agent"
tools: ["changes"]
---

# Conventional Commit Message Generator

## Workflow
1. Run `git status` and `git diff --staged` to analyze changes
2. Determine the commit type: feat, fix, refactor, test, docs, chore, perf, ci
3. Identify the scope (affected module/feature)
4. Write a concise description in imperative mood
5. Add body if changes are significant
6. Add footer for breaking changes or issue references

## Format
```
<type>(<scope>): <description>

[optional body]

[optional footer]
```

## Examples
- `feat(order): add order cancellation endpoint`
- `fix(auth): prevent session fixation on login`
- `refactor(user): extract address validation to value object`
- `test(payment): add integration tests for refund flow`
```

---

## Part 3: Agents (Specialized AI Personas)

Agents are **specialized personas** you invoke in Copilot Chat for complex multi-step tasks. You reference them with `@agent-name` in the chat.

### 3A. Code Reviewer Agent

**File:** `.github/agents/reviewer.agent.md`

```markdown
---
name: "Code Reviewer"
description: "Thorough code review with prioritized feedback for Java/Spring Boot"
tools: ["changes", "codebase", "search", "problems"]
---

# Code Reviewer

You are a senior Java/Spring Boot code reviewer. Your job is to review pull requests thoroughly.

## Review Process
1. **Scope**: Identify all changed files using the changes tool
2. **Analyze**: Review each change against project standards in `.github/instructions/`
3. **Categorize findings**:
   - CRITICAL (block merge): Security issues, logic errors, data loss, breaking changes
   - IMPORTANT (discuss): Missing tests, performance, architecture deviations
   - SUGGESTION (non-blocking): Readability, best practices, documentation

## Review Checklist
- Correct use of constructor injection and immutable fields
- DTOs used (no entity exposure in APIs)
- Input validation with @Valid
- Proper error handling (@ControllerAdvice, meaningful messages)
- No N+1 queries
- Tests for new/changed logic
- No hardcoded secrets or magic numbers
- Proper logging (SLF4J, parameterized)
- Clean separation: thin controllers, business logic in services
- Transactions at service layer

## Output Format
For each finding:
```
**[PRIORITY] Category: Title**
File: `path/to/File.java`, line X
Issue: description
Impact: why this matters
Fix: suggested correction with code example
```
```

### 3B. Security Reviewer Agent

**File:** `.github/agents/security-reviewer.agent.md`

```markdown
---
name: "Security Reviewer"
description: "OWASP-focused security review for Java/Spring Boot APIs"
tools: ["changes", "codebase", "search", "problems"]
---

# Security Reviewer

Review code for security vulnerabilities with focus on OWASP Top 10.

## Step 1: Classify Risk
- **High**: Authentication, authorization, payment, admin endpoints
- **Medium**: User data handling, external API calls
- **Low**: UI utilities, internal helpers

## Step 2: OWASP Top 10 Review
- **A01 Broken Access Control**: Missing @PreAuthorize, IDOR vulnerabilities, missing authz checks
- **A02 Cryptographic Failures**: Weak hashing, hardcoded secrets, missing encryption
- **A03 Injection**: String concatenation in queries, unsanitized inputs
- **A05 Misconfiguration**: Verbose errors in prod, missing security headers
- **A07 Auth Failures**: Session fixation, missing rate limiting, weak password policies

## Step 3: Spring Security Specific
- CSRF protection enabled for stateful endpoints
- CORS configured correctly (not wildcard in production)
- Method-level security with @PreAuthorize
- Password encoding with BCryptPasswordEncoder
- Security filter chain properly configured

## Output
For each finding:
- Severity: CRITICAL / HIGH / MEDIUM / LOW
- OWASP category
- Vulnerable code snippet
- Secure alternative with code example
- Impact assessment
```

### 3C. Debug Agent

**File:** `.github/agents/debug.agent.md`

```markdown
---
name: "Debugger"
description: "Systematic debugging for Java/Spring Boot applications"
tools: ["codebase", "search", "editFiles", "problems", "runTests", "terminalLastCommand"]
---

# Debug Mode

You are a systematic debugger. Follow this structured process:

## Phase 1: Assess
- Read error messages and stack traces
- Identify expected vs actual behavior
- Reproduce the issue by running tests

## Phase 2: Investigate
- Trace the execution path to the root cause
- Check for: null references, incorrect assumptions, race conditions, missing config
- Search for related code with the search tool
- Review recent git changes that might have introduced the bug

## Phase 3: Fix
- Make targeted, minimal changes
- Follow existing code patterns
- Add defensive checks where appropriate
- Consider edge cases and side effects

## Phase 4: Verify
- Run the failing test to confirm the fix
- Run the broader test suite for regressions
- Test edge cases related to the fix
- Add a regression test if one doesn't exist

## Guidelines
- Be systematic — don't jump to solutions
- Make small, testable changes
- Always reproduce before fixing
- Document the root cause in your summary
```

### 3D. Principal Engineer Agent

**File:** `.github/agents/principal-engineer.agent.md`

```markdown
---
name: "Principal Engineer"
description: "Senior engineering guidance for architecture, design patterns, and technical leadership"
tools: ["changes", "codebase", "search", "editFiles", "problems", "runTests"]
---

# Principal Software Engineer Mode

You provide expert-level engineering guidance balancing craft excellence with pragmatic delivery.

## Focus Areas
- **Design Patterns**: GoF patterns, SOLID, DRY, YAGNI, KISS — applied pragmatically
- **Clean Code**: Readable, maintainable code that minimizes cognitive load
- **Test Strategy**: Clear test pyramid — unit, integration, and end-to-end
- **Quality**: Balance testability, maintainability, scalability, performance, security
- **Technical Debt**: Identify it, document consequences, create remediation plans

## When Reviewing Code
- Analyze requirements, document assumptions, identify edge cases
- Recommend the simplest design that meets requirements (no over-engineering)
- Anticipate future needs and proactively flag technical debt
- Provide clear, actionable feedback with specific improvement suggestions
```

---

## Part 4: How It All Comes Together

### File Types and Their Roles

| Type | Location | How It Works | When It Activates |
|------|----------|-------------|-------------------|
| **Custom Instructions** | `.github/copilot-instructions.md` | Global context for ALL Copilot interactions | Always — every chat, every completion |
| **Instructions** | `.github/instructions/*.instructions.md` | Scoped rules via `globs` patterns | Automatically when editing matching files |
| **Prompts** | `.github/prompts/*.prompt.md` | On-demand task templates | Manually — type `#` in Copilot Chat, select prompt |
| **Agents** | `.github/agents/*.agent.md` | Specialized AI personas with tools | Manually — type `@agent-name` in Copilot Chat |

### How They Work Together — A PR Review Example

1. You open a Java file → **Instructions** (`java.instructions.md`, `springboot.instructions.md`) activate automatically, so Copilot's suggestions already follow your standards.

2. You write a new service method → Copilot auto-completes following constructor injection, SLF4J logging, and `@Transactional` because the **custom instructions** and **Spring Boot instructions** tell it to.

3. You need tests → Open Copilot Chat, type `#`, select **java-junit** prompt. Copilot generates JUnit 5 tests following your naming conventions and AAA pattern.

4. Ready for review → Type `@reviewer` in Copilot Chat. The **reviewer agent** reads your changes, cross-references them against your instructions, and produces categorized feedback (CRITICAL/IMPORTANT/SUGGESTION).

5. Security concern → Type `@security-reviewer`. It performs an OWASP-focused audit of the changed files.

6. Commit time → Type `#`, select **conventional-commit**. Copilot analyzes your staged changes and generates a properly formatted commit message.

### Setup Steps in IntelliJ

1. **Install GitHub Copilot plugin** (if not already installed): Settings → Plugins → Search "GitHub Copilot"

2. **Create the folder structure** in your project root:
   ```
   mkdir -p .github/instructions .github/prompts .github/agents
   ```

3. **Copy the files** from this guide into the appropriate locations

4. **Commit them to your repo** — these files are meant to be version-controlled and shared with your team

5. **Use Copilot Chat** in IntelliJ (the chat panel, usually on the right sidebar):
   - For prompts: type `#` and select from the list
   - For agents: type `@` and select the agent name
   - Instructions apply automatically — no action needed

### Pro Tips

- **Team alignment**: Since these files live in `.github/`, the whole team gets the same Copilot behavior when they pull
- **Iterate**: Start with the custom instructions file, then add specific instructions as you identify patterns
- **Combine**: Use `@reviewer` agent + `code-review.instructions.md` together — the agent reads the instructions automatically
- **Version them**: Treat these files like code — review changes to them in PRs too

---

## Files Selected From awesome-copilot

Here's exactly which files from the repository I selected and why:

| Repository File | Why Selected |
|----------------|-------------|
| `instructions/java.instructions.md` | Core Java standards, records, streams, null handling |
| `instructions/springboot.instructions.md` | Spring Boot DI, config, service/repo patterns |
| `instructions/code-review-generic.instructions.md` | Universal PR review framework with priority levels |
| `instructions/security-and-owasp.instructions.md` | OWASP Top 10 security standards |
| `instructions/self-explanatory-code-commenting.instructions.md` | Comment philosophy — WHY not WHAT |
| `instructions/performance-optimization.instructions.md` | N+1 queries, caching, algorithm choices |
| `prompts/java-springboot.prompt.md` | Spring Boot best practices on demand |
| `prompts/java-junit.prompt.md` | JUnit 5 test generation |
| `prompts/review-and-refactor.prompt.md` | Full code review against instructions |
| `prompts/java-refactoring-extract-method.prompt.md` | Method extraction refactoring |
| `prompts/conventional-commit.prompt.md` | Commit message generation |
| `prompts/create-spring-boot-java-project.prompt.md` | Project scaffolding template |
| `agents/gem-reviewer.agent.md` | Security-gated review agent |
| `agents/se-security-reviewer.agent.md` | OWASP + Zero Trust security review |
| `agents/debug.agent.md` | Structured debugging workflow |
| `agents/principal-software-engineer.agent.md` | Senior engineering guidance |
| `agents/java-mcp-expert.agent.md` | Java MCP server development (bonus for advanced use) |

### Files I Skipped and Why

- `gilfoyle.agent.md` — Fun but impractical for real PR reviews (it's the Silicon Valley character who insults your code)
- `springboot-4-migration.instructions.md` — Spring Boot 4 isn't released yet; premature for now
- `java-17-to-java-21-upgrade.instructions.md` — You're already on Java 21
- Language-specific files for non-Java stacks (Python, TypeScript, Go, etc.)
- Cloud/infrastructure files (Azure, Terraform, Kubernetes) — not relevant to your API development focus
