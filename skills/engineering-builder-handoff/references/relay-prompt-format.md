# Relay prompt format

The wrapper and reply instructions for a handoff the owner carries to an independent reviewer in a different session. The block makes the handoff paste-ready; the trailer makes the reviewer's reply paste-ready for the trip back. Nothing here changes what the handoff says — relay mode changes only how it travels. The owner ferries blocks; the owner never composes them and never has to ask for them.

## Contents

- H67 The paste-ready block
- H68 The reviewer trailer
- The return trip
- Round numbering
- Before sending

## H67 The paste-ready block

The whole handoff, unchanged, between two marker lines:

```
─── REVIEW PROMPT — paste to <reviewer> — round <N> ───

<the handoff>

<the reviewer trailer>

─── END REVIEW PROMPT ───
```

- Name the reviewer the project names — Codex when none is named.
- Round 1 for new work. The corrective patch after a reviewer's findings re-emits the block at the next round — `round 2`, `round 3` — so fatigue in a long loop stays visible to the owner, never hidden.
- One block per handoff. Never wrap two handoffs in one block, and never emit a block for a Blocked handoff — a block travels to a reviewer; a Blocked handoff travels to the owner.

## H68 The reviewer trailer

Standing text, appended inside the block after the handoff. It does not repeat the challenge list — that still says what to verify (H21). The trailer only says how to answer, so the reply can be pasted back without editing. The reviewer needs no copy of this skill; the trailer carries the protocol.

```
─── Reviewer instructions ───
You are reviewing another agent's delivered work in a fresh session. You did
not watch the build. Verify from the repository itself — the builder's claims
are leads, not evidence.

- Work the "Reviewer, verify" / challenge list above. Rerun what you can;
  report what you cannot run as NOT RUN.
- Report each finding as: ID · file:line · severity · what you observed · the
  evidence. Keep the builder's finding IDs; number new ones yourself.
- End your reply with exactly one of:

  APPROVED
    — nothing else — when nothing needs correcting.

  ─── FIX PROMPT — paste to the builder — round <N+1> ───
  Verdict: <one line>
  Findings:
  - <ID> · <file:line> · <severity> · <required correction> · <evidence>
  Definition of done: <what you will re-check>
  ─── END FIX PROMPT ───
```

The trailer asks for a verdict and its evidence. It never asks the reviewer to trust the builder, to sign a prepared verdict, or to soften findings for the trip back.

## The return trip

- `APPROVED`, or an acceptance the authorized reviewer or owner already gave: record it under `ACCEPTED`, naming who gave it. The loop ends; no new work starts.
- A `FIX PROMPT` block: its findings are the input to a Corrective handoff (H25). Quote the required corrections; where the builder must infer, mark the inference as one.
- Any other pasted review reply — freestyled, partial, malformed: still the finding source. The trailer's format is a request, never a precondition the builder enforces. An anchor the reviewer did not provide is reported as `NOT PROVIDED BY REVIEWER`, never invented.

## Round numbering

Round numbers count trips to the reviewer, not fixes. New work is round 1; every corrective re-review increments once. A finding argued again in a new handoff keeps counting rounds — the number tells the owner how many times this work has crossed the gap.

## Before sending

1. The block markers are exactly as shown, the reviewer named, the round correct.
2. The handoff inside is complete and unchanged — the block adds transport, not content.
3. The trailer is appended in full; nothing was trimmed for length.
4. A Blocked or owner-decision handoff carries no block.
