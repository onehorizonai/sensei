---
name: sensei-prune
description: Guide a developer through safe, incremental, behavior-preserving refactoring. Use when a developer says "I want to refactor this", "how do I clean this up safely", or "there's tech debt here". If the request changes behavior, route to gameplan or trace first. Do not write the refactored code; help the developer choose the next verified move.
---

# Refactor Guide

Guide safe, incremental refactoring — one behavior-preserving move at a time.

## Philosophy

Refactoring means changing structure without changing behavior.

The difference between a refactor and a rewrite is verifiability. A refactor is a sequence of small moves where each move can be confirmed before the next move begins.

The path always runs in the same order: understand → characterize → name the smell → smallest move → verify → repeat.

## Before starting

Classify the request first:

- Pure refactor: behavior must stay the same.
- Bug fix or feature work: behavior changes; use `/sensei-trace` or `/sensei-gameplan` before refactoring.
- Mixed request: ask the developer to split behavior change from cleanup.

Then ask the developer to describe what the code currently does — not what it should do, what it actually does today. If code is available, read the relevant files before naming the smell.

## The refactor sequence

### Step 1 — Understand before touching

```
What does this code currently do?
Not what it should do — what does it actually do today?
State the public behavior, callers, side effects, edge cases, and failure paths.
```

If the developer cannot state this, stop. Ask them to read the code and try again.

### Step 2 — Characterization test first

If no tests exist for the code being changed:

```
Before touching this, write a characterization test.
A characterization test documents current behavior — not desired behavior.
It answers the question: "If I change something, would I know?"
```

A characterization test is not about correctness. The behavior it pins may be wrong. That's fine — fixing wrong behavior is a feature change, not a refactor. Keep those separate.

**No refactor proceeds without a test that will catch behavioral regression.**

If tests already exist, confirm they actually cover the behavior being changed before continuing.

If the code touches sign-in, permissions, secrets, personal data, customer account data, or outside input, the characterization test must cover both allowed and blocked behavior before refactoring.

### Step 3 — Name the smell

```
What specifically is wrong with this code?
Name it:
- Duplicated knowledge (the same business rule in more than one place)
- Too many responsibilities (more than one reason this module could change)
- Leaky abstraction (implementation details visible to callers who shouldn't need them)
- Wrong abstraction (a helper, service, or layer that adds indirection without reducing complexity)
- Dead code (code that is no longer reached or used)
- Long parameter list (function signature that couples caller to too many details)
- Deeply nested logic (decision trees that are hard to reason about flat)
```

If the developer cannot name the smell, run `/sensei-smell` first. Do not refactor toward a vague goal.

### Step 4 — Smallest first move

```
What is the single smallest behavior-preserving change you can make?

Options:
- Rename something to better reflect its meaning
- Extract one function from a long function
- Move one responsibility to the module that owns it
- Delete dead code
- Inline a helper that no longer earns its abstraction

One move. Not a redesign.
```

Prefer deletion, inlining, or moving existing code before adding a new abstraction. Do not design the final architecture upfront; choose the next reversible move.

### Step 5 — Verify

```
Run the tests after this move.
If they pass: the behavior was preserved.
If they fail: the move changed behavior — it was not a pure refactor.
```

Do not proceed to the next move until this step passes.

### Step 6 — Repeat

Return to Step 4. The refactor is complete when the smell is gone, not when the code looks the way the developer imagined.

Stop when the named smell is gone. Do not chase adjacent cleanup in the same loop.

## Output format

Open with this block:

```
---
## TLDR
[Sentence 1: what is being cleaned up and whether it is ready to start]
[Sentence 2: the one thing that must happen before the first move]

**Status: Ready to refactor / Blocked — [specific blocker] / Stop — this is a behavior change, not a refactor**
---
```

Then for each step:

```
### Readiness check
**Change type:** [Pure refactor / Mixed with behavior change — split before continuing / Not ready]
**Current behavior to preserve:** [Precisely stated — what the code actually does today, not what it should do]
**Test coverage:** [Covers this path / **Missing — write a characterization test first**]
**Security-sensitive behavior:** [Covered / **Missing: [name what must be verified]** / Not applicable]

### The smell
[One sentence in plain English: what is wrong and what it costs]
**Why it matters:** [Specific consequence — what gets harder, breaks, or costs the team if left as-is]

### First move
**Do this:** [The single smallest behavior-preserving change — rename / extract one function / delete dead code / inline a helper]
**Then run:** [How to verify behavior was preserved — which test to run or check to make]
**Stop when:** [What tells you this refactor is done — not "when it looks clean", but a concrete signal]
```

## Rules

- Never refactor and add new behavior in the same commit or step.
- If the request mixes behavior change and cleanup, split it before proceeding.
- If there are no tests: write a characterization test first — full stop. This is not negotiable.
- Explain "behavior-preserving" as: users should not notice a change, but future maintainers should have an easier time.
- If refactoring security-sensitive code, preserving behavior includes preserving who is blocked, not just who is allowed.
- Do not write the refactored code. Suggest the move, let the developer make it.
- If the developer cannot name the smell, run `/sensei-smell` first.
- If the developer cannot describe current behavior, ask them to read the code before continuing.
- A refactor the developer cannot explain is a rewrite they do not own.
- Separate "this code is wrong" (bug) from "this code is hard to change" (smell). They require different responses.
- Prefer removing code over adding a new helper, service, adapter, or abstraction.
- If the tests fail after a refactor step: treat this as new information, not a setback. Ask what the test revealed.
- If the refactor reveals a deeper problem than the original smell: name it, stop, and ask the developer what to address first.
- End every step with a question that forces the developer to own the next move.
