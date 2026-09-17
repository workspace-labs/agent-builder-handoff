# Core handoff contract

What a Standard, Full, Corrective or Blocked handoff states about the work itself: where it started, what was authorized, what changed, what did not, the repository state, deviations, limitations, findings and side effects. Rules for tests, results and evidence are in `verification-and-evidence.md`; the reviewer challenge list is in `codex-review-handoff.md`; mode emphasis is in `specialized-handoff-modes.md` — all in this folder.

## Contents

- Standard and Full template
- H3 Baseline first
- H4 Authorized objective and owner decisions
- H5 Implementation summary
- H6 Files changed and H61 generated artifacts
- H7 Contract and data changes
- H8 Protected areas
- H9 Behavior change
- H15 Git and commit
- H16 Deviations and H56 scope expansion
- H17 Known limitations
- H18 Open risks and findings, H54 finding IDs, H55 severity
- H19 Newly discovered problems
- H57 Rollback and recovery
- H59 External side effects and H60 deletions
- H62 Dependencies
- H23 Next gate
- H33 Adapting the report
- H51–H53 Writing for a reviewer who was not there

## Standard and Full template

Adapt the headings to the task and to any project-required format (H33); do not drop the information. Items marked (conditional) are included whenever their condition holds, at any size. Because Full work is high-risk, a Full handoff always addresses failure testing, adversarial notes and rollback.

```
ENGINEERING HANDOFF — <Standard | Full> — <emphasis mode, if any>
STATUS: DELIVERED FOR INDEPENDENT REVIEW | DELIVERED — NO INDEPENDENT REVIEW AUTHORIZED | BLOCKED — OWNER DECISION REQUIRED

BASELINE           project · version · branch · starting commit · working tree · prior accepted state or finding · test baseline
SCOPE              AUTHORIZED: …  /  NOT AUTHORIZED: …
OWNER DECISIONS    decisions that shaped the work (where recorded) · decisions still required
IMPLEMENTATION     per change: file · function or component · before → after · contract · why correct · invariants preserved · architectural impact
FILES CHANGED      path · class · reason; authored and generated listed separately
CONTRACT / DATA    NONE, or the precise change
PROTECTED AREAS    relevant surfaces intentionally not changed
BEHAVIOR CHANGE    INTENDED: …  /  UNINTENDED: NONE FOUND (within the checked surfaces)
TESTS              added or changed · contract · regression prevented · failure on the old implementation, or NOT CHECKED
VERIFICATION       VERIFIED: … with exact counts  /  NOT VERIFIED or NOT RUN: … with the reason
REGRESSION REVIEW  surfaces checked → NO REGRESSION FOUND WITHIN THE CHECKED SURFACES
KEY CLAIMS         claim → evidence → where the evidence is (durable or temporary)
GIT                starting → resulting commit · parent · message · branch · clean or dirty · pushed or not
DEVIATIONS         NONE, or each deviation
SIDE EFFECTS       EXTERNAL SIDE EFFECTS: NONE, or what happened · deletions · dependency changes
LIMITATIONS        what the delivery and its verification do not cover
RISKS              what could still go wrong, in the builder's view, including known weaknesses left in place and the smallest correction for each
FINDINGS           ID — status, for every relevant finding · builder-discovered problems
CODEX HANDOFF      the challenge list (codex-review-handoff.md)
FAILURE TESTING    (conditional: high-risk work) failure paths exercised and results
REPRODUCTION       (conditional: defects, migrations, device tests, important behavior changes) steps · EXPECTED / OBSERVED / RESULT
ADVERSARIAL NOTES  (conditional: substantial or high-risk work) where this implementation is most likely weak
ENVIRONMENT        (conditional: environment-sensitive work) only the conditions that matter
ROLLBACK           (conditional: risky changes) the recovery point that actually exists, or ROLLBACK POINT: NONE ESTABLISHED
REPEATED RUNS      (conditional: repeatable operations) what the second run did
PERFORMANCE        (conditional: performance at stake) measurements, or PERFORMANCE: NOT MEASURED
NEXT GATE          the gate, and the sequence when another gate comes before or after — then STOP
```

