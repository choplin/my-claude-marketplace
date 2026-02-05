---
name: user-review
description: This skill is invoked automatically when self-review skill completes and presents results (PASS or NEEDS REVIEW status). Should NOT be invoked directly by user, before self-review completes, or when no implementation exists to review. Facilitates structured user review with appropriate response patterns based on feedback complexity.
allowed-tools: Read, Glob, Grep, Bash, AskUserQuestion
user-invocable: false
---

# User Review

Facilitate structured interaction between AI and user during the review phase. This skill ensures AI responds appropriately to user feedback based on its complexity.

## Purpose

Prevent AI from acting unpredictably during user review by establishing clear interaction patterns:

1. **Present review summary**: Show self-review results and NEEDS REVIEW items
2. **Wait for user feedback**: Do not proceed without user input
3. **Respond appropriately**: Match response to feedback complexity
4. **Obtain explicit approval**: Proceed to post-task only after LGTM

## Problem This Skill Solves

Without this skill, AI behavior during user review is ad-hoc:
- AI sometimes implements changes immediately without confirming intent
- AI skips clarification when user feedback is ambiguous
- AI proceeds to post-task without explicit user approval

These issues cause rework and frustration.

## Input

- Self-review results (from self-review skill)
- Spec document at `.claude/dev-workflow/story/{name}/spec.md`

## Process

### 1. Present Review Summary

After self-review completes, present:

```markdown
## Review Summary

**Spec**: `.claude/dev-workflow/story/{name}/spec.md`

### Self-Review Results

| # | Criterion | Result |
|---|-----------|--------|
| 1 | ... | PASS |
| 2 | ... | NEEDS REVIEW |

### Items Requiring User Review

{List NEEDS REVIEW items with context}

### Request

Please review the implementation and provide feedback:
- **LGTM**: Approve and proceed to post-task
- **Feedback**: Point out issues to address
```

### 2. Wait for User Feedback

After presenting the summary, **wait for user input**. Do not proceed autonomously.

### 3. Classify Feedback

When user provides feedback, classify it using the ambiguity test.

**Problem this classification solves**: Without clear classification, AI makes two recurring mistakes:
1. Implements changes prematurely when user intent is unclear (causes rework)
2. Over-asks for clarification on trivial fixes (causes frustration)

This test emerged from analyzing past failures: AI misidentified modification location (WHERE error) or misunderstood modification content (WHAT error). Both must be unambiguous to avoid rework.

**Ambiguity Test** (both must be met for "Minor"):

1. **Where**: Can the modification location be uniquely identified?
   - Pass: "line 42", "function processOrder", "before calling save()"
   - Fail: "error handling code" (multiple locations), "the service" (which part?)

2. **What**: Can the modification content be uniquely determined?
   - Pass: "rename to `result`", "add null check", "change timeout to 5000"
   - Fail: "improve", "fix", "make better", "optimize" (how specifically?)

**Why both conditions are required**: Past experience shows:
- WHERE clear + WHAT vague = wrong fix implemented in right place
- WHERE vague + WHAT clear = right fix implemented in wrong place

Either results in rework. Both must pass for immediate implementation.

| Classification | Test Result | Response Pattern | Rationale |
|----------------|-------------|------------------|-----------|
| **LGTM** | N/A | Proceed to post-task | User explicitly approved |
| **Minor** | Both pass | Fix immediately, report | No ambiguity = safe to implement |
| **Complex** | Either fails | dig → propose → wait | Ambiguity = clarify first |
| **Design Change** | Requires new/changed spec criteria | Return to create-spec | Cannot implement within current spec |

### 4. Handle Each Classification

#### LGTM (Approval)

User signals approval with phrases like "LGTM", "OK", "Looks good", "Approved".

**Action**: Proceed to post-task skill.

#### Minor Feedback

Feedback where both location and content are unambiguous.

**Examples of Minor**:
- "Fix the typo in line 42" (where: line 42, what: fix typo)
- "Change the variable name from `tmp` to `result`" (where: specific variable, what: rename)
- "Add a null check before calling process()" (where: before process(), what: null check)

**Action**:
1. Implement the fix
2. Report: "Fixed: {description}. Any other feedback?"
3. Wait for next input

#### Complex Feedback

Feedback where location OR content is ambiguous.

**Examples of Complex**:
- "The error handling seems off" (where unclear, what unclear)
- "This function is too long" (what: split? extract? unclear)
- "The naming could be better" (what: which names? to what?)
- "Performance might be an issue here" (where: which part? what: optimization approach?)

