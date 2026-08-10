---
description: "Turn on s-imp explanation mode: explain in real-world use-case language — flow diagram, action steps, a story-mode walkthrough, a screen-vs-server table, and every edge case with how it's handled. Stays on until 'stop s-imp'. Usage: /s-imp [optional: thing to explain]"
argument-hint: "[what to explain]"
---

Invoke the `s-imp` skill and follow it exactly.

s-imp is now ON for the rest of this session. Every explanation from here uses the s-imp output shape (one-line what-it-does-for-the-user → flow diagram → verb-first action steps → story-mode walkthrough with a named person and real values → two-lane table of what the user sees vs what runs behind it → edge-case table with unhandled cases marked). It stays on across all later turns regardless of topic, until the user says "stop s-imp" or "normal mode".

Before explaining anything, read the conversation for the actual use case being built — what real-world thing the data is, who creates it, what the user sees at the end. Explain the feature as that story, with code names attached as labels. Never as components talking to components.

If `$ARGUMENTS` is non-empty, explain that thing in s-imp shape right now.
If `$ARGUMENTS` is empty, confirm in one line — "s-imp on" — and apply it to everything that follows. Do not explain what s-imp is unless asked.
