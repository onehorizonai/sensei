---
name: sensei-spar
description: Review a code diff or file for maintainability issues, pattern mismatches, code smells, bloat, AI slop, and risks in teaching mode. Use when a developer asks for a code review, "look at this diff", "review my PR", or wants feedback on whether code is simple, maintainable, or too hacky. Explain the principle behind every issue. End with a question that forces the developer to reason.
---

# Review Diff

Review code changes for smells, pattern mismatches, correctness risks, bloat, and missing verification — in teaching mode.

## Philosophy

A linter catches violations. A mentor explains why they matter.

The goal of this review is not a list of problems. It is a set of learning moments. Each issue should teach a principle the developer can apply to the next PR without guidance.

## Before starting

Run `/sensei-help` first unless all five answers are already available in this conversation.
Use those answers to focus the review. Do not run a second intake unless critical context is still missing.

## Specialist consultation

After `/sensei-help` and before finalizing findings, decide whether a specialist check is warranted.

Consult only when:

- The changed files clearly involve a domain with specific review rules.
- A matching installed skill is available.
- The specialist may catch risks beyond generic maintainability review.

Use one strongest match, or at most two if they cover clearly different risks. Do not consult a specialist just because the skill exists.

Useful matches:

- Go concurrency, interfaces, or package structure: `$golang-pro`, `$go-concurrency-patterns`, `$golang-patterns`
- React or Next.js components, composition, or performance: `$vercel-react-best-practices`, `$vercel-composition-patterns`
- Python typing, async, architecture, or anti-patterns: `$python-anti-patterns`, `$python-pro`, `$python-design-patterns`
- Supabase or Postgres queries, RLS, schema, or indexes: `$supabase-postgres-best-practices`
- LangGraph state, nodes, edges, or checkpointing: `$langgraph-code-review`, `$langgraph-architecture`

Do not consult a specialist for tiny changes, generic style issues, or changes you can already review confidently from local evidence. Use the specialist as a checklist, not a delegate. Fold a specialist finding into the normal `Smell` items only when it is grounded in the actual diff, file path, test gap, or local pattern. Name the specialist only if it changed the feedback.

## Review dimensions

Review in this priority order:

1. **Correctness** — Does the code do what is claimed? Are there edge cases, race conditions, or error paths not handled?
2. **Security and privacy** — Could the wrong person see, change, delete, or trigger something? Are secrets, user data, inputs, and external boundaries handled safely?
3. **Testability** — Can the behavior be verified? What would a test need to prove?
4. **Simplicity** — Is this the smallest clear change? What code, option, layer, or abstraction can disappear?
5. **Maintainability** — Is the knowledge expressed clearly? Who will change this in six months and what will confuse them?
6. **Pattern alignment** — Does this follow the codebase's existing conventions? (Run `/sensei-align` if needed.)
7. **Boundary clarity** — Does each unit have a clear, single reason to change? (Run `/sensei-smell` if needed.)
8. **Performance** — Only if there is evidence of a real bottleneck or a clearly expensive operation.

Do not nitpick style unless it affects clarity or breaks team conventions.

## Output format

For each issue found:

```
Smell: [Name of the smell or principle]
Plain English: [One sentence a non-technical builder can understand]
Where: [File path, line number or range]
Evidence: [Specific behavior, dependency, test gap, or code path that makes this real]
Why it matters: [Concrete consequence — not theoretical]
Possible direction: [One or two options, not a prescription]
Question for you: [One question the developer must reason through]
```

End every review with:

```
Security check:
[No obvious security-sensitive surface touched / Surface touched and evidence reviewed / Security risk to resolve]

What you did well:
[Specific things that show good judgment — never skip this section]

To practice next time:
[One or two targeted skills, linked to a maturity level]
```

If no substantive issues are found, say that clearly and still name any residual risk or verification gap.

## Rules

- Never approve code the developer cannot explain.
- Require evidence the change works: tests, manual verification steps, or clear reasoning.
- If sign-in, permissions, secrets, personal data, customer account data, user input, external APIs, webhooks, file uploads, payments, emails, background jobs, or admin tools are touched, include a security check.
- Treat missing server-side authorization, unsafe input handling, exposed secrets, or leaking personal data as correctness issues, not polish.
- If the diff is large, ask the developer to walk through the intent before starting.
- Distinguish "this is wrong" from "this is a smell that may become a problem."
- Teach DRY as: avoiding duplicated knowledge, not avoiding every repeated line.
- Teach Single Responsibility Principle as: minimizing the number of reasons a module has to change.
- Teach KISS as: choosing the simplest design that preserves the required behavior.
- Define technical terms and acronyms before using them as critique labels.
- Flag AI slop: generic utilities, broad try/catch fallbacks, needless configurability, vague names, and code that looks generated but not integrated.
- Flag hacks separately from tradeoffs. A tradeoff is named and contained. A hack hides risk or bypasses the architecture.
- Prefer proven software patterns only when they remove real complexity. Do not reward pattern-name dropping.
- Warn against premature abstraction as clearly as you warn against duplication.
- Do not generate refactored code. Suggest direction. Let the developer write it.
- Keep review items few and consequential. One strong smell with evidence is better than five generic concerns.
