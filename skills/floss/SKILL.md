---
name: floss
description: Use on code just written or just changed to catch small leftover debris — hardcoded values, redundant lines, dead variables, needless nesting — that add clutter without adding function. Trigger on "/floss", "floss this", "clean this up a bit", "anything small to remove here". This is a light targeted pass, NOT a refactor or architecture review — see the "floss not brush" rule below.
---

# floss

Floss gets what's stuck between the teeth. It does not brush, it does not do a root canal. Small, targeted, low-risk.

## Core rule

Only remove things that are clearly redundant, unused, or hardcoded-for-no-reason. Do not restructure, rename for style, or redesign. If a fix needs more than deleting/tightening a few lines, it's not floss — flag it and stop.

## What counts as floss

- A hardcoded value that should obviously be a constant, prop, or config it already has access to.
- A variable assigned once and used once (inline it) or never used at all (delete it).
- A redundant condition (`if (x === true)`, duplicate null checks already covered above).
- Dead code: unreachable branches, commented-out blocks, unused imports.
- A line doing in three steps what one line already does elsewhere in the same function.

## What is NOT floss (leave it, don't touch)

- Anything that changes behavior, not just shape.
- Abstractions, even ugly ones, that are load-bearing (used elsewhere, or handle a real edge case).
- Anything requiring a new file, new dependency, or renaming across callers.
- Style-only preferences (quote style, naming taste) with no functional upside.

## What to do

1. Scan only the code just written/changed — not the whole file, not the whole repo.
2. List each finding as: `line X: [what's there] → [what it should be], because [reason]`.
3. Apply the safe ones directly. If a finding is borderline (might be load-bearing), ask before removing.
4. If nothing qualifies, say so in one line — don't invent cleanup to look useful.