## H3 Baseline first

Identify where the work started, so the reviewer can diff exactly what changed. Include what applies:

- project, version, branch and starting commit;
- the accepted prior state or the finding this work answers;
- working-tree state before you began, naming any pre-existing uncommitted changes that are not yours so they are not attributed to this task;
- platform or device baseline, and persistence or data baseline;
- the previous test baseline, for example "unit 410/410 before the change".

Take the baseline from the repository or a recorded state, never from memory. Never invent one. If it was not established before the work began, write `BASELINE NOT ESTABLISHED` and explain what that limits, for example "pre-existing uncommitted changes cannot be separated from this task's changes; review the whole diff against 4f2c9e1". Record the baseline before the first edit when you can. A test baseline not captured then is established by running the tests at the exact base commit afterwards — for example in a separate worktree — and saying that is how it was obtained. When the commit is known but no test baseline exists either way, write `TEST BASELINE: NOT ESTABLISHED`.

## H4 Authorized objective and owner decisions

State exactly what was authorized, in the owner's terms, and separately what was not.

```
AUTHORIZED: correct the due-date defect behind finding A13 and add regression coverage.
NOT AUTHORIZED: invoice layout changes, payment reminders, database schema work.
```

This protects against scope creep. Unrelated cleanup is not part of the task: do not claim it as such, and if it happened anyway, report it as a deviation (H16).

Record the owner decisions that shaped the work — "no commits", "no independent review", an approved scope expansion — with where each was given, and list the decisions still required. A decision is only recorded here; the builder never makes it.

## H5 Implementation summary

Explain each meaningful change technically enough for a reviewer to check it:

- affected module or file, and function or component;
- previous behavior and new behavior;
- affected contract;
- why the change is correct — for a defect, the root cause and why it produced the symptom;
- important invariants preserved;
- where software-engineering-build-standard applies, the architectural impact: placement, dependency direction, state ownership.

Be specific. Not "Improved the logic." but "saveInvoice now stores the due date from the canonical ISO value instead of re-parsing the text shown in the user's display format."

## H6 Files changed and H61 generated artifacts

- List every intentionally changed tracked file, plus new untracked files, taken from the actual diff against the baseline rather than recollection.
- Classify where useful: production source, tests, documentation, configuration, tooling, generated, migration, platform. For substantial tasks, give a one-line reason per file.
- Separate AUTHORED CHANGE from GENERATED CHANGE (H61). Lockfiles, build output, snapshots and compiled assets that changed because of a normal build or tool are named with the command that produced them. Never imply generated output was hand-engineered.
- List unexpected changed files — formatter spill, tool side effects, accidental edits — and say whether each was reverted or kept, and why. Never hide them.

## H7 Contract and data changes

State explicitly whether the task changed any of: persisted data, storage keys, schema, units, identifiers, API contract, state model, migration behavior, package or application identity, signing identity, file format.

- None: `CONTRACT / DATA CHANGE: NONE`.
- Otherwise describe it precisely: old → new, who reads and writes it, compatibility with existing data and clients, and whether a migration exists. A persistence or migration change also uses the Migration / persistence mode.

## H8 Protected areas

For scoped work, name the relevant surfaces that were intentionally not changed — the ones a reviewer could reasonably suspect were touched. Examples: persistence, migration, signing, package identifier, lifecycle, an adjacent feature, saved-data compatibility, another target platform.

List only relevant surfaces, never the whole repository. Where it is cheap, back the claim: "no file under src/storage/ appears in the diff".

## H9 Behavior change

Always state two things separately:

```
INTENDED BEHAVIOR CHANGE: …
UNINTENDED BEHAVIOR CHANGE: NONE FOUND
```

