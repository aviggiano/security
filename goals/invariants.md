Build and validate a metric-driven stateful invariant testing campaign using [Recon-Fuzz/recon-magic-framework](https://github.com/Recon-Fuzz/recon-magic-framework). Treat stateful invariant testing as property-based fuzzing over sequences of protocol actions, not as a separate campaign type or as stateful unit tests.

Read AGENTS.md and obey all repo rules. Do not edit src/ or contracts/ except for interfaces, if needed.

Use [Recon-Fuzz/chimera](https://github.com/Recon-Fuzz/chimera), [Recon-Fuzz/create-chimera-app](https://github.com/Recon-Fuzz/create-chimera-app) helpers, Recon-Fuzz/recon-magic-framework/covg_eval, [Recon-Fuzz/recon-fuzzer](https://github.com/Recon-Fuzz/recon-fuzzer), [crytic/echidna](https://github.com/crytic/echidna), and [crytic/medusa](https://github.com/crytic/medusa). Install missing dependencies as needed.

Use the Chimera layout under the repo test root, likely test/foundry/recon/: Setup, BeforeAfter, Properties, TargetFunctions, CryticTester, and CryticToFoundry. Run forge build before longer fuzzer runs.

Apply these harness rules:

* Setup deploys contracts, creates/funds actors, grants baseline approvals, and initializes tracked state.
* TargetFunctions should expose natural public protocol actions with minimal preprocessing, since we want the fuzzer to explore different combinations of actions with randomized input. Clamping is allowed to bound input inside target functions if minimum and maximum values are parameterized as global constants.
* Let the fuzzer choose action sequences. Avoid broad try/catch, artificial coverage handlers, sweep/surface handlers, forced require/revert branches, and stateful-unit-test-style scripts.
* Prefer asActor, _getActor(), and ActorsManager helpers from create-chimera-app over vm.prank in target functions. Do not use "contract actors" with proxies, since asActor already exposes a modifier with vm.startPrank(msg.sender)/vm.stopPrank().
* Track created objects, then select from tracked state as done in ManagersTargets from create-chimera-app.
* Use "shortcut" handlers sparingly, only when the exact sequence is the behavior under test, such as approve-followed-by-deposit, quote-followed-by-execute, permit-followed-by-transfer, or a force-liquidation shortcut like snapshot_liquidate with different actors.
* Do not weaken assertions or hide production bugs. Treat failures as findings and classify the root cause before changing the harness.

Use recon-fuzzer for coverage iteration because it is expected to be faster than Echidna. Measure standardized coverage with Recon Magic covg_eval, not ad hoc line counts. Your goal is to achieve at least 90% standardized line coverage of core production contracts. Remember: recon-generate excludes ABI view/pure functions before covg_eval; covg_eval analyzes recon-coverage.json plus LCOV and filters internal/private missing reports.

The goal is complete only when recon-fuzzer attains standardized core-contract coverage of at least 90%.

For every fuzzer-discovered failure, create a deterministic CryticToFoundry reproducer that hardcodes the fuzzer-generated input and fails, to be kept as a regression test.

