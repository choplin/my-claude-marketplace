---
name: kickoff
description: Use this skill when the user wants to START work on a new development task. This skill explores user needs through dialogue to understand What they want to achieve and Why. Triggers on phrases like "I want to start this task", "let's work on this", "I have an idea", "how should I approach this work", or when user presents a new work item (concrete or vague). Should NOT trigger for ongoing tasks (use continue-discussion), discussing features without intent to start work, or questions about existing code.
allowed-tools: Read, Glob, Grep, AskUserQuestion, Skill
user-invocable: true
---

# Task Assessment

Assess task complexity through thorough interview, then route to the appropriate workflow.

## Purpose

Determine whether a task is Task, Story, or Epic based on thorough understanding of user intent. The core question: **"Can you write Criteria directly from User Needs?"**

- **If Yes → Task**: Requirements are implicit, Criteria follows naturally from Needs
- **If No → Story**: Need to clarify Requirements before Criteria can be defined

Why this matters: When Criteria cannot be derived directly from User Needs, it means there are unresolved decisions. These decisions should be documented in a spec before implementation begins.

## Critical Anti-Pattern: AI Filling

**Problem**: AI tends to fill documents with general best practices and common patterns, creating documents that look complete but don't reflect user intent. When sessions span, only these documents survive, leading to implementations that miss the actual goal.

**Solution**: Use ONLY information confirmed by the user. Never infer or assume.

Rules:
- Every piece of information in output must be traceable to user confirmation
- If information is unknown, mark as "TBD" or ask for clarification
- Never fill gaps with "reasonable assumptions" or "best practices"
- When user says "I don't know": probe deeper if critical to spec, otherwise mark TBD or propose with explicit "[AI suggestion]" label

## Interview Process

### Philosophy (from skill-authoring)

- Ask questions that probe deeper than surface requirements
  - Avoid questions answerable from the request itself
  - Instead, ask about motivation, trade-offs, past failures
  - Example: User says "Add logging"
    - Obvious (skip): "Should we add logging?" (user already said this)
    - Not obvious (ask): "What debugging problem led you to need logging?"
- User can say "complete" at any time to end interview early
- If AI judges intent is sufficiently clear, confirm with user before proceeding
- **Never accept vague answers** - always follow up for specifics
  - Problem: Adjectives without criteria lead to AI filling. When user says "clean code",
    AI fills with generic best practices that don't reflect user's actual pain points.
  - Detection: Answers contain adjectives without measurable criteria
    (e.g., "clean", "good", "proper", "appropriate", "reasonable")
  - Response: Ask for the concrete problem these adjectives solve
    - User: "The code should be maintainable"
    - AI: "What maintenance problem occurred that this would solve?"

### Interview Content

Focus on two layers:

**Context Layer (always required):**

| Item | Description | Example Questions |
|------|-------------|-------------------|
| **Why** | Background, motivation, problem to solve | "What problem does this solve?", "Why is this important now?", "What happens if we don't do this?" |
| **What** | Implementation target + completion criteria | "What specifically needs to be built?", "How will you know it's done?", "What does success look like?" |

**Approach Layer (for determining Task vs Story):**

| Check | Result |
|-------|--------|
| Can you write "done" criteria directly from the user's request? | Yes → Task, No → Story |
| Do you need to ask "what kind of X?" before defining success? | Yes → Story |
| Are there multiple approaches that need trade-off discussion? | Yes → Story |

**Key insight**: "Multiple implementation choices" is a SYMPTOM, not the cause.
The root cause is "Cannot derive Criteria directly from Needs."

### Interview Completion

Continue until:
1. Why is concrete with specific problem/motivation
2. What is specific with measurable completion criteria
3. Enough information to determine Task/Story/Epic

Then confirm: "I have enough to assess. Ready to proceed, or anything else?"

## Assessment Criteria

| Level | Criterion | Output |
|-------|-----------|--------|
| **Task** | Criteria writable directly from User Needs (no Requirements clarification needed) | Launch `create-task` skill |
| **Story** | Requirements clarification needed before Criteria (need to decide "what kind" before "done") | Launch `create-spec` skill |
| **Epic** | Multiple independent Stories (What has multiple parts) | Launch `create-epic` skill |

**Examples**:
- Task: "Fix this bug" → Criteria "Bug fixed, tests pass" - directly writable
- Story: "Add authentication" → Need to decide "what kind of auth?" before defining "done"

### Detailed Criteria

**Task**: All of these are true:
- Criteria can be written directly from User Needs
- No specification decisions needed (Requirements are implicit)
- Implementation approach is obvious once What is clear

**Story**: Any of these are true:
- Cannot write Criteria without first clarifying Requirements
- Multiple implementation approaches with trade-offs to document
- Decisions will need to be referenced in future sessions

**Epic**:
- What consists of multiple independent parts
- Each part could be its own Story with separate spec
- Requires coordination document across Stories

## Output by Assessment Result

### If Task

Invoke `dev-workflow:create-task` skill.

The create-task skill will:
1. Receive Why/What context from this interview (via session history)
2. Structure the information into Claude Code plan file format
3. Request user approval via ExitPlanMode

### If Story

Invoke `dev-workflow:create-spec` skill.

The create-spec skill will receive the interview context via session history.

### If Epic

Invoke `dev-workflow:create-epic` skill.

The create-epic skill will receive the interview context via session history.

## Promotion Flow

Task → Story promotion is a **normal flow**, not a failure.

**Promotion triggers**:
- Design decision emerged during implementation that wasn't anticipated
- Discovered complexity requires documentation for future reference
- Need to preserve decisions across session boundaries

**When promoting**:
- Why/What from Plan carry over to spec (this is why create-task includes full Why/What)
- Document the How decisions made so far
- Invoke `create-spec` with existing Plan context

## Success Criteria

- [ ] All information in output is user-confirmed (no AI filling)
- [ ] Why is concrete with specific problem/motivation
- [ ] What is specific with measurable completion criteria
- [ ] Assessment rationale is clear and traceable (based on "Criteria directly from Needs?" question)
- [ ] Correct skill invoked based on assessment (create-task / create-spec / create-epic)

## Next Session

If session is cleared before completing this skill:

**Reference**: None (interview context is lost)
**Next phase**: Restart with `kickoff` (invoke `/dev-workflow:kickoff`)
