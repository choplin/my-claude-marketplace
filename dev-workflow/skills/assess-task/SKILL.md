---
name: assess-task
description: Use this skill when starting a new development task to assess its complexity and determine the appropriate workflow. Triggers on phrases like "I want to start this task", "assess this task", "how should I approach this", or when discussing a new feature, bug fix, or refactoring project.
allowed-tools: Read, Glob, Grep, AskUserQuestion
---

# Task Assessment

Assess task complexity and route to the appropriate workflow.

## Core Judgment Criterion

**"Will this complete in one session?"**

- Yes → **Task** (no documents needed)
- No → **Story/Epic** (documents required)

Why this matters: When a session clears, conversation history is lost. If work spans sessions, context must be preserved in documents.

## Task Levels

| Level | Criterion | Documents | Start Method |
|-------|-----------|-----------|--------------|
| Task | Completes in 1 session | None | Claude Code Plan |
| Story | Spans multiple sessions | spec + plan | From documents |
| Epic | Multiple independent stories | epic + story docs | Story by story |

## Assessment Process

### Step 1: Confirm Context Layer (Always Required)

Confirm **Why** and **What**:

1. **Why**: Background, motivation, problem to solve
2. **What**: Concrete implementation target

If unclear, ask questions to clarify.

### Step 2: Assess Approach Layer

**What ≈ How Check** (if ALL are Yes → Task):
- [ ] Only one implementation pattern is viable
- [ ] Can start without investigation or comparison
- [ ] No mid-implementation direction changes expected

If any is No, or if you're uncertain → Start as Task, promote if needed.

### Step 3: Promote if Needed

**Task → Story promotion is a normal flow**, not a failure.

Promotion triggers:
- Discovered complexity during implementation
- Need to document decisions for future reference
- Session boundary approaching with work remaining

When promoting:
- Why/What carry over as-is
- Document the How (approach, alternatives considered)
- Create spec as an "evolving document"

## Output Format

```markdown
## Assessment Result

**Level**: Task / Story / Epic

**Context**:
- Why: {background/motivation}
- What: {implementation target}

**Rationale**:
- {basis for level determination}

**Next Action**:
- Task → Use Claude Code Plan to implement
- Story → Use `create-spec` skill
- Epic → Use `create-epic` skill
```

## Key Principles

1. **Don't aim for perfect upfront design** - Start as Task, promote when needed
2. **Estimation errors are normal** - Task → Story promotion is expected
3. **Use operational criteria** - Check concrete conditions, not gut feelings
4. **Session boundary is the real issue** - "Will context be lost?" is the question
