---
name: engineering-builder-handoff
description: "Use before sending the final message of any software engineering work Claude did as the builder, even when nobody asked for a report: a feature, bug fix, corrective patch for a reviewer finding, refactor, repository restructuring, architecture, persistence, migration, database, data-model or state change, build, release, signing or platform work, test or tooling change, documentation foundation, technical-debt or security-sensitive change. Also use when the user says finish and give me the report, prepare this for Codex, handoff to Codex, engineering report, review handoff, or stop after the report. Turns a bare done, fixed or tests pass into an evidence-based Compact, Standard, Full or Corrective handoff an independent reviewer such as Codex can verify in a new session, separating facts, evidence, builder claims, unverified items, limitations, open findings and owner decisions, then stopping at the next gate. Complements software-engineering-build-standard; not for reviewing another agent's work."
---

# Engineering Builder Handoff

When Claude, as the builder, finishes meaningful software engineering work, it does not end with "Done.", "Fixed.", "Tests pass." or "Everything looks good." It ends with an evidence-based handoff that an independent reviewer can verify in a new session without having watched the work. The reviewer is the one the project names — Codex when none is named.

**Golden principle (H66):** the handoff is not written to prove Claude was right. It is written so another engineer can efficiently determine whether Claude was right.

Anchors H1–H66 follow this standard's source specification, which has no H38. The `H` prefix keeps them distinct from the `§` anchors of software-engineering-build-standard. They are for citing this standard; handoffs do not need to include them.

## Role boundary

This skill is for Claude as builder or implementer, never as reviewer.

- The handoff is a claim package for review, not the acceptance decision (H34). Its status is one of:
  - `DELIVERED FOR INDEPENDENT REVIEW` — the normal status;
  - `DELIVERED — NO INDEPENDENT REVIEW AUTHORIZED` — only when the owner has decided there will be no independent review;
  - `BLOCKED — OWNER DECISION REQUIRED` — the work cannot go further until the owner decides;
  - `ACCEPTED` — only when recording an acceptance the authorized reviewer or owner already gave, naming who gave it. An owner's remark such as "looks right" is an observation, not an acceptance, unless the owner says it is.
- Call your own checks builder verification. Never say "independently verified" unless a genuinely independent agent performed that verification, and then attribute it by name. Claude's implementation, tests, probes and confidence are never proof of independent acceptance.
- Never declare a reviewer's finding closed: write "M01 — CORRECTED / DELIVERED FOR RE-REVIEW", not "M01 CLOSED". Closure belongs to the independent reviewer or the accepted project process, unless that process explicitly assigns it differently (H26).
- Do not use this skill to review another agent's delivery.

The flow is **BUILD → VERIFY → EVIDENCE → HANDOFF → STOP → INDEPENDENT REVIEW → HUMAN GATE**. Verification comes before the handoff. Run the checks the change calls for that you are authorized to run: the tests that cover it, any regression test you added, and quick checks you can close yourself — such as searching for other uses of what you changed — instead of passing them to the reviewer. A check that needs authorization or access you do not have, or that belongs to a later gate (a staging deploy, a physical device), is reported as `NOT RUN` with the reason. Never skip a check to reach the handoff sooner. The builder's job ends at the handoff unless the owner explicitly authorizes further work.

## Precedence

