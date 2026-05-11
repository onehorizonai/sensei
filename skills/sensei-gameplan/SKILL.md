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

If the plan is vague, collect these details:

1. The behavior they want to change
2. The files, modules, or layers they expect to touch
3. The existing pattern they think this follows
4. The main risk or unknown
5. Whether the change touches sign-in, permissions, user data, secrets, or outside input
6. How they expect to verify the change

Collect those details one at a time. Ask the first missing question only, then wait for the developer's answer before asking the next.

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

Open every plan review with this block:

```
---
## TLDR
[Sentence 1: what the plan is trying to do and whether the approach fits the codebase]
[Sentence 2: the most important thing to resolve before writing a line of code]

**Verdict: Good to build / Fix the plan first / Stop and rethink**
---
```

Then for each issue, use this format:

```
### [MUST RESOLVE / SHOULD RESOLVE / WATCH OUT] — [Plain-English name, not a pattern label]
[One sentence anyone can understand: what the risk or problem is]
**What breaks:** [Specific consequence — what gets harder, breaks, or costs the team later]
**Do this before coding:** [Concrete action or decision to make first]

> _For the curious: [Optional: architectural principle, pattern name, or deeper reasoning question]_
```

Close with:

```
---
### Smallest plan that could work
[The minimum version that fits the existing architecture and still proves the behavior — one short paragraph]

### Security / privacy
[No security-sensitive surface named / Surface named and controlled / **Missing decision: [name it]**]

### What you did well
[Specific things that show clear thinking — never skip]

### One thing to sharpen
[Single named decision or habit to improve before the next plan]
```

If the plan cannot be restated clearly in two sentences, it is not ready to review — say so and ask for a clearer version before continuing.

## Example output

```
---
## TLDR
Plan adds an `AuthGuard` component to protect the settings route. The approach works but invents a new auth pattern when `withAuth` already handles this for 12 other routes.

**Verdict: Fix the plan first**
---

### MUST RESOLVE — New auth pattern when one already exists
The plan creates a new `AuthGuard` component, but `withAuth` in `src/middleware/auth.js` already protects every other route in the app. Adding `AuthGuard` means two ways to protect a route — future developers will not know which one to use, and one will drift.
**What breaks:** Inconsistent access control. The next person adding a protected route picks the wrong pattern. A misconfigured `AuthGuard` silently skips the permission check that `withAuth` enforces.
**Do this before coding:** Use `withAuth` the same way `pages/dashboard.js:8` does. Delete the `AuthGuard` plan entirely.

> _For the curious: This is about pattern alignment — a codebase's internal consistency is what makes it possible to reason about it as a whole. Two patterns for the same problem means two things to learn and two places security bugs can hide._

### WATCH OUT — No test plan for the blocked state
The plan describes testing the authenticated route but does not mention testing that an unauthenticated user is actually blocked.
**What breaks:** The happy path passes; a misconfigured redirect lets unauthenticated users through in production.
**Do this before coding:** Add to the plan: "Test that hitting `/settings` while logged out redirects to `/login` and does not render any settings content."

---
### Smallest plan that could work
Wrap the settings page export with `withAuth` from `src/middleware/auth.js`. Add one test confirming unauthenticated users are redirected. No new files, no new patterns.

### Security / privacy
Auth surface touched. The plan names the protected route but does not specify what `withAuth` checks (session token vs. role). Confirm the existing check is sufficient before coding.

### What you did well
The plan named the specific route and described the expected redirect behavior clearly. That is the right level of detail for a plan review.

### One thing to sharpen
Before planning a new helper or component: search for the existing solution first. `grep -r "withAuth"` would have found the pattern immediately.
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
- If two approaches are plausible, use `$sensei-tradeoff` instead of deciding for the developer.
- Explain architectural concerns in plain English before naming the technical pattern.
