# Known Spec-Compliance Issues

Tracker of known field-name, symbol, and value-format mismatches between vCon ecosystem repositories and the authoritative IETF spec ([draft-ietf-vcon-vcon-core](https://github.com/ietf-wg-vcon/draft-ietf-vcon-vcon-core)).

Use this doc before copying field names from another repo's source or database schema. When generating vCon JSON, always use the spec-correct name — even when the originating repo uses a legacy name internally.

## Open Issues

_None at this time._

## Closed Issues

| Repo | Incorrect symbol | Correct spec name | Impact | Source | Fixed in | Fixed on |
|---|---|---|---|---|---|---|
| vcon-lib | `appended` (Python attribute + serialized field) | `amended` | Spec-breaking when writing vCon JSON; external consumers reject | commit `a33d3cc` | vcon-lib `8461f2d` (v0.9.1) | 2026-04-16 |
| vcon-lib | `must_support` (Python attribute + serialized field) | `critical` | Spec-breaking when writing vCon JSON; critical-extension semantics lost | commit `a33d3cc` | vcon-lib `8461f2d` (v0.9.1) | 2026-04-16 |
| vcon-mcp | `appended` (Supabase DB column + TypeScript type) | `amended` | Spec-breaking when writing vCon JSON; external consumers reject | commit `a33d3cc` | migration `20251120150100_field_renames.sql`; `src/tools/handlers/schema.ts:34-44` | 2025-11-20 (DB) / current main (TS) |
| vcon-mcp | `must_support` (Supabase DB column + TypeScript type) | `critical` | Spec-breaking when writing vCon JSON; critical-extension semantics lost | commit `a33d3cc` | migration `20251120150100_field_renames.sql`; `src/tools/handlers/schema.ts:34-44` | 2025-11-20 (DB) / current main (TS) |
| vcon-lib | `content_hash` **value format**: unprefixed, padded base64url SHA-256 (e.g. `JBze…SvM_MU=`) | `sha512-<base64url, no padding>` (algorithm-prefixed SHA-512) | Spec-breaking external-media integrity hash; `vcon-js` v0.4.0 validator and strict parsers reject; `is_external_data_changed()` also recomputed the wrong algorithm on verify | `Dialog` helpers in `src/vcon/dialog.py` (2026-06-04) | vcon-lib v0.9.5 — `compute_content_hash()` in `src/vcon/dialog.py`; legacy unprefixed SHA-256 still verifies | 2026-06-04 |

Both legacy field names (`appended`, `must_support`) were inherited from an older vCon *container* draft that predated the current core spec. vcon-lib has renamed them at the API and serialization layer (v0.9.1). vcon-mcp added the spec-correct columns (`critical`, `amended`) alongside the legacy ones; the old columns are marked DEPRECATED via column comments and remain readable through the `vcons_legacy` view for back-compat. Removal of the legacy columns is pending a future migration.

## How to Add an Entry

When you discover a new field-name or symbol mismatch:

1. **Verify against the authoritative draft.** Open the relevant section of `draft-ietf-vcon-vcon-core` and confirm the spec name. Link: https://github.com/ietf-wg-vcon/draft-ietf-vcon-vcon-core
2. **Locate the wrong symbol.** Grep the target repo; record the exact `file:line` citation.
3. **Add a row.** Fill Repo, Incorrect symbol, Correct spec name, Impact (one line — what breaks), Status (`Open` / `In progress` / `Fix pending review`), Source (speckit line or commit hash that first flagged it).
4. **Mention in CHANGELOG.md.** Dated entry under the current date.

## How to Close an Entry

When a fix lands in the target repo:

1. Move the row from **Open Issues** to **Closed Issues**.
2. Append two columns to the closed row: **Fixed in** (commit hash or PR URL) and **Fixed on** (date).
3. Update CHANGELOG.md.

## Why this doc exists

AI code generators and new contributors read sibling repos to infer conventions. Without this tracker, legacy field names propagate. The entries here are the "don't copy this" list.
