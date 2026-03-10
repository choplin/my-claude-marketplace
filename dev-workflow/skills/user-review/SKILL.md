---
name: user-review
description: Structured user review interaction skill. Presents review summary, classifies user feedback (Minor/Complex/Design Change), and responds appropriately.
allowed-tools: Read, Write, Edit, Glob, Grep, Bash, AskUserQuestion
user-invocable: true
---

# User Review

Facilitate structured interaction between AI and user during the review phase. This skill ensures AI responds appropriately to user feedback based on its complexity.

## Purpose

Prevent AI from acting unpredictably during user review by establishing clear interaction patterns:

1. **Present review summary**: Show self-review results and NEEDS REVIEW items
2. **Collect all feedback first**: Record approach for each item without implementing
3. **Implement in batch**: Execute all recorded approaches at once after user signals completion
4. **Obtain explicit approval**: Proceed to post-task only after LGTM

## Problem This Skill Solves

Without this skill, AI behavior during user review is ad-hoc:
- AI implements changes after each feedback item, causing context switches
- Plan mode is invoked per item, bloating the session
- User cannot finish giving all feedback before implementation starts

These issues cause rework and frustration.

## Two-Phase Design

| Phase | Purpose | AI Action |
|-------|---------|-----------|
| **Collection Phase** | Gather all feedback, record approaches | Classify, dig if needed, record WHERE+WHAT — do NOT implement |
| **Implementation Phase** | Execute all recorded approaches at once | Implement each item, report results |

**Phase transition signals**:
- **Collection → Implementation**: User says "以上", "done", "完了", etc.
- **Implementation → Post-task**: User says "LGTM", "OK", "Looks good"
- **Implementation → Collection** (loop back): User provides additional feedback after implementation

## Input

- Self-review results (from self-review skill)
- Spec document at `.claude/dev-workflow/story/{name}/spec.md`

## Process

### 0. Check Review State

Before starting, check for existing review state:

1. Look for `review.md` at `.claude/dev-workflow/story/{name}/review.md`
2. If **review.md exists**, read it and resume based on Phase:
   - `COLLECTING FEEDBACK`: Show summary of recorded items (if any) and current state, then wait for next feedback
   - `READY FOR IMPLEMENTATION`: Show item summary, then proceed to Implementation Phase (Step 5)
   - `IMPLEMENTING`: Find next APPROACH RECORDED item and continue Implementation Phase (Step 5)
   - `LGTM`: Proceed to post-task
3. If **review.md does not exist**, continue to step 1 (normal flow)

### 1. Present Review Summary (Fallback Path)

> **Note**: In the normal workflow, self-review creates review.md before invoking handoff. Step 0 detects the existing review.md and resumes from `COLLECTING FEEDBACK`, skipping this step entirely. Step 1 is a fallback for when user-review is invoked directly without prior self-review (e.g., manual invocation).

If review.md does not exist (Step 0 found nothing):

1. **Create review.md** at `.claude/dev-workflow/story/{name}/review.md` using the review template (`references/review-template.md`):
   - Fill in Related Files (spec and plan paths)
   - Copy self-review results into the Self-Review Results table
   - Set Phase to `COLLECTING FEEDBACK`
   - Set Resolved to `0 / 0`
2. **Present** the review summary to the user:

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

When you've finished giving feedback, say "以上" to start implementation.
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

Either results in rework. Both must pass for immediate approach recording.

| Classification | Test Result | Collection Phase Action | Rationale |
|----------------|-------------|------------------------|-----------|
| **LGTM** | N/A | Proceed to post-task (or confirm pending items first) | User explicitly approved |
| **"以上" / "done"** | N/A | Transition to Implementation Phase | User finished giving feedback |
| **Minor** | Both pass | Record approach immediately | No ambiguity = safe to record |
| **Complex** | Either fails | dig → record approach | Ambiguity = clarify first |
| **Design Change** | Requires new/changed spec criteria | Record approach (spec changes needed) | Cannot implement within current spec |

### 4. Handle Each Classification (Collection Phase)

**Core rule: Do NOT implement any code changes in this phase. Only record approaches.**

#### LGTM (Approval)

User signals approval with phrases like "LGTM", "OK", "Looks good", "Approved".

**Action**:
1. If APPROACH RECORDED items exist: Ask user "There are {N} recorded items not yet implemented. Implement them first, or skip and approve as-is?"
   - If implement: Transition to Implementation Phase (Step 5)
   - If skip: Continue to step 2
2. Update review.md: Set Phase to `LGTM`
3. Proceed to post-task skill

#### Completion Signal ("以上" / "done" / "完了")

User signals they have finished giving all feedback.

