---
name: sch-said-what
description: Use before writing any code that reads, writes, or validates data touching the database — new fields, forms, API inputs, migrations. Finds the actual schema (column type, nullability, length, enum values, constraints, defaults) from the real source of truth, not from memory or assumption, then enforces validity at whichever layer the repo actually validates at (input/form, API boundary, service/transaction, or DB constraint). Trigger on "what type is this column", "can this be null", "add validation for X field", "what values are valid here".
---

# sch-said-what

"Schema said what?" — check what the schema actually says before writing a line that assumes it.

## Core rule

Never guess a column's type, nullability, length, or valid values. Read it from the real source (schema file, migration history, ORM model, or a live read-only query) before writing validation, form logic, or a query against it.

## What to look up, in order

1. Repo's schema reference/docs if one exists (e.g. a database-column-reference doc) — check this first, it's usually faster and already curated.
2. Migration files / schema definition (SQL, Prisma schema, etc.) for the authoritative type, nullable/not-null, default, unique/foreign-key constraints.
3. If still unclear and a read-only DB connection is available, query the live schema (information_schema, `\d table`, etc.) — read-only, never a write/migration to check something.
4. Enum/check constraints — get the exact allowed value list, don't infer it from one sample row.

## What to do with what you find

1. State the finding plainly: `column: type, nullable?, constraints, default`.
2. Identify where this repo actually enforces validity — some repos validate at the API/input boundary, some in a service/transaction layer, some rely on DB constraints alone. Match the existing pattern, don't invent a new validation layer.
3. Write validation that mirrors the schema exactly — same nullability, same length/range, same enum values. Don't validate stricter or looser than the schema without saying so.
4. If the schema itself looks wrong for the use case (e.g. column allows null but business logic never should), flag the mismatch instead of silently coding around it.

## What NOT to do

- Don't hardcode a guessed type/length/enum list without checking the source.
- Don't add a second validation layer if the repo already enforces this elsewhere — that's redundant, not safe.
- Don't run any write/migration just to "check" the schema — read-only only.
