---
name: foundry-spec-properties
description: Turn whitepapers, protocol specs, and public documentation into Foundry property tests. Use when Codex needs to derive invariants, conservation rules, atomicity rules, priority rules, price-view behavior, or accounting properties from external specs without weakening tests that expose production mismatches.
---

# Foundry Spec Properties

## Source Handling

Use the current spec source the user names. If it is a URL, fetch or browse it when current access is required. Cite or record the exact source used in the work summary when the property depends on a spec claim.

Separate spec properties from unit/fuzz mirrors. Use a clear directory such as:

```text
test/foundry/property/<spec-name>/
test/foundry/properties/
```

Follow the repository's existing directory convention when one exists.

## Deriving Properties

Look for properties that remain true across many inputs:

- conservation of balances, reserves, shares, or locked amounts
- quote/preview functions matching execution
- batch atomicity and required-action rollback
- price priority, time priority, or path priority
- price bucket/view consistency
- add/remove or deposit/withdraw roundtrips
- permission and lifecycle transitions
- no accidental balance creation or loss

Prefer public semantics over implementation-specific mechanisms.

## TDD Policy

Add the property test first. If it fails, do not fix production or relax the property in the same breath. Report whether the failure appears to be:

- invalid harness setup
- reference/spec modeling issue
- production/spec mismatch
- ambiguous spec language

Preserve strict equality unless the spec states a tolerance. Rounding-related failures should remain visible unless the user explicitly decides otherwise.

## Validation

Run the focused property file with enough verbosity to capture the counterexample. Then run the property subtree or full Foundry suite as appropriate.