**Action**:
1. Update review.md: Set Phase to `READY FOR IMPLEMENTATION`
2. Present item summary:
   ```markdown
   ## Feedback Summary

   | # | Classification | Approach |
   |---|----------------|----------|
   | 1 | Minor | {brief approach} |
   | 2 | Complex | {brief approach} |

   Proceeding to implementation.
   ```
3. Update review.md: Set Phase to `IMPLEMENTING`
4. Proceed to Implementation Phase (Step 5)

#### Minor Feedback

Feedback where both location and content are unambiguous.

**Examples of Minor**:
- "Fix the typo in line 42" (where: line 42, what: fix typo)
- "Change the variable name from `tmp` to `result`" (where: specific variable, what: rename)
- "Add a null check before calling process()" (where: before process(), what: null check)

**Action**:
1. Add Review Item to review.md (Status: OPEN, Classification: Minor)
2. Record approach (WHERE + WHAT):
   ```markdown
   - **Approach**: WHERE: {file:location} / WHAT: {specific change}
   ```
3. Update Review Item in review.md: Status → `APPROACH RECORDED`, fill Approach
4. Report: "Recorded: {description}. Any other feedback? Say '以上' when done."
5. Wait for next input

#### Complex Feedback

Feedback where location OR content is ambiguous.

**Examples of Complex**:
- "The error handling seems off" (where unclear, what unclear)
- "This function is too long" (what: split? extract? unclear)
- "The naming could be better" (what: which names? to what?)
- "Performance might be an issue here" (where: which part? what: optimization approach?)

**Action**:
1. Add Review Item to review.md (Status: OPEN, Classification: Complex)
2. Acknowledge: "I'd like to clarify the intent before recording the approach."
3. Use dig skill to explore:
   - **Why**: What is the underlying concern?
   - **Where**: Which specific location(s)?
   - **What**: What specific change is expected?
4. After clarification, record the approach:
   ```markdown
   - **Approach**: WHERE: {file:location} / WHAT: {specific change}
   ```
5. Update Review Item in review.md: Status → `APPROACH RECORDED`, fill Approach
6. Report: "Recorded: {description}. Any other feedback? Say '以上' when done."
7. Wait for next input

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
1. Add Review Item to review.md (Status: OPEN, Classification: Design Change)
2. Acknowledge: "This feedback suggests changes beyond the current spec."
3. Record approach with spec change details:
   ```markdown
   - **Approach**: SPEC CHANGE: {which success criteria to add/modify} / WHAT: {implementation changes after spec update}
   ```
4. Update Review Item in review.md: Status → `APPROACH RECORDED`, fill Approach
5. Report: "Recorded as design change: {description}. Any other feedback? Say '以上' when done."
6. Wait for next input

### 5. Implementation Phase

**Entry**: Phase is `IMPLEMENTING` (set by completion signal or resumed from review.md).

**Processing order**: Design Change items first (they may affect spec and other items), then remaining items in order.

#### 5a. Design Change Items

For each Design Change item with Status = `APPROACH RECORDED`:

1. Update Review Item: Status → `IMPLEMENTING`
2. Invoke create-spec skill to edit the existing spec
3. After spec update, re-implement and re-run self-review for affected changes
4. Update Review Item: Status → `RESOLVED`, fill Resolution
5. Update review.md Resolved counter

#### 5b. Minor and Complex Items

For each remaining item with Status = `APPROACH RECORDED`:

1. Update Review Item: Status → `IMPLEMENTING`
2. Implement the change according to the recorded Approach
3. Update Review Item: Status → `RESOLVED`, fill Resolution
4. Update review.md Resolved counter

#### Plan Mode Context Preservation

If you use EnterPlanMode during implementation of review items, include a `## dev-workflow Context` block in the plan file (see `references/plan-mode-context.md` for full template):

```markdown
## dev-workflow Context
**Active skill**: user-review (Implementation Phase)
**Phase**: User-Review
**Work level**: Story
**Documents**:
- Spec: .claude/dev-workflow/story/{name}/spec.md
- Plan: .claude/dev-workflow/story/{name}/plan.md
- Review: .claude/dev-workflow/story/{name}/review.md

### After This Plan Completes
Continue resolving remaining review items in review.md.
After all items resolved: present implementation summary to user.
```

#### 5c. Completion

After all items are resolved:

1. Present implementation summary:
   ```markdown
   ## Implementation Complete

   | # | Item | Resolution |
   |---|------|------------|
   | 1 | {summary} | {what was done} |
   | 2 | {summary} | {what was done} |

   All {N} items resolved. LGTM to proceed, or provide additional feedback.
   ```
2. Wait for user response:
   - **LGTM**: Update Phase to `LGTM`, proceed to post-task
   - **Additional feedback**: Update Phase to `COLLECTING FEEDBACK`, return to Collection Phase (Step 4)

