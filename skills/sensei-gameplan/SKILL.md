---
name: sensei-gameplan
description: Review a coding or implementation plan against the existing architecture before code is written. Use when a developer shares a plan, asks "does this plan make sense?", wants architecture feedback before implementing, or needs to check whether the intended approach follows local patterns, boundaries, dependencies, testing strategy, the KISS principle, and avoids code bloat, AI slop, and clever hacks.
---

# Plan Review

Review the intended implementation before the developer starts coding.

The goal is not to write the plan for them. The goal is to test whether their plan fits the architecture, uses existing patterns, and names the risky parts clearly enough to proceed.

Keep the bar high on simplicity. A good plan should avoid code bloat, AI slop, clever hacks, and abstractions that do not have a real second use case.

## Before starting

Ask the developer for the plan if it is not already present.

If the plan is vague, ask for:

1. The behavior they want to change
2. The files, modules, or layers they expect to touch
3. The existing pattern they think this follows
4. The main risk or unknown
5. Whether the change touches sign-in, permissions, user data, secrets, or outside input
6. How they expect to verify the change

## Specialist consultation

Before judging architecture fit, decide whether a specialist check is warranted.

Consult only when:

- The plan clearly involves a domain with specific architectural or maintenance risks.
- A matching installed skill is available.
- The specialist may reveal framework-specific anti-patterns, missing proven patterns, code bloat, or future maintenance risk.

Use one strongest match, or at most two if they cover clearly different risks. Do not consult a specialist just because the skill exists.

Useful matches:

- Go concurrency, interfaces, or package structure: `$golang-pro`, `$go-concurrency-patterns`, `$golang-patterns`
- React or Next.js components, composition, or performance: `$vercel-react-best-practices`, `$vercel-composition-patterns`
- Python typing, async, architecture, or anti-patterns: `$python-anti-patterns`, `$python-pro`, `$python-design-patterns`
- Supabase or Postgres queries, RLS, schema, or indexes: `$supabase-postgres-best-practices`
- LangGraph state, nodes, edges, or checkpointing: `$langgraph-code-review`, `$langgraph-architecture`

Do not consult a specialist for tiny changes, generic style issues, vague plans that need clarification first, or decisions you can already review confidently from local evidence. Use the specialist as a checklist, not a delegate. Fold a specialist finding into the review only when it is grounded in the actual plan, file path, dependency, or local architectural precedent.

Do not review architecture from memory. Read the closest existing files before judging the plan.

## Review dimensions

Check in this order:

1. Fit to existing architecture: Does the plan use the same layers, dependency direction, and ownership boundaries as nearby code?
2. Pattern alignment: Is there a closer existing pattern the developer missed?
3. Simplicity: Is this the smallest plan that can work cleanly?
4. Scope control: Is the plan trying to solve more than the requested behavior?
5. Responsibility split: Does each proposed module have a clear reason to change?
6. Security and privacy: Does the plan name sign-in, permissions, secrets, personal data, customer account data, inputs, and external boundaries when relevant?
7. Data and state flow: Is ownership of data, side effects, and error handling clear?
8. Test plan: Does verification match the risk, or is it only checking the happy path?
9. Migration risk: Are rollout, compatibility, and cleanup steps named when needed?

## Output format

Use this format:

```text
Plan summary:
[Restate the plan in two or three sentences. If you cannot restate it clearly, the plan is not ready.]

Architecture fit:
[Aligned / Partially aligned / Diverges]

Plain-English read:
[What this plan means for a non-technical stakeholder: safe to try, risky, too broad, or unclear]

Closest existing pattern:
[File path and module name, or "none found"]

Evidence read:
[Files, modules, or docs inspected before judging architecture fit]

Optional specialist checks used:
[Skill names that materially changed the review. Omit this line unless a specialist changed the feedback or the user asked for an audit trail.]

Security/privacy check:
[No obvious security-sensitive surface named / Surface named and controlled / Missing decision or risk]

Risks:
1. [Concrete risk]
2. [Concrete risk]

Missing decisions:
[What the developer still needs to decide before coding]

Suggested adjustment:
[One or two directions, not a full rewritten plan]

Smallest plan that could work:
[The minimum version that fits the architecture and still proves the behavior]

Question for you:
[One question the developer must answer before implementing]
```

## Rules

- Review the plan, not the imagined implementation.
- Cite the existing file or module that sets the architectural precedent.
- If the plan touches sign-in, permissions, secrets, personal data, customer account data, user input, external APIs, webhooks, file uploads, payments, emails, background jobs, or admin tools, require a security/privacy decision.
- Do not invent a pattern when none exists. Say "none found."
- Do not write the implementation steps for the developer unless they ask for help after the review.
- Flag plans that add a second way to do something already solved in the codebase.
- Flag plans that add framework-shaped code for a one-off problem.
- Prefer an existing proven pattern over a new helper, service, adapter, or abstraction.
- If the plan reads like generated boilerplate, ask which parts are actually needed.
- If the plan depends on an assumption, turn that assumption into a question or experiment.
- If the plan is too vague to review, stop and ask for a clearer plan.
- If two approaches are plausible, route to `/sensei-tradeoff` instead of deciding for the developer.
- Explain architectural concerns in plain English before naming the technical pattern.
