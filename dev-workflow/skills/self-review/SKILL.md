---
name: self-review
description: This skill is invoked automatically after implementation completes. Should NOT be invoked directly by user. Reviews implementation against spec's acceptance criteria and provides actionable feedback for self-correction.
allowed-tools: Read, Glob, Grep, Bash
user-invocable: false
---

# Self Review

Review implementation against spec's acceptance criteria. This is AI's feedback loop for self-correction.

## Purpose

Three core functions:
1. **Acceptance criteria verification**: Check each criterion from spec
2. **Implementation quality confirmation**: Verify the implementation meets requirements
3. **Completion judgment**: Determine if ready for user review

## Input

- Spec document at `.claude/dev-workflow/story/{name}/spec.md`
- Completed implementation

## Process

### 1. Load Spec

Load the spec document and extract:
- Acceptance criteria (Given-When-Then)
- Out of Scope (to avoid checking irrelevant items)

### 2. Verify Each Criterion

For each acceptance criterion:

1. **Check Given**: Are preconditions satisfied?
2. **Execute When**: Perform the described action (run tests, check code)
3. **Verify Then**: Does result match expectation?

### 3. Determine Result

For each criterion:
- **PASS**: Expected result is satisfied
- **FAIL**: Expected result is NOT satisfied - include what's wrong and how to fix
- **NEEDS REVIEW**: Cannot be determined by AI (requires user judgment)

### 4. Self-Correct (if FAIL)

This is a feedback loop. If FAIL exists:
1. Fix the identified issue
2. Re-run self-review
3. Repeat until all PASS or NEEDS REVIEW

**Escalation rule**: If the same FAIL occurs twice consecutively, promote it to NEEDS REVIEW.

**Rationale**: If AI cannot fix an issue after one attempt, continuing to retry won't help. The issue likely requires user judgment or clarification that AI cannot provide.

## Output Format

Output must be actionable for self-correction:

```markdown
## Self Review Results

**Spec**: `.claude/dev-workflow/story/{name}/spec.md`

### Results

| # | Criterion | Result | Details |
|---|-----------|--------|---------|
| 1 | {name} | PASS | {verification method used} |
| 2 | {name} | FAIL | **Problem**: {what's wrong} / **Fix**: {how to fix} |
| 3 | {name} | NEEDS REVIEW | {why AI cannot determine} |

### Summary

- PASS: X
- FAIL: X
- NEEDS REVIEW: X

### Next Action

{If FAIL: specific fix to apply}
{If all PASS: ready for user review}
{If NEEDS REVIEW: questions for user}
```

## Verification Methods

| Criterion Type | Method |
|----------------|--------|
| File exists | Glob/Read |
| Code contains X | Grep/Read |
| Tests pass | Bash (run tests) |
| Build succeeds | Bash (run build) |
| UI/UX behavior | NEEDS REVIEW |

## Success Criteria

- [ ] All acceptance criteria from spec are checked
- [ ] Each FAIL includes problem description AND fix instruction
- [ ] Feedback loop continues until no FAIL remains
- [ ] Ready to proceed to user review (all PASS or only NEEDS REVIEW)

## Next Session

After self-review completes (all PASS or NEEDS REVIEW only):

**Reference**:
- `.claude/dev-workflow/story/{name}/spec.md`
- `.claude/dev-workflow/story/{name}/plan.md`

**Next phase**: `user-review`

Read spec and plan, review the self-review results, and invoke `user-review` skill to present results to user.