- **Security and privacy requirements always hold** (H37).
- **Then explicit owner decisions for the task, then repository instructions (such as AGENTS.md, CLAUDE.md, contributing or delivery guides) and accepted ADRs, then this skill.** When two of those disagree — for example an owner's "no review" against an ADR's required sign-off — follow the more specific instruction and name the conflict as an OWNER DECISION instead of resolving it silently.
- **A required delivery format governs the shape.** Write in it, carrying this skill's substance as labeled lines under its nearest headings, rather than as a separate report competing with it. A looser format does not by itself remove the substance; an explicit project or owner limit does — then say what was left out and where the detail can be found. When the handoff goes into a tool or a file, the final message says where it went, with its status and next gate.
- **software-engineering-build-standard** governs how the work is engineered and when it is done; this skill governs how the finished work is reported. When both activate, both apply, in one handoff that uses this skill's layout. That standard's handoff list is content to include, not a separate format: the objective goes in the scope, architectural impact in the implementation summary, unresolved risks in the risks. Fix concerning self-check answers that fall within the authorized scope before handing off; report what remains as a risk, with the smallest correction that would remove it. If its completion criteria are not met, finish the work or send a Blocked handoff naming them; acceptance steps that belong to later gates, such as a staging sign-off, are named as gates instead. Do not restate the build standard itself.
- **Finding IDs, severities and acceptance gates belong to the project and its reviewer.** Preserve them exactly, record any disagreement only beside them in the findings (H54, H55), and name the project's own gates (H23).
- **No independent review authorized?** Do not invent a reviewer or a review: use the no-review status, list what nobody but the builder has checked, and name the owner's gate.

## When it applies (H1)

Use it when Claude has completed meaningful engineering work: a feature, bug fix, corrective patch, refactor, repository restructuring, architecture slice, persistence, migration or database work, build or release work, signing, platform integration, test-architecture or tooling change, documentation foundation, technical-debt remediation, security-sensitive change, or data-model or state-model change. Use it also when the owner says "finish and give me the report", "prepare this for Codex", "handoff to Codex", "give me the engineering report", "prepare the review handoff" or "stop after the report".

It does not apply to mid-task progress notes, plans written before building, answers to questions, or reviews of someone else's work. One task gets one handoff at the end — or a Blocked handoff earlier, when the work has to stop.

## 1. Choose the size (H2) and the mode

| Size | Use for | Shape |
|---|---|---|
| **Compact** | tiny isolated bug, small UI correction, small configuration fix, narrow documentation correction | a few short lines; template below |
| **Standard** | normal feature, meaningful bug fix, moderate refactor, tooling change, repository change | the Standard order below |
| **Full** | architecture, persistence or migration, data-model change, signing or release identity, platform change, repository restructuring, high-risk corrective patch, sensitive data operation, major feature foundation | the Standard order, the sections Full always addresses, and the matching mode |

Size follows risk and blast radius, not diff size: a one-line change to persisted data is Full. When two sizes fit, use the larger. Never omit critical information to keep a handoff short, and never print ceremonial sections with nothing useful in them — but keep explicit `NONE` lines where an absence is itself a fact the reviewer relies on.

**Corrective** is the fourth handoff mode. Use it whenever the task is fixing a finding raised by an independent reviewer (H25): it centers the handoff on that finding instead of re-reporting the project. Its size follows the patch's own risk and blast radius, and a finding the reviewer rated at the project's highest severity levels is never handed off as Compact.

**Emphasis modes** overlay any size when the work is of that kind:

- **Refactor** (H27) — the structure changes and behavior must not;
- **Migration / persistence** (H28) — changes how or where data is stored, or transforms stored data;
- **Release / signing** (H29) — builds, signs, packages or installs a release;
- **Device** (H30) — validates on, or for, physical hardware;
- **Documentation** (H31) — documentation-only work;
- **Architecture** (H32) — work whose purpose includes introducing or changing boundaries, ownership or dependency direction, or recording current and target architecture. A pure file move is Refactor; an incidental dependency change is reported as architectural impact.

## 2. Gather facts before writing

Take every fact from the repository and from runs that actually happened, never from memory or assumption:

