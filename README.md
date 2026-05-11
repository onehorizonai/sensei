# Sensei

![Sensei banner](assets/banner.png)

Code review that teaches engineering judgment, not just what to change.

Sensei is a senior engineering coach for developers who want better review, debugging, planning, refactoring, and test feedback. It helps you understand what changed, why it matters, what could break, and what evidence proves the work is safe to ship.

It is useful for senior engineers coaching juniors, solo developers who want a sharper second reviewer, students learning how real code gets reviewed, and anyone using AI to get code working who now needs to make it cleaner, safer, and easier to defend.

The developer still owns the code. Sensei asks the questions that make the code better.

---

## Works best inside your repo

Install Sensei in the repo you want it to review.

A repo is the project folder that holds your code. When Sensei runs inside that folder, it can use the context that actually matters:

- Your existing components, helpers, and patterns
- Your architecture docs, `AGENTS.md`, `CLAUDE.md`, Cursor rules, or other project instructions
- Your tests, package files, and folder structure
- Your Git state: changed files, open PRs, recent commits, and branch history
- Any other skills you have installed or pointed your agent at

That is the difference between generic code advice and review that knows how your project is supposed to work.

---

## Who it is for

- Senior engineers who want juniors to get guidance without being spoon-fed answers
- Solo developers who want a second reviewer before they push code
- Junior developers, students, and bootcamp grads learning how to think like maintainers
- AI-assisted builders who got something working and now need to know if it is fragile, bloated, risky, or ready
- Any developer looking for alternative code review, quality checks, debugging help, or refactoring guidance

---

## What Sensei helps with

- Reviews diffs and asks you to explain your reasoning before it judges the code
- Finds code bloat, duplicated logic, unclear responsibility, and over-engineered abstractions
- Checks whether the change follows existing project patterns
- Reviews implementation plans before you build the wrong thing
- Helps debug by turning guesses into hypotheses and experiments
- Explains what your tests prove and what they still miss
- Helps write PR descriptions with intent, risks, and verification
- Checks security-sensitive surfaces like auth, permissions, secrets, user data, and outside input
- Turns finished work into learning, so the next change is better

---

## Install

### Recommended: install from skills.sh

Run this from the repo where you want to use Sensei:

```bash
npx skills add onehorizonai/sensei
```

