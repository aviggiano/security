Build and validate a metric-driven Foundry fuzz harness for this Solidity/DeFi project. 

You should discover the project's behavior, build a calibrated test harness, find production bugs, preserve strict failing reproducers, deduplicate issues, and report them to developers.

Read AGENTS.md and obey all repo rules. Do not edit src/ or contracts/ except for public interfaces or harness-only adapters when strictly needed to compile tests. Do not fix production bugs. Do not weaken, skip, delete, or over-bound a failing strict-equality test until fresh triage proves it is not a production bug.

Use Foundry Solidity tests as the campaign output. If the repository is Hardhat-only or missing Foundry, bootstrap the minimal Foundry setup needed for `forge test`, then write tests under `test/foundry/**` unless the repo already has a stronger convention. Existing JavaScript or TypeScript tests should be mirrored as initial public behavior, but do not add new JavaScript or TypeScript tests.

Use subagents with maximum reasoning effort whenever an independent task or action item can be implemented, which will benefit from fresh context and isolation matters. At minimum, use separate subagents for repo/interface discovery, existing-test mining, surface planning, Foundry bootstrap, fixture authoring, reference authoring when needed, reference calibration, fuzz authoring, differential authoring, failure triage, issue deduplication, and final report review. The orchestrator should route artifacts, validate gates, and decide next steps; major coding passes should be delegated to pass-scoped subagents.

First measure the project and record the result:

- Run `cloc` or the closest available equivalent on production Solidity.
- Count public/external value-moving surfaces and behavior groups.
- Identify interfaces, deployment scripts, docs/specs, existing tests, and public APIs.
- Flag functions over 100 lines, assembly/Yul, heavy unchecked math, optimized libraries, packed encoding, fallback dispatch, custom calldata parsing, custom math, accounting, pricing, share/asset conversions, interest, rewards, liquidation, fee logic, order books, queues, auctions, routers, multicalls, vaults, pools, lending, and other synchronized ledgers.

Choose test surfaces by risk rather than by a fixed count:

- If production Solidity is under 1k SLOC, test all meaningful external/state-changing and value-moving surfaces.
- If production Solidity is 1k-5k SLOC, test all critical accounting/security surfaces plus at least the top 8-12 risk-ranked behavior groups.
- If production Solidity is over 5k SLOC, test at least 15-25 risk-ranked behavior groups, covering every major module and all value-moving paths.

A surface is a behavior group, not just one function: deposit/withdraw, mint/redeem, borrow/repay, liquidate, swap, quote/execute, add/remove liquidity, stake/unstake, claim, rebalance, settle, place/fill/cancel/replace order, batch/multicall, pause/unpause, configure, upgrade, or any equivalent project-specific public workflow.

Force an independent reference implementation plus differential tests when any of these are true:

- Production Solidity is over 3k SLOC.
- A high-value function is over 100 lines or has many interacting branches.
- The code uses assembly/Yul beyond trivial revert or memory plumbing.
- The code uses optimized/custom math, fixed-point math, rounding, pricing, interest, reward indexes, liquidation math, share/asset conversion, fee accounting, or value-sensitive accounting.
- Internal libraries are used for math, accounting, encoding, decoding, pricing, routing, or other correctness-critical input-output behavior.
- Multiple user balances, reserves, debts, collateral balances, shares, global indexes, locked balances, orders, or fee buckets must remain synchronized.
- Expected values in tests would otherwise copy or closely mimic production implementation logic.

For small simple projects where those force-reference triggers do not apply, a full system reference may be skipped only after the surface planner records why it is unnecessary. Even then, create small local reference or metamorphic tests for any tricky internal library, math function, encoder/decoder, or rounding path.

Follow this workflow using sub-agents as instructed:

