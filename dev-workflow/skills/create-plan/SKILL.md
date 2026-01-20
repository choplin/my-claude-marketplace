---
name: create-plan
description: Use this skill when creating an implementation plan for a story. Triggers on phrases like "create a plan", "plan the implementation", "how should I implement this", or after a spec has been created.
allowed-tools: Read, Write, Glob, Grep
file-references:
  - ${CLAUDE_PLUGIN_ROOT}/references/plan-template.md
---

# Create Plan Document

Create an implementation plan document based on a spec.

## Prerequisites

- Spec document exists (`docs/spec-{name}.md`)

## When to Use

- After spec has been created
- When you want to organize implementation order and steps
- When continuing work across sessions

## Process

### 1. Review Spec

Load the related spec document:
- Understand requirements
- Confirm acceptance criteria
- Note technical notes

### 2. Investigate Codebase

Gather information needed for implementation:
- Existing code structure
- Files that need changes
- Existing patterns to reference

### 3. Design Implementation Steps

Follow these principles:
- **Dependency-aware ordering**: Start with prerequisites
- **Appropriate granularity**: 1 step = 1 clear deliverable
- **Verifiable**: Each step completion can be confirmed

### 4. Create Plan Document

Create `docs/plan-{name}.md`:
- Include reference to spec
- List files to change
- Implementation steps
- Progress checklist

## Template

See `references/plan-template.md` for the template.

## Self-Contained Document

Plan document should be **self-contained**:

- Implementation can start without conversation history
- Work can continue by just reading the plan document in a new session
- All necessary information is included in the plan document

## Output

- `docs/plan-{name}.md` file
- Ready to start implementation

## After Creating Plan

After plan creation:
1. Session can be cleared
2. Load plan document and start implementation
3. Update progress section as you work
