---
name: adr
description: Use this skill when the user wants to record an important technical decision. Triggers on phrases like "create an ADR", "record this decision", "document this architecture decision", "write an ADR", or when a significant technical decision has been made that should be documented.
allowed-tools: Bash(ls, mkdir, find), Read, Write, LS
---

# Architecture Decision Record (ADR)

Record important technical decisions as Architecture Decision Records (ADR) in the project.

## ADR Components

1. **Title**: Brief description of the decision
2. **Status**: Accepted or Rejected
3. **Context**: Background on why this decision was needed
4. **Decision**: Details of the actual decision made
5. **Discussion**: The deliberation process and key discussion points
6. **Consequences**: Expected outcomes or impacts of this decision
7. **Alternatives**: Other options that were considered (optional)
8. **References**: External links or internal code references (optional)

## Storage Location

ADRs are saved in the following priority order:

1. `docs/adr/` (recommended)
2. `.adr/`

If no directory exists, `docs/adr/` will be created.

## Process

1. **Identify Project Root** - Find the current project's root directory
2. **Check/Create ADR Directory** - Check for the ADR storage directory and create if necessary
3. **Check Existing ADRs** - Retrieve the latest number from existing ADR files
4. **Collect ADR Information** - Gather necessary information from the user
5. **Create ADR File** - Create the ADR file following the standard format

## File Format

Filename format: `ADR-{number}-{slug}.md`

- Number: 4-digit zero-padding (0001, 0002, ...)
- Slug: URL-safe string generated from the title

## ADR Template

```markdown
---
created: { YYYY-MM-DD }
---

# ADR-{number}: {title}

## Status

{Accepted | Rejected}

## Context

{background explanation}

## Decision

{decision details}

## Discussion

{deliberation process and key points}

## Consequences

{expected outcomes or impacts}

## Alternatives

{other options considered}

## References

{external links or internal code references}
```

## Important

- This skill is for creating new ADRs only
- Will not overwrite existing ADR files
- Requires user confirmation before writing