1. Discovery: read repository instructions, public interfaces, public docs/specs, READMEs, deployment scripts, existing tests, and standard/library specs. Do not derive expected behavior from private production internals.
2. Surface planning: produce a risk-ranked list of behavior groups, required deterministic tests, required fuzz tests, reference needs, differential lanes, skipped surfaces, and why any skipped surface is low risk or infeasible.
3. Foundry bootstrap: create or repair only the minimal Foundry wiring required for `forge test`. Avoid build/cache artifacts in the diff.
4. Fixtures: build small Solidity deployment fixtures, actors, funding, approvals, snapshots, mocks, and reusable fuzz-bound helpers under `test/foundry/**`.
5. Reference authoring: when required, build a deliberately simple independent reference from public interfaces/docs/tests. Prefer explicit structs, mappings, arrays, and direct loops. Avoid assembly, packed storage tricks, optimized production algorithms, and copied production control flow.
6. Reference TDD calibration: before comparing production to the reference, port or mirror existing public unit tests so they run against the reference or a reference-only harness. Repair the reference until it satisfies public behavior, or explicitly mark unsupported surfaces out of scope. The reference must compile and pass its focused calibration suite before production-vs-reference differential tests consume it.
7. Deterministic mirrors: write clear Foundry happy-path and edge-path tests for selected public workflows. Mine existing tests for setup, actors, actions, assertions, and expected state changes.
8. Fuzz tests: convert deterministic flows into bounded parametric tests. Bound inputs to public preconditions, not to make assertions pass. Preserve strict equality unless a public spec defines tolerance.
9. Internal library tests: expose correctness-critical internal libraries through thin test harnesses and fuzz them directly, including invalid/precondition-breaking inputs when public callers may violate assumptions.
10. Differential tests: after reference calibration, compare production and reference side by side using public behavior only: return values, balances, public views, metadata, events when already public-tested, externally visible state, and revert behavior.
11. Failure triage: reproduce every failure with the narrowest focused `forge test` command. Use fresh subagents for independent failures. Classify each as production bug, harness defect, reference bug, spec mismatch, or unknown.
12. Deduplication and reporting: deduplicate production bugs by root cause and write `ISSUES.md` in the developer-facing format described below.

Include these testing methodologies where applicable:

- Deterministic mirrors for every selected public workflow before fuzzing that workflow.
- Bounded fuzz mirrors of deterministic flows.
- Accounting identity tests: total equals available plus locked, assets equal shares/reserves/fees/debt as applicable, conservation of balances, reserves, shares, liabilities, and fees.
- Roundtrip operation tests: deposit/withdraw, mint/redeem, borrow/repay, quote/execute, wrap/unwrap, stake/unstake, add/remove liquidity, place/cancel, encode/decode, compress/decompress, tick/price/tick, assets/shares/assets.
* Encoding and decoding identity tests: `decode(encode(x)) == x`, and `encode(decode(bytes)) == canonical(bytes)` where canonicalization is expected.
- Malformed calldata fuzzing: short calldata, extra trailing bytes, invalid offsets, overlapping dynamic tails, huge lengths, empty arrays, nested arrays/tuples, wrong selectors, selector-only calls, and arbitrary fallback calldata.
- Packed encoding and hash/signature tests for collision-prone inputs, adjacent dynamic fields, empty strings/bytes, variable-width integers, truncation, sign extension, and canonical length/padding.
- Selector and dispatcher fuzzing for fallback routers, proxies, diamonds, multicalls, packed calldata routers, and manual dispatch.
- Slow-vs-optimized equivalence tests for assembly, bit tricks, branchless logic, packed storage logic, custom math, and gas-specialized implementations.
- High-precision math reference tests for `mulDiv`, fixed-point math, exponentiation, square roots, tick/price math, interest accrual, fees, rewards, shares/assets, and liquidity math.
- Rounding and exactness tests: exact division vs inexact division, floor/ceil direction, maximum error, one-wei residuals, dust, near-empty pools/vaults, and min/max precision.
- Overflow and underflow edge tests, including intermediate overflow where the final mathematical result would fit.
- Casting and truncation tests across smaller uints, signed ints, addresses, bytesN, packed fields, bitmaps, and nonces.
- Domain partition fuzzing: zero/nonzero, exact/inexact, below/equal/above threshold, empty/single/many elements, signed negative/zero/positive, valid/invalid mode flags.
- Metamorphic tests: split action equals single action, repeated small actions equal one large action where specified, route composition, direct vs routed path, exact-in vs exact-out consistency, scaling relationships, and order independence where expected.
- Actor matrix tests for owner, user, keeper, liquidator, borrower, LP, router, malicious recipient, paused role, unauthorized caller, and privileged roles.
- Token compatibility tests using mocks for different decimals, fee-on-transfer, rebasing, no-return, false-return, callback/reentrant, blacklist/revert, and non-standard ERC20 behavior when token-facing flows exist.
- Temporal fuzzing around deadlines, cooldowns, maturities, epochs, stale oracle windows, accrual intervals, funding periods, and before/at/after boundary timestamps or blocks.
- Oracle perturbation tests around decimals, stale timestamps, zero/negative values where applicable, rapid jumps, confidence bounds, and normalization.
- Exchange-rate manipulation tests: donations, first depositor, empty vault/pool, flash deposit/withdraw, reserve skew, share price manipulation, and quote skew before victim actions.
- Slippage and MEV-ordering tests: quote then mutate state then execute; reorder fills, swaps, deposits, withdrawals, liquidations, and order placements.
- Negative/revert fuzzing for every documented precondition: unauthorized caller, stale price, insufficient balance/collateral, expired order, invalid path, invalid ids, slippage exceeded, unsupported asset, out-of-range amounts.
- Bounded short-sequence fuzzing with sequence length 2-6 for selected workflows, such as deposit/borrow/repay/withdraw, place/fill/cancel/claim, quote/execute/settle, or add/swap/remove. Do not build a full invariant harness in this goal.
- Gas and loop-bound tests for practical denial-of-service risks: list sizes, order counts, markets, reward recipients, collateral arrays, batched calls, cleanup loops, and pagination.

