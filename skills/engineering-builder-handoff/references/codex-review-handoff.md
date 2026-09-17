# Codex review handoff

The part of the handoff that tells the independent reviewer what to verify. The reviewer is the one the project names — Codex when none is named — and these rules apply to any independent reviewer. The purpose is to make an independent review efficient, never to steer its conclusion. The builder is not the reviewer, and nothing written here is a review.

## Contents

- H39 Independence first
- H21 The challenge list
- Template
- H22 Adversarial review suggestions
- Corrective re-review
- When no independent review is authorized
- Before sending

## H39 Independence first

Give the reviewer enough to start efficiently, but nothing that biases the verdict.

Provide:

- the implementation contract: what must now be true;
- evidence locations;
- reproduction paths;
- the protected behavior;
- the known risk surfaces;
- the claims that need verification.

Do not provide:

- the verdict you want;
- instructions to accept;
- arguments designed to persuade — a severity disagreement is recorded once in the findings (H55), not argued here;
- selective evidence that hides contrary results — report failures, flaky runs and inconsistent measurements too;
- language implying that passing builder tests proves acceptance.

The handoff enables independent review; it does not argue for a result.

## H21 The challenge list

Never end with "Please review." Write concrete things for the reviewer to determine independently, each phrased so it could turn out false. Cover what applies:

1. **Baseline and commit identity** — is the state under review exactly the delivered one?
2. **The exact implementation contract.**
3. **The primary reproduction.**
4. **Regression surfaces.**
5. **Negative and edge cases.**
6. **Protected areas** — do they really remain untouched?
7. **Test claims to rerun** — the commands, and the counts the builder saw.
8. **Evidence to check independently** — hashes, measurements, screenshots, logs.
9. **Known limitations to judge** — acceptable for this gate, or blocking?
10. **Findings to close or reopen.**

| Instead of | Write |
|---|---|
| "Confirm that this is correct and accept it." | "Independently determine whether changing the date display format leaves stored due dates unchanged." |
| "Tests pass, so this should be fine." | "Rerun the unit suite at a91d03b — the builder saw 412 passed, 0 failed — and decide whether the new invariance test would have failed at 4f2c9e1." |
| "M01 is fixed." | "Reproduce M01's original steps at 4f2c9e1 and at a91d03b, then decide whether M01 closes or reopens." |
| "The migration is safe." | "Run the migration twice on a copy of a version 3 data file; compare record counts and hashes after each run." |

## Template

Inside a Standard or Full handoff, point to earlier sections instead of repeating their facts. When this section is sent on its own — pasted into another tool, for example — it must carry the target commit and the builder's counts itself.

```
CODEX HANDOFF — independent verification requested
REVIEW TARGET: <commit and base, or the uncommitted diff hash> (details: BASELINE and GIT)
CONTRACT TO VERIFY: <the invariant or invariants, in one or two sentences>
REPRODUCE: <steps> · EXPECTED: <result>
CHALLENGE:
1. <determine whether …>
2. <…>
RERUN: <commands> (builder's counts: VERIFICATION)
EVIDENCE TO CHECK INDEPENDENTLY: <paths or commands>
LIMITATIONS AND FINDINGS TO JUDGE: <items, or see LIMITATIONS and FINDINGS>
WEAKEST POINTS, BUILDER'S VIEW: <substantial or high-risk work only — see H22>
```

In a Compact handoff this collapses to one line: `Reviewer, verify: <one to three concrete checks>`.

## H22 Adversarial review suggestions

For substantial or high-risk work, name where your own implementation is most likely to be wrong. This hands the reviewer attack surfaces, not conclusions, and it never replaces the reviewer's own reasoning. Common weak points:

- boundary conditions and numeric or coordinate edges;
- state transitions;
- first launch against second launch;
- migration of old data;
- asynchronous cancellation, ordering and races;
- repeated operations;
- package or signing identity;
- stale caches or stale build artifacts;
- time zones and clocks;
- permissions and missing-resource paths.

Name the specific ones that apply to this change, and why each is a risk here, rather than pasting the whole list.

## Corrective re-review

For a corrective patch (H25 in `specialized-handoff-modes.md`), center the challenge list on the finding:

1. Reproduce the original failure at the old commit.
2. Confirm it no longer reproduces at the new commit.
3. Check that the regression test fails without the correction and passes with it.
4. Probe the adjacent regression surfaces.
5. Decide whether the finding closes or reopens.

State the finding as `CORRECTED / DELIVERED FOR RE-REVIEW`. The decision in step 5 is the reviewer's, not the builder's.

## When no independent review is authorized

If the owner has explicitly decided that no independent review will happen, do not invent a reviewer, a review or a verdict. Use the status `DELIVERED — NO INDEPENDENT REVIEW AUTHORIZED`, record the owner's decision, and replace the challenge list with:

```
UNVERIFIED BY ANYONE BUT THE BUILDER: <claims resting only on builder verification>
OWNER, CONSIDER CHECKING: <the highest-risk items, if the owner wants to look>
NEXT GATE: WAITING FOR HUMAN ACCEPTANCE — NO INDEPENDENT REVIEW AUTHORIZED
```

Write the owner checks as things the owner can do or observe — open this screen, try this input — unless the owner reviews code. In a Compact handoff the block collapses to one line: `Unverified by anyone but the builder: …`.

If a project rule requires a review or sign-off that the owner's decision skips, name that conflict as an OWNER DECISION rather than resolving it. Builder verification stays builder verification, and the handoff never describes it as review.

## Before sending

- Could a reviewer in a new session begin from this section and the repository alone?
- Does any line tell the reviewer what to conclude?
- Is any failed, flaky or contrary result missing?
- Is anything described as independently verified that only the builder checked?

Fix any answer that is wrong before the handoff goes out.