- **Baseline** (H3): project, version, branch, starting commit, prior accepted state or finding, working-tree state, test baseline. Record it before the first edit when you can. A test baseline not captured then is established by running the tests at the exact base commit afterwards — for example in a separate worktree — and saying so. Never invent one: write `BASELINE NOT ESTABLISHED` or `TEST BASELINE: NOT ESTABLISHED` and explain the limitation.
- **Scope and owner decisions** (H4): what was AUTHORIZED, what was NOT AUTHORIZED, and the owner decisions that shaped the work.
- **Changed files** from the real diff against the baseline, including generated and unexpected files (H6, H61).
- **Verification that actually ran**, with exact counts, and every relevant check that did not (H11, H48).
- **Evidence locations** (H14) and **finding IDs** exactly as given (H54).

## 3. Stop when the work has to stop

Stop when the problem appears, not at the end (H19, H24, H56):

- the work needs a **material scope expansion** the owner has not approved. Material means it changes behavior, data, a contract or a public interface beyond the authorized objective, or noticeably enlarges what must be reviewed. When the authorization itself was vague, record the interpretation you chose as a deviation instead, and stop only when the work clearly goes beyond any reasonable reading of it;
- a **newly discovered defect blocks** the authorized task;
- **secrets or private data have reached, or are likely to reach, a system or person they clearly should not.** Handling sensitive data, or sending it where authorization is merely unclear, is not a stop: report it as the privacy section says.

Then send a Blocked handoff. It is self-contained and needs no reference file.

```
HANDOFF (Blocked) — BLOCKED — OWNER DECISION REQUIRED
Authorized: <the task> · Baseline: <branch @ commit, working-tree state>
Done so far: <changes, with their commit or uncommitted state — left as they are>
Blocker: <what stopped the work, with the evidence already in hand>
Not done / not verified: <what remains>
Decision needed: <the question, the options where known, and your recommendation marked as one>
Next gate: BLOCKED — OWNER DECISION REQUIRED, then <what follows the decision>
```

A quick read-only check to confirm the blocker is fine; continuing to build is not. Leave partial work as it is — reverting or discarding it is the owner's call. For a scope blocker, the Blocker line carries the original scope, the additional work required and why. If the problem only becomes clear while writing a finished handoff, send the full handoff with the Blocked status and the decision named.

## 4. Label what you write

| Label | Meaning |
|---|---|
| FACT | observable state: a commit hash, a file list, a version |
| EVIDENCE | an observable result supporting a claim, with its location: test output, hash, log, screenshot, measurement |
| BUILDER CLAIM | what Claude believes the work achieved; it needs evidence and is never evidence for itself (H40) |
| UNVERIFIED ITEM | relevant but not checked: `NOT RUN`, `NOT VERIFIED`, `NOT MEASURED` |
| KNOWN LIMITATION | a boundary of the delivery or of its verification, not a defect (H17) |
| OPEN FINDING | an unresolved issue with its ID and an explicit status (H18) |
| OWNER DECISION | something only the owner decides; recorded, never decided by the builder |

Not every line needs a prefix. What matters is that a reviewer can never mistake a claim for evidence, a builder check for an independent one, or a proposal for a decision.

Use these phrases exactly where they apply:

- **Status:** `DELIVERED FOR INDEPENDENT REVIEW` · `DELIVERED — NO INDEPENDENT REVIEW AUTHORIZED` · `BLOCKED — OWNER DECISION REQUIRED` · `ACCEPTED` (recording only)
- **Next gate:** `DELIVERED FOR CODEX REVIEW` · `WAITING FOR HUMAN ACCEPTANCE` · `BLOCKED — OWNER DECISION REQUIRED`
- **Verification:** `NOT RUN` · `NOT VERIFIED` · `NOT MEASURED` · `NOT CHECKED` · `NOT CAPTURED` · `NO REGRESSION FOUND WITHIN THE CHECKED SURFACES` · `PERFORMANCE: NOT MEASURED`
- **Baseline and state:** `BASELINE NOT ESTABLISHED` · `TEST BASELINE: NOT ESTABLISHED` · `COMMIT: PENDING OWNER APPROVAL` · `COMMIT: NONE — NOT PERMITTED` · `ROLLBACK POINT: NONE ESTABLISHED`
- **Change:** `NONE` · `CONTRACT / DATA CHANGE: NONE` · `INTENDED BEHAVIOR CHANGE: NONE` · `UNINTENDED BEHAVIOR CHANGE: NONE FOUND` · `DEVIATIONS: NONE` · `EXTERNAL SIDE EFFECTS: NONE` · `DEPENDENCIES: UNCHANGED`
- **Findings:** `CORRECTED / DELIVERED FOR RE-REVIEW` · `PARTIALLY CORRECTED / REMAINS OPEN` · `BUILDER-DISCOVERED / PENDING REVIEW` · `SEVERITY: BUILDER ESTIMATE`

