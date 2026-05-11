---
name: sensei-assess
description: Sensei's automatic context dispatcher. Run this when no specific skill is requested. Inspect git state to detect uncommitted changes, open PRs, branch position, and recent merges — then announce what was found and route to the right skill without waiting for instructions.
---

# Assess — Smart Context Dispatch

Detect the current development context and route to the right Sensei skill automatically.

## Protocol

1. Run the detection checks below in order. Stop at the first match.
2. Announce what you found — 2-3 lines, no more.
3. State which skill you are routing to and why.
4. Proceed immediately. Do not wait for confirmation unless context is genuinely ambiguous.

---

## Detection checks

### Check A — Uncommitted changes

```bash
git status --porcelain
```

If output is non-empty, also run:

```bash
git diff --name-only          # unstaged
git diff --cached --name-only # staged
git diff --stat               # rough size
```

**Route to:** `sensei-help` → `sensei-spar`

Announce:
```
Sensei sees [N] changed file(s): [list files].
Let's start with help — walk me through where you are before I review.
```

---

### Check B — Open PR for current branch

```bash
BRANCH=$(git branch --show-current)
gh pr view --json number,title,url 2>/dev/null
```

If a PR exists for the current branch:

```bash
gh pr diff
```

If `gh` is not installed or returns nothing, skip this check silently and continue to Check C.

**Route to:** `sensei-help` → `sensei-spar` (applied to the PR diff)

Announce:
```
Sensei sees open PR #[N]: "[title]".
Pulling the diff. Starting with help before I review it.
```

---

### Check C — Branch ahead of main with no uncommitted changes

```bash
git log --oneline $(git merge-base HEAD main 2>/dev/null || git merge-base HEAD master 2>/dev/null)..HEAD
```

If commits exist and Check A was empty:

```bash
git branch --show-current
```

**Route to:** `sensei-scroll` → `sensei-align`

Announce:
```
Sensei sees [N] commit(s) on [branch] ahead of main — and no uncommitted changes.
Looks like you're preparing to open a PR. Running scroll to help you frame it.
```

---

### Check D — Recent merge or just-completed work

```bash
git log -1 --since='30 minutes ago' --format='%h %s'
```

If output is non-empty and the commit message contains "Merge", "merge", "fix", or "complete":

```bash
git show --stat HEAD
```

**Route to:** `sensei-reflect`

Announce:
```
Sensei sees a recent commit: "[message]".
Good moment to reflect before moving on. Running reflection.
```

---

### Check E — No clear context

If none of A–D match, do not guess. Ask:

```
No clear git context detected. What are you working on?

I can:
  $sensei-spar    — review uncommitted changes or a diff
  $sensei-trace    — guide you through a bug
  $sensei-align  — check if your change follows existing patterns
  $sensei-smell — analyze boundaries and duplicated knowledge
  $sensei-gameplan   — review a coding plan against the architecture
  $sensei-prove-it     — evaluate what your tests actually prove
  $sensei-scroll    — help you write a PR description
  $sensei-help   — walk me through the code before I review it
  $sensei-reflect     — post-merge learning capture
  $sensei-prune — refactor safely, one verified move at a time
  $sensei-tradeoff — compare options, costs, constraints, and reversibility
```

---

## Rules

- Always run `git status --porcelain` first. Never assume the state.
- If Check A and Check B both match (uncommitted changes + open PR), prefer Check A — review the local changes first.
- If `gh` fails, skip Check B silently. Do not surface the error unless the user specifically asked about a PR.
- Do not open files or start reviewing before completing the announce step.
- If the diff from Check A or B is larger than ~300 lines, say so and ask the developer to walk through the intent first — do not attempt to review the full diff at once.
- The announce is not optional. Always state what you found before routing.
