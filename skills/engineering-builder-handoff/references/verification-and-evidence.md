# Verification and evidence

How to report what was tested, what actually ran, what it proves, and where the proof is. Report only verification that actually ran. Everything else is `NOT RUN`, `NOT VERIFIED` or `NOT MEASURED`, stated rather than silently omitted.

## Contents

- H40 Claims and evidence, H35 traceability
- H10 Tests added or changed
- H46 Test quality disclosure
- H47 Failures during the work
- H11 Verification results
- H48 Partial verification
- H12 Negative and failure testing
- H13 Regression review
- H14 Evidence index
- H36 Reproduction, H41 expected and observed, H42 before and after
- H43 Tolerances
- H44 Hash and byte identity
- H45 Data preservation
- H58 Repeated runs and idempotence
- H49 Environment
- H50 Source of truth
- H63 Performance
- H64 Manual observation
- Evidence privacy

## H40 Claims and evidence

For every important claim, keep the claim and its evidence apart:

```
CLAIM: changing the date display format no longer changes stored due dates.
EVIDENCE: the same invoice saved under day-first, month-first and ISO display settings kept
due_date = 2026-03-31 in all three — tests/unit/due-date.test.js, "display format invariance".
```

A claim is never evidence for itself, and "I checked it" is a claim.

**Traceability (H35).** Every important claim traces to a source line, a test, a command result, a file hash, a screenshot, a log, a measurement or a commit. Write "All 75 before/after hashes matched (evidence/hashes.json)", not "Everything is preserved."

## H10 Tests added or changed

For each meaningful test change, state:

- the test file and test name;
- the contract it checks;
- the regression it prevents;
- whether it was run against the previous implementation, and with what result.

For a regression fix, when practical, show both halves and how the old implementation was exercised — for example the base commit in a separate worktree, which leaves the delivered working tree untouched. For a corrective patch this proof is expected (H25):

```
OLD IMPLEMENTATION → TEST FAILS  (expected 2026-03-31, received 2026-03-30)
NEW IMPLEMENTATION → TEST PASSES
```

If the test was not run against the old implementation, say `NOT CHECKED` and do not claim it. A test of brand-new code has no old implementation; say so rather than implying a failure proof. A test that merely follows the implementation — recomputing the answer the same way the production code does — is weaker evidence; disclose that limitation when it matters.

## H46 Test quality disclosure

Name the kind of evidence; do not present these as equivalent:

| Kind | Meaning |
|---|---|
| EXISTING TEST | already in the suite before this work |
| NEW REGRESSION TEST | added for this change; say whether it failed on the old code |
| BUILDER-ONLY PROBE | a temporary script, command or check not kept in the suite |
| SIMULATED INPUT TEST | synthetic events, an emulator or simulator, a headless browser |
| PHYSICAL DEVICE TEST | on real hardware |
| MANUAL OBSERVATION | a person looked at it (H64) |

## H47 Failures during the work

Do not report only the final green run when meaningful failures happened along the way. For each relevant failure, state:

- what failed;
- whether the implementation caused it;
- how it was resolved;
- whether a regression test now covers it.

Expected failures in test-first work may be summarized in one line rather than dumped. Flaky or unexplained failures are reported as flaky or unexplained — never rerun into silence.

## H11 Verification results

Report only verification that actually ran: unit, integration, rendered or UI tests, lint, typecheck, build, packaging, signature verification, installed-application verification, device validation, migration checks, performance measurements.

- Name the command or suite and give exact counts where they mean something: "unit: 412 passed, 0 failed, 3 skipped (npm test)". Name any skipped test that touches the change.
- A required or expected check that did not run is listed as `NOT RUN`, with the reason. It is never simply left out.
- Never convert "the command exited 0" into a stronger claim than that command proves. A build that exits 0 did not test behavior; a runner that found zero tests passed nothing.

## H48 Partial verification

When verification is incomplete, say exactly what is missing:

```
VERIFIED: unit, rendered, lint, typecheck, build.
NOT VERIFIED: physical device; build from a fresh checkout.
```

Never collapse that into "Verification passed.", and use "Builder verification passed." only when every check you report ran and passed.

## H12 Negative and failure testing

For high-risk work, report the failure paths exercised and what happened: wrong key, missing key, malformed data, repeated migration, failed install, invalid state, missing file, stale baseline, boundary values.

In Compact and Standard handoffs, omit this section when failure testing was not relevant. In a Full handoff, also name the relevant failure paths that were not exercised.

## H13 Regression review

State which adjacent behavior was checked for regressions, and how.

```
CHANGE: due-date display-format correction
REGRESSION SURFACES CHECKED: overdue calculation, reminder scheduling, CSV export, invoice PDF,
settings persistence
RESULT: NO REGRESSION FOUND WITHIN THE CHECKED SURFACES
```

