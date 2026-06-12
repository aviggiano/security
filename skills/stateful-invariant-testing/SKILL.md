---
name: stateful-invariant-testing
description: Build metric-driven Chimera/create-chimera-app stateful invariant testing campaigns for Solidity projects. Use when Codex needs a Recon-style invariant harness with Setup, TargetFunctions, Properties, BeforeAfter, CryticTester, CryticToFoundry, actor and manager setup, Echidna/Medusa/recon-fuzzer smoke runs, and standardized Recon coverage gates.
---

# Stateful Invariant Testing

## Purpose

Use this skill to create a real stateful invariant fuzzing campaign using the Recon Chimera style. In this context, stateful invariant testing is property-based testing over sequences of protocol actions, not a separate campaign type. The fuzzer should choose action sequences. The harness should expose protocol entrypoints with minimal preprocessing, track actors and state, and report bugs instead of papering over them.

Default to the create-chimera-app layout and adapt it to the target repo. Do not hand-roll a generic `StdInvariant` campaign, a single monolithic handler, or a differential-only state machine unless the user explicitly asks for that instead of Chimera.

## Goal Discipline

For a full campaign, explicitly ask the user to run it as a Codex `/goal` if they did not already. Use a measurable objective such as:

```text
/goal Build and validate a stateful invariant testing campaign with 5-minute smoke runs on recon-fuzzer, Echidna, and Medusa, no unclassified failures, and at least 90% standardized line coverage of core production contracts.
```

Use 90% standardized line coverage as the default initial target. The user may choose a higher target or a different production scope.

## Ground Rules

- Read `AGENTS.md`, repo testing docs, and existing Foundry test patterns before editing.
- If the user asks for a separate worktree, create and work inside one. Keep production `contracts/` or `src/` unchanged unless the user explicitly asks for contract fixes.
- In benchmark or clean-room tasks, do not read sibling worktrees, previous generated harnesses, old commits, archived sessions, or copied candidate diffs as implementation sources unless the user explicitly permits it. Use the current checkout plus public upstream references.
- Install missing dependencies as needed, but prefer repo-local or worktree-local setup.
- Use `Recon-Fuzz/create-chimera-app` and `Recon-Fuzz/chimera` as the normative scaffold for new campaigns. Treat repo-local fixture helpers as inputs to `Setup`, not as a reason to abandon the Chimera file structure.
- Keep target functions close to public protocol actions. Avoid "surface", "sweep", or "coverage" handlers whose main purpose is to execute lines.
- Do not use broad `try/catch` to force artificial coverage. Let expected reverts be explored naturally by fuzzing unless the call is an intentional quote-then-execute or setup boundary.
- Prefer `asActor` and actor-manager helpers over direct `vm.prank(actor)` in target functions. Explicit pranks belong in deterministic setup or narrow shortcut scenarios.
- Treat failing invariants as findings. Classify them before changing tests. Do not weaken assertions or hide production bugs.

## Tooling References

Use these repositories as implementation references for every new Chimera campaign:

