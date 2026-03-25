# Review.md Initialization Guide

Shared procedure for creating `review.md` when it does not yet exist. Referenced by `self-review`, `user-review`, and `import-pr-comments` skills.

## 1. Determine Work Level

1. Check if `.claude/dev-workflow/story/{name}/spec.md` exists for any `{name}`
   - If found → **Story**
2. If not found → **Task**
   - Search `.claude/plans/*.md` for a file containing `**Work level**: Task` in its `## Workflow Context` section
   - If found → **Task (with plan)**
   - If not found → **Task (no plan)**

## 2. Resolve Metadata

| Field | Story | Task (with plan) | Task (no plan) |
|-------|-------|-------------------|----------------|
| Title | spec.md title (`# {title}`) | Plan file title (`# Plan: {name}`) | Git branch name (prefix removed) |
| Spec path | `story/{name}/spec.md` | N/A | N/A |
| Plan path | `story/{name}/plan.md` | Claude Code plan file path | N/A |
| review.md path | `story/{name}/review.md` | `task/{name}/review.md` | `task/{branch-name}/review.md` |

### Branch Name Normalization (Task no plan)

1. Get current branch: `git branch --show-current`
2. Remove common prefixes: `feat/`, `fix/`, `refactor/`, `chore/`, `docs/`, `test/`, `build/`
3. Convert to kebab-case (replace `_` and `/` with `-`, lowercase)
4. If branch is `main` or `master`: ask the user for a task name instead of proceeding

## 3. Task Plan Discovery

To find the active Task plan file:

1. Glob `.claude/plans/*.md`
2. For each file, check if `## Workflow Context` section contains `**Work level**: Task`
3. If multiple matches, use the most recently modified file
4. Return the path to the matched plan file, or `null` if none found

## 4. Self-Review Results Section

The content of the Self-Review Results table depends on which skill creates `review.md`:

| Creator | Self-Review Results content |
|---------|---------------------------|
| **self-review** | Actual review results from the self-review process |
| **user-review** | `| - | Self-review | SKIPPED | Self-review was not performed |` |
| **import-pr-comments** | `| - | Self-review | SKIPPED | Self-review was not performed |` |

## 5. Create review.md

1. Check for existing `review.md` at the resolved path — if it exists, ask user before overwriting
2. Read `references/review-template.md`
3. Fill in the template:
   - **Title**: Resolved title from step 2
   - **Related Files**: Based on work level:
     - **Story**: Spec and Plan paths
     - **Task (with plan)**: Plan path only (no Spec line)
     - **Task (no plan)**: No Related Files entries (remove both Spec and Plan lines, or add a comment `<!-- No plan file associated -->`)
   - **Self-Review Results**: Based on creator (see step 4)
   - **Review Items**: Empty (no feedback yet)
   - **Phase**: `COLLECTING FEEDBACK`
   - **Resolved**: `0 / 0`
4. Write to the resolved `review.md` path
