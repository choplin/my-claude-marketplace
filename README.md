# My Claude Marketplace

Personal collection of Claude Code plugins.

## Plugins

| Plugin | Description |
|--------|-------------|
| [discuss-toolkit](./discuss-toolkit/) | Discussion and idea exploration skills |
| [writing-toolkit](./writing-toolkit/) | Document review and writing improvement skills |
| [dev-workflow](./dev-workflow/) | Development workflow skills (ADR, continuity ledger) |
| [git-helpers](./git-helpers/) | Git helper skills for complex operations |
| [execplan-workflow](./execplan-workflow/) | ExecPlan-based development workflow |

## Skills

### discuss-toolkit

| Skill | Triggers |
|-------|----------|
| `discuss` | "let's discuss", "explore this idea", "brainstorm" |
| `name-project` | "name this project", "suggest a name" |
| `quick-chat` | "what do you think", "A vs B", "which is better" |
| `understand-idea` | "I have an idea", "help me understand" |

### writing-toolkit

| Skill | Triggers |
|-------|----------|
| `critical-review` | "review critically", "critique this", "find problems" |
| `fact-check` | "fact-check this", "verify this document" |
| `objective-review` | "review objectively", "how does this read" |
| `revise-document` | "revise this", "improve this writing" |

### dev-workflow

| Skill | Triggers |
|-------|----------|
| `adr` | "create an ADR", "record this decision" |
| `continuity-ledger` | Starting complex or multi-hour work sessions |

### git-helpers

| Skill | Triggers |
|-------|----------|
| `rebase-onto-rewritten` | "rebase onto rewritten", "base branch was force pushed" |

### execplan-workflow

| Skill | Triggers |
|-------|----------|
| `workflow-orchestrator` | Starting development tasks needing structured workflow |

## Installation

Add this marketplace to your Claude Code settings:

```json
{
  "plugins": [
    "/path/to/my-claude-marketplace"
  ]
}
```

Or install individual plugins:

```json
{
  "plugins": [
    "/path/to/my-claude-marketplace/discuss-toolkit",
    "/path/to/my-claude-marketplace/writing-toolkit"
  ]
}
```
