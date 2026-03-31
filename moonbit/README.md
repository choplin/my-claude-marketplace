# 🌙 MoonBit Plugin for Claude Code

MoonBit language development skills for Claude Code, restructured from the official [moonbitlang/skills](https://github.com/moonbitlang/skills) marketplace.

## Why This Plugin?

The official MoonBit skills marketplace uses a flat directory layout where SKILL.md files are not under a `skills/` directory. This means Claude Code cannot recognize them as skills when installed. This plugin restructures the content into a proper plugin layout while keeping upstream content as git submodules for easy updates.

## Skills

| Skill | Description |
|-------|-------------|
| `moonbit-agent-guide-dev` | Guide for writing, refactoring, and testing MoonBit projects |
| `moonbit-refactoring` | Refactor MoonBit code to be idiomatic |
| `moonbit-lang` | MoonBit language reference and coding conventions |
| `moonbit-spec-test-development` | Create formal spec-driven MoonBit APIs and test suites |
| `moonbit-extract-spec-test` | Extract formal spec and test suites from existing implementations |
| `moonbit-c-binding` | MoonBit bindings for C libraries using native FFI |

## Structure

```
moonbit/
├── .claude-plugin/plugin.json
├── README.md
├── _upstream/
│   └── skills/                      # submodule: moonbitlang/skills (recursive)
└── skills/                          # symlinks → _upstream/skills/...
    ├── moonbit-agent-guide-dev/     # → moonbit-agent-guide/moonbit-agent-guide
    ├── moonbit-refactoring/         # → moonbit-agent-guide/moonbit-refactoring
    ├── moonbit-c-binding/
    ├── moonbit-extract-spec-test/
    ├── moonbit-lang/
    └── moonbit-spec-test-development/
```

## Updating

To pull the latest upstream content:

```bash
git submodule update --remote --recursive moonbit/_upstream/skills
```

## Attribution

All skill content is authored by and belongs to the MoonBit team and contributors:

- [moonbitlang/skills](https://github.com/moonbitlang/skills) - Main skills marketplace
- [moonbitlang/moonbit-agent-guide](https://github.com/moonbitlang/moonbit-agent-guide) - Agent guide & refactoring
- [Milky2018/moonbit-lang-skill](https://github.com/Milky2018/moonbit-lang-skill) - Language reference
- [bobzhang/moonbit-spec-test-development](https://github.com/bobzhang/moonbit-spec-test-development) - Spec-driven development
