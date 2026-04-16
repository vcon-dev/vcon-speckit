# Known Spec-Compliance Issues

Tracker of known field-name and symbol mismatches between vCon ecosystem repositories and the authoritative IETF spec ([draft-ietf-vcon-vcon-core](https://github.com/ietf-wg-vcon/draft-ietf-vcon-vcon-core)).

Use this doc before copying field names from another repo's source or database schema. When generating vCon JSON, always use the spec-correct name — even when the originating repo uses a legacy name internally.

## Open Issues

| Repo | Incorrect symbol | Correct spec name | Impact | Status | Source |
|---|---|---|---|---|---|
| vcon-mcp | `appended` (Supabase DB column + TypeScript type) | `amended` | Spec-breaking when writing vCon JSON; external consumers reject | Open | [speckit L160](vcon-ecosystem-speckit.md), commit `a33d3cc` |
| vcon-mcp | `must_support` (Supabase DB column + TypeScript type) | `critical` | Spec-breaking when writing vCon JSON; critical-extension semantics lost | Open | [speckit L161](vcon-ecosystem-speckit.md), commit `a33d3cc` |

Both vcon-mcp field names were inherited from an older vCon *container* draft that predated the current core spec. The internal storage column names are preserved for now, but any code that writes vCon JSON out — MCP tools, exports, SCITT payloads — MUST translate at the boundary.

## Closed Issues

_None yet. Closed entries move here with the commit that fixed them and the date._

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