Browse Sensei on the registry: [skills.sh/onehorizonai/sensei](https://skills.sh/onehorizonai/sensei)

### Claude Code plugin

```text
/plugin marketplace add onehorizonai/sensei
/plugin install sensei
/reload-plugins
```

### Manual install

Project install, best for code review:

```bash
git clone --depth 1 https://github.com/onehorizonai/sensei .claude/skills/sensei
```

User install, useful when you want Sensei available everywhere:

```bash
git clone --depth 1 https://github.com/onehorizonai/sensei ~/.claude/skills/sensei
```

After installing, restart your agent tool and try `/sensei-assess`.

---

## Usage Guide

### Quick start: install it, then use it manually

Use this if you just want better review today.

1. Open your project repo in Claude Code, Codex, Cursor, or another agent tool that can use local skills.
2. Install Sensei in that repo.
3. Ask for the kind of help you need.

Good first prompts:

```text
/sensei-assess
/sensei-spar
/sensei-trace
/sensei-gameplan
/sensei-prove-it
```

Plain-English prompts work too:

```text
Use Sensei to review my uncommitted changes.
Use Sensei to help me debug this bug.
Use Sensei to check if this plan fits the existing codebase.
Use Sensei to tell me what my tests actually prove.
Use Sensei to help me write the PR description.
```

If you do not know where to start, use `/sensei-assess`. It reads the repo state and routes you to the right Sensei skill.

### More advanced: automate it locally

Use this when you want Sensei to show up at the right moments without remembering every command.

Local automation means your coding tool nudges or runs Sensei on your machine while you work. Claude Code hooks, Cursor hooks/rules, Codex local workflows, shell aliases, and editor tasks can all do this.

Useful local triggers:

| Moment | Run |
| --- | --- |
| You start a session with changed files | `/sensei-assess` |
| You are about to approve an AI coding plan | `/sensei-gameplan` |
| You wrote or changed tests | `/sensei-prove-it` |
| You are about to open a PR | `/sensei-scroll` |
| You just merged or finished a session | `/sensei-reflect` |

Example idea:

```text
When git has uncommitted changes, remind me to run /sensei-assess.
When I push a branch, remind me to run /sensei-scroll.
When a coding plan is proposed, ask Sensei to review the plan first.
```

You can wire that into whatever your tool supports. The important part is not the hook syntax. The important part is choosing the moments where better judgment prevents messy code.

### Even more advanced: run it with cloud agents

Use this when you work with background or cloud coding agents, such as Cursor cloud agents or similar remote agents.

Cloud use works best when the remote agent checks out the repo and has Sensei available inside that workspace. Then you can ask the agent to run the same review flow it would run locally:

```text
Before opening a PR, run Sensei on the diff.
Before implementing, run Sensei on the plan.
Before marking this done, run Sensei on the tests and risk areas.
```

For cloud agents, be explicit about what evidence you expect:

```text
Use Sensei to review this branch. Include the main risks, missing verification, and one question I need to answer before merging.
```

That keeps the cloud agent from just saying "looks good" without proving anything.

### Super advanced: use Git and GitHub hooks

Use this when you want Sensei to become part of the team workflow.

Common patterns:

- A local `pre-push` Git hook that reminds developers to run Sensei before pushing
- A PR template that asks for Sensei's risk, test, and architecture notes
- A GitHub Action that runs a skill-capable agent on pull requests
- A branch rule that requires review notes before merge

Simple local `pre-push` hook:

```bash
#!/usr/bin/env bash
echo ""
echo "Before pushing, consider running:"
echo "  /sensei-spar      review the diff"
echo "  /sensei-prove-it  check what the tests prove"
echo "  /sensei-scroll    prepare the PR description"
echo ""
```

Put it in `.githooks/pre-push`, then run:

```bash
chmod +x .githooks/pre-push
git config core.hooksPath .githooks
```

For GitHub, keep the rule simple: every PR should explain what changed, what could break, what was tested, and what Sensei challenged.

---

## Skills

| Skill | Use when |
| --- | --- |
| `sensei-assess` | You are not sure where to start |
| `sensei-help` | Sensei needs to ask what you are trying to do first |
| `sensei-spar` | A diff, file, or PR needs teaching-mode review |
| `sensei-trace` | There is a bug or unexpected behavior |
| `sensei-align` | A change may not follow local conventions |
| `sensei-smell` | Code feels bloated, duplicated, or responsible for too much |
| `sensei-gameplan` | A coding plan needs review before implementation |
| `sensei-prove-it` | Tests exist, but you need to know what they actually prove |
| `sensei-scroll` | A PR needs intent, risks, verification, and reviewer focus |
| `sensei-reflect` | Finished work should turn into a learning loop |
| `sensei-prune` | A refactor needs small, safe, verified steps |
| `sensei-tradeoff` | A decision needs options, costs, constraints, and reversibility |

---

## How routing works

Start with `/sensei-assess` when the request is unclear. It checks:

```bash
git status --porcelain
gh pr view
git log main..HEAD
git log -1 --since='30 minutes ago' --format='%h %s'
```

Then it routes based on what it finds:

| Git state | Route |
| --- | --- |
| Uncommitted or staged changes | `sensei-help` -> `sensei-spar` |
| Open PR for current branch | `sensei-help` -> `sensei-spar` on the PR diff |
| Branch ahead of main, clean tree | `sensei-scroll` -> `sensei-align` |
| Recent merge or completed work | `sensei-reflect` |
| No clear context | Ask the developer what they need |

---

## What Sensei pushes on

- Explain the reasoning before asking for review
- Show evidence: tests, logs, screenshots, reproduction steps, or clear reasoning
- Prefer the smallest clear change over a bigger clever one
- Follow existing codebase patterns unless there is a deliberate reason not to
- Remove code bloat, duplicated fallback paths, and abstractions that do not earn their keep
- Check security as normal engineering work, not a last-minute add-on
- Never approve work the developer cannot explain

---

## Repository layout

```text
skills/
  sensei-assess/    # Reads git context and routes
  sensei-help/      # Asks what you need before review
  sensei-spar/      # Teaching-mode code review
  sensei-trace/     # Hypothesis-driven debugging
  sensei-align/     # Pattern alignment against the repo
  sensei-smell/     # Responsibility, boundaries, duplication
  sensei-gameplan/  # Implementation plan review
  sensei-prove-it/  # Test quality review
  sensei-scroll/    # PR description and reviewer focus
  sensei-reflect/   # Post-merge learning capture
  sensei-prune/     # Safe, behavior-preserving refactoring
  sensei-tradeoff/  # Decision coaching
```

Each skill has a `SKILL.md` for the coaching workflow and `agents/openai.yaml` for UI metadata.

---

## The bar

Sensei is deliberately a little uncomfortable. It is not trying to produce an answer as fast as possible. It is trying to make the developer say what they believe, show the evidence, and decide what to do with the tradeoff in front of them.

---

## License

Apache 2.0 — see [LICENSE](LICENSE).

Built by [One Horizon](https://onehorizon.ai).