**Action**:
1. Acknowledge: "I'd like to clarify the intent before making changes."
2. Use dig skill to explore:
   - **Why**: What is the underlying concern?
   - **Where**: Which specific location(s)?
   - **What**: What specific change is expected?
3. After clarification, propose the fix:
   ```markdown
   ## Proposed Fix

   **Understanding**: {summarize clarified intent}

   **Changes**:
   1. {specific change 1}
   2. {specific change 2}

   Proceed with this fix?
   ```
4. Wait for approval before implementing

#### Design Change

Feedback that requires changes beyond the current spec's scope.

**How to identify Design Change** (check spec file):
1. Read current spec at `.claude/dev-workflow/story/{name}/spec.md`
2. Compare feedback against spec's "Success Criteria" and "Requirements"
3. If feedback requires NEW success criteria or changes EXISTING criteria → Design Change

**Concrete examples**:

| Feedback | Classification | Reason |
|----------|----------------|--------|
| "Add async support to the processor" | Design Change | Current spec defines sync processing; async requires new error handling, retry logic, timeouts (new success criteria) |
| "Change error message from X to Y" | Minor/Complex | Within current spec's error handling approach; no new criteria needed |
| "Add a new endpoint for bulk operations" | Design Change | Current spec defines single-item operations; bulk requires new transaction handling (new success criteria) |
| "Improve validation error messages" | Minor/Complex | Within current spec's validation approach; same criteria apply |

**When in doubt**: If implementing the feedback would require changing the spec's Success Criteria section, it's a Design Change.

**Action**:
1. Acknowledge: "This feedback suggests changes beyond the current spec."
2. Summarize what needs to change in spec (be specific: which success criteria?)
3. Invoke create-spec skill to edit the existing spec
4. After spec update, re-implement and re-run self-review

## Output Format

### After Presenting Summary

```markdown
## Review Summary

[Summary content as specified above]

Awaiting your feedback.
```

### After Minor Fix

```markdown
Fixed: {description of fix}.

Any other feedback, or LGTM to proceed?
```

### After Complex Clarification

```markdown
## Proposed Fix

**Understanding**: {clarified intent}

**Changes**:
1. {change 1}
2. {change 2}

Proceed with this fix?
```

### After Design Change Detection

```markdown
## Design Change Detected

This feedback requires spec-level changes:
- {change 1}
- {change 2}

Proceeding to update spec with create-spec skill.
```

## Success Criteria

### Process Criteria

- [ ] Review summary presented: Response contains "## Review Summary" section with spec path, self-review results, and NEEDS REVIEW items
- [ ] No autonomous action after summary: Response ends without implementation and without "Should I proceed?" question
- [ ] Minor feedback identified correctly: When both WHERE and WHAT are unambiguous, fix is implemented without asking for approval
- [ ] Minor feedback handled correctly: After fix, response includes "Fixed: {description}" and ends waiting for next input
- [ ] Complex feedback identified correctly: When either WHERE or WHAT is ambiguous, dig skill is invoked before proposing fix
- [ ] Complex feedback handled correctly: After dig, response includes "## Proposed Fix" section and waits for approval before implementing
- [ ] Design change identified correctly: When feedback requires new/changed spec success criteria, create-spec skill is invoked
- [ ] Explicit LGTM obtained: User explicitly signals approval ("LGTM", "OK", "Looks good") before invoking post-task skill

### Outcome Criteria

- [ ] User doesn't need to re-explain: Each response directly addresses the feedback without requiring user to clarify what they already said
- [ ] No premature implementation: Code changes only occur after user intent is confirmed (immediately for Minor, after approval for Complex)
- [ ] No excessive questioning: For Minor feedback, fix is implemented without asking "Should I proceed?"

## Integration with Workflow

```
self-review (all PASS or NEEDS REVIEW)
    ↓
user-review (this skill)
    ↓ (LGTM obtained)
post-task
```

## Anti-patterns to Avoid

| Anti-pattern | Why It's Wrong | Correct Behavior |
|--------------|----------------|------------------|
| Implementing immediately on any feedback | Skips clarification for complex cases | Classify first, then respond appropriately |
| Assuming LGTM without explicit signal | User may have more feedback | Wait for explicit approval |
| Asking "Should I proceed?" after summary | User review is for feedback, not approval of summary | Wait silently for feedback |
| Proposing multiple interpretations | Adds cognitive load to user | Use dig to narrow down |

## Next Session

After user gives LGTM:

**Reference**:
- `.claude/dev-workflow/story/{name}/spec.md`
- `.claude/dev-workflow/story/{name}/plan.md`

**Next phase**: `post-task`

Read spec and plan, then invoke `post-task` skill to capture knowledge and finalize.
