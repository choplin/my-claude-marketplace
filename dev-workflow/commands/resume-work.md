---
name: resume-work
description: Resume work on an existing Epic, Story, or Task. Counterpart to new-task for continuing interrupted work.
allowed-tools: Read, Glob, Grep, AskUserQuestion, Skill
---

# Resume Work

Resume work on an existing development task by evaluating current state and identifying the appropriate resumption point.

## Process

1. Launch the `dev-workflow:resume-work` skill
2. The skill will:
   - Discover existing documents (or use provided path)
   - Evaluate progress and actual state
   - Identify gaps between plan and reality
   - Recommend the appropriate next action

## Usage

```
/resume-work
/resume-work .claude/dev-workflow/feature-auth/plan.md
```

If a path is provided, skip document discovery and directly load the specified document.

## Action

Load and execute the `dev-workflow:resume-work` skill now.
