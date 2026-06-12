---
name: foundry-failure-triage
description: Classify failing Foundry fuzz, property, invariant, and differential tests. Use when Codex needs to run forge tests, reproduce counterexamples, spawn fresh investigations, separate harness defects from reference bugs and production bugs, and report strict-equality failures without masking them.
---

# Foundry Failure Triage

## Workflow

1. Read `AGENTS.md` and any testing-plan docs.
2. Run the requested Foundry command and capture the failing test list.
3. Reproduce each failing test with the narrowest command and useful verbosity.
4. For independent failures, use fresh context or subagents when available.
5. Classify each failure before fixing anything.
6. Report a table before making changes unless the user has already authorized specific fixes.

## Categories

- `harness defect`: invalid setup, impossible bounds, asymmetric production/reference state, wrong ABI shape, stale interface, or test expectation not actually implied by public behavior.
- `reference bug`: the independent model is incomplete or diverges from the public spec/interface.
- `production bug`: production diverges from the public spec/interface or from a valid independent reference.
- `spec mismatch`: the test encodes a desired/spec behavior that production does not satisfy and should remain failing for developer attention.
- `unknown`: insufficient evidence; preserve the failing test.

Do not assume production is correct. Do not assume the reference is correct. Do not assume the test is correct.

## Strictness

Treat one-unit and one-wei differences as real findings unless the spec defines tolerance. Never relax equality only because a failure is small.

Only fix tests when confidence is high that the test would otherwise create a false positive. If confidence is low or medium, keep the failing test and explain the ambiguity.

## Subagent Prompt Shape

Give each fresh investigation:

- failing test name and file
- exact forge command or counterexample
- relevant public interface/spec paths or URLs
- classification categories
- instruction not to edit files unless asked

Ask for root cause, classification, confidence, and recommendation. Merge results into one table and resolve conflicts by doing a fresh local check.

## Report Table

Use columns like:

| Failing test | Category | Root cause | Recommendation |
| --- | --- | --- | --- |

When the user needs developer-facing output, include exact file/test names, enough evidence to reproduce, and whether the test should remain failing.
