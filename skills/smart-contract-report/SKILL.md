---
name: smart-contract-report
description: Turn supplied smart-contract audit findings and supporting evidence into a submission-ready Markdown report with a contents list, issue counts, risk matrix, ordered finding IDs, actor-based scenarios, supplied unit-test evidence, and remediation diffs.
---

# Smart Contract Report

Prepare `smart-contract-report.md` from supplied audit findings, source context,
existing test evidence, and proposed fixes. Use another output path when requested.
This skill prepares the document; submit it externally only when the user specifies
the destination and authorizes submission.

## Evidence and scope

- Identify the reviewed repository, revision, scope, protocol roles, and supplied
  findings. Mark unavailable context explicitly; never invent test results,
  affected locations, assumptions, or validation status.
- Report established issues separately from unresolved candidates. Preserve source
  finding identities in working notes when assigning presentation IDs. Merge
  duplicates only when they share the same root cause and remediation.
- Format existing supplied PoCs without extending their exploitation capabilities.
  Do not discover exploit paths or generate missing exploit reproducers as part of
  report preparation. Flag missing evidence for the author; safe regression tests
  demonstrating intended behavior may accompany remediation, clearly labeled as
  regression evidence rather than a vulnerability PoC.
- Preserve the distinction between observed behavior and inferred consequences.
  If a supplied classification conflicts with the matrix, surface the conflict
  instead of silently changing an authoritative assessment.

## Report opening

Use this order:

1. Report title.
2. Table of contents linking the summary, severity matrix, every issue, and any
   appendix. Keep the contents at the beginning, directly after the title.
3. Summary containing the total issue count and separate High, Medium, Low, and
   Informational counts, including zeroes. Count only report entries; unresolved
   candidates do not contribute to these totals. Follow with repository, revision,
   and scope when available.
4. Issue index with `Issue ID` and linked `Title` columns, ordered H/M/L/I.
5. Severity matrix.
6. Issue entries, ordered H/M/L/I.
7. An optional appendix for unresolved candidates or material review limitations.

A report with no established issues still includes the contents, zero counts,
scope, and matrix. Do not equate zero findings with protocol safety.

## Severity matrix

Use only Low, Medium, and High for security severity, impact, and likelihood.
Display both matrix axes in ascending order. Preserve these cell values:

| Impact / Likelihood | Low | Medium | High |
| --- | --- | --- | --- |
| Low | Low | Low | Low |
| Medium | Low | Medium | Medium |
| High | Medium | High | High |

Impact describes the supported consequence: High requires direct asset loss or
compromise; Medium materially affects protocol operation, availability,
accounting, or value; Low covers limited defects with minor consequences.
Likelihood describes the realistic trigger conditions: High means reliably
reachable by an ordinary participant or naturally occurring in realistic use;
Medium requires meaningful but plausible conditions; Low requires restrictive
conditions or trusted participation. Explain the actual assumptions in each issue.

Assess intended privileged operations separately from administrator mistakes and
intentional misuse of trusted powers. State the protocol's trust assumptions;
do not infer an unprivileged attack from an administrator-only action. Unsupported
assumptions cannot establish High or Medium risk.

Informational is a separate category for established observations without a
demonstrated security consequence; it is outside the matrix. Do not use it to
hide unresolved vulnerability candidates.

## Issue identity and titles

- Stable-sort entries High, Medium, Low, Informational; preserve input order
  within each category.
- Assign independent, consecutive counters for each severity. Choose one digit
  width per severity from its total issue count: the greater of two digits or
  the digits needed to represent that count. Zero-pad every ID in that severity
  to the same width. For example, 101 High issues use `H-001` through `H-101`,
  including `H-100`, while 12 Medium issues use `M-01` through `M-12`. Apply the
  same rule to Low and Informational issues and update all references together.
- Use headings shaped as `## [H-01] - Attacker can drain vault due to incorrect permissions`.
  This is a title example, not a claim about the reviewed protocol.
