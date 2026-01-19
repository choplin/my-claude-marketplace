# git-helpers

Git helper skills for Claude Code.

## Skills

| Skill | Description |
|-------|-------------|
| `rebase-onto-rewritten` | Rebase onto force-pushed/squashed branches |

## When Skills Activate

- **rebase-onto-rewritten**: "rebase onto rewritten", "base branch was force pushed", "squash merged base"

## Use Cases

- Base branch was squash-merged
- Base branch was force-pushed
- Normal rebase fails due to history rewriting

The skill cherry-picks commits one-by-one for better handling of file moves and renames.

## Installation

Add to your `.claude/settings.json`:

```json
{
  "plugins": [
    "/path/to/git-helpers"
  ]
}
```
