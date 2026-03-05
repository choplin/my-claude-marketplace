---
name: workflow-status
description: Show overview of all active Epics and Stories. Useful for understanding current work status before resuming.
allowed-tools: Read, Glob, Grep
---

# Status

Display an overview of all active development work (Epics and Stories) with their current status.

## Process

1. Launch the `dev-workflow:workflow-status` skill
2. The skill will:
   - Scan for existing epic and story documents
   - Determine the status of each item
   - Display a summary table

## Usage

```
/workflow-status
```

## Action

Load and execute the `dev-workflow:workflow-status` skill now.
