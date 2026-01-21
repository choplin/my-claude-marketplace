---
name: continue-discussion
description: Save discussion state for continuation in a future session
---

Use the `continue-discussion` skill to save the current discussion state.

Load the skill and follow the workflow:

1. **Clarify** - Confirm understanding with user via AskUserQuestion
2. **Draft** - Create discussion file at `.claude/discussions/yyyymmdd-{topic}.md`
3. **Review** - Use subagent to verify context is captured
4. **Approval** - Get user confirmation
