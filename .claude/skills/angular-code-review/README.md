# Angular Code Review Skill

> Audit Angular frontend code for architecture, type safety, reactive correctness, performance, styling discipline, and testing quality

---

## What It Does

Reviews Angular code for:

- Proper feature-based architecture
- Separation of concerns (UI vs business logic)
- Strong TypeScript typing (no `any`)
- Correct RxJS usage (no nested subscribe)
- Performance best practices (trackBy, no heavy template logic)
- SCSS standards and styling hygiene
- Testing completeness and correctness
- SOLID design principle adherence
- ALWAYS / NEVER rule enforcement

This skill is for **auditing and enforcing Angular standards**, not just generating code.

---

## When to Use

- "Review this Angular PR"
- "Check this Angular feature"
- "Audit this component"
- Before merging frontend changes
- When refactoring Angular modules
- When debugging reactive or architectural issues

---

## Key Concepts

### Review vs Generation

| Angular Templates       | Angular Code Review     |
| ----------------------- | ----------------------- |
| How to write components | Audit existing code     |
| Example patterns        | Identify anti-patterns  |
| Flexible guidance       | Strict enforcement      |
| Code scaffolding        | Architecture validation |

---

## Common Issues Caught

| Issue                      | Example                        |
| -------------------------- | ------------------------------ |
| Business logic in template | `{{ items.filter(x => ...) }}` |
| Nested subscribe           | `subscribe(() => subscribe())` |
| Untyped API                | `Observable<any>`              |
| No trackBy                 | `*ngFor` without trackBy       |
| Layer mixing               | HTTP call inside component     |
| Missing return types       | `function foo() {}`            |
| Styling hacks              | Excessive `!important`         |
| Weak tests                 | Accessing private members      |

---

## What It Checks

1. **Architecture** – Layer separation and feature structure
2. **Type Safety** – Strong typing and explicit returns
3. **Reactive Discipline** – Proper RxJS operator usage
4. **Template Hygiene** – Formatting and logic control
5. **Performance** – Rendering efficiency and change detection
6. **Styling Standards** – SCSS structure and naming
7. **Testing Quality** – Behavior-driven coverage
8. **Design Principles** – SOLID enforcement
9. **Auto-Rejection Rules** – Critical violations

---

## Example Usage