## 5. Write the handoff

Deliver it where the project expects handoffs: the final message by default, or the project's delivery note, file or tool.

### Compact

A Compact handoff is self-contained and needs no reference file unless an emphasis mode applies.

- Each line may be a short phrase. Drop "Not authorized" when nothing nearby was at stake.
- Add a line only when there is something to report: a deviation, an external side effect or deletion, private-data handling, a builder-discovered problem.
- An uncommitted change is identified by its base commit and changed file; add a diff hash only when the working tree may change before review. This skill reports the commit state; it does not decide whether to commit.
- With no independent review authorized, replace "Reviewer, verify" with "Unverified by anyone but the builder: …".

```
HANDOFF (Compact) — DELIVERED FOR INDEPENDENT REVIEW
Authorized: <the task> · Not authorized: <nearby work deliberately left alone>
Baseline: <branch @ commit, working-tree state> | BASELINE NOT ESTABLISHED — <why>
Change: <file · element>: <before> → <after>. CONTRACT / DATA CHANGE: NONE
Behavior: intended <the change> · unintended NONE FOUND in <what was checked> | NOT VERIFIED
Builder verification: <what ran, its result, where any evidence is> | NOT RUN — <why> · NOT VERIFIED: <gaps>
Commit: <hash, message, pushed or not> | uncommitted — <why> | PENDING OWNER APPROVAL | NONE — NOT PERMITTED
Reviewer, verify: <one to three concrete, independent checks>
Next gate: <for example DELIVERED FOR CODEX REVIEW>
```

Example — one padding value changed. Its details are illustrative: report only the checks that actually ran.

```
HANDOFF (Compact) — DELIVERED FOR INDEPENDENT REVIEW
Authorized: add space below the Save button on the settings screen · Not authorized: any other layout change
Baseline: main @ 4f2c9e1, clean working tree
Change: src/settings/settings.css `.save-row`: padding-bottom 8px → 16px. CONTRACT / DATA CHANGE: NONE
Behavior: intended 8px more space below Save · unintended NONE FOUND on the settings screen
Builder verification: lint passed; searched for .save-row — used only on the settings screen; before/after screenshots at 1280px and 360px widths (evidence/save-row.png) · NOT VERIFIED: a physical phone
Commit: a91d03b "Settings: more space below Save", not pushed
Reviewer, verify: the settings screen on a physical phone; that the diff 4f2c9e1..a91d03b changes only this rule
Next gate: DELIVERED FOR CODEX REVIEW
```

### Standard order

