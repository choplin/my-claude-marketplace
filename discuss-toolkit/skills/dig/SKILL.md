---
name: dig
description: This skill should be used when the user's intent is unclear and needs to be clarified before proceeding. Triggers when AI needs to make assumptions to fill gaps, when specialized skills (like name-project) need to understand user intent first, or when user explicitly calls "/dig". Should NOT trigger for quick decisions with clear context (use quick-chat), or when requirements are already well-defined.
---

# dig - Intent Clarification

Dig deep to understand user intent before proceeding. Never fill gaps with assumptions.

## Why This Skill Exists

AI tends to fill unclear intent with general best practices. This produces outputs that don't reflect the user's actual context and fail to solve real problems. This skill ensures AI understands user intent through structured interview before acting.

## Base Skill Architecture

dig is a **base skill** invoked by other skills or directly by users.

**How it works**:
1. Caller provides context: what they need to clarify and why
2. dig conducts structured interview to clarify user intent
3. Clarified understanding remains in session context for caller to use

**Examples**:
- name-project calls dig to understand project goals → proceeds to generate names based on clarified context
- User calls /dig directly → understanding confirmed, session continues with clarity

**Key implication**:
The "never fill gaps with assumptions" principle applies to the entire workflow—
both the clarification process AND any content the caller creates based on the result.
If certain information remains unclarified, the caller must not fill it with general practices.

## Interview Structure: Axes and Subject

dig provides three **axes** (perspectives) to clarify intent:
1. **Intent & Motivation** - WHY is this needed?
2. **Use Cases & Edge Cases** - HOW will it be used concretely?
3. **Constraints & Priorities** - WHAT limits exist, what matters most?

The **subject** (what to clarify) comes from the caller's context.

**How axes and subject work together**:
- Caller specifies the subject and any additional items to clarify
- dig applies all three axes to the subject and each additional item
- Each axis reveals different aspects of the same subject

**Example**:
```
Caller context: "Creating a skill for code review"
Subject: "code review skill requirements"
Additional items: "trigger conditions", "success criteria"

dig applies axes:
- Intent & Motivation → Why do you need a code review skill? What problem does it solve?
- Use Cases & Edge Cases → Walk through a concrete code review scenario. What's a borderline case?
- Constraints & Priorities → What trade-offs would you accept? What must be avoided?

Then for "trigger conditions":
- Intent & Motivation → Why is it important to trigger at the right time?
- Use Cases & Edge Cases → What would you say when you want this skill? What should NOT trigger it?
- Constraints & Priorities → Are there situational factors that matter?
```

## Core Rule: Never Assume, Always Ask

When information is missing:
1. Do NOT fill with assumptions or general practices
2. Do NOT proceed with guesses
3. DO ask using AskUserQuestion tool

When making hypotheses based on general knowledge:
1. Present as hypothesis: "Generally X applies, but for your case..."
2. Explicitly ask for confirmation
3. Never use unconfirmed hypotheses in final understanding

## Interview Process

### Phase 1: Initial Assessment

Identify what's unclear in user's request:
- What is the goal?
- Why do they want this?
- What constraints exist?

### Phase 2: Deep Interview

**Critical**: Continue until quality indicators in Phase 3 are satisfied. No upper limit on questions.

Use AskUserQuestion tool repeatedly. **AI decides when to move to Phase 3** by self-evaluating quality indicators after each answer. User can say "done" or "complete" to end early (because prolonged questioning may frustrate users who already know their intent), but the default is AI-driven progression.

**Interview Rounds**:

1. **Intent & Motivation**
   - "Why do you need this?"
   - "What happens if you don't have it?"
   - Follow up until you can state the underlying need with specific criteria
     (not "user wants good performance" but "user needs <5s response time")

2. **Use Cases & Edge Cases**
   - "Walk me through a specific example"
   - "What's a borderline case?"
   - "What would failure look like?"
   - Follow up until you have a specific example with concrete inputs and outputs

3. **Constraints & Priorities**
   - "What trade-offs would you accept?"
   - "What must be avoided?"
   - "What's the minimum viable outcome?"
   - Follow up until decision criteria are clear

**Follow-up Question Patterns**:
- "Why?" - uncover motivation
- "For example?" - convert abstract to concrete
- "What else did you consider?" - reveal trade-offs
- "When did this fail before?" - extract lessons learned
- Present hypothesis for confirmation - validate assumptions

### Phase 3: Confirmation

**AI initiates this phase** once quality indicators are likely satisfied. Do NOT ask the user "Do you have anything else to clarify?" or wait for user to signal completion. The user's role is to confirm accuracy, not to decide when AI is done asking.

Before presenting the summary, verify quality indicators:

- [ ] **Motivation documented**: Summary includes user's answer to "why is this important?"
- [ ] **Examples collected**: Summary contains at least one example with specific inputs/outputs
- [ ] **Action determined**: Summary ends with "Next step: [specific action]"
- [ ] **Verification complete**: Every claim in summary was either stated by user or explicitly confirmed by user

Present understanding summary to user and get explicit confirmation ("correct", "yes", "approved" - not "maybe" or "I think so").

**Anti-patterns to avoid**:
- "Is there anything else you'd like to clarify?" - Incorrectly delegates completion decision to user
- "Do you have more questions?" - Same issue
- Waiting for user to say "that's all" before moving to summary - AI should proactively move forward

### Phase 4: Completion

Return results in format requested by caller:
- If called by specialized skill: format they need
- If called directly: verbal confirmation in session

## Success Criteria

The intent clarification deliverable is complete when:
- [ ] User explicitly confirms understanding with "correct", "yes", or "approved" (not "maybe" or "I think so")
- [ ] Every statement in summary can be traced to a specific user answer in the conversation
- [ ] Summary contains zero adjectives (appropriate, proper, good, clean, etc.) without accompanying measurable criteria
- [ ] Summary includes at least one concrete example provided by the user
- [ ] Summary states the user's underlying motivation in user's own words or confirmed paraphrase

## When NOT to Use

- Quick decisions with obvious context → use quick-chat
- Requirements already documented and clear
- User explicitly wants fast action without discussion

## Integration with Other Skills

This skill is called by specialized skills to ensure intent is clear before specialized work begins. For example:
- name-project calls dig first to understand project goals before generating names
- Other skills can invoke dig when they detect ambiguity