- Write the concrete outcome and its cause. Prefer an actor when one is relevant.
  Avoid titles that merely name a bug class or exaggerate the supported consequence.
- Keep IDs and titles identical in the contents, index, and entry headings.

## Each issue

### Description

Explain the affected behavior, root cause, necessary conditions, expected behavior,
and supported consequence. Identify affected contracts or functions when known.
Keep the narrative understandable without opening an external artifact. Source
links may supplement the explanation, but must not replace it.

### Risk assessment

For H/M/L entries, include three bullets, each with a one-sentence explanation:

- **Severity**: rating followed by why this impact/likelihood combination produces it.
- **Likelihood**: rating followed by the actors, permissions, and conditions needed.
- **Impact**: rating followed by the concrete effect on assets or protocol behavior.

Format each as `- **Label**: Rating: Explanation.` Severity must equal the
matrix result. For informational entries, omit this entire risk assessment;
the `I-` identifier and summary category establish their classification.

### Proof of Concept

This section applies only to H/M/L entries. Informational entries omit the
entire Proof of Concept section, including numbered scenarios and test code.

Place a short human-readable numbered list of actor actions first, sorted in
chronological execution order. Start each step with the actor, followed by the
concrete action and its relevant result. Include the setup actions needed to
understand how the final consequence occurs.

Prefer **Attacker** and **Victim** when adversarial and harmed participants are
applicable. Otherwise use the protocol's usual roles, such as Depositor, Borrower,
Liquidator, User, or Administrator. Keep names consistent across the scenario and
test, and distinguish multiple participants as Victim A and Victim B or Depositor
A and Depositor B. Avoid anonymous variables and Alice/Bob when a role is available.

Example of the required presentation, only when supported by the supplied finding:

1. Victim deposits 100 MON into the protocol.
2. Attacker calls `redeem` and drains the protocol.

Describe setup, relevant actions in their observed order, and the resulting
observable consequence. Keep the scenario faithful to the supplied evidence;
do not add an unsupported attack sequence.

Immediately after the numbered scenario, include the supplied self-contained
unit-test PoC in a language-tagged code fence. Use the project's existing test
framework. A self-contained test includes its imports, setup, fixtures, mocks,
helpers, and meaningful assertions, and may depend on the reviewed repository
and its declared dependencies. It must not depend on another finding's snippet
or undisclosed local files. Do not replace code with a file link, ellipsis, or
invented passing result.

Include the test filename, execution command, prerequisites, and recorded
validation result when available. Distinguish an observed successful reproduction
from unverified source. A test confirming the fix must be labeled separately.
If required evidence is unavailable, identify the gap and mark the report as a
draft rather than claiming it is submission-ready.

### Remediation

Briefly explain the proposed fix, then show it in a fenced `diff` block using
git unified-diff syntax, including `diff --git`, `--- a/...`, `+++ b/...`, and
accurate hunk headers and context. Base the diff on the reviewed revision, with
actual repository paths and code. Include documentation diffs when the fix is
documentary. Avoid pseudo-diffs, placeholder code, and unrelated refactoring.

When source is available, check patch applicability against the reviewed revision
in an isolated copy. Record validation honestly. Do not apply proposed fixes to
the user's working tree merely to prepare a report. If a fix cannot yet be
specified, describe the missing decision and mark remediation as pending.
For an informational observation requiring no change, use `Not applicable` with
a reason instead of an empty diff.

## Final review

Before calling the report submission-ready, check:

- Contents and index links resolve to actual headings.
- Counts equal the entries and category totals sum to the total.
- IDs are unique, consecutive, and uniformly zero-padded within each severity
  according to its total count; order is H/M/L/I.
- Each H/M/L entry has a supported outcome-based title and three concise risk
  explanations, with severity matching the matrix.
- Every H/M/L issue has its numbered scenario before supplied test evidence,
  followed by remediation. Informational entries have neither a risk assessment
  nor a Proof of Concept section; they proceed from description to remediation.
- Tests and patches are complete as supplied, and validation claims match
  recorded evidence. Missing evidence and pending remediation remain visible.
