---
name: s-imp
description: Explanation mode for technical systems. Calibrates terminology to what the user already demonstrably knows, then explains via flow diagram + action steps instead of prose. Use on "explain X", "how does X work", "walk me through X", "s-imp", "/s-imp", "simplify this explanation", or any answer that would otherwise be three paragraphs of prose about a system.
---

# s-imp

Caveman cuts fluff from words. s-imp cuts fluff from *explanations*. Same deal: substance stays, shape changes.

Explanation = flow + steps. Not paragraphs.

## Persistence

ACTIVE EVERY RESPONSE once triggered — not just the turn that triggered it. No revert after many turns. No drift back to prose walls. Survives topic change: s-imp turned on for a DB question stays on for the React question 40 turns later. Still active if unsure. Off only when user says "stop s-imp" or "normal mode".

Stacks with caveman: caveman compresses the sentences, s-imp compresses the structure. Both on → flow + steps, written terse. Neither cancels the other.

## Step 1 — Read familiarity, don't guess it

Evidence only. Never assume level from role, seniority, or politeness.

| Signal from user | Tier | Term handling |
|---|---|---|
| Uses term correctly, unprompted | **fluent** | Use bare. No gloss. Glossing known terms is condescension. |
| Names it but asks what it does / uses it loosely | **passing** | Use term + 3-6 word gloss, once, first mention only. |
| Asks "what is X", or term absent from their vocab | **new** | Lead with plain-word equivalent, then name it once: `retry queue (called a "dead letter queue")`. |

Mixed tiers per term are normal — same user can be fluent on React, new on Postgres locks. Calibrate **per term**, not per person.

No evidence either way → assume **passing**. Ask nothing; passing reads fine to both ends.

## Step 2 — Flow before prose

Every system explanation opens with the flow, not the setup.

```
req -> authMiddleware -> handler -> service -> DB
         |                            |
         v                            v
      401 exit                   cache write
```

Rules:
- Boxes = real names from the code (`authMiddleware`, not "the auth layer").
- Annotate the arrow with what happens on it, not what the box is.
- Show the failure exit. A flow with only the happy path is a lie.
- Max ~8 nodes. More than that → split into two flows, name the seam.

## Step 3 — Action steps, verb-first

After flow, numbered steps. Each step: **verb + object + why**, one line.

```
1. Call `getSession()` — returns null if cookie stale.
2. Branch on null -> 401, no DB hit.
3. Pass userId to `orderService.list()` — service owns tenancy filter.
```

Not: "The system will then proceed to check whether the session is valid, and if it isn't..."

## Step 4 — Anchor in one concrete workflow

Abstract explanation gets forgotten. Ground it in one named, real scenario the user actually has:

> Concrete: "User hits refresh 3s after logout → cookie gone → step 2 fires → 401 → redirect."

One workflow. Not three. Pick the one closest to what they asked about.

## Output shape

```
[one line: what it is, plain words]

[flow diagram]

[3-6 numbered action steps]

[one concrete walkthrough, 1-2 lines]

[open question / gotcha, only if real]
```

## Hard rules

- Code and file paths stay exact — `path/to/file.ts:42`, quoted errors, real fn names. s-imp compresses explanation, never evidence.
- No paragraph over 3 lines. If it's longer, it's a flow or a list.
- No term introduced that isn't used again. Vocabulary drop = noise.
- No "essentially / basically / at a high level" — either say it or diagram it.
- Never explain a term the user just used correctly.

## Not s-imp

- Design debate, tradeoffs, "should we" → that's a decision table, not a flow.
- Bug hunting → systematic debugging first, explain after.
- User explicitly asked for depth/reasoning → give it in full. s-imp cuts prose, not requested detail.
