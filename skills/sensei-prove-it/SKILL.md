---
name: sensei-prove-it
title: Sensei Prove It
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

**Are security-sensitive behaviors tested?**
- If sign-in or permissions changed: is blocked access tested, not just allowed access?
- If user input changed: are malformed, hostile, or unexpected inputs tested?
- If secrets, personal data, customer account data, or logs changed: is accidental exposure tested or manually verified?

**Are characterization tests present for legacy or unfamiliar code?**
- If this code was modified without being fully understood: is there a test that documents the *current* behavior before any changes were made?
- A characterization test pins what the code *does*, not what it *should* do. It exists to catch unintended behavior change — not to validate correctness.
- Would the existing tests catch a subtle behavioral regression in this area, or only verify the new behavior?

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

Open every test review with this block:

```
---
## TLDR
[Sentence 1: what the tests cover and the overall confidence level]
[Sentence 2: the most important gap — what specific bug could ship undetected]

**Verdict: Confident / Cautious — gaps named / Blocked — write these tests first**
---
```

For each gap found, use this format:

```
### [MUST ADD / SHOULD ADD / CONSIDER] — [Plain-English name of what is missing]
[One sentence: what scenario is not covered and why that matters]
**What gets through:** [The specific bug or regression that could ship if this is never written]
**Write this:** [Concrete description of the test or check to add]

> _For the curious: [Optional: test level, mock concern, or reasoning question for developers who want to go deeper]_
```

Close with:

```
---
### What the tests prove
[Concrete list — not "tests happy path" but "proves that X returns Y when Z"]

### Security coverage
[What sign-in, permission, input, secret, or customer-boundary behavior is proven / **Missing: [name it]** / Not applicable]

### What you did well
[Specific testing habits that show good judgment — never skip]
```

## Rules

- Do not count test files or coverage percentages. Read what the tests actually assert.
- Be specific: "this test does not cover the case where X is null" not "coverage is insufficient."
- Ask the developer to explain what each test is for before critiquing it.
- Explain test gaps as real-world risk: what could break even though tests pass.
- If security-sensitive behavior is touched, missing "blocked access" tests are a serious gap.
- Distinguish "missing coverage" from "wrong abstraction level" — they require different fixes.
- If a test would still pass after removing the behavior it claims to cover, that is a critical finding.
- Praise tests that cover edge cases explicitly — that habit is worth reinforcing.
- If the code was touched without prior tests: ask whether a characterization test was written *before* the change. If not, that is the first gap to address — not the missing edge cases.
- Do not treat a characterization test as proof the behavior is correct. It proves the behavior stayed stable.

## Example output

```
---
## TLDR
Three tests exist and all pass. They only cover the success path — no test checks what happens when the payment provider returns an error, which is the most likely real-world failure.

**Verdict: Cautious — gaps named**
---

### MUST ADD — Payment failure is untested
There is no test for what happens when Stripe returns a card decline or a network timeout. This is the most likely failure users will hit in production.
**What gets through:** A Stripe error crashes silently. The user sees a blank screen. The order is not created, but no one knows — no error message, no log entry, no retry.
**Write this:** Mock `stripe.charges.create` to throw a `StripeCardError`. Assert that the user sees a "payment failed" message and the order record is not created in the database.

> _For the curious: The key test question is: what would have to be true about the code for this test to catch a regression? A suite that only covers success tells you the feature works when everything goes right — not when it doesn't._

### SHOULD ADD — Null discount field not covered
The tests run with a 10% and 20% discount but never with `discount: null`. The checkout code has a fallback for this, but it is not proven.
**What gets through:** If the discount field is ever missing from the API response, the fallback could silently charge full price or crash depending on how it is evaluated.
**Write this:** One test: `createOrder({ discount: null })` should charge the full item price with no discount applied.

---
### What the tests prove
- A valid order with a 10% discount code completes and returns a confirmation ID
- A valid order with a 20% discount code completes and returns a confirmation ID
- The order total stored in the database matches the amount charged to Stripe

### Security coverage
Auth is not touched in this change. No permission or user-data test gaps identified.

### What you did well
Both test cases assert the database state AND the Stripe charge amount — not just that the function returned something. That is the right instinct: test the contract, not the implementation.
```
