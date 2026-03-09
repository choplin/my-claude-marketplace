---
name: self-review
description: This skill is invoked automatically after implementation completes. Should NOT be invoked directly by user. Orchestrates three parallel reviews (acceptance criteria, plan compliance, code quality) and provides unified feedback for self-correction.
allowed-tools: Read, Glob, Grep, Bash, Task
user-invocable: false
---

# Self Review

Orchestrate comprehensive review of implementation through parallel execution of specialized reviewers.

## Purpose

Three parallel reviews for complete coverage:
1. **Acceptance Criteria Review**: Verify each criterion from spec (acceptance-reviewer agent)
2. **Plan Compliance Review**: Verify all planned changes are complete (plan-compliance-reviewer agent)
3. **Code Quality Review**: Check for bugs, security issues, and code quality (feature-dev:code-reviewer agent)

## Input

- Spec document at `.claude/dev-workflow/story/{name}/spec.md`
- Plan document at `.claude/dev-workflow/story/{name}/plan.md`
- Completed implementation

## Process

### 1. Invoke Reviewers (Parallel)

Launch three reviewers in parallel using Task tool:

```
Task 1: acceptance-reviewer (internal agent)
- subagent_type: dev-workflow:acceptance-reviewer
- prompt: "Review implementation against acceptance criteria. spec_path: {spec_path}"

Task 2: plan-compliance-reviewer (internal agent)
- subagent_type: dev-workflow:plan-compliance-reviewer
- prompt: "Review implementation against plan. plan_path: {plan_path}"

Task 3: feature-dev:code-reviewer (external agent)
- subagent_type: feature-dev:code-reviewer
- prompt: "Review code quality for the changes in this implementation"
- Note: Skip if agent not available
```

### 2. Aggregate Results

Combine outputs from all reviewers into unified report.

### 3. Determine Overall Status

| Reviewer | PASS Condition |
|----------|----------------|
| Acceptance | All criteria PASS or NEEDS REVIEW |
| Plan Compliance | All items COMPLETE or NEEDS REVIEW |
| Code: Bugs | No issues found |
| Code: Logic Errors | No issues found |
| Code: Security | No issues found |
| Code: Code Quality | No HIGH severity issues |
| Code: Conventions | No issues found |

Overall PASS requires all reviewers to pass.

### 4. Self-Correct (if FAIL)

This is a feedback loop. If any FAIL exists:

1. **Acceptance FAIL**: Fix implementation to meet criteria
2. **Plan FAIL**: Complete missing steps/file changes
3. **Code Quality FAIL**: Fix identified issues

After fixing, re-run self-review.

**Escalation rule**: If the same FAIL occurs twice consecutively, promote it to NEEDS REVIEW.

**Rationale**: If AI cannot fix an issue after one attempt, the issue likely requires user judgment.

#### Plan Mode Context Preservation

If you use EnterPlanMode to fix issues, include a `## dev-workflow Context` block in the plan file (see `references/plan-mode-context.md` for full template):

```markdown
## dev-workflow Context
**Active skill**: self-review (Self-Correct)
**Phase**: Self-Review
**Work level**: Story
**Documents**:
- Spec: .claude/dev-workflow/story/{name}/spec.md
- Plan: .claude/dev-workflow/story/{name}/plan.md

### After This Plan Completes
Re-run self-review to verify fixes are effective.
```

## Output Format

```markdown
## Self Review Results

**Spec**: `.claude/dev-workflow/story/{name}/spec.md`
**Plan**: `.claude/dev-workflow/story/{name}/plan.md`

### 1. Acceptance Criteria Review

{Output from acceptance-reviewer agent}

### 2. Plan Compliance Review

{Output from plan-compliance-reviewer agent}

### 3. Code Quality Review

{Output from feature-dev:code-reviewer agent, or "Skipped (agent not available)" if unavailable}

### Overall Summary

| Review | PASS | FAIL | NEEDS REVIEW |
|--------|------|------|--------------|
| Acceptance Criteria | X | X | X |
| Plan Compliance | X | X | X |
| Code: Bugs | X | X | X |
| Code: Logic Errors | X | X | X |
| Code: Security | X | X | X |
| Code: Code Quality | X | X | X |
| Code: Conventions | X | X | X |
| **Total** | X | X | X |

### Next Action

{If any FAIL: specific fixes to apply, then re-run self-review}
{If all PASS: ready for user review}
{If only NEEDS REVIEW: questions requiring user judgment}
```

## Success Criteria

- [ ] All three reviewers are invoked in parallel
- [ ] Results are aggregated into unified report
- [ ] Each FAIL includes actionable fix instruction
- [ ] Feedback loop continues until no FAIL remains
- [ ] Ready to proceed to user review (all PASS or only NEEDS REVIEW)

## Next Session

After self-review completes (all PASS or NEEDS REVIEW only):

**Reference**:
- `.claude/dev-workflow/story/{name}/spec.md`
- `.claude/dev-workflow/story/{name}/plan.md`

**Next phase**: `user-review`

Read spec and plan, review the self-review results, and invoke `user-review` skill to present results to user.
