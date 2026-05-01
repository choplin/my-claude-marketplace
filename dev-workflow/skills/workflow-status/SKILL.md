---
name: workflow-status
description: Show overview of all active Epics and Stories with their current status. Use this to get a bird's-eye view of development progress before resuming work. Triggers on "/workflow-status", "show status", "what's in progress", "overview of work". Should NOT trigger for resuming a specific task (use resume-work) or starting new work (use kickoff).
allowed-tools: Read, Glob, Grep
user-invocable: true
---

# Status Overview

Display a summary of all active Epics and Stories under `.claude/dev-workflow/`.

## Tool Usage Constraints

- **Read-only**: This skill only reads files. No modifications, no Bash commands.

## Process

### Phase 1: Document Discovery

Scan for all existing work documents:

1. **Epics**: Glob for `.claude/dev-workflow/epic/*/epic.md`
2. **Stories**: Glob for `.claude/dev-workflow/story/*/spec.md`

If no documents are found at all, output:

```
Active な作業はありません。
`/dev-workflow:kickoff` で新しい作業を開始できます。
```

Then stop.

### Phase 2: Status Extraction

#### Epic Status

For each `epic.md` found:

1. Read the file
2. Find the `## Stories` section
3. Parse the Stories table rows
4. Count rows where Status column is `Done` vs total rows
5. Record: `{Done count}/{Total count} Stories Done`

#### Story Status

For each story directory found (containing `spec.md`):

1. Check which files exist in the directory:
   - `spec.md` (always present — this is how we found the story)
   - `plan.md`
   - `review.md`

2. Determine status based on file presence and content:

| Priority | Condition | Status |
|----------|-----------|--------|
| 1 | `review.md` exists, Phase = `LGTM` | Done |
| 2 | `review.md` exists, Phase = `REVIEWING` | Review: In Progress |
| 3 | `plan.md` exists, all Progress items checked (`[x]`) | Implementation Complete |
| 4 | `plan.md` exists, some Progress items checked | In Progress ({done}/{total} steps) |
| 5 | `plan.md` exists, no Progress items checked | Planned |
| 6 | `spec.md` only | Spec Created |

**How to check review.md Phase**:
- Read `review.md` and find the line matching `- **Phase**: {value}`
- Extract the value (REVIEWING, LGTM, or legacy values: COLLECTING FEEDBACK, READY FOR IMPLEMENTATION, IMPLEMENTING — all non-LGTM values map to REVIEWING)

**How to check plan.md Progress**:
- Read `plan.md` and find the `## Progress` section
- Count lines matching `- [x]` (done) and `- [ ]` (pending)
- Total = done + pending
- If total is 0, treat as Planned

#### Epic-Story Association

For each story, determine if it belongs to an epic:
- Read each `epic.md` Stories table
- If a story name appears in an epic's Stories table, associate it with that epic
- If a story does not appear in any epic, it is independent (Epic = `-`)

### Phase 3: Display

Output the status overview in this format:

```markdown
## Epics

| Epic | Status |
|------|--------|
| {epic-name} | {done}/{total} Stories Done |

## Stories

| Story | Epic | Status |
|-------|------|--------|
| {story-name} | {epic-name or -} | {status} |
```

**Display rules**:
- Sort Epics alphabetically by name
- Sort Stories alphabetically by name
- If no Epics exist, omit the Epics section entirely
- If no Stories exist, omit the Stories section entirely
- Epic name is the directory name (from the path `.claude/dev-workflow/epic/{epic-dir}/epic.md`), formatted as `{yyyy-mm-dd}-{epic-name}`
- Story name is the directory name (from the path `.claude/dev-workflow/story/{story-dir}/spec.md`), formatted as `{yyyy-mm-dd}-{prefix}-{story-name}`

## Success Criteria

- [ ] All existing epics and stories under `.claude/dev-workflow/` are discovered
- [ ] Each item's status is correctly determined based on file presence and content
- [ ] Output is displayed as a clear, readable table
- [ ] When no work exists, a helpful empty-state message is shown
