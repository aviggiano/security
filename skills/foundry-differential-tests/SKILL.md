---
name: foundry-differential-tests
description: Create Foundry differential tests comparing production Solidity contracts against an independent reference model. Use when Codex needs paired deployments, paired actions, public-view state comparisons, strict equality checks, and root-cause classification of production/reference divergence.
---

# Foundry Differential Tests

## Setup

Deploy production and reference systems side by side. Use shared actor setup, balances, markets, approvals, and configuration. Keep the paired-action helpers in a differential base contract.

Compare public behavior, not internals:

- return values
- ERC20/native balances
- deposited/locked/available balances from view functions
- public order/market/price-level views
- public metadata and configuration views

Do not compare internal storage layout, packed ids, or implementation-private traversal state unless the public interface exposes it.

## Test Construction

1. Start with deterministic scenarios that each prove one public semantic.
2. Execute the same operation on production and reference.
3. Compare all relevant public state after each step.
4. Add fuzzing only after deterministic coverage has pinned the behavior.
5. Keep helpers symmetric: `_placeLimitPair`, `_marketOrderPair`, `_batchPair`, `_assert...Pair`.

Use typed interfaces for the reference boundary. Raw fallback calldata should only be modeled if the task explicitly includes fallback ABI behavior.

## Equality And Tolerance

Default to strict equality. A one-unit mismatch is still a finding. Add tolerance only when the public spec defines tolerance or the user explicitly asks for an approximate property.

## Failure Handling

When a differential test fails, classify before fixing:

- `harness defect`: the paired setup is invalid or asymmetric
- `reference bug`: the independent model does not match public/spec behavior
- `production bug`: production diverges from public/spec behavior
- `spec mismatch`: the test documents a behavior not satisfied by production and should intentionally remain failing

Fix harness defects and reference bugs when requested. Preserve production/spec failures unless the user explicitly asks to change production code.

## Validation

Run focused tests first, then the differential suite. If the fixture has a reference-only switch, also run representative tests against the reference-only deployment to catch reference completeness gaps.
