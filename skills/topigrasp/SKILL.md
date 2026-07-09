---
name: topigrasp
description: Generate a card-based topical map of a repository as ASCII flowchart graphs — product topology, developer topology, and per-feature backend processing — verified against real code, not docs. Trigger on "topigrasp", "map repo topology", "generate topical map", "map the repo functionality", "/topigrasp".
---

# topigrasp

Generates `docs/TOPICAL_MAP.md`: a card-based topology of the repository, drawn as ASCII flowchart graphs. Three maps: product, developer, per-feature backend processing.

## Ground rules (do these BEFORE writing anything)

1. **Survey the real repo, not the README.** List: top-level dirs, route/page dirs, domain/lib modules, API route files, server action/controller files, DB migrations (count + last ~10 filenames), hooks/realtime code, external service clients, test layout, CI/git hooks, middleware/proxy entry.
2. **Verify every architectural claim against code.** Docs and conventions files lie by aspiration. For each claimed mechanism (rate limiting, feature gating, event fan-out, caching), grep for it. If absent from code, still include it but mark `▓ planned ▓` — never state it as fact.
3. **Find where writes actually flow.** Trace one real mutation end-to-end (form → action/controller → validation → DB → cache invalidation → side effects). Check whether side effects (notifications, emails, audit) happen in app code or DB triggers — don't assume.
4. **Note code gravity honestly.** If "domain modules" are thin and logic lives in fat controllers/actions, say so with LOC evidence.

## Output file structure

Header line: generation date, commit short-sha, one-line legend (`⟶` = dependency edge, `▓` = planned/aspirational — claimed in docs, absent from code).

### MAP A — Product Topology

- One ASCII flowchart: every user-facing feature as a box (A0, A1, …), arrows showing user-flow/data dependencies (auth → profile → content features → notifications → search/admin). Mark stub/planned features `▓`.
- Then one **card per node**:
  ```
  **[A#] Feature Name**
  - What: 1-2 lines, concrete capabilities.
  - Where: actual paths (routes, domain modules, API endpoints).
  - Docs: related doc files + staleness flags.
  - Edges: ⟶/⟵ other nodes with the reason (e.g. "⟶ A8 (notify)").
  ```

### MAP B — Developer Topology

- One ASCII flowchart: every internal technical unit as a box (B0, B1, …) — domain/lib modules, shared pipelines (build, test, CI, migration runner, codegen), core methodology enforced in the repo (validation layer, error-handling convention, state-management pattern). Arrows show dependency/call direction (lib ⟶ service ⟶ router, or codegen ⟶ types ⟶ consumers). Mark aspirational-only conventions (claimed in a conventions doc, absent from code) `▓`.
- Then one **card per node**, same format as MAP A:
  ```
  **[B#] Unit Name**
  - What: 1-2 lines — its technical responsibility or methodology it enforces.
  - Where: actual paths (module/lib/pipeline files).
  - Docs: related conventions/architecture doc files + staleness flags.
  - Edges: ⟶/⟵ other nodes with the reason (e.g. "⟶ B4 (calls shared validator)").
  ```

### MAP C — Per-feature backend processing

- One compact ASCII flow per feature (C1…Cn), showing: entry point (form/route) → exact action/handler file → validation → helper/lib calls → table(s) written → triggers/side effects → cache revalidation → realtime/read path. Use real file paths and real function names (grep the exports first). Note shared functions reused across flows.

### Cross-map bridges

- Closing table: | Product node | Runs on (B/C nodes) | Key seam to check | — the consistency checks a developer must verify when touching that feature.

## Style rules

- Cards separated by `---`. One fact per bullet. Real paths/function names only — no invented ones.
- ASCII charts: boxes with `┌─┐│└┘`, arrows `──>` and `v`, tree nesting `├──`/`└──`. Charts must fit ~90 chars wide.
- Every claim in a card must be traceable to a file you actually listed or grepped this session.
- Flag doc-vs-code drift explicitly (conventions file says X, code does Y) — that's the map's highest-value content.

## Maintenance footer behavior

If a documentation-map file (e.g. `PREFACTOR.md`) exists, register `TOPICAL_MAP.md` in it in the same change. If the repo changes later, regenerate stale sections rather than patching prose.
