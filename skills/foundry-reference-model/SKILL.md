---
name: foundry-reference-model
description: Build a simple independent Solidity reference implementation for Foundry tests. Use when Codex needs a non-optimized, non-production-derived model for differential testing, based only on public interfaces, specs, README/API docs, and existing public tests.
---

# Foundry Reference Model

## Inputs

Use only allowed public behavior sources:

- `contracts/interfaces/**`
- README/API/fallback ABI documentation
- existing public tests
- whitepapers/specs/product docs
- observed runtime calls inside differential tests

Do not read, summarize, or derive reference behavior from production implementation files when the repository forbids it.

## Model Style

Keep the reference intentionally boring:

- structs, mappings, arrays, and direct loops
- explicit fields instead of packed flags
- named enums instead of magic action numbers
- plain arithmetic that follows public semantics
- simple mocks for tokens or wrappers when needed

Avoid gas-shaped implementation details:

- no assembly
- no packed storage
- no bitmaps
- no bit shifting or masking unless the user explicitly allows it
- no production-style internal balance shortcuts unless that behavior is explicitly in scope

## Workflow

1. Define the reference boundary and directory, usually under the test tree.
2. Implement only the first selected behavior phase.
3. Add a reference deployment helper that uses the same config shape as production tests where possible.
4. Run existing tests against the reference-only deployment if the fixture supports it.
5. Fill reference gaps TDD-style: first add the public behavior test, then implement the missing model behavior.
6. Keep unsupported surfaces explicit, but do not add tests whose only assertion is a placeholder revert.

## Phase Discipline

Start with deterministic core behavior before AMM/router/vault/admin surfaces. Do not model internals simply because production has them. If public semantics are unclear, leave the behavior out of scope and record the reason in the task report or plan.
