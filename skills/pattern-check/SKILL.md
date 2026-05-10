---
name: pattern-check
description: Compare a code change against the existing codebase to check pattern alignment. Use when a developer introduces new structure, a new abstraction, or a new approach, and you need to verify it follows the local conventions and does not create a second way to do something.
---

# Pattern Check

Compare a change against the existing codebase patterns, conventions, and architecture decisions.

## Philosophy

Many junior mistakes are not technically wrong. They are locally inconsistent.

A new service, abstraction, or module that solves the problem correctly but diverges from the team's approach creates two costs:
1. Every future developer has to learn two patterns.
2. The codebase loses its internal coherence — the quality of being self-similar enough to be reasoned about as a whole.

Pattern alignment is not about conformity. It is about keeping the codebase legible.

## Questions to answer

**Have we done something similar before?**
- Which existing module is the closest parallel?
- How did the team approach that case?
- What does that precedent suggest for this change?

**Does this follow the local dialect?**
- Does it respect how the team organizes layers (controllers, services, schemas, validators)?
- Does it use the same naming conventions?
- Does it follow the same dependency direction?
- Does it use the same idioms for error handling, logging, and async patterns?

**Is a new abstraction justified?**
- Is there a second use case that makes the abstraction worthwhile?
- Or is this premature generality — one use case wrapped in a framework?

**Is this divergence intentional?**
- If the change breaks the existing pattern, is there a documented reason?
- Has the team discussed changing the pattern, or is this a unilateral departure?

## How to check

1. Ask the developer to name the closest existing example before looking yourself.
2. Read the existing example. Do not compare from memory.
3. Identify specific differences — naming, structure, dependency direction, error handling.
4. Ask the developer to explain each divergence.

## Output format

```
Pattern examined: [What is being checked]
Closest existing example: [File path and module name]
Alignment: [Aligned / Diverges / Partially aligned]

If diverges or partially aligned:
What differs: [Specific, concrete differences]
Impact: [Why this matters — or why it might be acceptable in this case]
Question for you: [Force the developer to articulate the reason for the divergence]
```

## Rules

- Do not flag divergence as wrong before checking whether it is intentional.
- Read the existing code before comparing. Do not assume the pattern from memory.
- Prefer the existing pattern unless there is strong evidence to improve it.
- If the team convention itself is bad, note it separately — do not mix systemic criticism with PR feedback.
- If no existing example exists, say so. Do not invent a pattern to compare against.
