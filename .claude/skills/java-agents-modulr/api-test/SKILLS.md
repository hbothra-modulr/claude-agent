---
description: 'Instructions for the api-test module — integration and Cucumber tests'
---

## Module Purpose

This module houses all **integration tests**, primarily using **Cucumber**. It includes test configurations, feature files, step definitions, and test data. It ensures coverage of controller behaviour, service orchestration, and external integrations.

## What belongs here
- Cucumber feature files and step definitions
- Integration test classes (suffix `IT`, e.g., `MyServiceIT`)
- Test configurations and test data
- Testcontainers setup for integration tests

## What does NOT belong here
- Unit tests for specific classes — those go alongside source in `application`
- Production code, services, or configuration
- Domain models or DTOs

---

## Cucumber Conventions

- Use `@Given`, `@When`, `@Then` — avoid `@And`.
- Feature files should describe behaviour in business language.
- Step definitions should be reusable across features where possible.
- Keep step definitions focused — one step, one action.

---

## Test Naming

- Test method names must start with `should`, use camelCase (e.g., `shouldSendIban()`).
- Prefer the `should..When..Then..` naming pattern (e.g., `shouldReturnNotFoundWhenCustomerDoesNotExist`).
- Integration test classes must end with `IT` (e.g., `MyServiceIT`).

---

## Assertions

- Use AssertJ for all assertions, not JUnit's `assertEquals`.
- Assert exceptions with `assertThatThrownBy` including both exception type and message.

---

## Testcontainers

- Use Testcontainers for integration tests that need real infrastructure (database, messaging).
- Never use Testcontainers for unit tests.
- Always clean up resources after tests — use `@AfterEach` or container lifecycle management.

---

## Test Data and Fixtures

- When multiple test cases require the same string value, define it as a `private static final` constant within the test class.
- When multiple test cases require the same object instance, use a factory pattern to generate those objects. Use factory pattern, not builder pattern.
- Use `@BeforeEach` and `@AfterEach` for setup and teardown logic.
- Use `@Nested` classes for grouping related tests.

---

## What NOT to do in tests

- Do not name the service under test as `underTest` in integration tests — that convention is for JUnit unit tests only (in the `application` module).
- Do not use `@InjectMocks` — prefer constructor injection for better testability.
- Do not swallow exceptions — always assert on expected exceptions.