1. **Status** — one of the statuses above, or the project's equivalent.
2. **Baseline** (H3); **scope** — AUTHORIZED / NOT AUTHORIZED (H4); and the **owner decisions** that shaped the work or are still required.
3. **Implementation summary** — per meaningful change: where, before → after, affected contract, why it is correct, invariants preserved, and the architectural impact where the build standard applies (H5).
4. **Files changed** — every intentionally changed file, authored separated from generated, with a reason each (H6, H61).
5. **Contract / data change** — or `NONE` (H7) — and **protected areas** (H8).
6. **Behavior change** — INTENDED and UNINTENDED, stated separately; for a defect, BEFORE / AFTER and the reproduction (H9, H36, H42).
7. **Tests and verification** — tests added or changed, then what ran with exact counts, as VERIFIED / NOT VERIFIED; never collapsed into "verification passed" (H10, H11, H46–H48).
8. **Regression review** — the surfaces checked (H13).
9. **Key claims and evidence** — each important claim beside the evidence that supports it, and where that evidence is (H14, H35, H40).
10. **Git and side effects** — commit or working-tree state (H15); deviations and scope expansion (H16, H56); `EXTERNAL SIDE EFFECTS: NONE` or exactly what happened; any deletion or dependency change (H59, H60, H62).
11. **Limitations, risks and findings** — known limitations (H17); risks, including known weaknesses you chose to leave, each with the smallest correction that would remove it; open findings with IDs and statuses (H18); newly discovered unrelated problems recorded as `BUILDER-DISCOVERED / PENDING REVIEW`, not silently fixed (H19).
12. **Codex handoff** — the challenge list (H21, H39).
13. **Next gate** (H23) — then STOP.

**Conditional sections** — include each whenever its condition holds, at any size. Because Full work is high-risk, a Full handoff always addresses the first three:

- negative and failure testing — high-risk work (H12);
- adversarial review suggestions — substantial or high-risk work (H22);
- rollback or recovery point — risky changes (H57);
- reproduction with EXPECTED / OBSERVED / RESULT — defects, migrations, device tests, important behavior changes (H36, H41);
- environment — environment-sensitive work (H49);
- repeated-run behavior — operations meant to be repeatable (H58);
- performance measurements, or `PERFORMANCE: NOT MEASURED` — when performance is part of acceptance or clearly at stake (H63);
- `DEPENDENCIES: UNCHANGED` — when dependencies matter to the task (H62);
- the matching emphasis mode.

### Corrective order (H25, H26)

Start with the status line, then:

- finding ID and the previous reviewer verdict;
- the required correction, quoted — or your inference, marked as one;
- root cause, and the correction implemented;
- the regression test: failing on the old implementation (expected; `NOT CHECKED` needs a concrete reason), passing on the new one (or `NOT RUN` and why);
- adjacent regression surfaces and the commit;
- finding status `CORRECTED / DELIVERED FOR RE-REVIEW`;
- the Codex re-review handoff and the next gate.

Then add the Standard sections those items do not cover — files changed, contract / data change, behavior change, side effects, limitations and other findings — at the depth the size needs. A small corrective patch may carry all of it in a few lines.

Section names may adapt to the task and to the project's format; the information an independent reviewer needs may not disappear (H33).

## Language (H20)

Use precise, bounded statements: "Builder verification passed." — only when every check you report ran and passed · "The rendered suite passed 318/318." · "No mismatch was found in the compared files." · "This was not tested on a physical device." · "The implementation is delivered for independent review."

Avoid these unless the evidence proves exactly that statement: "Perfect." · "Definitely fixed." · "100% safe." · "No bugs." · "Production ready." · "No regressions." · "Everything is preserved." · "Independently verified." Never turn "the command exited 0" into a stronger claim than that command proves, and never call emulator, simulator or synthetic-input results device testing (H30).

## Codex handoff (H21, H22, H39)

Tell the reviewer what to verify independently: never just "Please review", and never a verdict to sign.

- Cover what applies: baseline and commit identity, the exact implementation contract, the primary reproduction, regression surfaces, negative and edge cases, protected areas, test claims to rerun, evidence to check, limitations to judge, findings to close or reopen.
- Phrase each item so the reviewer could prove it false. Good: "Independently determine whether changing the date display format leaves stored due dates unchanged." Bad: "Confirm this is correct and accept it."
- Include contrary and failed results. Never argue for acceptance, select only favorable evidence, or imply that passing builder tests proves acceptance.
- Point to earlier sections instead of repeating their facts. A reviewer handoff sent on its own must carry its target commit and the builder's counts itself.
- For substantial or high-risk work, name where the implementation is most likely weak (H22).

