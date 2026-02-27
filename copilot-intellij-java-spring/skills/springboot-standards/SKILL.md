| name | description |
| --- | --- |
| springboot-standards | Apply Spring Boot 3.x conventions and best practices to Java, YAML, and properties files. |

# Spring Boot 3.x Standards

Your goal is to review the current code and apply Spring Boot 3.x best practices, making targeted edits where the code deviates from conventions. Scan all Java files, YAML, and properties files in scope.

## Dependency Injection — Fix Any Violations

- Replace field injection (`@Autowired` on fields) with constructor injection.
- Replace setter injection with constructor injection.
- Make all injected fields `private final`.
- Remove redundant `@Autowired` from single-constructor classes (Spring injects automatically).

## Configuration — Fix Any Violations

- Convert `.properties` files to `application.yml` format.
- Ensure environment profiles exist: `application-dev.yml`, `application-prod.yml`.
- Replace manually bound config properties with `@ConfigurationProperties` classes for type safety.
- Flag any hardcoded secrets (passwords, tokens, keys) and recommend externalization via environment variables or a vault.

## Code Organization — Flag Any Violations

- Confirm feature-based packaging (`com.example.app.<feature>`) — flag layer-based structures (`controller/`, `service/`, etc.).
- Flag controllers containing business logic — controllers should be thin and delegate to services.
- Flag `@Service` classes that hold mutable state — services must be stateless.
- Flag service methods that modify data but lack `@Transactional`.

## API Layer — Fix Any Violations

- Ensure controllers use `@RestController` + `@RequestMapping`.
- Replace direct entity return types with DTOs.
- Add `@Valid` to request body parameters missing it.
- Confirm a `@ControllerAdvice` global handler exists; create a stub if missing.
- Ensure methods return `ResponseEntity<T>` for explicit HTTP status control.

## Data Layer — Flag Any Violations

- Flag repositories not extending `JpaRepository` or `CrudRepository`.
- Flag queries fetching full entities where DTO projections would reduce data transfer.
- Flag native SQL string concatenation — replace with `@Query` JPQL and named parameters.
- Flag unbounded list queries that should paginate with `Pageable`.

## Logging — Fix Any Violations

- Replace `System.out.println()` calls with SLF4J log calls at the appropriate level.
- Replace string-concatenated log messages with parameterized form: `log.info("Processing order {}", orderId)`.
- Add missing logger declarations: `private static final Logger log = LoggerFactory.getLogger(MyClass.class);`

## Output

- List every change made, grouped by the category above.
- List remaining issues the developer must address manually.
