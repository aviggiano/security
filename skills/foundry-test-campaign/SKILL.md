---
name: foundry-test-campaign
description: Master skill for running an end-to-end multi-pass Foundry testing campaign for Solidity projects. Use when Codex needs to create a goal and coordinate fixture setup, fuzz tests, spec properties, reference implementations, differential tests, and independent failure triage until the testing objective is genuinely complete.
---

# Foundry Test Campaign

## Purpose

Use this as the master orchestration pass. Do not put every testing detail in one context. Create a concrete campaign goal when the user asks for an end-to-end testing effort, then run the focused Foundry skills in sequence until the objective is genuinely complete or blocked.

## Goal Mode

When the user asks for a complete campaign, use a goal such as:

```text
Build and validate a multi-pass Foundry fuzz, property, reference, and differential testing campaign for this Solidity project.
```

Keep the goal active across passes. Do not mark it complete after only planning, after only adding tests, or while unclassified failures remain. Complete the goal only when the requested campaign state is reached, validation has been run, and remaining failures are intentionally classified and reported.

## Pass Order

1. Establish fixtures with `foundry-deploy-fixtures`.
2. Convert deterministic behavior into parametric fuzz tests with `foundry-fuzz-mirrors`.
3. Add spec or whitepaper properties with `foundry-spec-properties`.
4. Build the independent reference model with `foundry-reference-model`.
5. Audit the reference model against public specs with `foundry-reference-spec-audit`.
6. Add production-vs-reference tests with `foundry-differential-tests`.
7. Classify failures with `foundry-failure-triage`.

Skip passes the repository has already done well. Do not rebuild a fixture layer only because later tests need a helper; extend the existing local pattern.

## Fresh Context

Use fresh context for repeated judgment loops:

- classifying failures
- deciding whether a test is a harness defect
- deciding whether to relax equality or add tolerance
- reviewing reference-model behavior
- comparing spec text to implementation behavior

When parallel agents are available and failures are independent, give each agent one failing test, the exact command or counterexample, the relevant public interface/spec input, and the classification rubric. Do not include the suspected answer unless validating a specific hypothesis.

## Campaign Rules

- Read `AGENTS.md` and scoped repository instructions before choosing a pass.
- Use the focused subskill for the current pass; return to this master skill between passes to decide the next step.
- Preserve strict equality unless the spec defines a tolerance.
- Treat failing tests as useful findings. Do not fix production bugs by weakening tests.
- Prefer deterministic tests before broad stateful fuzzing.
- Keep reports tabular: failing test, category, root cause, recommendation.
- Commit only focused, pass-scoped changes when asked to commit.

## Production Bug Harvest

When the objective is scored by production bugs in `ISSUES.md`, optimize the campaign for independently classified, reproducible bug rows:

- Keep a live candidate ledger while testing. For each red result, record the failing test or exact command, observed violation, current classification, suspected production root cause, duplicate link if any, and whether an `ISSUES.md` row has been written.
- Favor breadth after reproducibility: once a strict reproducer is stable and the failure is plausibly production-side, classify and record it before spending time shrinking further, beautifying tests, or building broad new harness layers.
- Keep every candidate bug tied to a strict red reproducer: a deterministic failing test is best; otherwise preserve the exact failing command, seed, calldata, inputs, and shrink notes needed to recreate the failure.
- Do not delete, skip, relax, or over-tolerate a red test after it exposes plausible production behavior. Move or rename reproducers if needed, but keep them red until the issue is classified.
- During bug harvest, do not patch production code, reference code, or assumptions merely to make a red reproducer pass. If a harness or reference fix is required to continue, first preserve any still-plausible production failure in the ledger with its original repro command.
- Triage failures one at a time. Use fresh context or independent agents for unrelated failures, and classify each as production bug, harness/reference/spec defect, duplicate, or unresolved.
- Deduplicate narrowly. Merge failures only when they share the same production root cause and remediation. Keep separate rows for distinct preconditions, call paths, violated invariants, accounting errors, access-control gaps, or remediation changes.
- For every classified production bug, immediately add or update one `ISSUES.md` row with exactly these columns: `id | Issue | Red tests | Root cause | Remediation`. Preserve existing rows and choose the next stable id already used by the file; if the file is missing, create it with that header.
- The `Red tests` cell must name the concrete failing test(s) or exact reproduction command. The `Root cause` cell must identify the production code behavior, not only the symptom. The `Remediation` cell must describe the production-side fix direction without weakening the test.
- Do not route harness-only, reference-only, environment-only, or spec-uncertain failures into `ISSUES.md` as production bugs. Keep them in the final triage summary so they are not confused with missed production findings.
- Before ending the goal, scan all failing tests, counterexamples, notes, candidate-ledger entries, and triage summaries. Ensure every non-duplicate classified production bug has a corresponding deduped `ISSUES.md` row, and report any unresolved red candidate separately rather than silently dropping it.

## Output

For a planning-only request, end with a concise pass list, the next skill to use, any directories that should contain new tests, and the first validation command.

For an end-to-end request, end with completed passes, files changed, validation commands, `ISSUES.md` production-bug row count, remaining classified failures, and whether the campaign goal is complete or blocked.
