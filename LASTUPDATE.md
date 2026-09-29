# Last update — 2026-09-29

## Relay mode: the handoff now travels paste-ready (H67, H68)

The skill used to end at the handoff. When the owner carries that handoff to a
reviewer in a *different* session — pasted into Codex, Claude, or any agent that
saw none of the build — it now ships inside a marked, copy-paste-ready block, and
it carries the instructions that make the reviewer's reply paste-ready for the
trip back.

The owner ferries blocks. The owner never composes them and never has to ask for
them.

## What changed

- **New section in `SKILL.md`** — *Human relay to another session (H67, H68)*:
  when relay mode applies, the whole handoff wraps between
  `─── REVIEW PROMPT — paste to <reviewer> — round <N> ───` and
  `─── END REVIEW PROMPT ───`, with the reviewer instructions appended inside.
- **New reference file** — `references/relay-prompt-format.md`: the block
  markers, the full reviewer trailer, the return-trip rules, and round
  numbering.
- **The reviewer needs no skill.** The trailer teaches the protocol: verify
  independently, report findings with `file:line`, and end the reply with
  `APPROVED` — or a `FIX PROMPT` block the owner pastes straight back to the
  builder.
- **Rounds are numbered.** Each corrective re-review re-emits the block at the
  next round, so a long loop stays visible to the owner.
- **Final quality gate** gains item 18: a relayed handoff must be inside the
  block with the trailer in full.
- **Blocked and owner-decision handoffs carry no block** — a block travels to a
  reviewer; a Blocked handoff travels to the owner.

## The loop it closes

```
builder ── REVIEW PROMPT ──▶ owner pastes ──▶ reviewer
builder ◀── owner pastes ◀── FIX PROMPT (or APPROVED)
builder ── REVIEW PROMPT · round 2 ──▶ … until APPROVED
```

## Compatibility

Relay mode is gated: it activates when the owner says the review happens in
another session, or when the handoff is plainly the only context the reviewer
will have. A reviewer who shares the session needs no block — nothing changes.

The description was shortened to fit the 1024-character skill contract after the
relay triggers were added.
