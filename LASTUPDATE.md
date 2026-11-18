# Last update — 2026-11-18

## The owner's lines: every handoff opens in plain words (H69)

A handoff is written for the reviewer: labels, paths, exact counts. The owner
reads it first, and may not be an engineer. Until now there was nothing in it
for them.

Every handoff now opens with a few plain lines for the owner, above the status
line and outside any REVIEW PROMPT block:

```
FOR YOU
What I did: <the change, in one everyday sentence>
Checked by me: <what ran, and what it showed>
Not checked: <what nobody has checked yet> | nothing left out
Your next step: <the one thing the owner does now> | nothing
```

## What changed

- **New section in `SKILL.md`** — *The owner's lines (H69)*: plain words, two
  to five lines, no code marks and no capital status phrases.
- **A summary, never an addition.** Each line repeats something the handoff
  says and never says it more strongly. A check that failed or did not run is
  said here too.
- **Never a verdict.** The builder does not tell the owner to accept the work;
  the last line names the owner's next step at the gate.
- **The handoff below is unchanged.** Nothing is cut from the reviewer's part.
- **Relay mode:** the owner's lines sit above the block and are never pasted
  to the reviewer.
- **Ordinary text, never a code block,** with bold labels: code blocks show in a
  pale colour many owners cannot read.
- **A start line and a stop line** in bold frame the reviewer's part, so the
  owner sees where it begins and ends.
- **Final quality gate** gains item 19.

## Earlier — 2026-09-29

Relay mode (H67, H68): a handoff the owner carries to a reviewer in another
session ships inside a paste-ready `REVIEW PROMPT` block, with the reviewer's
reply instructions inside it and the rounds numbered.