## Output Format

### After Presenting Summary

```markdown
## Review Summary

[Summary content as specified above]

Awaiting your feedback. Say "以上" when you've finished giving all feedback to start implementation.
```

### After Recording Minor Feedback

```markdown
Recorded: {description of approach}.

Any other feedback? Say "以上" when done to start implementation.
```

### After Recording Complex Feedback (post-dig)

```markdown
Recorded: {description of clarified approach}.

Any other feedback? Say "以上" when done to start implementation.
```

### After Recording Design Change

```markdown
Recorded as design change: {description}.

Any other feedback? Say "以上" when done to start implementation.
```

### After Implementation Complete

```markdown
## Implementation Complete

| # | Item | Resolution |
|---|------|------------|
| 1 | {summary} | {what was done} |

All {N} items resolved. LGTM to proceed, or provide additional feedback.
```

## Session Persistence

Review state is persisted in `review.md`. After completing each review item:

1. Inform the user that review state is saved to `review.md`
2. Mention that they can clear the session and resume later with `resume-work`
3. Do not force session clear — leave the decision to the user

This enables handling each feedback item in a separate session when the context becomes large.

## Success Criteria

### Process Criteria

- [ ] Review summary presented: Response contains "## Review Summary" section with spec path, self-review results, and NEEDS REVIEW items
- [ ] No autonomous action after summary: Response ends without implementation and without "Should I proceed?" question
- [ ] Collection Phase and Implementation Phase are clearly separated: No code changes during Collection Phase
- [ ] Minor feedback identified correctly: When both WHERE and WHAT are unambiguous, approach is recorded without implementation
- [ ] Minor feedback handled correctly: After recording, response includes "Recorded:" and prompts for more feedback
- [ ] Complex feedback identified correctly: When either WHERE or WHAT is ambiguous, dig skill is invoked before recording approach
- [ ] Complex feedback handled correctly: After dig, approach is recorded and response prompts for more feedback
- [ ] Design change identified correctly: When feedback requires new/changed spec success criteria, recorded as Design Change
- [ ] Completion signal handled correctly: "以上" triggers transition to Implementation Phase with item summary
- [ ] Implementation Phase processes all items: All APPROACH RECORDED items are implemented and marked RESOLVED
- [ ] Explicit LGTM obtained: User explicitly signals approval ("LGTM", "OK", "Looks good") before invoking post-task skill

### Outcome Criteria

- [ ] User doesn't need to re-explain: Each response directly addresses the feedback without requiring user to clarify what they already said
- [ ] No premature implementation: Code changes only occur during Implementation Phase, never during Collection Phase
- [ ] No excessive questioning: For Minor feedback, approach is recorded without asking "Should I proceed?"
- [ ] Batch implementation: All feedback items are implemented together, not one at a time

## Integration with Workflow

```
self-review (all PASS or NEEDS REVIEW)
    ↓ review.md created (by self-review)
    ↓ handoff invoked (by self-review)
    ↓ user copies prompt → /clear → pastes
    ↓ resume-work detects in_review → dispatches user-review
    ↓
user-review (this skill)
    ↓ Step 0: existing review.md found → resume COLLECTING FEEDBACK
    ↓ Collection Phase (record approaches)
    ↓ "以上" signal
    ↓ Implementation Phase (batch implement)
    ↓ (LGTM obtained)
post-task
```

## Anti-patterns to Avoid

| Anti-pattern | Why It's Wrong | Correct Behavior |
|--------------|----------------|------------------|
| Implementing during Collection Phase | Causes context switches, bloats session | Record approach only, implement in batch |
| Asking "Proceed with this fix?" per item | Interrupts feedback flow | Record and move on; implement all at once |
| Implementing immediately on any feedback | Skips clarification for complex cases | Classify first, then record approach |
| Assuming LGTM without explicit signal | User may have more feedback | Wait for explicit approval |
| Asking "Should I proceed?" after summary | User review is for feedback, not approval of summary | Wait silently for feedback |
| Proposing multiple interpretations | Adds cognitive load to user | Use dig to narrow down |

## Next Session

**Reference**:
- `.claude/dev-workflow/story/{name}/spec.md`
- `.claude/dev-workflow/story/{name}/plan.md`
- `.claude/dev-workflow/story/{name}/review.md`

**Next phase** (depends on review.md Phase):

| review.md Phase | Next phase |
|-----------------|------------|
| `COLLECTING FEEDBACK` | Continue `user-review` (resume Collection Phase from review.md) |
| `READY FOR IMPLEMENTATION` / `IMPLEMENTING` | Continue `user-review` (resume Implementation Phase from review.md) |
| `LGTM` | `post-task` |

Read spec, plan, and review.md, then invoke the appropriate skill.
