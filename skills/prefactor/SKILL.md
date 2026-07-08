---
name: prefactor
description: Use to map a repository's documentation into a MECE tree and generate a navigation file showing which doc to read, what patterns to learn, and what neighboring segment to cross-check for consistency — before building in any area (product, client-facing, frontend, or backend). Trigger on "prefactor this repo", "map the docs", "where's the documentation for X", "generate a documentation map", "what should I read before building this", "/prefactor".
---

# prefactor

Documentation, diagnosed. Splits a repo's docs into a fixed MECE tree, tells you which file answers which question, and tells you which neighboring branch to check for consistency before you build.

## The tree

Four binary splits, 16 leaf segments. Each split is Mutually Exclusive (a doc belongs to exactly one side) and Collectively Exhaustive (every doc about that parent fits one side or the other).

```
Documentation
├── User
│   ├── Product Manager
│   │   ├── Features
│   │   │   ├── 1. Feature Definitions            (what a feature is/does, scope)
│   │   │   └── 2. Feature Roadmap & Prioritization (why/when, sequencing, tradeoffs)
│   │   └── Workflow
│   │       ├── 3. Process & Decision Flow          (how work moves: planning, PRD, approval)
│   │       └── 4. Metrics & Success Criteria       (how outcomes are measured)
│   └── Client
│       ├── Features
│       │   ├── 5. Usage Guides                    (how the end user performs a task)
│       │   └── 6. Limitations & FAQ                (edge cases, what it doesn't do, troubleshooting)
│       └── Workflow
│           ├── 7. Onboarding                       (getting a new client to first value)
│           └── 8. Ongoing Operations & Support     (steady-state use, support process)
└── Developer
    ├── Frontend
    │   ├── UI Components
    │   │   ├── 9. Component Reference              (props, variants, composition)
    │   │   └── 10. Styling & Theming                (design tokens, visual conventions)
    │   └── Backend Connection
    │       ├── 11. API Contracts Consumed           (shape/type of data expected from backend)
    │       └── 12. State & Data Flow                (how fetched data becomes UI state)
    └── Backend
        ├── Functionality Pipelines
        │   ├── 13. Domain & Business Logic          (core rules, services, invariants)
        │   └── 14. Data & Schema                    (DB schema, migrations, column meaning)
        └── Frontend Connection
            ├── 15. Queries Exposed                  (read endpoints returned to frontend)
            └── 16. Mutations Exposed                (write/action endpoints, side effects, validation surfaced)
```

## When invoked

1. **Find every file that belongs, not just the obvious one.** Search the whole repo, not just `docs/` — READMEs, ADRs, schema-reference files, inline architecture comments, design-system docs, config files that define contracts (OpenAPI/GraphQL schemas, Prisma schema, component prop types). A segment with 4 relevant files gets 4 bullets, not 1.
2. **Classify each file into exactly one of the 16 leaves.** Use the parenthetical definitions above as the test: read the file's actual content, not its filename. If a file genuinely covers two leaves, list it in both (this preserves MECE — the *file* can appear twice, but each *fact* still belongs to one segment).
3. **Flag gaps.** If a leaf has no matching file anywhere in the repo, mark it "no dedicated doc — gap" rather than inventing a reference.
4. **Generate the output file** at `docs/PREFACTOR.md` (create `docs/` if absent). Start with a header block (template below), then one `##` section per leaf segment, in the order shown above, each containing exactly three subsections (template below).
5. **When asked "where do I look before building X"** instead of a full regeneration: identify which 1-2 leaves the task belongs to, answer with just those sections, and name the cross-segment leaf to also check.
6. **Before finishing any task that touched a listed file**, update its segment in `PREFACTOR.md` in the same change — see Maintenance below. Don't leave the map to drift for someone else to notice.

## Header template (top of the generated file)

```markdown
# PREFACTOR — Documentation Map

_Generated: <date>, commit <short-sha>. If files under a segment changed after this commit, re-verify that segment before trusting it — see Maintenance._

## Index
| # | Segment | Files | Status |
|---|---|---|---|
| 1 | Feature Definitions | <count> | ok / gap / stale |
| ... | ... | ... | ... |
```