Apply these failure and red-preservation rules:

- Treat one-wei and one-unit mismatches as real findings unless a public spec defines tolerance.
- A green aggregate run is not evidence against a historical pre-repair red candidate.
- Preserve every strict-equality semantic failure in a red-candidate ledger before repair.
- Each red candidate must include test path, failing test name, focused command, seed/counterexample when available, observed value, expected value, assertion predicate, public oracle basis, failure signature, and whether it is production bug, harness defect, reference bug, spec mismatch, unknown, or untriaged.
- Do not edit, skip, soften, delete, or over-bound a semantic red test unless two fresh-context triage records agree it is not a production bug.
- Keep production-bug tests failing and runnable. Report them instead of fixing production.
- Fix harness defects and reference bugs only after classification and with minimal edits.
- If a reference or fuzz test fails because the public spec is ambiguous, preserve the failure as unknown or spec mismatch and explain the ambiguity.

Write `ISSUES.md` at the end. It must contain a deduplicated production-bug table, not a list of every raw failing test. Use this format:

```markdown
# Deduped Red Issues

| id | Issue | Red tests | Root cause | Remediation |
| --- | --- | --- | --- | --- |
| ISSUE-01 | Short issue title | `testName1()` `testName2()` | Explain the production behavior and why it violates the public oracle. | Explain what the developer should change or decide. |
```

Only include production bugs in the table. Exclude harness defects, reference bugs, spec mismatches, unknowns, and coverage notes from production-bug rows. If no production bugs are classified, `ISSUES.md` must say the campaign found no classified production bugs only if the completion gates below are satisfied. Otherwise, state that the campaign is incomplete and name the missing gate.

The goal is complete only when all applicable gates pass:

- Foundry is bootstrapped or already present, and focused `forge test` commands compile and run the authored tests.
- The project measurement and risk-ranked surface plan are recorded.
- Every selected high-risk value-moving surface has deterministic tests and fuzz tests, or an explicit infeasibility/skipped rationale.
- Every forced-reference trigger has either a calibrated reference plus differential tests, or a written explanation why a full reference is infeasible and what local reference/metamorphic substitute was used.
- Any required reference implementation has passed its TDD calibration suite before production differential tests rely on it.
- Correctness-critical internal libraries for math, accounting, encoding, decoding, pricing, routing, or value movement are fuzzed directly or covered by an equivalent high-level differential/reference lane.
- Encoding/decoding, selector/fallback, packed calldata, and malformed calldata tests exist whenever the project exposes those surfaces.
- Every strict-equality failure has a focused reproduction command and a classification.
- Every classified production bug has a preserved failing Foundry reproducer.
- `ISSUES.md` contains every deduplicated classified production bug and no non-production rows.
- The final diff does not include `out/**`, `cache/**`, `broadcast/**`, coverage outputs, unrelated generated artifacts, unnecessary vendored dependency churn, or production bug fixes.

Before declaring completion, run a final fresh-context report review subagent. It must verify that high-risk surfaces were not skipped silently, production-bug red candidates were not lost after repair, reference calibration happened before differential testing, and every production bug in the red-candidate ledger appears in `ISSUES.md`.
