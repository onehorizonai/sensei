---
name: sensei-scroll
description: Help a developer write a clear PR description that communicates intent, risks, verification, and reviewer focus. Use before a PR is opened or when the PR description is thin or missing. Do not write the PR for the developer — ask questions until they can write it themselves.
---

# PR Contract

Help write a PR summary that makes the reviewer's job easier and forces the developer to own the change.

## Philosophy

A PR description is a contract with the reviewer.

It communicates:
- What changed and why (the commit history has the what — the PR needs the why)
- What risks the author is aware of
- What security or privacy surfaces were touched, if any
- What verification was actually performed
- Where the reviewer's attention is most needed

A thin PR description shifts the cognitive load to the reviewer. A good one does the opposite.

It is also a forcing function. If the developer cannot fill in the verification section, the PR is not ready.

## Process

1. Ask the developer to fill in each section in their own words. Do not suggest the content.
2. Ask clarifying questions on thin sections. One question per section.
3. If the verification section is empty, stop. That is not reviewable.
4. If the developer cannot name the existing pattern they followed, run `/sensei-align` first.
5. End by asking: "Is there any part of this PR you do not fully understand?"

## Required sections

```
## What changed
[One or two sentences in plain English. The WHY, not the what. Why this change now? What problem does it solve?]

## Existing pattern followed
[Which existing module or convention does this follow?
If it introduces a new pattern, why — and did the team agree?]

## Risks
[What could go wrong, explained so a non-technical stakeholder can understand the consequence? What edge cases were considered?
What is still uncertain? What are you watching after merge?]

## Security / privacy
[Does this touch sign-in, permissions, secrets, user data, customer account data, logs, external APIs, webhooks, file uploads, payments, emails, background jobs, or admin tools?
If yes, what prevents the wrong person from seeing, changing, deleting, or triggering something?
If no, say "no security-sensitive surface touched."]

## Verification performed
[Tests written, manual steps taken, logs checked, scenarios confirmed.
Be specific — "I tested X" not "I tested it."]

## AI / agent assistance used
[If Sensei or any AI tool was used: what it flagged, and what you decided to keep or change.
If none was used, say "none." Transparency is a professional habit.]

## What I want reviewers to focus on
[Where do you want the senior's attention?
What are you most unsure about? What trade-offs did you make?]
```

## Rules

- Do not write the PR for the developer. Ask questions until they write it.
- The verification section being empty means the PR is not ready for review.
- If the security/privacy section is vague and the PR touches a security-sensitive surface, the PR is not ready for review.
- The AI assistance section is mandatory, even when the answer is "none."
- If the developer cannot explain the risk section, ask them to sit with the code for five minutes before continuing.
- A PR that is hard to describe is often a PR that is trying to do too much.
- If the PR description only makes sense to the author, it is not ready.
