---
name: foundry-reference-spec-audit
description: Audit a Solidity reference implementation against a protocol whitepaper or public specification. Use when Codex needs to decide whether reference-model behavior, math, edge cases, or unsupported surfaces match the spec before creating or trusting Foundry differential tests.
---

# Foundry Reference Spec Audit

## Purpose

Use this pass before treating a reference model as an oracle. The reference is allowed to be incomplete, but it must not silently encode behavior that disagrees with the public spec for surfaces it claims to cover.

## Source Rules

Use current public sources:

- whitepaper or protocol specification
- README/API docs
- public interfaces
- public tests

If the source is a URL or PDF, fetch or browse it when needed and record the exact source in the summary. Do not derive spec meaning from production implementation files when the repository forbids it.

## Audit Workflow

1. List the reference surfaces in scope.
2. Map each surface to its public spec source.
3. Check inputs, outputs, rounding, failure modes, state transitions, and accounting side effects.
4. Mark each surface as `conformant`, `reference gap`, `ambiguous spec`, or `out of scope`.
5. Recommend reference fixes only for `reference gap`.
6. Recommend new property or differential tests for important spec claims that are not covered.

## What To Look For

- math formulas and rounding direction
- exact-in vs exact-out semantics
- partial fill and complete-fill behavior
- batch atomicity and required-action behavior
- priority rules
- reserve and balance conservation
- lifecycle/permission gates
- public view consistency
- documented ABI limits, such as bit-width or enum ranges

## Output

Return a table:

| Surface | Spec source | Reference status | Finding | Recommendation |
| --- | --- | --- | --- | --- |

Do not patch the reference during the audit unless the user explicitly asks for implementation after the report.
