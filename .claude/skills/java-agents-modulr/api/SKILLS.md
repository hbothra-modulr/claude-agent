---
description: 'Instructions for the api module — interfaces, DTOs, and API contracts'
---

## Module Purpose

This module contains **controller interfaces**, shared DTOs, enums, and request/response objects. It is designed to be **reusable across microservices** and must never contain implementation logic.

## What belongs here
- Controller interfaces (not concrete classes)
- Request and response DTOs
- Enums shared across modules
- Swagger/OpenAPI annotations
- Validation annotations on request fields

## What does NOT belong here
- Concrete controller implementations — those go in `application`
- Service classes, repositories, or mappers
- Domain/entity models
- Business logic of any kind
- `@Transactional` annotations
- Spring configuration classes

## Controller Interfaces

- Define REST endpoint contracts as interfaces.
- Each endpoint method must include the full URL path (e.g., `/customers/{customerId}/accounts`). Never split between class-level and method-level prefixes.
- Use proper Spring annotations per HTTP method:
  - POST: `@PostMapping`, `@ResponseStatus`, Swagger docs, `@Valid`, `@RequestBody`, `@Parameter`
  - GET: `@GetMapping`, Swagger docs
  - DELETE: `@DeleteMapping`, `@ResponseStatus(HttpStatus.NO_CONTENT)`, Swagger docs
  - PATCH: `@PatchMapping`, Swagger docs
- Class-level annotations: `@Validated`, `@RequestMapping`, `@Tag`
- Methods must return dedicated response types, not generic ones.

## DTOs and Request/Response Objects

- Use Java Records for immutable DTOs.
- Request classes: annotate fields with validation constraints (`@NotBlank`, `@NotNull`, `@NotEmpty`). No message is required for these annotations.
- Separate fields and annotations with line breaks for readability.
- Avoid `@Example` on enum fields.
- Use `@ParameterObject` for Swagger docs on query parameters.
- Use `@Schema` to document custom validators.
- Use explicit request objects, not generic maps.
- Accept a single `SearchCriteria` object for search methods, not multiple `@RequestParam`.

## Validation

- All validation annotations belong in request classes in this module, not in controller methods or service classes.
- Custom validators: implement validation logic, use custom annotations, document with `@Schema`.
- Use `@Valid` on controller method parameters to trigger validation.

## Enums

- Use descriptive names. Avoid abbreviations.
- Enums used as status fields should be annotated with `@Enumerated(EnumType.STRING)` in the domain model (not here — that belongs in `application`).

## Swagger/OpenAPI

- Document all endpoints with Swagger annotations.
- Include response codes, descriptions, and parameter documentation.
- GET endpoints returning a list must be paginated. GET endpoints returning a single object must not be paginated.