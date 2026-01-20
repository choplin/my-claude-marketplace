---
name: post-task
description: Use this skill after completing implementation and review. Triggers on phrases like "task complete", "finish up", "wrap up this task", "commit and document", or after self-review passes.
allowed-tools: Read, Write, Edit, Glob, Bash(git commands, ls, mkdir)
---

# Post-Task Processing

Complete a task with commit, knowledge capture, and cleanup.

## Prerequisites

- Implementation is complete
- Self-review shows PASS (or only NEEDS REVIEW)

## When to Use

- When implementation and review are complete
- When you want to create a commit
- When you want to record learnings

## Process

### 1. Prepare Commit

Review changes:
- Check changed files with `git status`
- Review changes with `git diff`
- Create appropriate commit message

**Commit message format**:
```
type(scope): description

- detail 1
- detail 2
```

types: `feat`, `fix`, `refactor`, `docs`, `test`, `chore`

### 2. Determine Knowledge Capture

Suggest creating an ADR if any of the following apply:

- **Important technical decisions**: Architecture, library selection, design patterns
- **Trade-offs**: Cases where alternatives were considered and a choice was made
- **Future impact**: Decisions that affect future development

If ADR is needed, create in `docs/adr/`.

### 3. Update Plan Progress

If plan document exists:
- Check off completed steps
- Update discoveries section

### 4. Suggest Additional Actions

Suggest as needed:
- [ ] Update documentation (README, etc.)
- [ ] Add tests
- [ ] Create PR
- [ ] Start next story

## ADR Template

```markdown
---
created: YYYY-MM-DD
---

# ADR-{number}: {title}

## Status

Accepted

## Context

{Background}

## Decision

{Decision details}

## Consequences

{Impact and results}
```

## Output

1. Create commit (after user confirmation)
2. Create ADR (if needed)
3. Update plan progress
4. Suggest next actions

## Important

- Execute commit after user confirmation
- Only create ADRs for important decisions (don't over-create)
- Always update plan document progress
