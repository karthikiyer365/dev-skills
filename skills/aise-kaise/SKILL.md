---
name: aise-kaise
description: Use when a decision, feature, or design is about to be finalized and needs a product-usage sanity check. Questions the decision from a product manager / product lead point of view — does this actually help the end user, or does it just satisfy the engineering plan? Trigger on "does this make sense for the user", "product pov", "will people actually use this", or before locking in any user-facing decision.
---

# aise-kaise

"aise kaise" = "how/why is this even like this" — the question a product lead asks when a decision sounds technically correct but doesn't hold up from a usage standpoint.

## Core rule

Before a decision ships, argue it from the user's chair, not the builder's chair. Technical correctness is not the same as product friendliness.

## When to use

- Right before finalizing a feature decision, UX flow, or default behavior.
- When a plan reads well engineering-wise but no one has asked "would a real user want this."

## What to do

1. State the decision in one line, plain language.
2. Ask, out loud, in the response:
   - Who is the user here, and what were they trying to do?
   - Does this decision make their task easier or just make the system simpler to build?
   - What's the most annoying way a real user could hit this?
   - Is there a simpler, more obvious behavior a non-technical person would expect instead?
3. If the decision survives all four questions, say so briefly and move on — this is not a mandate to redesign everything.
4. If it doesn't survive, name the specific friction and propose the smaller, more usage-friendly alternative.

## Tone

Push back like a product lead in a review, not an engineer defending the ticket. Skeptical, user-first, brief — not a design essay.
