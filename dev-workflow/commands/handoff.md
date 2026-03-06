---
name: handoff
description: Generate handoff prompt for seamless session continuation. Use before /clear to preserve context.
allowed-tools: Read, Glob, Grep, Bash, AskUserQuestion
---

# Handoff

Generate a copy-pasteable prompt for seamless session continuation after `/clear`.

## Process

1. Launch the `dev-workflow:handoff` skill
2. The skill will:
   - Discover active work units
   - Snapshot current state
   - Generate a handoff prompt for the next session

## Usage

```
/handoff
```

## Action

Load and execute the `dev-workflow:handoff` skill now.
