# Plan: dev-workflow Plugin Improvement

**Related spec**: `docs/spec-dev-workflow-improvement.md`

## Approach

1. Create templates first (skills reference them)
2. Then create skills in order (considering dependencies)
3. Finally delete existing components and update metadata

## Files to Change

| File | Change Type |
|------|-------------|
| `references/epic-template.md` | Create |
| `references/spec-template.md` | Create |
| `references/plan-template.md` | Create |
| `skills/assess-task/SKILL.md` | Create |
| `skills/create-epic/SKILL.md` | Create |
| `skills/create-spec/SKILL.md` | Create |
| `skills/create-plan/SKILL.md` | Create |
| `skills/self-review/SKILL.md` | Create |
| `skills/post-task/SKILL.md` | Create |
| `agents/workflow-orchestrator.md` | Delete |
| `commands/workflow/start.md` | Delete |
| `skills/adr/SKILL.md` | Delete |
| `skills/continuity-ledger/SKILL.md` | Delete |
| `references/PLANS.md` | Delete |
| `references/PLANS-LIGHT.md` | Delete |
| `.claude-plugin/plugin.json` | Update |
| `README.md` | Update |

## Implementation Steps

### Step 1: Create Templates
- Create `references/epic-template.md`
- Create `references/spec-template.md`
- Create `references/plan-template.md`

### Step 2: Create assess-task skill
- Describe volume assessment logic
- Guide branching to Task/Story/Epic

### Step 3: Create create-epic skill
- Describe epic document creation procedure
- Reference epic-template

### Step 4: Create create-spec skill
- Describe spec document creation procedure
- Include how to write AI self-reviewable acceptance criteria
- Reference spec-template

### Step 5: Create create-plan skill
- Describe plan document creation procedure
- Describe integration with spec
- Reference plan-template

### Step 6: Create self-review skill
- Describe review procedure based on spec's acceptance criteria

### Step 7: Create post-task skill
- Describe commit preparation, knowledge capture, additional actions
- Integrate existing adr skill content

### Step 8: Delete existing components
- Delete agents/, commands/ directories
- Delete skills/adr/, skills/continuity-ledger/
- Delete references/PLANS.md, PLANS-LIGHT.md

### Step 9: Update metadata
- Update plugin.json
- Update README.md

## Progress

- [x] Step 1: Create Templates
- [x] Step 2: Create assess-task skill
- [x] Step 3: Create create-epic skill
- [x] Step 4: Create create-spec skill
- [x] Step 5: Create create-plan skill
- [x] Step 6: Create self-review skill
- [x] Step 7: Create post-task skill
- [x] Step 8: Delete existing components
- [x] Step 9: Update metadata

## Decision Log

- 2026-01-19: Task level uses Claude Code Plan feature, no external document needed - retained internally even after session clear
- 2026-01-19: adr integrated into post-task - easier to use as part of post-task processing
- 2026-01-19: continuity-ledger deleted - covered by plan document progress management

## Discoveries

(Record during implementation)
