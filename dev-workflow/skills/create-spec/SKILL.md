---
name: create-spec
description: This skill is invoked ONLY from explore-needs when Story-level work is identified. Should NOT be invoked directly by user or auto-triggered by AI. Creates spec document with requirements and acceptance criteria.
allowed-tools: Read, Write, Glob, Grep, AskUserQuestion
user-invocable: false
---

# Create Spec Document

Create a specification document that captures requirements and acceptance criteria for Story-level work.

## Purpose

Create a spec document that captures What and Why for Story-level work.

### What Spec Is NOT

Spec is **NOT** an exhaustive specification. It does not aim for:
- Complete coverage of all edge cases
- Trivial validation checks
- Rare corner case handling

These are implementation details to be addressed during the implementation phase.

### What Spec IS

Spec aims to **keep focus on task purpose**. It captures:
- **Why**: The motivation and problem being solved
- **What**: User Needs expressed as MECE requirements
- **Criteria**: Verifiable acceptance criteria (few, focused)

The spec survives /clear as the sole source of truth for the next session.

## Critical Rule: Fewer Criteria is Better

**Problem from experience**: When AI generates acceptance criteria, it tends to create many items. This leads to:
1. Review cost increases
2. User skips review due to volume
3. Implementation proceeds with criteria that don't reflect user intent

**Rule**: Requirements and Criteria should be as few as possible while still covering User Needs.
- Ask: "Is this criterion essential to verify the User Need is met?"
- If not essential, don't include it
- Prefer 3-5 focused criteria over 10+ comprehensive ones

## Input

This skill receives Why/What context from explore-needs interview via session history.

## Process

### 1. Confirm Why/What from explore-needs

Review the interview results:
- **Why**: Background, motivation, problem being solved
- **What**: User Needs to satisfy

If unclear, ask for clarification.

### 2. Organize Requirements

Expand User Needs into specific Requirements:
- What the system must do (functional)
- Constraints it must satisfy (non-functional)

Keep requirements minimal. Only include what's necessary for User Needs.

### 3. Define Acceptance Criteria (Gherkin)

**Why Gherkin?**: Adopting a well-established format eliminates ambiguity about methodology and clarifies expectations.

Write each criterion in Given-When-Then format:

```gherkin
Scenario: {Criterion name}
  Given {Preconditions - what exists/is prepared}
  When {Action - specific action to perform}
  Then {Verifiable result - confirmable in code or files}
```

**Example**:
```gherkin
Scenario: Successful login
  Given user is on the login page
  When user enters valid credentials and clicks login
  Then user is redirected to dashboard with username displayed
```

Each criterion must be:
- **Verifiable**: AI can determine PASS/FAIL by checking code or files
- **User-confirmed**: Traceable to user statement
- **Essential**: Necessary to verify User Need is met

### 4. Define Out of Scope

Explicitly state what this spec does NOT include. This prevents scope creep during implementation.

### 5. Create Spec Document

Create `.claude/dev-workflow/story/{name}/spec.md`:

```markdown
# Spec: {title}

## Related Files

- **Workflow concepts**: `dev-workflow/docs/workflow-concepts.md`
- **Epic**: `.claude/dev-workflow/epic/{parent}/epic.md` (if part of an Epic)

## Why
{Background, motivation, problem being solved - from explore-needs interview}

## What
{User Needs to satisfy - from explore-needs interview}

## Requirements
{Specific requirements derived from User Needs}

## Acceptance Criteria

### Scenario: {name}
- Given: {preconditions}
- When: {action}
- Then: {verifiable result}

## Out of Scope
{What this spec explicitly does NOT include}

## TBD
{Items that need clarification later, if any}
```

### 6. User Review

Present spec to user for approval before proceeding.

## Success Criteria

- [ ] Why/What from explore-needs is captured
- [ ] Requirements are MECE relative to User Needs (see Purpose for what spec is NOT)
- [ ] Acceptance criteria are in Gherkin format
- [ ] Acceptance criteria are few and focused (prefer 3-5 over 10+)
- [ ] Each criterion is verifiable by AI: Can identify specific file(s) or code section(s) to check for PASS/FAIL. If no verification target can be named, the criterion is not verifiable.
- [ ] Out of Scope is explicitly stated
- [ ] User has approved the spec

## Next Action

After spec is approved, suggest creating implementation plan using `create-plan` skill.
