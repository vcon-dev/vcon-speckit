# Changelog

Dated entries for spec-alignment and doc-structure changes in the vCon Ecosystem Spec Kit. Entries are reverse-chronological.

Commit hashes reference this repository.

---

## 2026-05-07 (later)

- Reframed the Repository Map in [vcon-ecosystem-speckit.md §1](vcon-ecosystem-speckit.md#1-ecosystem-overview) to cover **all public repositories in the [vcon-dev](https://github.com/vcon-dev) GitHub organization**, grouped by purpose (core libraries; server/MCP/tooling; capture adapters; transcription/WTF; storage/sync; search/analytics; privacy/SCITT; test data; documentation; upstream forks). Each row now links to the GitHub repo.
- Removed references to closed-source / out-of-scope deployments that previously appeared in the map and other docs: `conserver-extras`, `vfun`, `portal`, `conserver-pipeline-config`, `laptop-vcon-adapter` (the public vcon-dev equivalent is `vcon-laptop`), `vcon-fadapter`, `vcon-pdf-adapter`. Added a one-line note explaining that proprietary GPU transcription, SaaS analytics portals, and internal pipeline configs are out of scope for code generation against this kit.
- Expanded the IETF Standards Documents table to include all draft repositories that live in vcon-dev (`draft-howe-vcon-wtf-extension`, `draft-howe-vcon-mcp-session`, `draft-howe-vcon-sip-signaling`, `draft-howe-sipcore-mcp-extension`, `draft-ietf-vcon-vcon-overview`).
- Removed the SvelteKit/portal subsection from §4 Technology Stack Reference, the `vfun` GPU service from the §12 Docker Compose example, and the `Portal: Auth0 with JWT tokens` line from §14 Security/Authentication.
- Updated companion docs to match: trimmed [README.md](README.md) Repository Map, [CONTRIBUTING.md](CONTRIBUTING.md) "Where to Contribute" table, [DEVELOPER_QUICK_REFERENCE.md](DEVELOPER_QUICK_REFERENCE.md) Stack-by-Repo (added vcon-js, removed portal), and [GLOSSARY.md](GLOSSARY.md) (removed `Portal` term; broadened `WTF` to mention public producers).
- In [VCON_USAGE_GUIDE.md](VCON_USAGE_GUIDE.md): removed the `vcon-fadapter` and `vcon-pdf-adapter` adapter sections; renamed `laptop-vcon-adapter` to `vcon-laptop`; rewrote the "Claude / LLM Analysis Link" and "Redaction Link" subheads as generic vcon-server link patterns (no `(conserver-extras)` parenthetical), pointing readers at [vcon-sample-link](https://github.com/vcon-dev/vcon-sample-link) as a public reference implementation.

## 2026-05-07

- Re-verified the spec kit against shipped library releases:
  - **vcon-lib v0.9.1** (commit `8461f2d`, 2026-04-16): `appended` → `amended`, `must_support` → `critical` at the API and serialization layer; Dialog `mimetype` → `mediatype`; Dialog dropped `alg`/`signature` in favor of `content_hash`; Attachment `type` → `purpose`; Analysis and Attachment gained `product`, `mediatype`, `filename`, `url`, `content_hash`; valid Attachment encodings now include `json` (replacing `base64`); `session_id` accepts `Dict | List[Dict]`.
  - **vcon-js v0.3.0** (commit `87a1e15`, 2026-04-16): `VCON_VERSION` bumped to `'0.4.0'`; `SessionId` restructured to `{local, remote}` per RFC 7989 §5; `content_hash` is `string | string[]` everywhere; `PartyHistory.button` for DTMF; Dialog `message_id`; Redacted/Amended `type`/`url`/`content_hash`; `Analysis.type` valid values documented.
  - **vcon-mcp** has migrated its Supabase schema (`supabase/migrations/20251120150100_field_renames.sql`) and TypeScript schema (`src/tools/handlers/schema.ts`) to spec-correct `critical` and `amended`. Legacy columns (`must_support`, `appended`) remain as deprecated and are exposed via the `vcons_legacy` view for back-compat.
- Closed both rows in [SPEC_COMPLIANCE_ISSUES.md](SPEC_COMPLIANCE_ISSUES.md): `appended` → `amended` and `must_support` → `critical`. Tracker now lists no Open issues.
- Updated [vcon-ecosystem-speckit.md](vcon-ecosystem-speckit.md) `CREATE TABLE vcons` example to show `critical`/`amended` as the canonical columns, with a one-line note that the legacy columns persist as deprecated.
- Softened the cross-references in [GLOSSARY.md](GLOSSARY.md) for `amended` and `critical extension` from "incorrectly named" to a brief history note.
- Trimmed the "Don't copy these from sibling repos" section in [CLAUDE.md](CLAUDE.md) now that the legacy field names no longer ship in the canonical libraries.
- Re-stamped the **Last reviewed** banner in [vcon-ecosystem-speckit.md](vcon-ecosystem-speckit.md) and [VCON_USAGE_GUIDE.md](VCON_USAGE_GUIDE.md) to 2026-05-07.

## 2026-04-16

- Added [CLAUDE.md](CLAUDE.md) as the AI-assistant entry point: task-to-doc routing plus non-negotiable spec rules.
- Added [GLOSSARY.md](GLOSSARY.md) with ~30 vCon ecosystem terms and pointers to canonical sections.
- Added [SPEC_COMPLIANCE_ISSUES.md](SPEC_COMPLIANCE_ISSUES.md) tracking known field-name bugs across sibling repos. Seed rows: vcon-mcp `appended`→`amended`, `must_support`→`critical`.
- Added banner block to [vcon-ecosystem-speckit.md](vcon-ecosystem-speckit.md) and [VCON_USAGE_GUIDE.md](VCON_USAGE_GUIDE.md) pinning the target IETF draft (`draft-ietf-vcon-vcon-core-02`) and disambiguating it from the `vcon: "0.4.0"` syntax parameter.
- Expanded [.vcon-repo/graph.json](.vcon-repo/graph.json) from a 1-node stub into a full knowledge graph: all spec-kit documents, the 12 ecosystem repositories from the speckit Repository Map, and the IETF spec node, connected by `references` / `describes` / `implements` / `tracks_bugs_in` edges. `archetype` changed from `minimal` to `spec-kit`.
- Cleaned up [.vcon-repo/README.md](.vcon-repo/README.md): dropped stale references to a non-existent `annotations.yaml` and to an external generator script that doesn't live in this repo.
- De-duplicated Tags-attachment text in [DEVELOPER_QUICK_REFERENCE.md](DEVELOPER_QUICK_REFERENCE.md); it now cross-references the canonical definition in the speckit and the glossary entry.
- Initial graph stub committed earlier in the day (commit `4655e47`).

## 2026-03-26

- Added [VCON_USAGE_GUIDE.md](VCON_USAGE_GUIDE.md) (878 lines) covering domain profiles, feature usage guidelines, and app-specific output schemas for adapters and processing links (commit `a33d3cc`).
- Spec-alignment fixes in [vcon-ecosystem-speckit.md](vcon-ecosystem-speckit.md):
  - Removed `group` from core structure (reserved for future extension — do not use).
  - Separated extension-only Party fields (`jcard`, `timezone`, `role`, `meta`) from core fields with explicit EXTENSION ONLY labels.
  - Flagged vcon-mcp spec bugs: DB columns `appended` and `must_support` should be `amended` and `critical` per the core draft.
  - Updated spec reference URL to the authoritative repo (`ietf-wg-vcon/draft-ietf-vcon-vcon-core`).
- Added [VCON_USAGE_GUIDE.md](VCON_USAGE_GUIDE.md) to the Document Index in [README.md](README.md).
- Added a usage-pattern quick-lookup table to [DEVELOPER_QUICK_REFERENCE.md](DEVELOPER_QUICK_REFERENCE.md).

## Earlier

Initial spec-kit scaffold: `README.md`, `vcon-ecosystem-speckit.md`, `CODE_STYLE_GUIDE.md`, `CONTRIBUTING.md`, `DEVELOPER_QUICK_REFERENCE.md`.
