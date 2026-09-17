# Specialized handoff modes

A mode changes what the handoff emphasizes; the size — Compact, Standard or Full — still follows risk. Combine modes when the work spans them: a corrective patch that migrates stored data uses Corrective and Migration / persistence together. Every mode keeps the core contract in `core-handoff-contract.md` and the evidence rules in `verification-and-evidence.md`.

| Mode | Applies when the work |
|---|---|
| Corrective | fixes a finding raised by an independent reviewer |
| Refactor | changes structure while behavior must not change |
| Migration / persistence | changes how or where data is stored, or transforms stored data |
| Release / signing | builds, signs, packages or installs a release |
| Device | validates on, or for, physical hardware |
| Documentation | changes documentation only |
| Architecture | has the purpose of introducing or changing boundaries, ownership or dependency direction, or of recording current and target architecture |

## Contents

- H25 Corrective patch
- H26 Corrective finding status
- H27 Refactor
- H28 Migration and persistence
- H29 Release and signing
- H30 Device validation
- H31 Documentation
- H32 Architecture

## H25 Corrective patch

Use when fixing a finding raised by an independent reviewer. Center the handoff on that finding. Do not re-report the whole project unless the correction affects it broadly. Size it by the patch's own risk and blast radius; a finding the reviewer rated at the project's highest severity levels is never handed off as Compact.

```
CORRECTIVE HANDOFF — <finding ID> — <Standard | Full>
STATUS: DELIVERED FOR INDEPENDENT REVIEW
FINDING: <ID exactly as the reviewer wrote it, with its title and the reviewer's severity>
PREVIOUS REVIEWER VERDICT: <quoted, or where it is recorded> | only the finding title is available
REQUIRED CORRECTION: <what the reviewer said must become true> | inferred from the finding: <…> (builder interpretation, to be confirmed)
ROOT CAUSE: <why the defect happened, not only where>
CORRECTION IMPLEMENTED: <file · function · before → after>
REGRESSION TEST: <file · test name · contract checked>
OLD IMPLEMENTATION: <the test fails — how that was shown> | NOT CHECKED — <concrete reason>
NEW IMPLEMENTATION: <the test passes — suite and exact counts> | NOT RUN — <why>
ADJACENT REGRESSION SURFACES: <surfaces checked → result> | NOT CHECKED — <which, and why>
FILES CHANGED · CONTRACT / DATA · BEHAVIOR (BEFORE / AFTER): <as in the Standard order>
DEVIATION FROM THE REQUIRED CORRECTION: NONE | <what differs, and who agreed>
COMMIT: <hash and message> | uncommitted — <why> | COMMIT: PENDING OWNER APPROVAL | COMMIT: NONE — NOT PERMITTED
SIDE EFFECTS · LIMITATIONS · OTHER FINDINGS: <as in the Standard order>
FINDING STATUS: <ID> — CORRECTED / DELIVERED FOR RE-REVIEW
CODEX RE-REVIEW HANDOFF: <for the reviewer to determine: whether the failure reproduces at the old commit and not at the new one; whether the regression test fails without the correction and passes with it; the adjacent surfaces; whether the finding closes or reopens>
NEXT GATE: DELIVERED FOR CODEX REVIEW (re-review of <ID>)
```

- Fix the root cause the finding names, and run its regression test before handing off; `NOT RUN` is only for a test that genuinely could not run. Showing the test fail on the old implementation is expected for a corrective patch; `NOT CHECKED` needs a concrete reason. Record other problems noticed on the way as builder-discovered items instead of widening the patch.
- Never paraphrase a verdict you do not have: quote it, point to where it is recorded, or say that only the finding title is available. When the finding states only a symptom, write the correction you inferred and mark it as your interpretation, never as the reviewer's words.
- Implement the required correction. If you believe it is wrong, record the disagreement with evidence in the handoff; a materially different correction needs the reviewer's or owner's agreement first.

## H26 Corrective finding status

The builder never declares a reviewer finding closed.

- Write `M01 — CORRECTED / DELIVERED FOR RE-REVIEW`, not `M01 CLOSED`.
- A finding only partly addressed: `M03 — PARTIALLY CORRECTED / REMAINS OPEN`, with what remains.
- Closure belongs to the independent reviewer or the accepted project process. If the owner or the project's process explicitly assigns closure differently, follow that rule and cite it.

