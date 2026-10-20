---
name: smart-contract-report
description: Turn smart-contract audit findings and source evidence into a standalone Markdown report with a concise executive summary, issue index, ordered finding IDs, actor-based scenarios, unit-test proofs of concept, and validated remediation where available.
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
- Preserve supplied PoCs when they accurately reproduce the finding. When a PoC is
  missing, reconstruct the smallest self-contained unit test that demonstrates the
  reported behavior against the reviewed revision. Keep it within the supplied
  finding's actors, preconditions, and consequence; do not target live systems or
  expand into unrelated exploit discovery. Validate it in an isolated copy when
  feasible and record the command and observed result. If execution is unavailable
  or the test does not reproduce the issue, state that clearly and keep the report
  as a draft.
- Preserve the distinction between observed behavior and inferred consequences.
  If a supplied classification conflicts with the severity rubric, surface the
  conflict instead of silently changing an authoritative assessment.
- Make the report understandable on its own. Do not refer readers to the source
  report, external documents, test files, logs, worktrees, or this skill for
  context or evidence. Required code imports inside a PoC are permitted.
  Reviewed commit permalinks and relevant pull request permalinks are allowed
  and required when identifying scope or discussing a pull request.
- Use the final report's issue IDs for cross-references. Replace source-only
  labels or branch letters with plain feature names in reader-facing prose;
  explain any essential shorthand where it first appears.

## Report opening

Use this order:

1. Report title.
2. A short `Executive summary` with exactly one CEO-level TL;DR paragraph
   followed by one two-column table (`Item` and `Detail`). The paragraph states
   the main business exposure and release decision without test mechanics.
   Use these table rows:
   - **Scope:** one cell naming the reviewed repository, then bullets for the
     baseline and every reviewed branch. Give each its exact name and pinned
     full commit hash linked to a commit permalink. Link associated pull
     requests. Use `<br>` and `•` for bullets within the Markdown table cell.
   - **Findings:** counts of High, Medium, Low, and Informational issues on the
     first line, then a short, supported statement about the most significant
     findings on a second line separated by `<br>`.
   - **Methodology:** model, harness, and relevant execution configuration when
     supplied; leave test framework and PoC details to individual issues.
   - **Time:** recorded elapsed time and the events defining its window.
   - **Cost:** recorded or estimated model cost, labeled as such, with its
     pricing basis and included work when known.
   - **Release decision:** the affected changes to hold or prioritize when the
     evidence supports a recommendation.
   Do not invent missing usage figures; say `Not recorded` in a row when they
   are unavailable. Explain terminology and evidence limits in the relevant
   issue, without adding `Terms` or `Evidence limits` rows.
3. Issue index with `Issue ID` and linked `Title` columns, ordered H/M/L/I.
4. Issue entries, ordered H/M/L/I.

Do not add a separate table of contents, severity-matrix section, or appendix.
Keep the executive summary brief and decision-oriented; put technical detail in
the issues. A report with no established issues still includes the index. Do not
equate zero findings with protocol safety.

## Severity ratings

Use only Low, Medium, and High for security severity, impact, and likelihood. Do
not include a severity matrix in the report. Assign severity as follows: Low
impact is Low at every likelihood; Medium impact is Low at Low likelihood and
Medium otherwise; High impact is Medium at Low likelihood and High otherwise.

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
- Keep IDs and titles identical in the index and entry headings.
- Link each pull request reference in report prose to its permalink, formatted
  as `[PR #71](https://github.com/owner/repository/pull/71)`. Check the repository
  before linking; issue numbers and pull request numbers can overlap. Avoid pull
  request numbers in issue titles when the title is itself an index link, since
  Markdown links cannot be nested. Code fences cannot contain clickable links;
  use the branch or commit name in code comments and assertion messages, and
  put the linked pull request reference in the surrounding prose.

## Each issue

### Description

Explain the affected behavior, root cause, necessary conditions, expected behavior,
and supported consequence. Identify affected contracts or functions when known.
Keep the narrative understandable without opening another artifact. Include the
relevant code behavior directly rather than sending the reader to a link.

### Risk assessment

For H/M/L entries, include three bullets:

- **Severity**: rating followed only by `X impact × Y likelihood`.
- **Likelihood**: rating followed by the actors, permissions, and conditions needed.
- **Impact**: rating followed by the concrete effect on assets or protocol behavior.

Format severity as `- **Severity**: Medium: Medium impact × Medium likelihood.`
Do not mention the rubric or restate how the rating is derived. Format the other
bullets as `- **Label**: Rating: Explanation.` Severity must still follow the
severity rubric. For informational entries, omit this entire risk assessment;
the `I-` identifier establishes their classification.

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

Immediately after the numbered scenario, include a self-contained unit-test PoC
in a language-tagged code fence. Use an accurate supplied PoC when available;
otherwise reconstruct it from the finding and reviewed source. Use the project's
existing test framework. A self-contained test includes its imports, setup,
fixtures, mocks, helpers, and meaningful assertions, and may depend on the
reviewed repository and its declared dependencies. It must not depend on another
finding's snippet or undisclosed local files. Do not replace code with a file
link, ellipsis, or an invented passing result.

Label the PoC as supplied or reconstructed. After the code, state an observed
validation result in one short sentence when it has been verified. Do not list
test file paths, commands, logs, or evidence-packaging instructions in the report.
Distinguish an observed successful reproduction from unverified source, and label
a test confirming the fix separately. If a PoC relies on an auxiliary artifact,
inline it when practical and rerun the test; otherwise identify its dependency
type and validation limit within that issue without citing an external path. If
required evidence is unavailable, mark the report as a draft rather than claiming
it is submission-ready.

### Remediation

Briefly explain the proposed fix. Include a fenced `diff` block only for a
concrete patch checked against the reviewed revision. Use git unified-diff syntax,
including `diff --git`, `--- a/...`, `+++ b/...`, and accurate hunk headers and
context. Include documentation diffs when the fix is documentary. Avoid
pseudo-diffs, placeholder code, and unrelated refactoring.

Check patch applicability against the reviewed revision in an isolated copy.
Record validation honestly. Do not apply proposed fixes to the user's working
tree merely to prepare a report. Do not repeat a `Pending verified patch` or
similar boilerplate in each finding. If a fix cannot yet be specified, state the
missing decision in that issue's remediation prose.
For an informational observation requiring no change, use `Not applicable` with
a reason instead of an empty diff.

## Final review

Before calling the report submission-ready, check:

- Index links resolve to actual headings.
- IDs are unique, consecutive, and uniformly zero-padded within each severity
  according to its total count; order is H/M/L/I.
- Each H/M/L entry has a supported outcome-based title and three concise risk
  explanations, with severity following the rubric.
- Every H/M/L issue has its numbered scenario before its unit-test evidence,
  followed by remediation. Informational entries have neither a risk
  assessment nor a Proof of Concept section; they proceed from description to
  remediation.
- The executive summary precedes the issue index and has one TL;DR paragraph
  and one table. Scope occupies one row with pinned commit and pull request
  links; Findings has accurate counts and a second context line; Methodology,
  Time, Cost, and Release decision occupy separate rows. There is no appendix
  or unexplained source-only shorthand in reader-facing prose.
- Supplied or reconstructed tests and included patches are complete, and
  validation claims match recorded evidence. Missing evidence and pending
  remediation remain visible without repeated boilerplate or external references.
