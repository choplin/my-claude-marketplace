---
name: continuity-ledger
description: Maintain continuity across context compaction for long-running tasks. Use when starting complex or multi-hour work sessions.
allowed-tools: Read, Write, Edit, Glob
---

# Continuity Ledger

Maintain a single Continuity Ledger for the workspace in `CONTINUITY.md`. The ledger is the canonical session briefing designed to survive context compaction; do not rely on earlier chat text unless it's reflected in the ledger.

## How It Works

1. **At the start of every turn**: Read `CONTINUITY.md`, update it to reflect the latest state, then proceed with the work

2. **Update triggers**: Update `CONTINUITY.md` whenever any of these change:
   - Goal
   - Constraints/assumptions
   - Key decisions
   - Learnings (technical findings, risks)
   - Progress state (Done/Now/Next)
   - Open questions
   - Working set (files/URLs being referenced)

3. **Writing rules**:
   - Keep it short and stable: facts only, no transcripts
   - **All sections are flat lists** - no nested structures
   - One item per line, add brief note on same line if needed
   - Mark uncertainty as `UNCONFIRMED` (never guess)
   - Remove outdated items to prevent bloat

4. **On context loss**:
   - Refresh/rebuild the ledger from visible context
   - Mark gaps as `UNCONFIRMED`
   - Ask up to 1–3 targeted questions, then continue

## TodoWrite vs the Ledger

| Tool | Purpose |
|------|---------|
| `TodoWrite` | Short-term execution scaffolding (3–7 step plan with pending/in_progress/completed) |
| `CONTINUITY.md` | Long-running continuity (what/why/current state), not a step-by-step task list |

Keep them consistent: when the plan or state changes, update the ledger at the intent/progress level (not every micro-step).

## Response Format

Begin replies with a brief "Ledger Snapshot":
- Goal + Now/Next + Open Questions

Print the full ledger only when it materially changes or when the user asks.

## CONTINUITY.md Format

All sections use flat lists. No nesting.

```
## Goal
(success criteria)

## Constraints/Assumptions
- item

## Key Decisions
- decision - rationale

## Learnings
- finding

## State

### Done
- item

### Now
- item

### Next
- item

## Open Questions
- question

## Working Set
- `path/or/url` - brief note
```