A passing main suite does not justify "NO REGRESSIONS". Bound the statement by the surfaces actually checked.

## H14 Evidence index

Index the evidence; do not paste it into the handoff. For each item give what it is and where it is, or the command that regenerates it: reports, logs, screenshots, JSON measurements, manifests, hashes, test output, before and after comparisons.

Distinguish, where it matters:

- **DURABLE EVIDENCE** — kept where the reviewer can reach it later;
- **TEMPORARY REVIEW ARTIFACT** — in a scratch or temporary location that may be cleaned up.

If evidence you rely on is temporary, say so and give the command that recreates it. Store evidence only where the project allows. When no artifact exists — an unsaved observation, a defect only noticed — write `NOT CAPTURED`.

## H36 Reproduction, H41 expected and observed, H42 before and after

**Reproduction (H36).** For defects and high-risk work, give exact steps when practical. The reviewer should not have to infer which state, input, setting, sequence or expected result. Concise, but sufficient.

**Expected and observed (H41).** For defects, migrations, device tests and important behavior changes — especially when the reviewer should reproduce the result independently:

```
EXPECTED: switching the display format from day-first to ISO leaves the stored due date unchanged.
OBSERVED: due_date stays 2026-03-31; the screen changes from 31/03/2026 to 2026-03-31.
RESULT: PASS
```

**Before and after (H42).** When a change corrects existing behavior, state the meaningful difference:

```
BEFORE: switching the date display format could move a stored due date back by one day.
AFTER: display-format changes leave stored due dates unchanged.
```

Skip before-and-after sections when nothing meaningful changed; they are not decoration.

## H43 Tolerances

When verification involves numbers, report the tolerance or acceptance criterion if one exists: coordinate error, pixel displacement, timing tolerance, hash equality, file count, performance threshold.

Never write "very close" when the result can be measured: "maximum displacement 0.4 px (criterion ≤ 1 px)". If no formal tolerance was defined, say so and give the measured value.

## H44 Hash and byte identity

When claiming files or data are unchanged, prefer deterministic evidence where practical: SHA-256 hashes, byte comparison, a deterministic manifest, exact file counts, exact byte counts. Never claim byte identity from visual inspection.

For a low-risk task where identity is not in question, do not generate hashes merely for ceremony.

## H45 Data preservation

For tasks that involve real persisted data, "data preserved" needs evidence proportional to the risk: before and after hashes, record counts, schema validation, migration analysis, reopen or relaunch proof, verification of a recovery copy.

Never infer data preservation from the application launching. Say what kind of data the proof used — synthetic, a copy of real data, or the real data — without reproducing its contents.

## H58 Repeated runs and idempotence

For operations meant to be repeatable — a migration on second launch, a setup script's second run, an installer rerun, a baseline scan rerun — report what the repeated run did.

A successful first run does not prove idempotence, and a second run that nobody inspected proves nothing about it. If the repeat was not checked, its behavior is `NOT VERIFIED`.

## H49 Environment

For environment-sensitive work, report only the conditions that matter to reproduction: operating system, runtime and its version, device, CPU architecture, browser or WebView, package-manager version, build mode such as debug or release.

Do not dump irrelevant system information.

## H50 Source of truth

When artifacts disagree — an accepted ADR, the current production source, the persisted data contract, package metadata, an owner-approved roadmap — name which one is authoritative and why, and report the contradiction.

Never silently choose whichever source makes the implementation look correct.

## H63 Performance

Never write "performance is good". When performance is part of acceptance, report measurements — latency, frame timing, memory, CPU, build time — with the method, sample size and environment.

When performance was not measured: `PERFORMANCE: NOT MEASURED`. Subjective smoothness is never a numeric performance result.

## H64 Manual observation

When the owner or another person physically observes behavior, keep that separate from automated verification:

```
USER OBSERVATION: "Scrolling feels smooth on my phone." — the owner
AUTOMATED MEASUREMENT: about 22 ms median input-to-screen response over 40 samples
```

Both may be valuable; they are different kinds of evidence, and neither stands in for the other. An owner's observation is not an acceptance unless the owner says it is.

## Evidence privacy

Never place passwords, private keys, tokens, secret environment values or private user or device records in evidence you index or quote (H37). Redact screenshots and logs before indexing them, and describe a secret's state — present, external to the repository, fingerprint verified — without printing it.

When the only evidence contains private data, do not index it: describe it by its kind and origin, and say where it is kept without reproducing its contents. Offer a synthetic reproduction the reviewer can run instead when one exists or is cheap to build within scope; otherwise say how one could be built. A handoff is never made more detailed by exposing private data.
