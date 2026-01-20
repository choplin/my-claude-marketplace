---
name: create-spec
description: Use this skill when creating a specification document for a story. Triggers on phrases like "create a spec", "write a specification", "document the requirements", or when starting a story-level task.
allowed-tools: Read, Write, Glob, Grep
file-references:
  - ${CLAUDE_PLUGIN_ROOT}/references/spec-template.md
---

# Create Spec Document

Create a specification document with AI-verifiable acceptance criteria.

## When to Use

- Starting a story-level task
- When multiple implementation steps are needed
- When you want to clarify acceptance criteria
- When work may span across sessions

## Process

### 1. Organize Requirements

Confirm the following:
- What to accomplish (Goal)
- Why it's needed (Background)
- How far to go (Scope)
- Technical constraints

### 2. Design Acceptance Criteria

Write in **AI self-review verifiable format**:

```markdown
1. **{Criterion name}**
   - Given: {Preconditions - what exists/is prepared}
   - When: {Action - specific action to perform}
   - Then: {Verifiable result - confirmable in code or files}
```

**Good example**:
```markdown
1. **Login functionality works**
   - Given: User is on the login page
   - When: Enter correct email and password and click login button
   - Then: Redirected to dashboard and username is displayed
```

**Bad example**:
```markdown
1. Implement login functionality
```
(Verification method is unclear)

### 3. Create Spec Document

Create `docs/spec-{name}.md`:
- Follow the template
- Make acceptance criteria specific and verifiable
- Explicitly state what's out of scope

## Template

See `references/spec-template.md` for the template.

## Acceptance Criteria Guidelines

Acceptance criteria should satisfy:

1. **Specific**: Avoid vague expressions
2. **Verifiable**: AI can determine by checking code or files
3. **Independent**: Each criterion can be verified individually
4. **Complete**: Covers all requirements

## Output

- `docs/spec-{name}.md` file
- Suggest creating plan as next action
