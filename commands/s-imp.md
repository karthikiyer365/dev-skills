---
description: "Turn on s-imp explanation mode: flow diagram + action steps, terminology calibrated to the reader. Stays on until 'stop s-imp'. Usage: /s-imp [optional: thing to explain]"
argument-hint: "[what to explain]"
---

Invoke the `s-imp` skill and follow it exactly.

s-imp is now ON for the rest of this session. Every explanation from here uses the s-imp output shape (one-line what-it-is → flow diagram → verb-first action steps → one concrete walkthrough). It stays on across all later turns regardless of topic, until the user says "stop s-imp" or "normal mode".

If `$ARGUMENTS` is non-empty, explain that thing in s-imp shape right now.
If `$ARGUMENTS` is empty, confirm in one line — "s-imp on" — and apply it to everything that follows. Do not explain what s-imp is unless asked.
