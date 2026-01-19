# dev-workflow

Development workflow skills for Claude Code.

## Skills

| Skill | Description |
|-------|-------------|
| `adr` | Create Architecture Decision Records |
| `continuity-ledger` | Maintain context across long sessions via CONTINUITY.md |

## When Skills Activate

- **adr**: "create an ADR", "record this decision", "document this architecture decision"
- **continuity-ledger**: Starting complex or multi-hour work sessions

## ADR Format

ADRs are stored in `docs/adr/` with format `ADR-{number}-{slug}.md`:

```markdown
---
created: YYYY-MM-DD
---

# ADR-{number}: {title}

## Status
## Context
## Decision
## Discussion
## Consequences
## Alternatives
## References
```

## Continuity Ledger

The continuity-ledger skill maintains a `CONTINUITY.md` file tracking:

- Goal and success criteria
- Constraints and assumptions
- Key decisions with rationale
- Progress state (Done/Now/Next)
- Open questions
- Working set of files

## Installation

Add to your `.claude/settings.json`:

```json
{
  "plugins": [
    "/path/to/dev-workflow"
  ]
}
```
