---
name: git-commit
description: Generate conventional commit messages for Java & Angular projects. Use when user says "commit", "create commit", "commit changes", or after completing code changes that need to be committed.
---

# Git Commit Message Skill

Generate conventional, informative commit messages for Java & Angular projects.

## When to Use
- After making code changes
- User says "commit this" / "commit changes" / "create commit"
- Before creating PRs

## Format Standard

Use Conventional Commits format:
```
<ticket Id>: <subject>

<body>

<footer>
```

### Ticket Id
- Ask user for Ticket ID which should follow something like CAL-1234 or NEO-5432 or HE-5000

### Subject line
- Prefix with Jira ticket number, then a colon (e.g., `MOD-1234: ...`)
- Imperative mood (command-style): “Fix timeout”, not “Fixed timeout”
- Capitalized
- No period at the end
- Length: typically **≤ 50 chars**, and **never > 72** (prevents truncation in logs and when used as a GitLab MR title)

### Message body
- Separate from the subject line with a **blank line**
- Explain **WHAT** and **WHY** over **HOW** (the diff already shows how)
- Include enough information that a reader can understand the change without further digging
- A useful structure:
  - How things worked **before** (and what was wrong)
  - How they work **now**
  - Why you chose to solve it that way
- Small changes may not need much body text

---

## Template

### Subject Rules
- `MOD-1234: <imperative summary>`
- Capitalized
- No trailing period
- Aim for ≤ 50 chars; never > 72

### Body (optional but recommended)
- Blank line after subject
- Explain WHAT and WHY, not HOW
- Include before/after/decision rationale where relevant

## Workflow

1. **Analyze changes** using `git diff --staged`
2. **Identify scope** from modified files
3. **Determine type** based on change nature
4. **Generate message** following format
5. **Execute commit**: `git commit -m "message"`

## Token Optimization

- Read staged changes ONCE: `git diff --staged --stat` + targeted file diffs
- Don't read entire files unless necessary
- Use concise body - aim for 2-3 lines max
- Batch multiple small changes into logical commits

## Anti-patterns

❌ Avoid:
- "fix stuff" / "update code" / "changes"
- "WIP" commits (unless explicitly requested)
- Mixing unrelated changes (use separate commits)
- Over-detailed technical implementation in message

✅ Good commits:
- Single logical change
- Clear, searchable subject
- References issues when applicable
- Explains business value

## Integration with GitLab

After commit, suggest next steps:
- "Push changes?" 
- "Create PR for issue #X?"
- "Continue with next task?"
