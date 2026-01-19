# Lightweight Execution Plans (ExecPlan-Light)

This document describes the lightweight execution plan format for single-session tasks. Use this format when a task can be completed within one Claude Code session and does not require complex architectural decisions.

## When to Use ExecPlan-Light

Use this format when:
- The task affects 1-3 files
- Requirements are clear and well-defined
- No significant architectural decisions are needed
- The work can be completed in one session
- Risk is low and changes are easily reversible

## Format Requirements

ExecPlan-Light files should be saved as `ExecPlan-Light.md` in the project root.

## Skeleton

    # [Task Name]

    ## Purpose

    [What this task accomplishes in 1-2 sentences]

    ## Current State

    [Brief description of the current situation]

    ## Plan

    [What changes will be made]

    - Change 1: [file/location] - [what to change]
    - Change 2: [file/location] - [what to change]

    ## Progress

    - [ ] Step 1
    - [ ] Step 2
    - [ ] Step 3

    ## Decision Log

    [Record any non-trivial decisions made during implementation]

    - Decision: ...
      Rationale: ...

    ## Result

    [Filled after completion]

    - What was done:
    - Verification:
    - Notes:

## Guidelines

1. **Keep it concise**: This is for simple tasks; don't over-document
2. **Update Progress**: Mark items complete as you work
3. **Record decisions**: Even in simple tasks, note why you chose an approach
4. **Verify completion**: The Result section should confirm the task is done

## Comparison with Full ExecPlan

| Aspect | ExecPlan-Light | Full ExecPlan |
|--------|----------------|---------------|
| Scope | 1-3 files, single session | Multi-file, multi-session |
| Approval | No approval gate | Requires user approval |
| Sections | 6 required | 12+ required |
| Milestones | Not required | Required with acceptance criteria |
| Living Document | Minimal updates | Continuous updates required |

## Example

    # Fix README Typo

    ## Purpose

    Correct the misspelled word "recieve" to "receive" in README.md.

    ## Current State

    README.md line 42 contains "recieve" which should be "receive".

    ## Plan

    - Change 1: README.md line 42 - Fix spelling of "receive"

    ## Progress

    - [x] Locate the typo
    - [x] Fix the spelling
    - [x] Verify no other instances

    ## Decision Log

    (No significant decisions for this task)

    ## Result

    - What was done: Fixed typo on line 42
    - Verification: grep confirmed no remaining instances of "recieve"
    - Notes: None
