---
name: foundry-fuzz-mirrors
description: Create Foundry fuzz tests from deterministic unit tests. Use when Codex needs to mirror unit-test flows as parametric fuzz tests, add bounded inputs, expected-vs-actual assertions, quote/execution checks, exact-in/out cases, partial fills, and rounding-sensitive tests without changing production contracts.
---

# Foundry Fuzz Mirrors

## Workflow

1. Read `AGENTS.md`, `BaseTest`, `BaseFuzzTest`, and deterministic unit tests.
2. Enumerate public user actions and behavior branches before editing.
3. Create one fuzz test contract per action or surface, matching existing naming and directory style.
4. Parameterize the same scenario the unit test proves.
5. Add semantic bound helpers to `BaseFuzzTest` when the bound is reused.
6. Assert observable public state, balances, return values, and events only when the repo already tests events.
7. Run the focused fuzz contract, then the relevant suite.

## Test Families

Prefer these families before broad stateful fuzzing:

- `expected == actual`: quote result vs execution result, preview vs actual, returned amounts vs balance deltas.
- exact input and exact output: include both sides when the interface exposes both.
- partial fills: generate enough liquidity to guarantee a partial path.
- boundary and rounding: min size, dust-adjacent values, decimals, fees, rebates, slippage limits.
- roundtrip invariants: quote then execute, exact-output then exact-input, create-decrease-cancel, deposit-withdraw, add-remove liquidity.

## Bounding Rules

Bound inputs to valid preconditions, not to make assertions pass. If a precondition is part of the property, assert or `vm.assume` it explicitly. Keep edge values in range whenever they are semantically valid.

Use helper names that explain domain meaning:

```solidity
uint256 amount = _boundQuoteAmount(amountSeed);
uint256 price = _boundPrice(priceSeed);
uint256 size = _boundSellSize(sizeSeed, price);
```

## Failure Policy

If a fuzz test fails, do not immediately change production code or weaken the test. First isolate whether the failure is:

- invalid harness setup
- ambiguous spec expectation
- rounding/spec mismatch
- production behavior bug

When a single assertion is ambiguous, add a narrower sibling test that keeps the setup but checks only the disputed relationship.
