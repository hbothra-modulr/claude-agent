---
name: angular-code-review
description: Audit and enforce Angular frontend standards. Reviews architecture, type safety, reactive patterns, performance, SCSS discipline, testing quality, and ALWAYS/NEVER rule compliance.
---

# Angular Code Review Skill

> Audit Angular frontend code for architecture, type safety, reactive correctness, performance discipline, styling hygiene, and testing quality.

This skill defines strict review and enforcement rules for Angular frontend development.

These are enforcement constraints for AI output.  
They are not optional suggestions.  
If a rule is violated, it must be flagged.

This skill is primarily a **review and governance skill**.  
It may guide generation, but its main purpose is auditing and rejecting violations.

---

# When to Use

- "Review this Angular PR"
- "Audit this Angular feature"
- "Check if this Angular code follows best practices"
- Before merging frontend changes
- When refactoring Angular modules
- When debugging architecture or reactive issues

---

# 1. DEFAULT ASSUMPTIONS

Unless explicitly instructed otherwise:

- Use modern Angular
- Prefer standalone components
- Use strict TypeScript
- Use reactive patterns
- Use feature-based folder structure
- Use SCSS
- Use Jest-style testing

Do NOT approve legacy Angular patterns unless explicitly required.

---

# 2. ALWAYS RULES (MANDATORY BEHAVIOR)

The AI MUST ALWAYS enforce the following:

---

## Architecture

- Separate presentation, orchestration, services, and domain layers
- Keep business logic out of templates
- Keep HTTP calls inside services only
- Organize by feature, not file type
- Keep features self-contained
- Lazy load feature routes when applicable
- Keep components reasonably small and focused
- Avoid multi-responsibility ("god") components

---

## Components

- Prefer standalone components
- Strongly type all Inputs and Outputs
- Emit domain models, not DOM events
- Explicitly type all public methods
- Keep components focused on UI orchestration only

---

## Templates

- Use 2-space indentation
- Keep `>` on the same line as last attribute
- Use trackBy in all list rendering
- Keep template expressions simple
- Extract complex logic to component or pipe
- Keep templates declarative

---

## TypeScript

- Use explicit return types
- Define interfaces for API responses
- Use enums or const objects for repeated values
- Use `const` / `let` (never `var`)
- Use modern ES6+ syntax
- Maintain strict typing discipline

---

## Reactive Patterns

- Prefer async pipe or managed reactive state
- Handle loading, error, and empty states
- Use appropriate RxJS operators based on intent
- Ensure subscription teardown when required
- Prefer declarative stream composition

---

## SCSS

- Use kebab-case naming
- Use rem units for font sizes
- Prefer flexbox
- Extract reusable patterns to utilities or mixins
- Keep styles component-scoped by default
- Maintain consistent spacing and indentation

---

## Testing

- Test behavior, not implementation details
- Cover rendering, inputs, outputs, loading, and error states
- Mock service dependencies properly
- Use stable selectors (not fragile tag selectors)
- Test public APIs only
- Keep tests deterministic

---

# 3. NEVER RULES (PROHIBITED OUTPUT)

The AI MUST NEVER allow the following:

---

## Architecture Violations

- Mix UI and business logic
- Perform HTTP calls inside components
- Cross-import internal files between features
- Place domain logic inside templates
- Create multi-responsibility components

---

## Type Violations

- Use `any` where structure is known
- Omit return types
- Use implicit `any`
- Use `var`

---

## Reactive Violations

- Nest subscribe calls
- Subscribe without teardown
- Ignore error handling
- Ignore loading state
- Use subscribe where async pipe or managed state is possible

---

## Template Violations

- Use heavy computation in interpolation
- Use inline sorting/filtering
- Use nested ternary chains
- Break formatting rules
- Omit trackBy in lists

---

## Styling Violations

- Use excessive `!important`
- Use excessive `::ng-deep`
- Inline styles in templates
- Duplicate CSS unnecessarily
- Override structure instead of fixing architecture

---

## Testing Violations

- Access private members in tests
- Use NO_ERRORS_SCHEMA to hide issues
- Rely on fragile class/tag selectors
- Put expect inside subscribe when emission may not occur
- Skip testing loading or error states

---

## Code Quality Violations

- Leave TODO comments
- Leave console.log
- Leave unused imports
- Leave dead code
- Generate placeholder comments
- Introduce unnecessary abstraction

---

# 4. ARCHITECTURE ENFORCEMENT

Required Layering:

Presentation Layer

- Pure UI
- Inputs + Outputs

Container/Page Layer

- Orchestration
- Routing

Service Layer

- API calls
- Data mapping

Domain Layer

- Interfaces
- Enums
- Models

Reject code that mixes responsibilities across layers.

---

# 5. REACTIVE ENFORCEMENT

Operator Intent Rules:

- switchMap → cancel previous
- concatMap → ordered writes
- exhaustMap → prevent double submit
- mergeMap → parallel execution

Always require:

- Loading state handling
- Error handling
- Proper teardown
- Declarative composition over imperative flows

---

# 6. PERFORMANCE ENFORCEMENT

The AI MUST enforce:

- trackBy in every ngFor
- No heavy template computation
- No repeated expensive function calls in template
- No unnecessary re-renders
- Lazy loading for feature routes when applicable
- Recommendation of virtualization for large lists

Reject performance anti-patterns.

---

# 7. STATE MANAGEMENT RULES

Choose the simplest valid solution.

Local state → component-level reactive state  
Feature state → encapsulated facade/service  
Global state → only when clearly cross-feature

Over-engineering is a violation.

---

# 8. AUTO-REJECTION CRITERIA

Immediately flag as critical if:

- `Observable<any>` used where structure known
- Nested subscribe
- Business logic inside template
- Missing return types
- No trackBy in list rendering
- Use of `var`
- Inline CSS
- Excessive `!important`
- Architecture layer mixing
- Feature cross-imports
- Memory leak risk

---

# 9. REVIEW OUTPUT STRUCTURE

When reviewing Angular code, respond with:

1. Critical Violations
2. Architecture Issues
3. Type Safety Issues
4. Reactive Issues
5. Performance Concerns
6. Testing Gaps
7. Required Refactors
8. Example Fixes

---

# Scope of Enforcement

If project-specific rules override these defaults, follow project rules.  
Otherwise, this document is the authoritative Angular review standard.

---

# Trigger Keywords

Angular, standalone, TypeScript, ES6, RxJS, async pipe, SOLID, SCSS, performance, testing, trackBy, modular architecture
