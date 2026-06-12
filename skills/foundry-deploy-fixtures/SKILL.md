---
name: foundry-deploy-fixtures
description: Create or refactor Foundry deployment fixtures for Solidity tests. Use when Codex needs to separate Deploy.t.sol, BaseTest.t.sol, BaseFuzzTest.t.sol, deployment scripts, shared actors, token funding, approvals, and reusable fuzz bounds before adding unit, fuzz, property, or differential tests.
---

# Foundry Deploy Fixtures

## Goal

Create a fixture stack where deployment, shared test setup, and fuzz input shaping are separate. This keeps later fuzz and differential tests small and makes deployment switches possible.

## Workflow

1. Read `AGENTS.md`, Foundry config, existing tests, and deployment scripts.
2. Identify the minimum deployment graph needed by tests.
3. Create or refactor an abstract `Deploy.t.sol` that owns deployment only.
4. Keep `Deploy.t.sol` free of `Test` inheritance unless the repository already requires it.
5. Create or refactor `BaseTest.t.sol` so it extends `Test` and `Deploy`, calls deployment in `setUp`, and owns actors, funding, approvals, registration, deposits, snapshots, and common assertions.
6. Create or refactor `BaseFuzzTest.t.sol` so it extends `BaseTest` and owns bounds/generators.
7. Run focused tests, then the relevant suite.

## Fixture Shape

Prefer this dependency direction:

```solidity
abstract contract Deploy {
    function deploy() internal virtual { ... }
}

abstract contract BaseTest is Test, Deploy {
    function setUp() public virtual {
        deploy();
        // fund, approve, register, deposit
    }
}

abstract contract BaseFuzzTest is BaseTest {
    function _boundAmount(uint256 seed) internal pure returns (uint256) { ... }
}
```

If the project has scripts such as `DeployToken.s.sol`, instantiate and reuse them instead of duplicating deployment logic in tests. If a reference deployment is needed later, add a small switch such as an env var or overrideable `deployReference()` rather than branching inside every test.

## Bounds And Actors

Use named actors and realistic balances. Include different token decimals when decimals affect math. Bounds should encode semantic validity, such as minimum order size, available balance, max price, slippage constraints, and partial-fill ranges.

## Guardrails

- Do not mix deployment-only logic with fuzz assumptions.
- Do not hide invalid input by over-bounding; preserve meaningful edge cases.
- Keep assertion reasons as asserted predicates when the repo requires it.
- Preserve unrelated user changes and avoid contract changes unless explicitly requested.