The index exists so a fresh session can jump straight to the right `##` section by number instead of reading the whole file top to bottom.

## Output template (repeat per leaf segment)

```markdown
## <N>. <Segment path, e.g. Developer > Backend > Functionality Pipelines > Domain & Business Logic>

### 1. Reference
- `<path/to/file>` — <knowledge title: the one thing to gather from this file>
- `<path/to/another/file>` — <knowledge title>
(list every file that belongs to this segment — one bullet each, not a single "main" file. If none: "No dedicated doc found — gap. Nearest related file: `<path>`, if any.")

### 2. Patterns to learn
- <pattern or convention someone must internalize before building something in this segment>
- <repeat as needed — keep to what's actually load-bearing, not generic advice>

### 3. Cross-segment check
- See **<other segment name>** (`<its reference file>`) for <specific thing to verify there — e.g. "the response shape this segment's UI renders" or "the DB constraint this validation must mirror"> — check this before finishing, for consistency and quality.
```

## Cross-segment map (which neighbor to check, and why)

| Segment | Check this neighbor for |
|---|---|
| 1–2 Feature Definitions/Roadmap | 5–6 Client Features — does the client-facing doc match what PM defined |
| 3–4 PM Workflow | 7–8 Client Workflow — internal process vs external-facing steps should agree |
| 5–6 Client Features | 9–10 UI Components — the UI must actually expose what the usage guide describes |
| 7–8 Client Workflow | 15–16 Backend Frontend-Connection — onboarding/support steps must match real exposed actions |
| 9–10 UI Components | 11–12 Frontend Backend-Connection — components must render what the API contract actually returns |
| 11–12 Frontend↔Backend (consumed) | 15–16 Backend↔Frontend (exposed) — the two sides of the same contract must match exactly |
| 13–14 Backend Pipelines | 15–16 Backend Frontend-Connection — exposed endpoints must reflect real domain rules and schema |
| 15–16 Backend Frontend-Connection | 11–12 Frontend Backend-Connection — same pairing, checked from the other side |

## Maintenance (keep this file honest)

A stale map is worse than no map — it sends the next session or agent to the wrong file with false confidence. Treat `PREFACTOR.md` as enforced, not optional:

- **Update in the same change, not later.** If a task adds, deletes, renames, or meaningfully rewrites a file that belongs in a segment, update that segment's Reference bullets and `Index` count as part of that same task — not a follow-up.
- **Check staleness before trusting it.** Compare the header's commit against current state. If any file listed under a segment changed since then, re-verify that segment's bullets and knowledge titles before relying on them; mark the Index `Status` column `stale` until re-verified.
- **Never leave a dead reference.** A bullet pointing at a deleted/renamed file is worse than marking the segment a gap — remove or fix it immediately when noticed, even if that's not the main task.
- **Full regenerate on explicit request** ("prefactor this repo") or once several segments are marked `stale` — don't let partial patching substitute for a real pass indefinitely.

## Formatting rules

- Bullets only — no prose paragraphs in any subsection.
- One file per bullet, one pattern per bullet, one cross-check per bullet. Don't merge multiple facts into a single line.
- Keep each bullet to one line. If something needs more explanation than that, the explanation belongs in the source file, not in `PREFACTOR.md`.
- Cap "Patterns to learn" at what's actually load-bearing — 3–6 bullets is typical. Needing more usually means the segment is too broad; flag that instead of stuffing the list.

## What NOT to do

- Don't force a doc into a leaf it doesn't fit just to avoid a "gap" — an honest gap is more useful than a wrong classification.
- Don't regenerate the full `PREFACTOR.md` for a narrow "where do I look" question — answer with the relevant leaves only.
- Don't invent documentation content — this skill maps and cross-references what exists; it doesn't author new docs.
- Don't skip the Maintenance step because the current task "isn't about docs" — if it touched a listed file, updating the map *is* part of the task.
