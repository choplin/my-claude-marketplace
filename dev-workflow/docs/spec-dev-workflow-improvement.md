# Spec: dev-workflow Plugin Improvement

## Overview

Systematize the entire development workflow using Claude Code and implement skills for each phase.

## Background

Current dev-workflow has the following issues:
- Overall workflow is unclear
- Skills are not tied to phases
- Too dependent on ExecPlan

By explicitly defining the development flow with Claude Code and implementing branching based on work volume, we can achieve more efficient development support.

## Requirements

### Functional Requirements

1. **Volume Assessment Feature**
   - Determine Task (single session) / Story (multiple sessions) / Epic (multiple stories)
   - Branch to appropriate flow based on assessment

2. **Document Creation Feature**
   - epic: Requirements organization + story management
   - spec: Requirements + acceptance criteria (AI self-reviewable)
   - plan: Implementation steps + progress

3. **Self-Review Feature**
   - AI reviews based on spec's acceptance criteria
   - PASS/FAIL/NEEDS REVIEW determination

4. **Post-Task Processing**
   - Integration of commit + knowledge capture
   - Suggest cleanup, docs update, PR creation as needed

### Non-Functional Requirements

- All documents are self-contained (can execute autonomously without conversation history)
- Session clear is assumed at the start of implementation phase

## Acceptance Criteria

1. **assess-task skill works**
   - Given: User explains a task
   - When: Trigger with "I want to start this task"
   - Then: Assessed as Task/Story/Epic with rationale and next actions presented

2. **create-epic skill works**
   - Given: There is an epic-level task
   - When: Trigger with "create an epic"
   - Then: `docs/epic-{name}.md` is created with story list included

3. **create-spec skill works**
   - Given: There is a story-level task
   - When: Trigger with "create a spec"
   - Then: `docs/spec-{name}.md` is created with AI-verifiable acceptance criteria

4. **create-plan skill works**
   - Given: Spec exists
   - When: Trigger with "create a plan"
   - Then: `docs/plan-{name}.md` is created with implementation steps and progress section

5. **self-review skill works**
   - Given: Implementation is complete
   - When: Trigger with "review"
   - Then: Each acceptance criterion is determined as PASS/FAIL/NEEDS REVIEW

6. **post-task skill works**
   - Given: Implementation and review are complete
   - When: Trigger with "task complete"
   - Then: Commit preparation and knowledge capture suggestions are made

7. **Implementation can start from documents after session clear**
   - Given: spec + plan have been created
   - When: Load spec + plan in a new session and start implementation
   - Then: Implementation can proceed without conversation history

## Out of Scope

- Changes to Claude Code core functionality
- Integration with other plugins
- GUI/Dashboard features

## Technical Notes

- Skills are implemented in Claude Code plugin skill format (SKILL.md)
- Templates are placed in `references/` directory
- Existing agents, commands, and old skills are deleted and replaced with new skills
