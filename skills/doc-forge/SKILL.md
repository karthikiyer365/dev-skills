---
name: doc-forge
description: Use after a repo topical map exists (or create one first via the topical-map prompt) to audit doc coverage, restructure docs into product/features + product/dev, and generate missing docs from code. Trigger on "doc forge", "fill doc gaps", "audit doc coverage", "restructure docs", "/doc-forge".
---

# doc-forge

Turns a repo's topical map into a complete, honestly-named, gap-filled documentation tree. Nine phases, run in order; each phase's output feeds the next. Never invent content — every generated doc must be derivable from code, migrations, or git history, or it stays a stub.

## Phase 1 — Verify the map against code

Before trusting any existing map or conventions file (CLAUDE.md, README):
- grep for every claimed mechanism (feature gating, rate limiting, event fan-out, middleware). Absent from code → mark `▓ planned ▓` in the map, never state as fact.
- Check who really performs side effects (notifications, emails, audit): app code or DB triggers. Grep `insert into <table>` across migrations.
- Check middleware reality (e.g. Next 16 renamed `middleware.ts` → `proxy.ts`) and what it actually does.
- Count action/controller layers (colocated vs shared) and measure code gravity (LOC of fat action files vs thin lib modules).
- Correct the map with evidence: quote file:line for every correction.

## Phase 2 — Per-feature backend processing map

Append "Map C" to the topical map: one compact ASCII flow per feature — entry point → exact handler file → validation → helper calls → tables written → triggers fired → cache revalidation → realtime path. Real file paths + real function names only (grep exports first).

## Phase 3 — Doc coverage audit

Append a coverage table to the map: one row per map card → `| Card | Doc(s) | ✅/🟡/❌ |`.
- ✅ dedicated current doc · 🟡 partial/stale/conventions-only · ❌ none.
- Also list **inverse gaps**: docs that exist but no card owns them (missing map node).
- Report score per map (counts of ✅/🟡/❌) and the pattern (e.g. "everything shipped after <date> shipped docless").

## Phase 4 — Gap triage, three buckets

1. **Generate new** — priority table: doc name | fills which gap | must contain | derive-from sources. Split code-derivable vs needs-product-input.
2. **Rewrite stale** — doc | why stale | action (rewrite / fold / patch).
3. **Fix or delete** — doc-vs-code drift in conventions files (highest value, smallest edit), self-deleting docs past their expiry.

## Phase 5 — Folder restructure

- `docs/product/features/` (product/feature docs) + `docs/product/dev/` (developer docs). Leave at docs root: nav maps (PREFACTOR, TOPICAL_MAP), design/, plans/, releases/, changelogs.
- Move with `git mv` (history preserved). Untracked files use plain `mv`.
- **Fix every reference repo-wide**: grep all old paths first (*.md, *.ts, code comments, test comments), then `LC_ALL=C sed -i '' 's|old/path|new/path|g'` over the affected file list (LC_ALL=C avoids illegal-byte-sequence crashes; a crashed sed leaves `.!NNNNN!file` temp artifacts — delete them and verify the real file's line count against git HEAD).
- Fix relative cross-folder links inside moved files (`](FILE.md)` → `](../dev/FILE.md)`).
- Verify with a repo-wide grep for old paths → must print nothing.

## Phase 6 — Naming convention + ordering

- **Single-word ALL-CAPS** for feature/part docs: `USERS.md`, `JOBBOARD.md`, `ARENA.md`, `ARCHITECTURE.md`, `STORAGE.md`, `NOTIFICATIONS.md`.
- **kebab-case** for workflow/process/integration docs: `workflow.md`, `tdd.md`, `api-catalog.md`, `client-guides.md`.
- **Numeric prefix on feature docs only** (`1.USERS.md`, `2.JOBBOARD.md`…), ordered by topical-map card sequence. Process docs stay unnumbered. New feature doc = next number; mid-order insert = rename cascade (accepted, renames are cheap with the sed pipeline).
- Same git mv + sed + verify pipeline as Phase 5.

## Phase 7 — Stub every gap

For each doc that can't be generated yet, create a stub in its final location:

```markdown
# <Title>

> **GAP — stub, not yet generated.** Fills <map card / segment>. <One-line why it matters.>

## Must contain when generated
- <scope bullets>

## Derive from
- <exact files/migrations/PRs to read>
```

## Phase 8 — Generate the code-derivable docs

Only generate what code proves. Formats by doc type:

**Feature doc** (e.g. ARENA): header line naming sources → What it is → **Locked spec table** (invariants: do not re-litigate) → pipeline ASCII flowchart with real fn names → Schema summary (FKs, RLS, indexes) → Read path → **Deferrals list** (explicitly out of scope) → **Gotchas** (footguns with file refs).

**Event/trigger doc** (e.g. NOTIFICATIONS): architecture rule ("app code never inserts X") → table shape (SQL verbatim) → **event → trigger fn → type → payload table** → guards & dedupe invariants (quote the `on conflict` clause) → enum values + TS mirror + "adding a value = N places" checklist → consumption flow → what the system does NOT cover.

**Catalog doc** (e.g. api-catalog): state generation method + date in header ("regenerate the same way"). Extract mechanically:
`grep "^export async function"` per action file; `grep -o "\.from\(['\"]...\)"` for tables; `grep revalidatePath` for cache paths. Group tables: shared actions / feature actions / auth / admin / route handlers (method + auth). End with **Known asymmetries** section — duplicated fns, convention drift found during extraction.

**Generated reference** (e.g. database-column-reference): derive from generated types (`types/database.ts` or equivalent), never hand-transcribe — awk/script the extraction, keep the script in git history, header says "regenerate, don't hand-edit". Include enums, RPC surface, views, conventions block.

Docs needing product/PM input (metrics, client guides): leave as stubs, say so.

## Phase 9 — Map maintenance (same change, not later)

- Update PREFACTOR/doc-map: index counts, gap→ok statuses, new reference rows, and **purge any fiction** the verify pass disproved (mark "verified not implemented <date>").
- Update topical-map coverage table (❌→✅ with paths) and card `Docs:` lines.
- Report new coverage score vs old.
- Commit style: conventional `type:scope-description`, subject + one line, split moves/refs from content generation. Never push without explicit approval.

## Rules

- Evidence before assertion: every claim in a generated doc traces to a file read this session.
- Flag doc-vs-code drift loudly — it's the highest-value output.
- Mechanical extraction over prose recall for catalogs/references.
- Honest gap > invented content. Stubs are fine; fiction is not.
- All moves via `git mv`; verify zero stale refs after every rename wave.
- Surprising finds during extraction (dead tables, duplicate action sets) go in the doc's asymmetries/gotchas section AND the summary to the user.
