---
name: test-proof
description: Evaluate what the tests actually prove and what they miss. Use when reviewing a PR's test coverage, when a developer says "I wrote tests", or when you want to challenge the developer to reason about test quality — not just test quantity.
---

# Test Proof

Evaluate what the tests prove and what they fail to prove.

## Philosophy

Tests that pass are not the same as tests that verify the behavior.

A test suite that checks the happy path, mocks every dependency, and never tests error paths may give 80% coverage while proving almost nothing real.

The question is not: did the tests pass? The question is: what would have to be true about the code for these tests to catch a regression?

Ask the developer to answer that question before reviewing the tests yourself.

## Questions to ask

**What does each test actually verify?**
- Is it testing the contract (inputs and outputs) or the implementation (internal calls)?
- Would this test catch the most likely bugs?
- Does the test name describe what it proves?

**What is not tested?**
- What happens when inputs are invalid or at their boundaries?
- What happens when a dependency fails?
- What happens with empty arrays, zero values, null, or very large inputs?
- What happens under concurrent access?

**Are the mocks meaningful?**
- Do the mocks return realistic data?
- Would a real dependency behave the same way as the mock in edge cases?
- Is the test actually testing the mock's behavior rather than the code's?

**Is this the right test level?**
- Should this be a unit test, integration test, or end-to-end test?
- Is the test isolated at the right boundary, or is it testing too much or too little at once?

**What is the failure mode?**
- If the behavior this test covers were to break, would the test catch it?
- Would the test still pass even if [specific behavior] were removed?

## Output format

```
What the tests prove:
[Concrete list — not "tests happy path" but "proves that X returns Y when Z"]

What the tests do not prove:
[Specific uncovered scenarios]

Riskiest uncovered case:
[The one missing test most likely to correspond to a real bug]

Mock quality:
[Are mocks realistic? What assumptions do they embed?]

Question for you:
[A specific question about what the developer was trying to prove]
```

## Rules

- Do not count test files or coverage percentages. Read what the tests actually assert.
- Be specific: "this test does not cover the case where X is null" not "coverage is insufficient."
- Ask the developer to explain what each test is for before critiquing it.
- Distinguish "missing coverage" from "wrong abstraction level" — they require different fixes.
- If a test would still pass after removing the behavior it claims to cover, that is a critical finding.
- Praise tests that cover edge cases explicitly — that habit is worth reinforcing.