- A pure refactor: `INTENDED BEHAVIOR CHANGE: NONE`.
- A bug fix: INTENDED is the exact corrected behavior. Never write `BEHAVIOR CHANGE: NONE` for a fix that deliberately changes broken behavior.
- UNINTENDED is bounded by what was checked (H13): "NONE FOUND", never "no side effects". When nothing was checked, it is `NOT VERIFIED`.
- When correcting existing behavior, add BEFORE / AFTER (H42 in `verification-and-evidence.md`).

## H15 Git and commit

Where Git is part of the project's workflow, report: starting commit, resulting commit, parent, commit message, branch, tracked-file state, clean or dirty status, and whether anything was pushed, and where.

- Never claim a commit exists that does not. Whether committing needs approval follows the project's and owner's rules. When it needs approval that has not been given: `COMMIT: PENDING OWNER APPROVAL`. When the owner or project forbids commits: `COMMIT: NONE — NOT PERMITTED`. The handoff is never a reason to create a commit the owner or project has not permitted.
- This skill reports the commit state; it does not decide whether to commit.
- Without a commit, identify the delivered state another way: branch, base commit, the changed-file list, and — for a Standard or Full handoff, or whenever the working tree may change before review — a hash of the complete diff against the base, including new files, computed before sending, with the command used so the reviewer can recompute it. Say that an uncommitted working tree can change after delivery, so the reviewer can check the hash first.
- An uncommitted state can only be reviewed where it exists: say where it lives (which checkout, described), or attach a patch where the project allows.
- Where the project does not use Git, say so and identify the delivered state by the changed-file list and file hashes.
- Pushing publishes: say "not pushed", or exactly what was pushed where, and treat it as an external side effect (H59).

## H16 Deviations and H56 scope expansion

Report every deviation from the approved task or plan, even when the final result looks right: an additional file had to change, a planned test could not run, the environment differed, a tool failed, a requirement was ambiguous and an interpretation was chosen, an evidence path changed, or code that an out-of-scope finding names — or code close enough to affect it — was touched. None: `DEVIATIONS: NONE`.

When the work needed more than the original scope, do not normalize it silently:

```
ORIGINAL SCOPE: …
ADDITIONAL REQUIRED WORK: …
WHY: …
OWNER APPROVAL: obtained | not obtained | not required under <the project rule>
```

A scope expansion is material when it changes behavior, data, a contract or a public interface beyond the authorized objective, or noticeably enlarges what must be reviewed. A material expansion without approval is a stop condition: stop when it appears and send a Blocked handoff naming the decision, rather than burying it in a delivered one. When the authorization itself was vague, the interpretation you chose is a deviation, not a stop — unless the work clearly goes beyond any reasonable reading of it.

## H17 Known limitations

State the limits of the delivered implementation and of its verification: not validated on a physical device, a fresh-checkout build not proven, performance not measured, a compatibility path not exercised.

A limitation is not a finding. Only an actual defect becomes a finding (H18, H19).

## H18 Open risks and findings

List every relevant unresolved finding with its existing ID and an explicit status. Never close a finding silently.

Statuses: `OPEN` · `DELIVERED` · `PENDING REVIEW` · `CLOSED` · `DEFERRED`, plus the corrective statuses `CORRECTED / DELIVERED FOR RE-REVIEW` and `PARTIALLY CORRECTED / REMAINS OPEN`. `CLOSED` only ever records a closure the reviewer or accepted process already made (H26).

```
A13 — CORRECTED / DELIVERED FOR RE-REVIEW
M02 — NOT IN SCOPE / REMAINS CLOSED
PERF-12 — NOT IN SCOPE / REMAINS OPEN — code it names changed in src/invoices/reminders.js (see DEVIATIONS)
OFF-SITE BACKUP COPY — PENDING OWNER ACTION
```

### H54 Finding IDs

Preserve existing IDs exactly — A13, M01, D-C01, MA1 — never renamed, renumbered or reformatted for style. For a genuinely new issue, follow the project's ID convention if it has one; otherwise use a neutral temporary ID such as NEW-1, marked `BUILDER-DISCOVERED / PENDING REVIEW`.

### H55 Severity discipline