## H27 Refactor

For zero-behavior-change restructuring, emphasize:

- the before baseline: commit, test counts, and any outputs captured before anything moved;
- moved or renamed modules, as old path → new path — for many moves, a summary plus the command that lists every one;
- import and reference changes;
- public contract preservation: exports, APIs, command-line interfaces, file formats and persisted keys unchanged;
- output equivalence: the same tests with the same counts — and the same test names where practical — output or snapshot comparison, hashes where outputs are files;
- the Git diff: renames detected as renames, and no logic edits hidden inside moves;
- the tests run;
- `INTENDED BEHAVIOR CHANGE: NONE`.

The reviewer must be able to challenge whether behavior really stayed invariant. Point them at the riskiest moves: dynamic or string-built imports, reflection, build or packaging globs, configuration paths, test discovery patterns.

A pure move is Refactor. Add the Architecture labels (H32) only when the restructuring also changes boundaries, ownership or dependency direction — for example when tooling such as lint boundaries or code-owner rules keys off the new folders, or a module gains a dependency it did not have.

## H28 Migration and persistence

For persistence or migration work, include:

- the old data shape and the new data shape: schema, keys, units, versions;
- the migration trigger: when it runs and what starts it;
- idempotence: what a second launch or run actually did (H58);
- recovery behavior: what happens when the migration fails partway;
- malformed-data behavior;
- first-launch and second-launch results;
- data-preservation proof (H45): counts, hashes, field-level comparison, on synthetic data, a copy of real data, or the real data — say which;
- compatibility: whether the new version reads old data, and whether an older version can still read the new data;
- rollback and recovery evidence: the backup's location, and whether a restore was actually tested (H57).

For a new, empty store, say so and skip the items about existing data. Never summarize migration work as "Data preserved." Show how that was proven.

## H29 Release and signing

For release or signing work, include:

- the package or application identifier;
- the version and build number;
- the signing identity, with a certificate fingerprint where it is safe to share;
- key externality: the key lives outside the repository — say where by description, never the key or its password;
- failure behavior: what the build does when the key or identity is missing, and whether that was tested;
- the build artifact hash;
- the installed artifact comparison: the hash or signature of what is actually installed against the built artifact;
- whether an installation actually occurred, where, and who performed it;
- whether user data survived the update, and how that was proven.

Never expose private signing material, keystore passwords or tokens.

## H30 Device validation

Distinguish **SIMULATED** — an emulator, a simulator, a headless browser, synthetic touch or input events — from **PHYSICAL DEVICE**. Simulated touch is never presented as physical-device proof.

Report:

- device and model;
- operating system version or API level where relevant;
- the installed version, who installed it, and how the version was confirmed;
- test conditions;
- measured behavior;
- user-observed behavior, attributed to who observed it (H64);
- logs, redacted of private data;
- data preservation;
- limitations.

When the target device was not available, write `NOT VERIFIED — no access to <the device>`, and list device validation among the checks that remain. Whether that blocks acceptance is the reviewer's or owner's call, not the builder's.

## H31 Documentation

For documentation-only work, emphasize:

- the documents created or changed;
- source claims fact-checked against code, configuration or commands — list what was checked, and mark claims that were not;
- links and anchors, and how they were verified;
- current architecture kept distinct from future architecture;
- contradictions found between documents, or between documents and code;
- a private-data scan for secrets, personal paths and private records;
- zero product-source changes, shown by the diff;
- product verification appropriate to the task, or a statement that none was needed and why.

Never claim documentation is correct because the Markdown renders.

## H32 Architecture

Label every architectural statement:

| Label | Meaning |
|---|---|
| CURRENT | exists in the repository now |
| TARGET | the intended end state |
| IMPLEMENTED | delivered by this work |
| NOT IMPLEMENTED | still outstanding |
| APPROVED | decided by the owner — say by whom and where it is recorded |
| PROPOSED | awaiting a decision |

- Never describe a proposed or target architecture as current reality.
- Record owner decisions separately from engineering recommendations: a recommendation is not a decision.
