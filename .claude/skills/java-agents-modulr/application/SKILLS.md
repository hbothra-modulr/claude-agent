---
description: 'Instructions for the application module — all implementation logic'
---

## Module Purpose

This module contains **all implementation logic**: concrete controllers, services, DAOs, repositories, mappers, and configuration classes. It is **not shared** with other services and follows a package-by-feature structure.

## What belongs here
- Concrete controller implementations
- Service classes
- Repository and DAO classes
- Domain/entity models
- Mappers (service POJO → HTTP response)
- Spring configuration classes
- Liquibase migration changesets

## What does NOT belong here
- Controller interfaces or shared DTOs — those go in `api`
- Integration tests — those go in `api-test`

---

## Controller Implementations

- Controllers of `@ExternalGroup`, `@ExternalUndocumentedGroup`, or `@InternalGroup` must be concrete classes in this module with no entry in the api layer.
- Confirm which group the controller belongs to — always ask if unsure.
- Implementation classes must use `@InternalCall` or `@SystemUserOnly` — always confirm which.
- Controller class annotations: `@Validated`, `@RestController`, `@RequestMapping`, `@Tag`
- Controller implementations are responsible for executing the mapper that converts the service layer's returned POJO into an HTTP response.
- Controller methods must return dedicated response types, not generic ones.
- Never place business logic in controllers — delegate to services.
- Use `@Valid` on request body parameters.
- Use a builder for paginated responses.

---

## Service Layer

- Service methods must be focused on a single responsibility. If scope increases, refactor into more specific service classes.
- Services return POJOs and must be client agnostic.
- Services must specify security permissions and actions.

### Security and Permissions

- All external endpoints must have permission checks.
- Internal endpoints referencing business entities must perform the same permission check or document why it is safe to skip.
- Internal endpoints may increase restrictions with `@SystemUserOnly` for admin/platform-level endpoints.
- Set `@HasPermission` on endpoints to determine access conditions.
- Service implementations must verify the security context contains the appropriate Action and Permission for the protected resource.
- Verify the client has permission to view the protected resource before returning associated data (e.g., invoke get customer to verify access).
- If you need to restrict access by origin, use `@AsSystemUser` and check with `@SystemUserOnly` or `isSystemUser()`.
- `isSystemUser()` can also be applied within external endpoints for sub-section access restriction.

### Transactions

- Use `@Transactional` for create/update/delete operations.
- Use `@Transactional(readOnly = true)` for read operations.
- **NEVER** use `@Transactional` when the method includes an external proxied call. This holds database connections open during network calls and causes connection pool exhaustion.

---

## Domain Models

- Use descriptive variable names, avoid abbreviations.
- Use `@Column` for DB mapping. Never set constraint attributes (`length`, `nullable`, etc.) — constraints are managed via Liquibase only.
- Use enums for status fields with `@Enumerated(EnumType.STRING)`.
- Keep domain and API models separate — do not leak API annotations into domain.
- Encapsulate query specs in repository classes.
- Entity classes must include table and schema name:
```java
@Entity
@Table(name = "my_table", schema = "my_schema")
public class MyEntity {
    // fields, getters, setters
}
```
- Entity classes must extend one of:
  - `com.modulr.modulo.common.domain.base.BaseDeleteableEntity`
  - `com.modulr.modulo.common.domain.base.BaseImmutableEntity`
  - `com.modulr.modulo.common.domain.base.BaseUpdateableEntity`

---

## Error Handling

- Error logs must include the unique business identifier when available.
- Catch specific exceptions, not generic `Exception`.
- Log meaningful messages with `logger.warn` / `logger.error`.
- Never swallow exceptions silently.
- Where errors are recoverable or actionable, post custom metrics to Coralogix so monitors can be set up.

---

## Unit Tests (in this module)

- JUnit test classes: same package structure as source, class name ends with `Test` (e.g., `MyServiceTest`).
- Name the service under test as `underTest`.
- Prefer constructor injection over `@InjectMocks`.
- Use `@Nested` classes to group related tests.
- Use `@BeforeEach` and `@AfterEach` for setup/teardown.
- Use AssertJ for assertions. Assert exceptions with `assertThatThrownBy` including type and message.
- Shared string values: define as `private static final` constants within the test class.
- Shared object instances: use factory pattern (not builder pattern).