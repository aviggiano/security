# Property specification guidelines

These guidelines answer the question: how can we write good properties for a project?

1. Understand the system before writing property code: docs, storage, interfaces, actors, assets, permissions, and the state machine.
2. Focus on how the documentation, README, and whitepaper describe how the system should behave, and compare that with how it actually behaves. There may be a gap between the specification and the implementation.
3. What we want is for the implementation to follow the spec, not the other way around.
4. The specification can also be wrong, and could also be questioned. Issues in the spec are harder to reason about because they can simply lead to worse systems, but not necessarily insecure systems. Because of that, our job here is to focus on spec mismatches, not spec challenging.
5. Make assumptions explicit, and keep setup assumptions separate from property logic.
6. Keep properties independent where possible, so each broken property is easy to reason about, and multiple properties can be layered together and fuzzed or verified to maximize system coverage.
7. Avoid properties that merely mirror implementation details.
8. Maintain a property inventory with property description, type, impact, and priority. Even in the age of AI, where you can implement many properties quickly, prioritization is still useful to keep context manageable for both humans and machines.
9. Remove duplicate properties, or merge them into clearer checks.
10. Review the property list with protocol designers, engineers, auditors, and business teams to challenge hidden assumptions. Different teams may have different understandings of the spec.
11. Prioritize high-impact guarantees first: solvency, accounting, value extraction, and other critical system guarantees.

## Property discovery

We extend [Certora's](https://github.com/Certora/Tutorials/tree/master/06.Lesson_ThinkingProperties) categorization when brainstorming properties:

1. Valid states: what must be true in every meaningful system state? For example, when the system is frozen, paused, initialized, liquidating, recovering, or settled, what must be true?
2. State transitions: what must change, or never change, when moving between states?
3. Variable transitions: which variables are monotonic, bounded, or only change under specific operations? Which quantities should never decrease, never increase, or stay within bounds?
4. High-level properties: what broad system guarantees must always hold?
5. Unit test / scenario properties: what should hold when a precise flow is executed?
6. Roundtrip properties: whenever an action is reversible, users should not be able to extract value from the system. For example: deposit -> withdraw.
7. Denial of Service properties: which public functions should never unexpectedly revert? For example, failing to withdraw, liquidate, or repay will typically indicate risk to user funds or to the health of the system.

## Mathematical precision

1. Mathematically prove important invariants where possible.
2. Write the equation for the invariant and test it against all protocol modes, not only the common path.
3. If an invariant breaks, consider that the invariant might be invalid or incomplete.
4. Refine overly strict assertions into the actual security requirement. For example, equality may need to become `<=` when solvency is what matters.
5. Prefer strict equality over tolerances. The impact of a 1 wei difference can be hard to understand, since it may require multiple preconditions and scenarios to fully escalate into a protocol drain.