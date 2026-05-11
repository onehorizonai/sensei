---
name: sensei-smell
description: Analyze code for responsibility, boundary, abstraction, code-bloat, and duplicated-knowledge problems. Use when a function or module seems to be doing too many things, when the same business rule appears in multiple places, when the developer asks about SRP, DRY, KISS, code smells, or whether code is too complex, or when reviewing whether a split or abstraction is justified.
---

# Responsibility Review

Analyze responsibilities, boundaries, abstractions, and duplicated knowledge.

The bias is less code, clearer boundaries, and fewer moving parts.

When reviewing more than one file, work file-by-file. Group smells under the file that owns the responsibility or knowledge, and use a cross-file section only when the smell is the relationship between files.

## Teaching DRY correctly

DRY does not mean: never repeat a line of code.

DRY means: every piece of knowledge should have a single, authoritative representation in the system.

### Acceptable duplication
Two similar code paths where the underlying knowledge is genuinely different and expected to diverge independently. The duplication is structural coincidence, not shared intent.

### Problematic duplication
The same business rule, validation logic, pricing formula, authorization check, or state transition expressed in more than one place.

The test: if this rule changes, how many files need to update? More than one is a DRY violation.

Teach the developer to ask: "If the product manager changes this rule, how many files do I open?" That is the real question.

## Teaching responsibility correctly

SRP means Single Responsibility Principle.

It does not mean: a function should do one thing.

It means: a module should have one primary reason to change. One stakeholder or concern should dominate why it exists.

The test: list every distinct stakeholder or concern that could request a change to this module. More than one is a signal to inspect. More than two is strong evidence the module is carrying too many responsibilities.

### Signs of responsibility problems
- The function name contains "and" or "or"
- The module can be described in two unrelated sentences
- The module changes for both domain logic reasons and infrastructure reasons
- Tests for this module span multiple unrelated scenarios

### Signs of over-splitting
- Modules that are only ever used by one other module
- Abstractions named after what they wrap, not what they do
- Interfaces with a single implementation

## Teaching KISS correctly

KISS does not mean: make the code simplistic.

It means: keep the design as simple as the behavior allows. Add structure only when it pays for itself in readability, testability, or future change.

### Signs of code bloat
- A helper exists only to call one other helper
- A config option has one caller and no clear future variation
- A class or service mostly forwards arguments
- The abstraction is harder to explain than the duplicated code it replaced
- There are multiple fallback paths but no clear failure model

## Review questions

**For DRY:**
- Is any business rule, validation, or domain knowledge expressed in more than one place?
- If this rule changes, how many files need to update?
- Is this duplication accidental (the logic happens to look similar) or problematic (it represents the same knowledge)?

**For responsibility:**
- List everything this function or module does. How many distinct things are there?
- List every stakeholder who could request a change to this module.
- If you had to rename this module to exactly describe what it does now, what would that name be? If the name is vague, the responsibility is probably too broad.

**For security boundaries:**
- Are permission checks concentrated in the module that owns access control, or scattered across helpers and UI code?
- Could this split, helper, or abstraction create a second path around validation, authorization, logging, or customer account boundaries?

**For simplicity:**
- What code can be deleted without losing behavior?
- Which abstraction has not earned its keep?
- Is this elegant because it is clear, or merely clever?
- What would the code look like if we solved only the current case?

## Output format

Open with this block:

```
---
## TLDR
[Sentence 1: what this code does and the biggest structural problem]
[Sentence 2: what to do about it — or "no action needed"]

**Verdict: Clean / Has smells — see below / Needs redesign before touching**
---
```

For each smell found:

```
## File: [path/to/file or Cross-file]

### [MUST FIX / SHOULD FIX / CONSIDER] — [Plain-English name — never just cite DRY/SRP/KISS without explaining]
[One sentence: what the problem is and what it costs]
**What breaks:** [Specific consequence — "if this rule changes, N files need updating and one will be missed" / "this is the second way to do X, and they'll drift"]
**Where:** [file:line or range]
**Do this:** [The smallest useful move: delete / inline / move / split — stated as a concrete action]

> _For the curious: [Optional: the principle behind it (DRY means one authoritative source, SRP means one reason to change, etc.) and a reasoning question for developers who want to go deeper]_
```

Close with:

```
---
### Security boundary
[No security-sensitive boundary / Boundary is clear / **Split or easy to bypass: [name it]**]

### Do not change yet
[What should stay untouched because it is out of scope for this smell]

### What you did well
[Specific structural choices that show good judgment — never skip]
```

## Rules

- Teach the principle first, then apply it. Do not cite "SRP" without explaining that it means Single Responsibility Principle.
- Translate DRY, SRP, and KISS into plain English before using them as labels.
- Distinguish "this is complex" from "this has too many responsibilities." Complexity is not always a violation.
- Treat duplicated or scattered permission checks as a security boundary smell, not only a DRY problem.
- Warn against over-splitting as clearly as you warn against over-bundling.
- Treat code bloat as a design smell, not a style preference.
- Separate elegant solutions from hacks: elegance reduces future decisions; hacks defer them.
- Do not prescribe a refactor. Suggest a direction and ask the developer to reason through it.
- If you cannot name what each piece of the split would be called, the split is probably not ready.
- Prefer "keep as-is" when the proposed cleanup would add more concepts than it removes.
