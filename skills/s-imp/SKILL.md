---
name: s-imp
description: Explanation mode for technical systems. Explains in the language of what the app actually does for its users — the real-world thing flowing in, where it travels, what the user ends up seeing — not component names alone. Calibrates terminology to what the user demonstrably knows, then explains via flow diagram + action steps instead of prose. Use on "explain X", "how does X work", "walk me through X", "what are we building", "s-imp", "/s-imp", "simplify this explanation", or any answer that would otherwise be three paragraphs of prose about a system.
---

# s-imp

Caveman cuts fluff from words. s-imp cuts fluff from *explanations*. Same deal: substance stays, shape changes.

Explanation = flow + steps. Not paragraphs.

## Persistence

ACTIVE EVERY RESPONSE once triggered — not just the turn that triggered it. No revert after many turns. No drift back to prose walls. Survives topic change: s-imp turned on for a DB question stays on for the React question 40 turns later. Still active if unsure. Off only when user says "stop s-imp" or "normal mode".

Stacks with caveman: caveman compresses the sentences, s-imp compresses the structure. Both on → flow + steps, written terse. Neither cancels the other.

## Step 0 — Name the real-world thing first

Before any file name, answer three questions from the *conversation context* — what feature is actually being built, for whom:

1. **What real-world thing is this data?** Not "a `Booking` row" — *an appointment a customer made for Tuesday 3pm*.
2. **Where does it come from, where does it end up?** Who or what creates it, what the user sees at the other end.
3. **What does the user do with it?** The action the whole feature exists to enable.

Every later step is phrased in those nouns. Code names are the *labels* on that story, never the story itself.

> Building a calendar: the thing is **an event with a date**, it comes from **the events table when the user opens a month**, it ends up as **a colored pill inside that day's cell** the user clicks to see details.

If context doesn't reveal the use case, infer it from the code's domain nouns — and say the inference out loud in one line so it can be corrected.

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

Every box carries **two labels**: the code name, and what that thing *is* in the real world. Arrows carry the payload in domain words.

```
user opens March          month range           event rows            one pill per event
      |                        |                     |                       |
      v                        v                     v                       v
 <Calendar/>  ---------> useEvents()  ------> eventsRepo.list() ------> day cell renders
 (the grid)              (fetch hook)         (Postgres events)        (title + time chip)
                                                     |
                                                     v
                                              no events -> empty day, muted
```

Rules:
- Boxes = real names from the code (`authMiddleware`, not "the auth layer") **plus** a 2-5 word plain-language line under it saying what it is to the user.
- Arrow labels = the actual data moving, named as the real thing (`the 3 events for that day`), not its type (`Event[]`).
- Show the failure/empty exit. A flow with only the happy path is a lie.
- Max ~8 nodes. More than that → split into two flows, name the seam.

## Step 3 — Action steps, verb-first

After flow, numbered steps. Each step: **verb + the real-world object + why**, one line. Code name goes in backticks *inside* the sentence, it doesn't replace it.

```
1. User clicks March -> `useEvents('2026-03')` asks for that month's events.
2. Fetch every event whose start date lands in the range — `eventsRepo.list()` owns the date filter.
3. Bucket events by day so each date cell gets only its own.
4. Render one pill per event in the cell — title + start time, colored by event type.
5. Empty day renders muted, no pill, still clickable to create.
```

Not: "The component receives an array of typed entities which are then mapped over and conditionally rendered..."

## Step 4 — Story mode: narrate it as the user living it

Abstract explanation gets forgotten. Tell it as a story with a named person, a real date, real values — beat by beat, what they do and what the screen answers back. Present tense. The user is the subject of every sentence, the system is what responds.

> **Priya, Monday morning, opening the schedule.**
>
> She lands on the calendar and it's already showing March — the grid dims for a blink while it asks for the month. Twelve events come back. Most days hold one pill; March 14 stacks three, the top one reading `Standup 9:00` in blue because it's a recurring internal. The 21st is empty and sits muted, but still invites a click.
>
> She taps the standup pill. Nothing goes back to the server — the event is already in hand — so the drawer slides in instantly with the description and attendee list. She hits the arrow for April; the grid dims again, and the whole cycle repeats for a different month.

