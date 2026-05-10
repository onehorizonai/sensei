---
name: srp-dry-review
description: Analyze code for Single Responsibility Principle violations and duplicated knowledge. Use when a function or module seems to be doing too many things, when the same logic appears in multiple places, or when reviewing boundary and abstraction quality.
---

# SRP + DRY Review

Analyze responsibilities, boundaries, and duplicated knowledge.

## Teaching DRY correctly

DRY does not mean: never repeat a line of code.

DRY means: every piece of knowledge should have a single, authoritative representation in the system.

### Acceptable duplication
Two similar code paths where the underlying knowledge is genuinely different and expected to diverge independently. The duplication is structural coincidence, not shared intent.

### Problematic duplication
The same business rule, validation logic, pricing formula, authorization check, or state transition expressed in more than one place.

The test: if this rule changes, how many files need to update? More than one is a DRY violation.

Teach the developer to ask: "If the product manager changes this rule, how many files do I open?" That is the real question.

## Teaching SRP correctly

SRP does not mean: a function should do one thing.

SRP means: a module should have one reason to change. One stakeholder who could require it to change.

The test: list every distinct stakeholder who could request a change to this module. If there are more than two, the module probably has too many responsibilities.

### Signs of SRP violation
- The function name contains "and" or "or"
- The module can be described in two unrelated sentences
- The module changes for both domain logic reasons and infrastructure reasons
- Tests for this module span multiple unrelated scenarios

### Signs of over-splitting
- Modules that are only ever used by one other module
- Abstractions named after what they wrap, not what they do
- Interfaces with a single implementation

## Review questions

**For DRY:**
- Is any business rule, validation, or domain knowledge expressed in more than one place?
- If this rule changes, how many files need to update?
- Is this duplication accidental (the logic happens to look similar) or problematic (it represents the same knowledge)?

**For SRP:**
- List everything this function or module does. How many distinct things are there?
- List every stakeholder who could request a change to this module.
- If you had to rename this module to exactly describe what it does now, what would that name be? If the name is vague, the responsibility is probably too broad.

## Output format

```
Responsibility analysis:
[List everything this unit currently does — not what it should do]

Reasons to change:
1. [Reason one — stakeholder or concern]
2. [Reason two]
...
Count: N

If N > 2: This module likely has too many responsibilities.

Duplication analysis:
[Identify duplicated knowledge — not just repeated code]
Duplication type: [Structural coincidence / Shared knowledge — which is it?]

Suggested direction: [One or two options, not a prescription]

Question for you: [One question the developer must reason through before acting]
```

## Rules

- Teach the principle first, then apply it. Do not cite "SRP" without explaining what it means in this specific context.
- Distinguish "this is complex" from "this has too many responsibilities." Complexity is not always a violation.
- Warn against over-splitting as clearly as you warn against over-bundling.
- Do not prescribe a refactor. Suggest a direction and ask the developer to reason through it.
- If you cannot name what each piece of the split would be called, the split is probably not ready.
