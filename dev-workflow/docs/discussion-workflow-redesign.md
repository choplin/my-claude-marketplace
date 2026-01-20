# Discussion: dev-workflow Redesign

**Status**: In Progress
**Last Updated**: 2026-01-20

## Goal

Redesign dev-workflow plugin to provide effective workflow guidance for Claude Code development. The current implementation is a "reference document" style that doesn't enable actual interactive workflow execution.

## Decisions

### Core Principles

- **spec/plan must be self-complete**: Documents must allow autonomous execution without conversation history. This is critical.

- **assess-task's core criterion is "Will this complete in one session?"**
  - Yes → Task (no document needed)
  - No → Story/Epic (document required)
  - Secondary indicators (file count, time) are just proxies for this

- **SKILLs should contain "why" and "concrete criteria"**: Not general explanations or textbook definitions. AI needs actionable judgment criteria, not descriptions.

- **Discussion phase can also span sessions**: This discussion itself is proof. Need a skill for continuing discussions.

- **Don't proceed to implementation until all ambiguities are resolved**: The failed first attempt happened because we started implementing with unresolved questions (e.g., where to save epic/spec/plan).

### Process Insights

- **assess-task should be thorough discussion phase**: Use AskUserQuestion to eliminate all ambiguities before creating documents.

- **Document creation flow**:
  1. Create self-complete document
  2. AI self-review against quality criteria
  3. User confirmation
  4. Then proceed to implementation

## Open Questions

### 1. Document Storage Location
Where should epic/spec/plan be saved?
- User's project `docs/`?
- Different path?
- Configurable?

### 2. assess-task Specific Questions
What questions to ask? What determines "ready to proceed"?
- Need concrete question list
- Need exit criteria

### 3. Self-Complete Document Quality Criteria
What specifically makes a document "self-complete"?
- Checklist items needed
- How to verify

### 4. Session Clear Timing and Method
Who clears, when, how?
- User responsibility?
- Claude suggests?
- Automatic?

### 5. Knowledge Capture Method
Where does knowledge go?
- ADR in `docs/adr/`?
- MCP claude-mem?
- Something else?

### 6. SKILL Writing Style Details
Beyond "why" and "criteria", what else?
- Examples needed?
- Anti-patterns?
- Edge cases?

### 7. Discussion Continuation Details
How exactly does continue-discussion work in practice?
- Just created the skill, needs validation

## Context

### Related Files
- `docs/original-plan.md` - Original detailed plan (preserved)
- `docs/spec-dev-workflow-improvement.md` - Current spec (information lacking)
- `docs/plan-dev-workflow-improvement.md` - Current plan (information lacking)
- `skills/` - Current skills need major rewrite

### Background
- First implementation attempt failed due to:
  - Info loss when transferring from original plan to spec/plan
  - Proceeding to implementation with unresolved questions
  - SKILLs became reference docs instead of actionable guides

## Next Steps

1. Discuss each open question one by one
2. Decide on document storage location first (foundational)
3. Define assess-task's concrete question flow
4. Define self-complete quality criteria
5. Rewrite SKILLs with "why" and "concrete criteria"
