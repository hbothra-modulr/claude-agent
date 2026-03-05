---
name: angular-feature-development
description: Build production-grade Angular frontend features with strict architecture layering, type safety, reactive correctness, performance discipline, SCSS hygiene, and testing quality. Generates code that follows ALWAYS/NEVER rules and avoids Angular anti-patterns.
---

# Angular Feature Development Skill

> Generate production-ready Angular code that adheres to strict architecture, type safety, reactive correctness, performance discipline, SCSS standards, and testing quality.

This skill builds Angular features correctly from the start while enforcing strict architectural and reactive discipline.

If project-specific conventions override these defaults, follow project rules. Otherwise, this document defines the authoritative development standard.

---

# When to Use

- "Create a new Angular feature"
- "Generate Angular component/service/facade"
- "Build Angular page with API integration"
- "Create reactive form"
- "Scaffold feature routes"
- "Generate Angular tests"
- "Refactor feature to layered architecture"

---

# 1. DEFAULT ASSUMPTIONS

Unless explicitly instructed otherwise:

- Use modern Angular
- Prefer standalone components
- Use strict TypeScript
- Use reactive patterns
- Organize by feature
- Use SCSS
- Use Jest-style tests
- Use ChangeDetectionStrategy.OnPush
- Prefer async pipe over manual subscriptions
- Use stable selectors (data-testid)

Do NOT generate legacy Angular patterns unless explicitly required.

---

# 2. ALWAYS RULES (MANDATORY GENERATION BEHAVIOR)

## Architecture

ALWAYS:

- Separate presentation, orchestration, services, and domain layers
- Keep business logic out of templates
- Keep HTTP calls inside services only
- Organize by feature, not file type
- Keep features self-contained
- Lazy load feature routes when applicable
- Keep components small and focused
- Avoid multi-responsibility components
- Keep cross-feature imports minimal and explicit

---

## Components

ALWAYS:

- Use standalone components
- Use OnPush change detection
- Strongly type Inputs and Outputs
- Emit domain models, not DOM events
- Explicitly type all public methods
- Keep components focused on UI orchestration only
- Prefer ReadonlyArray<T> for collections
- Use readonly properties when appropriate

---

## Templates

ALWAYS:

- Use 2-space indentation
- Keep `>` on the same line as last attribute
- Use trackBy in all list rendering
- Keep template expressions simple
- Extract complex logic to component or pipe
- Handle loading, error, and empty states
- Keep templates declarative

---

## TypeScript

ALWAYS:

- Use explicit return types
- Define interfaces for API responses (DTOs)
- Map DTOs to domain models
- Use enums or const objects for repeated values
- Use const / let (never var)
- Avoid implicit any
- Prefer unknown over any when structure is uncertain
- Maintain strict typing discipline

---

## Reactive Patterns

ALWAYS:

- Prefer async pipe
- Handle loading, error, and empty states
- Compose streams declaratively
- Ensure teardown for manual subscriptions
- Centralize side effects in facade/application layer

Operator Intent Rules:

- switchMap → cancel previous
- concatMap → ordered writes
- exhaustMap → prevent double submit
- mergeMap → parallel execution

---

## SCSS

ALWAYS:

- Use kebab-case naming
- Use rem units for font sizes
- Prefer flexbox
- Keep styles component-scoped
- Extract reusable patterns to mixins/utilities
- Maintain consistent spacing
- Avoid styling that compensates for poor structure

---

## Testing

ALWAYS:

- Test behavior, not implementation
- Cover rendering, inputs, outputs
- Cover loading, error, empty states
- Mock services properly
- Use stable selectors (data-testid)
- Test public APIs only
- Keep tests deterministic

---

# 3. NEVER RULES (PROHIBITED OUTPUT)

## Architecture Violations

NEVER:

- Mix UI and business logic
- Perform HTTP calls inside components
- Cross-import internal files between features
- Place domain logic inside templates
- Create multi-responsibility components

---

## Type Violations

NEVER:

- Use any where structure is known
- Omit return types
- Use implicit any
- Use var

---

## Reactive Violations

NEVER:

- Nest subscribe calls
- Subscribe without teardown
- Ignore error handling
- Ignore loading state
- Use subscribe when async pipe is possible
- Put side effects inside map

---

## Template Violations

NEVER:

- Use heavy computation in interpolation
- Use inline sorting/filtering
- Use nested ternary chains
- Omit trackBy
- Call expensive methods repeatedly in template

---

## Styling Violations

NEVER:

- Use excessive !important
- Use excessive ::ng-deep
- Use inline styles in templates
- Duplicate CSS unnecessarily

---

## Testing Violations

NEVER:

- Access private members in tests
- Use NO_ERRORS_SCHEMA to hide issues
- Rely on fragile class/tag selectors
- Put expect inside subscribe when emission may not occur
- Skip testing loading/error states

---

## Code Quality Violations

NEVER:

- Leave TODO comments
- Leave console.log
- Leave unused imports
- Leave dead code
- Generate placeholder comments
- Introduce unnecessary abstraction

---

# 4. REQUIRED LAYERING

## Domain Layer

- Interfaces
- Enums
- Types
- Pure mapping helpers
- No Angular imports

## Data Access Layer

- HTTP calls only
- DTO typing
- DTO → domain mapping
- Error normalization if required

## Application Layer (Facade)

- Feature state orchestration
- Expose readonly streams
- Handle loading and error
- Centralize side effects
- No template logic

## Container/Page Layer

- Route params
- ViewModel composition
- Delegates rendering
- Minimal imperative logic

## Presentation Layer

- Pure UI
- Inputs/Outputs only
- No HTTP
- Emits domain models

Reject mixing responsibilities across layers.

---

# 5. PERFORMANCE ENFORCEMENT

MUST enforce:

- trackBy in every ngFor
- OnPush by default
- No heavy template computation
- No repeated expensive function calls
- Lazy loading for feature routes
- Recommend virtualization for large lists
- Prefer single vm$ in containers

---

# 6. STATE MANAGEMENT RULES

Choose the simplest valid solution:

- Local state → component-level reactive state
- Feature state → facade/service
- Global state → only when clearly cross-feature

Over-engineering is a violation.

---

# 7. AUTO-REJECTION CONDITIONS

Treat as critical and refactor immediately:

- Observable<any> where structure is known
- Nested subscribe
- Business logic in template
- Missing return types
- Missing trackBy
- Use of var
- Inline CSS
- Architecture layer mixing
- Feature cross-imports
- Memory leak risk

---

# 8. FEATURE FOLDER STRUCTURE (REFERENCE)

feature/
domain/
models/
enums/
types/
mappers/
data-access/
feature.api.service.ts
feature.dtos.ts
feature.endpoints.ts
application/
feature.facade.ts
feature.state.ts
pages/
feature.page.ts
feature.page.html
feature.page.scss
feature.page.spec.ts
components/
feature-list/
feature-list.component.ts
feature-list.component.html
feature-list.component.scss
feature-list.component.spec.ts
feature-item/
feature-item.component.ts
feature-item.component.html
feature-item.component.scss
feature-item.component.spec.ts
feature.routes.ts

---

# 9. OUTPUT FORMAT WHEN GENERATING A FEATURE

When generating a feature, always provide:

1. Folder Structure
2. Domain Models + Mappers
3. API Service
4. Facade (if needed)
5. Page Component
6. Presentation Components
7. SCSS
8. Routing (lazy-loaded)
9. Tests

All generated code must be production-ready.

---

# Trigger Keywords

Angular, standalone, TypeScript, ES6, RxJS, async pipe, SOLID, SCSS, performance, testing, trackBy, modular architecture