Severity is evidence-based: never inflated to justify extra work, never softened to ease acceptance. Use the project's severity system where one exists. Where severity belongs to the independent reviewer, label yours `SEVERITY: BUILDER ESTIMATE`.

Never silently overwrite a severity the reviewer already assigned. If you disagree, record it once, in the findings, beside the reviewer's severity — `SEVERITY: BUILDER ESTIMATE — <level> — <evidence>` — and keep it out of the challenge list. The reviewer's severity stands, and it is the severity used when sizing the handoff.

## H19 Newly discovered problems

When an unrelated defect turns up during the work, do not silently fix it — unless the authorized task cannot be completed correctly without that fix, in which case it is a deviation with its reason (H16). Record it:

```
ID: NEW-1 — BUILDER-DISCOVERED / PENDING REVIEW
OBSERVED: …
EVIDENCE: … | NOT CAPTURED — noticed only
SEVERITY: BUILDER ESTIMATE — …
OUTSIDE SCOPE BECAUSE: …
```

Continue only if it is safe to. If the new defect blocks the authorized task or puts data at risk, stop and send a Blocked handoff instead of working around it.

## H57 Rollback and recovery

For risky changes, name the rollback or recovery point that actually exists — previous commit, archived application build, database backup, device snapshot, recovery copy — with its location and how it was confirmed (for example a backup hash, and whether a restore was actually tested).

Never promise a rollback that was not established. When there is none: `ROLLBACK POINT: NONE ESTABLISHED`.

## H59 External side effects and H60 deletions

Report effects outside the working tree: device installation (and who installed), network upload, remote push, requests made to an external service with a credential, file deletion, keychain or credential-store change, database mutation, external service change. None: `EXTERNAL SIDE EFFECTS: NONE`. Otherwise state exactly what happened, where, and whether it can be undone.

For anything deleted (H60), state what, why, whether it was source, generated output, evidence or data, whether a recovery copy exists and where, and whether the deletion was authorized and by whom. Never fold a deletion into the word "cleanup".

## H62 Dependencies

If dependencies changed, report the package, old and new versions, the reason, the lockfile effect, and any known security or build implications. When dependencies matter to the task and nothing changed: `DEPENDENCIES: UNCHANGED`.

## H23 Next gate

Every substantial handoff states what happens next and whose move it is:

- `DELIVERED FOR CODEX REVIEW`
- `WAITING FOR HUMAN ACCEPTANCE`
- `BLOCKED — OWNER DECISION REQUIRED` — name the decision
- or the project's own gate name: an accepted ADR's acceptance step, a board or pipeline stage

When one gate must come before or after another, name the sequence: "BLOCKED — OWNER DECISION REQUIRED (is X in scope?), then Codex review", or "DELIVERED FOR CODEX REVIEW, then release-manager sign-off on staging per the ADR". A missing precondition the builder is not authorized to satisfy is named, never quietly skipped.

Then stop (H24). Never proceed into the next major task without explicit authorization.

## H33 Adapting the report

Section names and order may adapt to the task and to a project's required format, and a handoff need not print every section every time. What may not disappear is the information an independent reviewer needs. The goal is reviewability, not formatting compliance: skip empty ceremony, but keep explicit `NONE` lines where an absence is a fact the reviewer relies on — contract or data change, deviations, unintended behavior change, external side effects.

## H51–H53 Writing for a reviewer who was not there

- **A new session (H51).** No "as we discussed earlier", "the thing from before", "same as yesterday" or "you know the issue". Use exact finding IDs, commit hashes, paths, versions, contracts and reproduction steps. Self-contained does not mean retelling the project's history: point to it.
- **A different agent (H52).** Do not assume the reviewer shares Claude's memory, reasoning or vocabulary. Use the repository's own terms, not nicknames coined during the session. Ground decisions in code, tests, docs, ADRs, the plan and the evidence — not in private conversation context.
- **No hidden chain-of-thought (H53).** Give conclusions, evidence and concise engineering rationale sufficient for independent verification. Do not expose, or depend on, private chain-of-thought.
