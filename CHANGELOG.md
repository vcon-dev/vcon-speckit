# Changelog

Dated entries for spec-alignment and doc-structure changes in the vCon Ecosystem Spec Kit. Entries are reverse-chronological.

Commit hashes reference this repository.

---

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
