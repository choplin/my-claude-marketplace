---
name: resume-work
description: Use this skill to resume work on an existing Epic, Story, or Task. Triggers on phrases like "resume work", "continue previous task", "pick up where I left off", "what was I working on", or when user wants to continue existing development work. Should NOT trigger for starting new tasks (use explore-needs), or for discussion continuity without implementation artifacts.
allowed-tools: Read, Glob, Grep, AskUserQuestion, Skill, Bash
---

# Resume Work

Resume work on an existing Epic, Story, or Task by evaluating current state and identifying the appropriate resumption point.

## Tool Usage Constraints

- **Bash**: ONLY for git branch operations (`git branch --show-current`, `git checkout`, `git status --porcelain`, `git stash`). No other use.

## Purpose

Enable seamless continuation of development work across sessions by:
1. Detecting existing work documents
2. Evaluating progress against plans
3. Identifying gaps between plan and reality
4. Recommending the appropriate next action

## Process

### Phase 1: Document Discovery

If path is provided as argument:
- Read the specified document directly

If no path provided:
- Scan `.claude/dev-workflow/` for existing documents
- List found documents with basic metadata
- Present options to user for selection

**Scan pattern**:
```
.claude/dev-workflow/
  └── {work-unit}/
        ├── epic.md
        ├── spec.md
        ├── plan.md
        └── review.md
```

### Phase 2: Document Loading

Read the selected document(s) and extract:
- **Type**: Epic / Story / Task
- **Spec path**: If exists
- **Plan path**: If exists
- **Review path**: If exists
- **Review Phase**: Phase value from review.md (AWAITING FEEDBACK / IN PROGRESS / LGTM)
- **Review Items**: Count of OPEN, IN PROGRESS, and RESOLVED items
- **Branch**: Name and Base from spec.md `## Branch` section (if exists)
- **Why**: Background and motivation
- **What**: Implementation target and success criteria
- **Progress**: Current implementation status

### Phase 3: State Evaluation

Assess actual state by:
1. Reading Progress section from documents
2. Checking actual file changes (if referenced files exist)
3. Running any verification commands if defined in acceptance criteria

**State categories**:
- `not_started`: No implementation artifacts found
- `in_progress`: Partial implementation detected
- `potentially_complete`: All planned items appear done, needs review
- `in_review`: review.md exists and Phase is not LGTM (AWAITING FEEDBACK or IN PROGRESS)
- `review_complete`: review.md exists and Phase is LGTM
- `blocked`: Implementation cannot proceed (missing dependencies, etc.)

### Phase 4: Gap Analysis

Compare plan with actual state:

| Check | Detection Method |
|-------|------------------|
| Unimplemented items | Plan steps marked as pending vs actual code |
| Divergence from plan | Implemented but not in plan |
| Incomplete implementations | Partial code, TODO comments |
| Failed tests | Run tests if test commands defined |

Output gap summary:
- What's done vs what's planned
- Unexpected changes
- Potential issues

### Phase 5: Resumption Point Decision

Based on state and gaps, recommend one of:

| State | Recommended Action |
|-------|-------------------|
| `not_started` | Begin implementation from step 1 |
| `in_progress` | Continue from last completed step |
| `potentially_complete` | Invoke `self-review` skill |
| `in_review` | Invoke `user-review` skill (resumes from review.md) |
| `review_complete` | Invoke `post-task` skill |
| `blocked` | Report blockers, suggest resolution |
| Major divergence | Suggest plan update before continuing |

### Phase 6: Report and Propose

Output structured report:

```markdown
## Resume Work Report

### Document Summary
- **Type**: [Epic/Story/Task]
- **Spec**: [path or N/A]
- **Plan**: [path or N/A]
- **Branch**: [spec branch name] (current: [current git branch])

### Context
**Why**: [Brief summary of motivation]
**What**: [Brief summary of target]

### Progress Assessment

| Step | Status | Notes |
|------|--------|-------|
| 1. [description] | ✅ Done | |
| 2. [description] | 🔄 In Progress | [partial details] |
| 3. [description] | ⬜ Pending | |

### Review Status (if review.md exists)
- **Phase**: [AWAITING FEEDBACK / IN PROGRESS / LGTM]
- **Items**: [N OPEN, N IN PROGRESS, N RESOLVED]

### Gap Analysis
[Summary of differences between plan and current state]

- **On track**: [items proceeding as planned]
- **Divergence**: [items that differ from plan]
- **Issues**: [problems detected]

### Recommended Action

**Resume from**: [step/phase]

Options:
1. [Primary recommendation] (Recommended)
2. [Alternative action]
3. [Update plan first, then continue]
```

### Phase 7: Branch Checkout

If spec.md contains a `## Branch` section:

1. **Check current branch**: Run `git branch --show-current`
2. **Already on correct branch**: If current branch matches spec branch name, report "Already on branch {name}" and skip
3. **Different branch**: If on a different branch:
   - Check for uncommitted changes with `git status --porcelain`
   - If uncommitted changes exist, present options:
     - Stash changes and switch (`git stash && git checkout {branch}`)
     - Stay on current branch
     - Commit first before switching
   - If clean, ask user: "Switch to {branch-name}?"
4. **Execute only after user approval**: Run `git checkout {branch-name}`

If no Branch section in spec, skip this phase.

### Phase 8: User Confirmation and Execution

Wait for user selection before proceeding.

Based on selection:
- **Continue implementation**: Proceed from identified step
- **Run self-review**: Invoke `dev-workflow:self-review` skill
- **Update plan**: Update plan document, then continue
- **Update spec**: If requirements changed, update spec first

## Integration with Other Skills

| Scenario | Skill Invoked |
|----------|--------------|
| Ready for review | `dev-workflow:self-review` |
| In review (review.md exists, not LGTM) | `dev-workflow:user-review` |
| Review complete (review.md Phase=LGTM) | `dev-workflow:post-task` |
| Spec needs update | `dev-workflow:create-spec` (update mode) |
| Plan needs update | `dev-workflow:create-plan` (update mode) |
| Task promotion needed | `dev-workflow:create-spec` (promote Task to Story) |

## Anti-Patterns

### Avoid Starting Fresh

If documents exist, DO NOT:
- Ask user to re-explain their requirements
- Create new documents without acknowledging existing ones
- Ignore progress already made

### Avoid Assumptions

When gap analysis shows ambiguity:
- Report findings clearly
- Ask user for clarification
- Do not assume which divergence is "correct"

## Success Criteria

- [ ] Existing documents are discovered or specified path is validated
- [ ] Document content is correctly parsed (Type, Spec, Plan, Progress)
- [ ] Actual state is evaluated against plan
- [ ] Gap analysis clearly identifies differences
- [ ] Recommended action is appropriate for the state
- [ ] User approves action before execution
- [ ] Correct skill is invoked based on user selection

## Next Session

This skill evaluates state and recommends the next phase. After user selects action:

| Document State | Next phase |
|----------------|------------|
| spec.md only | `create-plan` |
| spec.md + plan.md, not started | Implementation |
| spec.md + plan.md, in progress | Continue implementation |
| spec.md + plan.md, potentially complete | `self-review` |
| spec.md + plan.md + review.md (not LGTM) | `user-review` |
| spec.md + plan.md + review.md (LGTM) | `post-task` |
| epic.md | `create-spec` for next Story |
