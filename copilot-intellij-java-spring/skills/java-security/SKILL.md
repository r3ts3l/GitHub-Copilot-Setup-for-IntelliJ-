| name | description |
| --- | --- |
| java-security | Run an OWASP Top 10 security audit on Java/Spring Boot code with severity-rated findings and secure code alternatives. |

# Java/Spring Boot Security Audit (OWASP Top 10)

Your goal is to audit the current code against the OWASP Top 10 and produce a prioritized security findings report with specific fixes.

## Step 1: Classify the Code Under Review

For each file in scope, assign a risk tier before reviewing:

- **High**: Authentication, authorization, payment processing, admin endpoints, session management.
- **Medium**: User data handling, external API calls, file uploads, email sending.
- **Low**: UI utilities, internal helpers, read-only operations.

Apply stricter scrutiny to High-tier code.

## Step 2: Review Each OWASP Category

**A01 — Broken Access Control**
- Flag endpoints missing `@PreAuthorize` or `@Secured` checks.
- Look for IDOR vulnerabilities: user-supplied IDs used without ownership validation.
- Flag any access control logic that relies on client-supplied values.

**A02 — Cryptographic Failures**
- Flag password hashing with MD5, SHA-1, or plain SHA-256 — must use BCrypt or Argon2.
- Flag any hardcoded secrets, tokens, API keys, or passwords in source or config files.
- Flag sensitive data stored or logged in plaintext.
- Flag HTTP URLs where HTTPS is required.

**A03 — Injection**
- Flag any string concatenation into SQL, JPQL, HQL, or OS commands.
- Flag missing input validation or sanitization on user-supplied data.
- Flag `@Query` annotations using string interpolation instead of named parameters (`:param`).

**A05 — Security Misconfiguration**
- Flag verbose error messages that expose stack traces or internal class names in production profiles.
- Flag missing security response headers (CSP, HSTS, X-Content-Type-Options, X-Frame-Options).
- Note outdated dependencies as candidates for `mvn dependency-check:check`.

**A07 — Authentication Failures**
- Flag session IDs not regenerated after login (session fixation).
- Flag cookies missing `HttpOnly`, `Secure`, or `SameSite=Strict` attributes.
- Flag authentication endpoints without rate limiting.

## Step 3: Spring Security Specific Checks

- CSRF protection enabled for stateful (session-based) endpoints; correctly disabled for stateless JWT APIs.
- CORS not configured with a wildcard (`*`) origin in production profiles.
- `BCryptPasswordEncoder` used — not `NoOpPasswordEncoder` or weaker alternatives.
- Method-level security via `@PreAuthorize` is present on sensitive service methods.
- Security filter chain is explicitly configured, not relying on auto-configured defaults.

## Output Format

For each finding:

```
**[SEVERITY] OWASP A0X — Short Title**
File: `path/to/File.java`, line X
Vulnerable code:
// the problematic code

Issue: what is wrong and the potential impact
Fix:
// the secure alternative
```

Severity levels: **CRITICAL** / **HIGH** / **MEDIUM** / **LOW**

End with a summary table: finding count by severity and OWASP category.
