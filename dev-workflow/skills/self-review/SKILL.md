---
name: self-review
description: Use this skill when reviewing implementation against acceptance criteria. Triggers on phrases like "review my implementation", "check if I'm done", "verify acceptance criteria", or after completing implementation work.
allowed-tools: Read, Glob, Grep, Bash(test commands, build commands)
---

# Self Review

Review implementation against spec's acceptance criteria.

## Prerequisites

- Spec document exists (`docs/spec-{name}.md`)
- Implementation is complete

## When to Use

- When implementation appears to be complete
- When you want to verify acceptance criteria are met
- Self-check before creating a PR

## Process

### 1. Load Spec Document

Load the related spec document and extract acceptance criteria.

### 2. Verify Each Criterion

For each acceptance criterion:

1. **Check Given**: Are preconditions satisfied?
2. **Execute When**: Perform the described action (or review code)
3. **Verify Then**: Determine if result matches expectation

### 3. Determine Result

For each criterion, determine one of:

- **PASS**: Expected result is satisfied
- **FAIL**: Expected result is not satisfied
- **NEEDS REVIEW**: Cannot be determined by AI alone (requires user confirmation)

## Verification Methods

Verification methods by criterion type:

| Criterion Type | Verification Method |
|----------------|---------------------|
| File creation | Confirm with Glob/Read |
| Code changes | Confirm with Grep/Read |
| Functionality | Run tests, check build |
| UI/UX | NEEDS REVIEW (user judgment) |

## Output Format

```markdown
## Self Review Results

**Spec**: `docs/spec-{name}.md`
**Date**: YYYY-MM-DD

### Results

| # | Criterion | Result | Notes |
|---|-----------|--------|-------|
| 1 | {Criterion name} | PASS/FAIL/NEEDS REVIEW | {Details} |
| 2 | {Criterion name} | PASS/FAIL/NEEDS REVIEW | {Details} |

### Summary

- PASS: X items
- FAIL: X items
- NEEDS REVIEW: X items

### Next Actions

- {If FAIL exists, what to fix}
- {If NEEDS REVIEW exists, questions for user}
```

## After Review

- **All PASS**: Proceed to `post-task` skill for completion
- **FAIL exists**: Fix and re-run self-review
- **NEEDS REVIEW exists**: Request user confirmation
