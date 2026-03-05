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
- **Workflow Context**: Current phase, work level, post-work instructions (if exists in plan)

### Phase 3: State Evaluation

Assess actual state by:
1. Reading Progress section from documents
2. Checking actual file changes (if referenced files exist)
3. Running any verification commands if defined in acceptance criteria

**State categories** (evaluated in priority order):

| Priority | State | Condition |
|----------|-------|-----------|
| 1 | `review_complete` | review.md exists and Phase = LGTM |
| 2 | `in_review` | review.md exists and Phase ≠ LGTM (AWAITING FEEDBACK or IN PROGRESS) |
| 3 | `potentially_complete` | plan.md exists and all Progress items are `[x]` |
| 4 | `in_progress` | plan.md exists and some Progress items are `[x]` |
| 5 | `planned` | plan.md exists but no Progress items are `[x]` |
| 6 | `spec_only` | spec.md exists but no plan.md |
| 7 | `epic_next_story` | epic.md with Stories that have "Not Started" status |
| 8 | `blocked` | Implementation cannot proceed (missing dependencies, etc.) |

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

| State | Recommended Action | Target Skill |
|-------|-------------------|--------------|
| `spec_only` | Create implementation plan | `dev-workflow:create-plan` |
| `planned` | Begin implementation from step 1 | _(none — read plan and implement)_ |
| `in_progress` | Continue from last completed step | _(none — read plan and continue)_ |
| `potentially_complete` | Run self-review | `dev-workflow:self-review` |
| `in_review` | Resume user review | `dev-workflow:user-review` |
| `review_complete` | Run post-task | `dev-workflow:post-task` |
| `epic_next_story` | Start next Story | `dev-workflow:create-spec` |
| `blocked` | Report blockers, suggest resolution | _(depends on blocker)_ |
| Major divergence | Suggest plan update first | `dev-workflow:create-plan` |

**Workflow Context integration**: If the plan document contains a `## Workflow Context` section, use it to:
- Include post-work instructions in the recommendation (e.g., "After completing remaining steps, invoke self-review")
- Confirm the recommended action aligns with the workflow sequence

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
**Post-work**: [instructions from Workflow Context, e.g., "After all steps complete, invoke self-review → user-review → commit → post-task"]

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

### Phase 8: Skill Dispatch

After user confirms the recommended action, **dispatch to the appropriate skill or begin implementation**. This is the critical step that reconnects to the dev-workflow flow.

#### Dispatch Table

Use the `Skill` tool to invoke the appropriate skill:

| State | Dispatch |
|-------|----------|
| `spec_only` | `Skill(skill: "dev-workflow:create-plan")` |
| `planned` | _(No skill — see Implementation Handoff below)_ |
| `in_progress` | _(No skill — see Implementation Handoff below)_ |
| `potentially_complete` | `Skill(skill: "dev-workflow:self-review")` |
| `in_review` | `Skill(skill: "dev-workflow:user-review")` |
| `review_complete` | `Skill(skill: "dev-workflow:post-task")` |
| `epic_next_story` | `Skill(skill: "dev-workflow:create-spec")` |
| Update spec requested | `Skill(skill: "dev-workflow:create-spec")` |
| Update plan requested | `Skill(skill: "dev-workflow:create-plan")` |

**CRITICAL**: For states with a target skill, you MUST invoke it via the `Skill` tool. Do NOT attempt to replicate the skill's behavior manually. The skills contain workflow context and process definitions that ensure correct flow continuation.

#### Implementation Handoff (`planned` / `in_progress`)

When the state is `planned` or `in_progress`, there is no dedicated implementation skill. Instead, ensure proper workflow continuation by following these steps:

1. **Read documents**: Load both spec.md and plan.md fully into context
2. **Locate Workflow Context**: Find the `## Workflow Context` section in plan.md
3. **Identify resumption point**: From `## Progress`, find the first unchecked `- [ ]` step
4. **Begin/continue implementation**: Follow the plan steps sequentially
5. **Track progress**: Update `## Progress` as each step completes (`- [ ]` → `- [x]`)
6. **After all steps complete**: Invoke `Skill(skill: "dev-workflow:self-review")` — this is specified in plan's Workflow Context and MUST NOT be skipped

This handoff ensures the AI follows the full workflow (implement → self-review → user-review → post-task) even without a dedicated implementation skill.

## Integration with Other Skills

| State | Skill Invoked | Invocation |
|-------|--------------|------------|
| `spec_only` | `dev-workflow:create-plan` | `Skill(skill: "dev-workflow:create-plan")` |
| `planned` / `in_progress` | _(implementation, then self-review)_ | Read plan, implement, then `Skill(skill: "dev-workflow:self-review")` |
| `potentially_complete` | `dev-workflow:self-review` | `Skill(skill: "dev-workflow:self-review")` |
| `in_review` | `dev-workflow:user-review` | `Skill(skill: "dev-workflow:user-review")` |
| `review_complete` | `dev-workflow:post-task` | `Skill(skill: "dev-workflow:post-task")` |
| `epic_next_story` | `dev-workflow:create-spec` | `Skill(skill: "dev-workflow:create-spec")` |
| Spec needs update | `dev-workflow:create-spec` | `Skill(skill: "dev-workflow:create-spec")` |
| Plan needs update | `dev-workflow:create-plan` | `Skill(skill: "dev-workflow:create-plan")` |
| Task promotion needed | `dev-workflow:create-spec` | `Skill(skill: "dev-workflow:create-spec")` |

## Anti-Patterns

### Avoid Starting Fresh

If documents exist, DO NOT:
- Ask user to re-explain their requirements
- Create new documents without acknowledging existing ones
- Ignore progress already made

### Avoid Skipping Skill Dispatch

After state evaluation, DO NOT:
- Start implementing without invoking the target skill (e.g., jumping to code review without invoking `self-review`)
- Manually replicate a skill's behavior instead of invoking it via `Skill` tool
- Skip `self-review` after implementation completes — this breaks the workflow chain

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

| State | Next Phase | Skill |
|-------|------------|-------|
| `spec_only` | Create plan | `dev-workflow:create-plan` |
| `planned` | Implementation → self-review | _(implement, then `dev-workflow:self-review`)_ |
| `in_progress` | Continue implementation → self-review | _(continue, then `dev-workflow:self-review`)_ |
| `potentially_complete` | Self-review | `dev-workflow:self-review` |
| `in_review` | User review | `dev-workflow:user-review` |
| `review_complete` | Post-task | `dev-workflow:post-task` |
| `epic_next_story` | Create spec for next Story | `dev-workflow:create-spec` |