## Write for a stranger (H51–H53)

The reviewer may be a different agent in a new session with none of this conversation. Replace "as we discussed", "the thing from before" and "same as yesterday" with exact finding IDs, commit hashes, paths, versions, contracts and reproduction steps, in the repository's own terms. Give conclusions, evidence and concise engineering rationale — not private chain-of-thought.

## Privacy and secrets (H37)

Never put passwords, private keys, tokens, secret environment values or private user or device records into a handoff or its evidence. Report a secret's state — present, external to the repository, verified — without printing it; naming where it lives, such as an environment variable's name, is fine. Redact evidence where needed.

When sensitive data or credentials were used during the work, say so without their contents: what kind, where any copy lives (a path is fine unless it reveals private data itself), whether it still exists, whether its use was authorized, and any owner decision needed. Sending private data to another system, or using a credential against an external service, is an external side effect (H59); name an OWNER DECISION when its authorization is unclear. Describe evidence built on private data by its kind and origin, and offer a synthetic reproduction when one exists or is cheap to build within scope.

## Stop (H23, H24)

End with the next gate — `DELIVERED FOR CODEX REVIEW` (for a re-review, name the finding), `WAITING FOR HUMAN ACCEPTANCE`, `BLOCKED — OWNER DECISION REQUIRED` with the decision named, or the project's own gate. When another step must come first or follows (an owner decision before review, an acceptance step after it), name the sequence. Then stop.

Do not start the next feature, slice, refactor or corrective action, begin cleanup, fix other findings, or update unrelated roadmap items. Remaining capacity is not authorization.

## Final quality gate (H65)

Before sending a handoff, confirm each item below; for a Compact or Blocked handoff, skip the items its template has no line for.

1. The exact baseline is identifiable.
2. The authorized scope is stated.
3. Every changed file is accounted for.
4. The implementation contract is explicit.
5. Intended and unintended behavior changes are separated.
6. Data and contract changes are explicit.
7. Tests and their actual results are reported.
8. Checks that did not run are disclosed.
9. Regression surfaces are identified.
10. Evidence is locatable.
11. Limitations and deviations are visible.
12. Open findings are preserved with their IDs.
13. No secrets or private data are present.
14. The commit or working-tree state is clear.
15. The Codex handoff asks for independent verification, not acceptance — or, with no independent review authorized, lists what only the builder checked.
16. The next gate is explicit.
17. Claude stopped instead of starting unauthorized work.

If any important answer is no, fix the handoff before sending it.

## References

Compact and Blocked handoffs are self-contained unless an emphasis mode applies.

| File | Load for | Holds |
|---|---|---|
| `references/core-handoff-contract.md` | Standard and Full handoffs, and Corrective handoffs at Full size | the full template and the rules for baseline, scope, owner decisions, implementation, files, contract and data, protected areas, behavior, Git, deviations and scope expansion, limitations, findings, IDs and severity, new defects, rollback, side effects, deletions, generated artifacts, dependencies and the next gate |
| `references/verification-and-evidence.md` | Standard, Full and Corrective handoffs | tests, verification results, failure testing, regressions, claims and evidence, reproduction, before and after, tolerances, hashes, data preservation, repeated runs, environment, conflicting sources, performance, manual observation, private evidence |
| `references/codex-review-handoff.md` | Full handoffs, and any Standard or Full handoff with no independent review authorized | the reviewer challenge list and its template, adversarial attack surfaces, the corrective re-review, the no-review handoff |
| `references/specialized-handoff-modes.md` | Corrective handoffs and any emphasis mode | Corrective, Refactor, Migration / persistence, Release / signing, Device, Documentation and Architecture |

Provenance: before this skill was built, the open skills ecosystem was searched on 2026-09-17 for engineering handoff, handoff report, review handoff, implementation report and completion report skills. The nearest results were session-continuity handoffs, reviewer-side quality gates and a design-review phase; none was a builder claim package for independent review.
