---
name: create-epic
description: Use this skill when creating an epic document for a large-scale project. Triggers on phrases like "create an epic", "start a new epic", "this is a big project", or when a task is assessed as epic-level.
allowed-tools: Read, Write, Glob
file-references:
  - ${CLAUDE_PLUGIN_ROOT}/references/epic-template.md
---

# Create Epic Document

Create an epic document for large-scale projects that span multiple stories.

## When to Use

- Projects that can be decomposed into multiple independent stories
- Large undertakings spanning weeks to months
- Work involving architecture changes
- Long-term projects requiring progress tracking

## Process

### 1. Gather Information

Confirm the following:
- Project purpose and goals
- Scope of features and changes to include
- Technical constraints and assumptions
- Identified risks

### 2. Story Decomposition

Break down the epic into independent stories:
- Each story can be implemented independently
- Each story has clear completion criteria
- Note order if there are dependencies

### 3. Create Epic Document

Create `docs/epic-{name}.md`:
- Follow the template
- Include story list
- Prepare links to spec/plan for each story

## Template

See `references/epic-template.md` for the template.

## Output

- `docs/epic-{name}.md` file
- Suggest creating spec for the first story as next action

## Important

- Prioritize story independence
- Size each story to be completable in 1-2 days
- Split out parts with high uncertainty as separate stories
