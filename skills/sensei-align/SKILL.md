---
name: sensei-align
description: Compare a code change against the existing codebase to check pattern alignment. Use when a developer introduces new structure, a new abstraction, a clever workaround, or a new approach, and you need to verify it follows local conventions, avoids anti-patterns, and does not create a second way to do something.
---

# Pattern Check

Compare a change against the existing codebase patterns, conventions, and architecture decisions.

## Philosophy

Many maintainability problems are not technically wrong. They are locally inconsistent.

A new service, abstraction, or module that solves the problem correctly but diverges from the team's approach creates two costs:
1. Every future developer has to learn two patterns.
2. The codebase loses its internal coherence — the quality of being self-similar enough to be reasoned about as a whole.

Pattern alignment is not about conformity. It is about keeping the codebase legible.

Less is more here too. A new pattern has to earn its place. If the existing code already has a boring solution, prefer that unless the developer can explain why it no longer fits.

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

**Does this follow local security practice?**
- Does sign-in or permission checking happen in the same layer as nearby code?
- Are secrets, user data, logs, and external inputs handled the same way as the trusted local pattern?
- Does this create a second way to bypass validation, authorization, or audit logging?

**Is a new abstraction justified?**
- Is there a second use case that makes the abstraction worthwhile?
- Or is this premature generality — one use case wrapped in a framework?
- Is the abstraction named for the domain behavior, or just for the technical thing it wraps?

**Is this a proven pattern or a local anti-pattern?**
- Does the plan use a known pattern because it reduces coupling or improves clarity?
- Or does it import pattern-shaped complexity without solving a real maintenance problem?
- Does it hide a hack behind a clean-looking name?

**Is this divergence intentional?**
- If the change breaks the existing pattern, is there a documented reason?
- Has the team discussed changing the pattern, or is this a unilateral departure?

## How to check

1. Ask the developer to name the closest existing example before looking yourself.
2. Read the existing example. Do not compare from memory.
3. Identify specific differences — naming, structure, dependency direction, error handling.
4. Ask the developer to explain each divergence.
5. If no precedent exists, say so and judge the change against the smallest local convention that still applies.

## Output format

```
Pattern examined: [What is being checked]
Plain English: [What this means for someone who only needs to know whether the code will be harder to maintain]
Closest existing example: [File path and module name, or "none found"]
Evidence read: [Files or modules inspected]
Alignment: [Aligned / Diverges / Partially aligned]
Security pattern: [Aligned / Diverges / Not applicable — with evidence]

If diverges or partially aligned:
What differs: [Specific, concrete differences]
Impact: [Why this matters — or why it might be acceptable in this case]
Smallest alignment move: [One change that would bring this closer to the local pattern, if needed]
Question for you: [Force the developer to articulate the reason for the divergence]
```

## Rules

- Do not flag divergence as wrong before checking whether it is intentional.
- Read the existing code before comparing. Do not assume the pattern from memory.
- Explain local pattern mismatches as maintenance risk, not as insider jargon.
- Treat security conventions as part of the pattern, especially sign-in, permission checks, input validation, secret handling, logging, and customer account boundaries.
- Prefer the existing pattern unless there is strong evidence to improve it.
- Flag code that creates a parallel pattern, even if the new pattern looks cleaner in isolation.
- Do not praise cleverness unless it also makes future maintenance simpler.
- If the team convention itself is bad, note it separately — do not mix systemic criticism with PR feedback.
- If no existing example exists, say so. Do not invent a pattern to compare against.
- Prefer aligning with an imperfect local pattern over introducing a second pattern unless the developer can name the migration path.
