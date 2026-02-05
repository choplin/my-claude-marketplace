# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This repository is a collection of Claude Code plugins. It is not an executable application - plugins are loaded by Claude Code as documentation and skill definitions.

## Plugin Structure

Each plugin follows this structure:
```
plugin-name/
├── .claude-plugin/plugin.json    # Plugin metadata (name, version, author)
├── README.md                      # Plugin documentation
├── skills/                        # Skill definitions
│   └── skill-name/SKILL.md
├── commands/                      # Slash commands (optional)
├── agents/                        # Subagents (optional)
└── references/                    # Templates and reference docs (optional)
```

## Recommended Skills

When working in this repository, actively use these skills:

| Skill | Use When |
|-------|----------|
| `/skill-authoring:skill-authoring` | Creating or improving skills (content quality). Use together with plugin-dev:skill-development |
| `/plugin-dev:skill-development` | Skill file structure and formatting |
| `/plugin-dev:agent-development` | Creating subagents |
| `/plugin-dev:command-development` | Creating slash commands |
| `/plugin-dev:hook-development` | Creating hooks (PreToolUse, PostToolUse, etc.) |
| `/plugin-dev:plugin-structure` | Understanding plugin directory layout |
| `/plugin-dev:create-plugin` | End-to-end plugin creation workflow |

## Commit Convention

Conventional commit style: `type(module): description`
- Types: `feat`, `fix`, `refactor`, `docs`, `test`, `chore`
- Module is the plugin name (optional for cross-plugin changes)
