---
allowed-tools: Read, Write, Edit, Glob, Grep, AskUserQuestion, TodoWrite, Task
description: Start a development workflow with AI-assisted complexity assessment
---

# Start Development Workflow

This command initiates a structured development workflow. It helps you clarify your task through discussion, assesses complexity, and guides you through the appropriate approach (single-session or document-driven).

## Process

### 1. Understand the Task

Begin by asking the user about their task:

- What are you trying to accomplish?
- What is the context or background?
- Are there any constraints or preferences?

Use follow-up questions to clarify ambiguities. Do not assume; ask.

### 2. Assess Complexity

Based on the discussion, evaluate complexity using these factors:

**Indicators of HIGH complexity (document-driven approach)**:
- Multiple files need to be modified (>5 files)
- Architectural decisions are required
- Requirements are unclear or need exploration
- Implementation will take multiple sessions
- High uncertainty or risk
- Integration with external systems

**Indicators of LOW complexity (single-session approach)**:
- Isolated change to 1-3 files
- Clear, well-defined requirements
- No architectural decisions needed
- Can be completed in one session
- Low risk, easy to verify

### 3. Propose Approach

Present your complexity assessment to the user:

    Based on our discussion, I assess this task as [HIGH/LOW] complexity because:
    - [reason 1]
    - [reason 2]

    I recommend the [document-driven / single-session] approach.

    Would you like to proceed with this approach?

### 4. Execute Appropriate Workflow

#### For LOW Complexity (Single-Session)

1. Create a lightweight ExecPlan following `./references/PLANS-LIGHT.md`
2. Save to `{project root}/ExecPlan-Light.md`
3. Execute the plan immediately (no approval gate needed)
4. Update Progress as you work
5. Record any important decisions in Decision Log
6. Complete the "結果" section when done

#### For HIGH Complexity (Document-Driven)

1. Read `./references/PLANS.md` thoroughly
2. Create a full ExecPlan following the skeleton exactly
3. Save to `{project root}/ExecPlan.md`
4. Present the plan for user review:
   - "I've created an ExecPlan at {path}"
   - "Please review the plan. I will wait for your approval before beginning implementation."
5. STOP and wait for explicit user approval
6. After approval, begin implementation following the plan
7. Keep all living document sections updated

### 5. Record Completion

When the task is complete:

- Ensure Progress section shows all items completed
- Document any surprises or discoveries
- Write Outcomes & Retrospective summary
- If significant architectural decisions were made, consider suggesting `/task:adr` to record them

## Key Principles

- **Discussion First**: Always start with understanding, not implementation
- **User Decides**: The complexity assessment is a recommendation; user has final say
- **Living Documents**: Keep ExecPlan updated throughout the work
- **Trace Decisions**: Record why choices were made, not just what was done

## Examples

### Example: Low Complexity Task

User: "Fix the typo in the README"

→ Assess as LOW complexity (single file, clear requirement)
→ Create lightweight ExecPlan
→ Execute immediately
→ Record completion

### Example: High Complexity Task

User: "Add user authentication to the app"

→ Discuss: What auth method? Session vs JWT? OAuth providers?
→ Assess as HIGH complexity (architectural decisions, multiple files)
→ Create full ExecPlan with detailed milestones
→ Wait for approval before implementing

Let's begin. What task would you like to work on?
