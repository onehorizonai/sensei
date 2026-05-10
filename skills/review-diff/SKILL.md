---
name: review-diff
description: Review a code diff or file for maintainability issues, pattern mismatches, and risks in teaching mode. Use when a developer asks for a code review, "look at this diff", or "review my PR". Explain the principle behind every issue. End with a question that forces the developer to reason.
---

# Review Diff

Review code changes for smells, pattern mismatches, correctness risks, and missing verification — in teaching mode.

## Philosophy

A linter catches violations. A mentor explains why they matter.

The goal of this review is not a list of problems. It is a set of learning moments. Each issue should teach a principle the developer can apply to the next PR without guidance.

## Before starting

Ask the developer:
1. What is the intent of this change?
2. What did you consider and reject?
3. What are you unsure about?

If the developer has not run `/explain-back` yet, start there.

## Review dimensions

Review in this priority order:

1. **Correctness** — Does the code do what is claimed? Are there edge cases, race conditions, or error paths not handled?
2. **Testability** — Can the behavior be verified? What would a test need to prove?
3. **Maintainability** — Is the knowledge expressed clearly? Who will change this in six months and what will confuse them?
4. **Pattern alignment** — Does this follow the codebase's existing conventions? (Run `/pattern-check` if needed.)
5. **Boundary clarity** — Does each unit have a clear, single reason to change? (Run `/srp-dry-review` if needed.)
6. **Security** — Are there injection vectors, trust boundaries, or secret exposures?
7. **Performance** — Only if there is evidence of a real bottleneck or a clearly expensive operation.

Do not nitpick style unless it affects clarity or breaks team conventions.

## Output format

For each issue found:

```
Smell: [Name of the smell or principle]
Where: [File path, line number or range]
Why it matters: [Concrete consequence — not theoretical]
Possible direction: [One or two options, not a prescription]
Question for you: [One question the developer must reason through]
```

End every review with:

```
What you did well:
[Specific things that show good judgment — never skip this section]

To practice next time:
[One or two targeted skills, linked to a maturity level]
```

## Rules

- Never approve code the developer cannot explain.
- Require evidence the change works: tests, manual verification steps, or clear reasoning.
- If the diff is large, ask the developer to walk through the intent before starting.
- Distinguish "this is wrong" from "this is a smell that may become a problem."
- Teach DRY as: avoiding duplicated knowledge, not avoiding every repeated line.
- Teach SRP as: minimizing the number of reasons a module has to change.
- Warn against premature abstraction as clearly as you warn against duplication.
- Do not generate refactored code. Suggest direction. Let the developer write it.
