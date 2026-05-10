---
name: reflection
description: Run a post-merge or post-session reflection to capture what was learned and identify what to practice next. Use after a PR is merged, after a bug is fixed, or at the end of a coaching session. Keep it short enough to review in two minutes.
---

# Reflection

Capture what was learned and identify a single skill to practice deliberately next time.

## Philosophy

Experience density is not just about writing more code. It is about extracting learning from each piece of code written.

Without reflection, the same mistakes repeat. With it, each PR builds toward a specific, named skill. The developer begins to see their own growth.

This is also where patterns in feedback become visible. If the same gap appears across three PRs, that is a development priority — not a one-off comment.

## Questions

Ask these in order. Let the developer answer in their own words:

```
1. What was the hardest part of this change to reason about?
   [Not the hardest to write — the hardest to think through]

2. What was your first approach, and why did you change it?
   [If they did not change it, why not? Were you confident or did you push through uncertainty?]

3. What did the review catch that you missed?
   - Was it a code smell?
   - A pattern mismatch?
   - A missing test case?
   - A logic error?
   - Something you did not know?

4. What would you do differently if you started this over?
   [Be specific — not "I'd write better tests" but "I'd isolate the auth logic from the handler"]

5. What specific skill do you want to practice in the next PR?
   [Choose one: debugging discipline, DRY, SRP, pattern alignment, test quality,
   PR communication, tradeoff reasoning, reading existing code before writing]

6. Is there a concept from this change you still do not feel confident about?
   [If yes, what would help: a focused read, a pair session, or another PR in the same area?]
```

## Output format

Keep this brief. A reflection that takes ten minutes to read is not used:

```
Learning summary
PR / Task: [Title]
Date: [Date]

What landed well:
[One or two specific things that showed good judgment]

What to practice next:
[One named skill + one concrete behavior to watch for in the next PR]

Open question:
[Anything still unresolved — a concept, a trade-off, a decision the developer is not sure about]

Suggested next challenge:
[A specific type of task or code area that would develop the target skill]
```

## Rules

- Do not skip this step even when the PR went smoothly. Clean PRs have lessons too — often about what was avoided.
- Keep the output short enough to review in two minutes.
- Link "what to practice next" to a specific upcoming task or PR when possible.
- If the same gap appears across two or more reflections, name it as a development focus — not a coincidence.
- If the developer had a significant confusion during this cycle, note it separately for the senior engineer to discuss.
- The goal is one concrete thing to practice — not a long improvement list.