Rules:
- Name the person and the moment. `Priya, Monday morning` beats "the user".
- Concrete values only — `12 events`, `March 14`, `Standup 9:00`. Never `foo`, never "some data".
- Every beat pairs the action with the visible result. Action with no visible consequence is a step, not a beat — it belongs in Step 3.
- Call out where *nothing* happens ("nothing goes back to the server") — that's the part people misjudge.
- One story. Not three. Pick the path closest to what they asked about.

## Step 5 — Two-lane trace: screen vs. server

Every explanation ends with the interaction table. One row per user action. Left = what the user does and sees. Right = what actually runs. Rows line up in time — same row, same moment.

| # | User does / sees | What runs behind it |
|---|---|---|
| 1 | Clicks `>` to March, grid dims | `useEvents('2026-03')` fires -> `GET /events?from=2026-03-01&to=2026-03-31` |
| 2 | Skeleton pills, ~200ms | `eventsRepo.list()` hits `events` on the `start_at` index, org filter applied |
| 3 | 12 pills paint into day cells | rows returned, grouped by date in `groupByDate()` |
| 4 | Clicks the `Standup 9:00` pill | no fetch — event already in memory, drawer reads from cache |
| 5 | Clicks an empty day | opens create form, nothing hits DB until submit |

Rules:
- Every row is one user-observable moment. No row for pure internals.
- Right column names the real call, table, or file. Left column names only what a person could point at on screen.
- Include the rows where the answer is "nothing happens on the server" — those are the ones people get wrong.

## Step 6 — Edge cases, and what each one does

List every edge case in *this* use case, not generic ones. Each row: the situation in real-world words, what the user sees, and where it's handled. Handled and unhandled both get listed — an unhandled case is the most valuable line in the table.

| Edge case | What the user sees | Handled where |
|---|---|---|
| Month has zero events | Empty grid, muted days, create prompt in center | `<Calendar/>` empty branch |
| Event spans midnight / two days | Pill drawn in both day cells, marked continued | `groupByDate()` splits on range |
| More events than fit one cell | First 3 pills + `+4 more`, click expands the day | `<DayCell/>` overflow cap |
| Fetch fails mid-month-change | Old month stays on screen, toast with retry | `useEvents()` error branch |
| User's timezone ≠ event timezone | ⚠️ **unhandled** — event stored UTC, rendered local, can land on wrong day | nowhere yet |
| Two users edit the same event | ⚠️ **unhandled** — last write wins, no conflict notice | nowhere yet |

Rules:
- Derive cases from the domain, not a checklist: empty, one, many, too many, boundary (midnight, month edge, timezone), stale, concurrent, permission-denied, offline.
- Mark unhandled cases ⚠️ and say what breaks in user terms — never quietly omit them.
- If a case is deliberately out of scope, say so in the row. Silence reads as a bug.

## Output shape

```
[one line: what this does for the user, plain words, zero file names]

[flow diagram — code name + what-it-is on every box, real payload on every arrow]

[3-6 numbered action steps, real-world object per step]

[story-mode walkthrough — named person, real values, beat by beat]

[two-lane table: user does/sees | what runs behind it]

[edge-case table, unhandled ones marked ⚠️]

[open question / gotcha, only if real]
```

## Hard rules

- Code and file paths stay exact — `path/to/file.ts:42`, quoted errors, real fn names. s-imp compresses explanation, never evidence.
- No paragraph over 3 lines. If it's longer, it's a flow or a list.
- No term introduced that isn't used again. Vocabulary drop = noise.
- No "essentially / basically / at a high level" — either say it or diagram it.
- Never explain a term the user just used correctly.
- Never explain a system purely as components talking to components. Every layer names what it does *to the real thing* — "stores the appointment", not "persists the entity".
- Type names are not explanations. `Event[]` is a shape; "the events on that day" is the meaning. Give both, meaning first.
- If the explanation would read the same for a calendar, a chat app and an invoice tool, it's too abstract — rewrite it with this product's nouns.
- Story, two-lane table and edge cases are not optional extras. An explanation without them answered "what does the code do" instead of "what happens when someone uses this".
- Never present an edge-case list as complete when a case is only assumed handled. Check the code, or mark it unverified.

## Not s-imp

- Design debate, tradeoffs, "should we" → that's a decision table, not a flow.
- Bug hunting → systematic debugging first, explain after.
- User explicitly asked for depth/reasoning → give it in full. s-imp cuts prose, not requested detail.
