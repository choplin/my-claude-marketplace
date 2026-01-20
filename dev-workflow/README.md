# dev-workflow

Development workflow management for Claude Code.

## Overview

A plugin that systematizes development workflows with Claude Code. Supports workflow branching based on task scale and work continuation across sessions.

## Workflow Levels

| Level | Characteristics | Recommended Approach |
|-------|-----------------|---------------------|
| **Task** | 1-2 hours, few files | Direct implementation with Claude Code Plan |
| **Story** | Multiple steps, hours to days | Create spec/plan documents then implement |
| **Epic** | Multiple stories, weeks to months | Manage stories with epic document |

## Skills

| Skill | Description |
|-------|-------------|
| `assess-task` | Assess task complexity and suggest appropriate workflow |
| `create-epic` | Create epic document (manage multiple stories) |
| `create-spec` | Create spec document (AI self-reviewable acceptance criteria) |
| `create-plan` | Create implementation plan document (self-contained) |
| `self-review` | Self-review based on acceptance criteria |
| `post-task` | Commit preparation, knowledge capture, completion |

## Templates

| File | Description |
|------|-------------|
| `references/epic-template.md` | Epic document template |
| `references/spec-template.md` | Spec document template |
| `references/plan-template.md` | Plan document template |

## Typical Workflow (Story Level)

1. Assess task level with `assess-task`
2. Create spec document with `create-spec`
3. Create implementation plan with `create-plan`
4. (Session can be cleared)
5. Load plan document and implement
6. Verify acceptance criteria with `self-review`
7. Commit and complete with `post-task`

## Installation

Add to your `.claude/settings.json`:

```json
{
  "plugins": [
    "/path/to/dev-workflow"
  ]
}
```
