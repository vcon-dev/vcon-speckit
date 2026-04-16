# AI Assistant Guide

This repo is a documentation-only spec kit. It exists to be loaded as context when AI assistants generate, review, or maintain code across the vCon ecosystem. Read this file first.

## Pick the right doc by task

| Task | Read first |
|---|---|
| Generating code — links, adapters, MCP tools, storage backends | [vcon-ecosystem-speckit.md](vcon-ecosystem-speckit.md) |
| Designing a vCon for a specific domain (contact center, healthcare, sales, support, legal) | [VCON_USAGE_GUIDE.md](VCON_USAGE_GUIDE.md) |
| Formatting, naming, linting, per-language conventions | [CODE_STYLE_GUIDE.md](CODE_STYLE_GUIDE.md) |
| Contributing, PR checklist, branch strategy | [CONTRIBUTING.md](CONTRIBUTING.md) |
| Quick field/pattern lookup while coding | [DEVELOPER_QUICK_REFERENCE.md](DEVELOPER_QUICK_REFERENCE.md) |
| Defining or disambiguating a vCon ecosystem term | [GLOSSARY.md](GLOSSARY.md) |
| Avoiding known spec-compliance bugs in sibling repos | [SPEC_COMPLIANCE_ISSUES.md](SPEC_COMPLIANCE_ISSUES.md) |
| What changed recently in the spec kit | [CHANGELOG.md](CHANGELOG.md) |

## Non-negotiables (do not regenerate bugs)

These are the most common spec-compliance mistakes. Full list: [speckit §Critical Spec Compliance Rules](vcon-ecosystem-speckit.md#critical-spec-compliance-rules).

- **Analysis:** field is `schema`, **never** `schema_version`. `vendor` is REQUIRED on every analysis object.
- **Analysis body:** always a string type, even when the content is JSON (pair with `encoding: "json"`).
- **Attachment:** field is `purpose`, **not** `type` (in core). `party` and `dialog` indices are REQUIRED. Use `party: 0, dialog: 0` for vCon-level attachments.
- **External references:** MUST have both `url` and `content_hash` (format: `sha512-` + Base64Url of SHA-512 digest).
- **Timestamps:** ISO 8601 with timezone (`Z` suffix or explicit offset).
- **`redacted`** and **`amended`** are mutually exclusive. `group` is reserved — do not use.

## Don't copy these from sibling repos

The vcon-mcp repo has two legacy field names inherited from an older vCon container draft. **Do not propagate them into generated vCon JSON or new code elsewhere in the ecosystem.** See [SPEC_COMPLIANCE_ISSUES.md](SPEC_COMPLIANCE_ISSUES.md) for details.

- vcon-mcp DB/TS uses `appended` — spec name is **`amended`**.
- vcon-mcp DB/TS uses `must_support` — spec name is **`critical`**.

If reading from the vcon-mcp database, translate at the boundary when writing vCon JSON out.

## Spec target

IETF `draft-ietf-vcon-vcon-core-02` ([authoritative repo](https://github.com/ietf-wg-vcon/draft-ietf-vcon-vcon-core)).

The `vcon` syntax parameter value is `"0.4.0"` — this is a syntax parameter, not the draft identifier, and it is deprecated in draft-02 but retained for parser compatibility. When in doubt, refer to the banner at the top of the speckit and usage guide.

## How this kit is meant to be consumed

- For broad code-generation tasks: load the full speckit (it is sized to fit a single context window).
- For focused tasks: load CLAUDE.md + the one specific doc listed in the table above.
- For spec validation of a proposed vCon structure: load the speckit §vCon Data Model + GLOSSARY.md.
- For reviewing code that writes vCon JSON: load the speckit + SPEC_COMPLIANCE_ISSUES.md.
