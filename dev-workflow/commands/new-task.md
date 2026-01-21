---
name: new-task
description: Start a new development task with assessment. Launches explore-needs skill to determine task complexity and route to appropriate workflow.
allowed-tools: Read, Glob, Grep, AskUserQuestion, Skill
---

# New Task

Start a new development task by assessing its complexity and routing to the appropriate workflow.

## Process

1. Launch the `dev-workflow:explore-needs` skill
2. The skill will conduct an interview to understand Why/What
3. Based on assessment, route to:
   - **Task**: Create detailed Plan with Why/What/How, then request ExitPlanMode
   - **Story**: Launch `dev-workflow:create-spec` skill
   - **Epic**: Launch `dev-workflow:create-epic` skill

## Usage

```
/new-task
/new-task implement user authentication
```

If arguments are provided, use them as initial task description for the interview.

## Action

Load and execute the `dev-workflow:explore-needs` skill now.
