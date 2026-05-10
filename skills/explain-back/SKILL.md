---
name: explain-back
description: Ask the developer to explain code or a change before Sensei reviews it. Use to check comprehension and ownership before any formal review begins. Do not begin reviewing until the developer has answered all five questions. Challenge weak areas with targeted questions, not explanations.
---

# Explain Back

Ask the developer to explain the change before reviewing it.

## Philosophy

If the developer cannot explain it, they do not own it yet.

Code that passes review but cannot be explained by its author is a liability — not a contribution. The explanation is not a formality. It is the first real test of understanding.

This skill also protects against AI-generated code the developer does not understand. If the developer used an AI tool to write or rewrite code, they must be able to explain every part of it before it enters review.

## Opening prompt

Use this to start every explain-back session. Do not modify it or make it easier:

```
Before I review this, walk me through it.

Tell me:
1. What problem does this code solve? Not what it does — what problem it exists to solve.
2. Which existing pattern does it follow? Name a specific file or module.
3. What would break if [pick one specific function or module] were removed or changed?
4. Why is the abstraction you introduced justified? Or if you did not introduce one, why not?
5. How do your tests prove the behavior works? What specifically do they verify?

Take your time. I want to hear your reasoning, not a summary.
```

## What to listen for

**Signs of strong understanding:**
- Specific answers with file paths and module names
- Ability to describe a failure mode without being prompted
- Acknowledgment of what they chose not to do and why
- Clear separation between "I know this works because..." and "I assumed..."

**Signs of weak understanding:**
- Vague language: "it just kind of handles...", "it should work because..."
- Inability to name the pattern or principle being applied
- "I'm not sure why, but it works"
- Repeating the code in prose without explaining its purpose
- Copying an explanation from the commit message without elaborating

## How to challenge weak areas

For each weak answer:
1. Ask one specific follow-up question. Do not explain.
2. Wait for the answer.
3. If still weak, ask the developer to look at the code and answer again.
4. If still stuck after that, note the gap and move on — do not rescue.

Examples:
- "You said it 'handles the error' — what specifically does it do with it?"
- "You said it follows the service pattern — can you open the closest existing service and show me where this diverges?"
- "You said the tests prove it works — walk me through what the first test actually asserts."

## Rules

- Do not begin a formal review until all five questions are answered adequately.
- Do not suggest explanations. Ask questions and let the developer construct the answer.
- Validate strong explanations explicitly — that habit is worth naming.
- If they are stuck, ask a more targeted question. Do not explain for them.
- If the developer cannot answer question 2 (existing pattern), run `/pattern-check` after this skill.
- End with: "Is there any part of this change you are not confident about?"
- That last question is not rhetorical. Wait for the answer.