- [Recon-Fuzz/chimera](https://github.com/Recon-Fuzz/chimera)
- [Recon-Fuzz/recon-magic-framework](https://github.com/Recon-Fuzz/recon-magic-framework)
- [Recon-Fuzz/create-chimera-app](https://github.com/Recon-Fuzz/create-chimera-app)
- [Recon-Fuzz/recon-fuzzer](https://github.com/Recon-Fuzz/recon-fuzzer)
- [crytic/echidna](https://github.com/crytic/echidna)
- [crytic/medusa](https://github.com/crytic/medusa)

Before the first edit, read the current create-chimera-app `AGENT.md` or `README.md`, its `test/recon` skeleton, and the relevant recon-magic prompts for scout, setup, properties, and coverage. If local clones exist, prefer them; otherwise fetch the public upstream files. Summarize the scaffold and command choices in the first implementation update.

If `create-chimera-app` or `recon-magic-framework` exists as a local checkout in the workspace or home directory, use those local references. Helpful files include:

- `<create-chimera-app>/AGENT.md`
- `<create-chimera-app>/test/recon/Setup.sol`
- `<create-chimera-app>/test/recon/TargetFunctions.sol`
- `<create-chimera-app>/test/recon/Properties.sol`
- `<create-chimera-app>/test/recon/BeforeAfter.sol`
- `<create-chimera-app>/test/recon/CryticTester.sol`
- `<create-chimera-app>/test/recon/CryticToFoundry.sol`
- `<recon-magic-framework>/prompts/agent/scout-v2-phase-0.md`
- `<recon-magic-framework>/prompts/agent/setup-v2-phase-0.md`
- `<recon-magic-framework>/prompts/agent/properties-v4-phase-0.md`
- `<recon-magic-framework>/prompts/agent/coverage-phase-0.md`
- `<recon-magic-framework>/prompts/agent/chimera-test-linter.md`

## Setup Workflow

1. Create or switch to the requested worktree/branch.
2. Inspect the project layout, Foundry config, remappings, interfaces, deploy scripts, existing tests, and instructions.
3. Read the create-chimera-app and recon-magic references listed above.
4. Bring in Chimera and helper libraries with repo-local remappings, usually `@recon/=lib/chimera/src/`.
5. Generate or adapt the harness under the repo's test tree using the standard Chimera shape: `Setup.sol`, `TargetFunctions.sol`, `Properties.sol`, `BeforeAfter.sol`, `CryticTester.sol`, and `CryticToFoundry.sol`.
6. Progressively deploy contracts, assets, actors, managers, and tracked targets through setup helpers instead of hardcoding everything inside target functions.
7. Add fuzzer configs for recon-fuzzer, Echidna, and Medusa, adapting from create-chimera-app where possible.
8. Run `forge build` and a short Foundry invariant or reproducer check before longer fuzzer runs.

The fuzzer entrypoint should normally be `CryticTester`. The Foundry debug entrypoint should normally be `CryticToFoundry`.

Prefer assertion mode for Chimera campaigns: properties should use Chimera assertions such as `t`, `eq`, `gte`, or `lte` through `CryticAsserts`/`FoundryAsserts`. Use `echidna_` property mode only when the local toolchain or repo convention clearly requires it.

## Stateful Harness Guidelines

1. Separate setup from exploration. Deploy contracts, create baseline actors, mint/fund assets, and grant baseline approvals in setup, not inside target functions.
2. Model actors and assets explicitly. Let the fuzzer switch actors, markets, tokens, managers, vaults, or positions through manager helpers rather than scripting fixed stories.
3. Expose one natural state transition per target when possible: deposit, withdraw, borrow, repay, place order, cancel, liquidate, claim, deploy, register, approve.
4. Bound only enough to keep inputs in meaningful protocol domains. Avoid pre-checking every success condition; invalid states and reverts are part of stateful fuzzing.
5. Let validation and revert branches be reached naturally. Do not add handlers whose purpose is to force `require` lines, impossible states, or intentionally invalid payloads.
6. Track created objects and select from tracked state. For example, place-order targets should record live orders, while cancel/decrease targets should choose from those tracked orders.
7. Split large lifecycle bundles into separate target functions unless the sequence itself is the behavior under test.
8. Define a shortcut as a target or scenario that intentionally composes multiple actions into one entrypoint because the exact sequence is hard to discover or is the property being tested. Use shortcuts sparingly, name them clearly, and document why they must be atomic.
9. Accept shortcuts for flows like approve-followed-by-deposit, quote-followed-by-execute, permit-followed-by-transfer, or a force-liquidation scenario such as `snapshot_liquidate` that performs `supply_1`, `supply_2`, `borrow_1`, `change_price`, and `liquidate_2` with different actors.
10. Do not add handlers solely to cover pure/view functions; the standard Recon coverage-generation path excludes ABI `view`/`pure` functions before `covg_eval` analyzes `recon-coverage.json`. Keep important read-only behavior as focused invariant checks or no-revert canaries, and move introspection sweeps, intentionally invalid calls, and revert-branch exercises out of primary stateful targets.

Good targets look like public user actions. Suspicious targets look like scripts that force a protocol story from start to finish.

## Coverage

Use standardized coverage from [`recon-magic-framework/tools/covg_eval`](https://github.com/Recon-Fuzz/recon-magic-framework/tree/main/tools/covg_eval), not ad hoc line counts. The goal is not complete until the covg_eval-style number meets the requested target for the requested production scope.

Prefer recon-fuzzer for coverage iteration because it is usually faster than Echidna. Use recon-fuzzer LCOV/corpus output to decide which real actions to add or split. Do not chase coverage with handlers whose only value is forcing lines through setup.

Track:

- fuzzer command
- elapsed time
- corpus path
- LCOV path
- standardized covered/total lines and percent
- any broken invariant, failing target, or harness defect

## CryticToFoundry Reproducers

When a fuzzer finds a broken invariant or property violation, create a deterministic `CryticToFoundry` reproducer, following the pattern of hardcoded failing sequences in examples like [rheo-xyz/rheo-solidity's `CryticToFoundry.t.sol`](https://github.com/rheo-xyz/rheo-solidity/blob/main/test/invariants/CryticToFoundry.t.sol#L88).

The reproducer should:

- live in the repo's invariant or Foundry test tree using the local naming convention
- hardcode the exact fuzzer-generated sequence and calldata/arguments
- avoid fuzz parameters
- fail deterministically before the production fix
- stay in git as a regression test after the bug is fixed
- include enough comments or test names for a developer to connect it to the fuzzer finding

## Validation Gate

For an end-to-end request, do not stop at "it compiles." A normal completion gate is:

- `forge build` passes.
- Short Foundry invariant smoke passes.
- recon-fuzzer runs for the requested smoke duration, often 5 minutes.
- Echidna runs for the requested smoke duration, often 5 minutes.
- Medusa runs for the requested smoke duration, often 5 minutes.
- Standardized coverage meets the requested threshold.
- No failing invariant remains unclassified.

If a fuzzer exits because a wrapper timeout stops it after the requested duration but it wrote corpus/LCOV and reports passing invariants, record that clearly rather than treating it as an unexplained failure.

## Failure Triage

When a smoke run or regression test fails:

- Reproduce with the narrowest command and high verbosity.
- Use a subagent for independent root-cause analysis when available.
- Classify as harness defect, production bug, spec mismatch, reference bug, or unknown.
- Keep production-bug regression tests failing if the user asks to preserve the finding and not edit production contracts.
- Use root-cause wording in commit messages for regression commits.

## Final Report

End with the files changed, commands run, fuzzer results, coverage numbers, remaining classified findings, and whether the requested campaign gate is complete. Mention any untouched dirty worktree changes separately.
