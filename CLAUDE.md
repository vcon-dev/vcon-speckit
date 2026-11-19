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
- **Inline body:** with `encoding: "json"`, `body` is any JSON value (analysis, attachment, dialog). With `base64url` or `none` it is a string.
- **Attachment:** field is `purpose`, **not** `type` (in core). `party` is REQUIRED, and at least one of `dialog` or `session` MUST be present. Use `party: 0, dialog: 0` for vCon-level attachments.
- **External references:** MUST have both `url` and `content_hash` (format: `sha512-` + Base64Url of SHA-512 digest).
- **Timestamps:** ISO 8601 with timezone (`Z` suffix or explicit offset).
- **`redacted_from`** and **`amended_from`** (core-04 names: `redacted`, `amended`) are mutually exclusive. `group` is reserved, do not use.
- **Sessions:** if `dialog` or `events` has any Objects, `sessions` MUST have at least one Session. Dialog `parties` are contributors only; receivers go in the Session `parties`. `originator` lives on the Session.
- **Events:** join, drop, hold, mute, key and transfer activity is an Event Object (`type`, `time`, `sessions`, `party`). `party_history` and the `transfer` and `recording-set` Dialog types are gone.
- **Signing:** JWS `alg` MUST NOT be `"none"`.
- **Extensions:** `extensions` MUST list every extension used; every `critical` entry MUST also be listed in `extensions`.

## Legacy field names (historical)

`vcon-lib` 1.0.3+ (`vcon` on PyPI) implements core-05: it writes `"0.5.0"`, Sessions and Events, and migrates 0.4.0 input on load. `vcon-js` (v0.3.0+) and `vcon-mcp` (current main) use the core-04 spec names, `amended` and `critical`, and have not yet been ported to core-05, so they still write `"0.4.0"`, `amended`/`redacted`, Dialog `originator`, `party_history` and `transfer` dialogs; when generating core-05 output, use the core-05 names above. If you encounter `appended` or `must_support` in older code branches, pre-v0.9.1 vcon-lib output, vcon-mcp's deprecated `must_support`/`appended` columns, or the `vcons_legacy` view, translate to the spec name when writing vCon JSON out. See [SPEC_COMPLIANCE_ISSUES.md](SPEC_COMPLIANCE_ISSUES.md) (Closed) for details.

## Spec target

IETF `draft-ietf-vcon-vcon-core-05` ([authoritative repo](https://github.com/ietf-wg-vcon/draft-ietf-vcon-vcon-core)). The Appendix C JSON Schema and Appendix D CDDL are vendored verbatim in [schema/](schema/). The schema covers the unsigned form only.

The `vcon` syntax parameter value is `"0.5.0"` (was `"0.4.0"` in core-04). It is a syntax parameter, not the draft identifier, and is deprecated as of RFC publication but retained for parser compatibility. When in doubt, refer to the banner at the top of the speckit and usage guide.

## How this kit is meant to be consumed

- For broad code-generation tasks: load the full speckit (it is sized to fit a single context window).
- For focused tasks: load CLAUDE.md + the one specific doc listed in the table above.
- For spec validation of a proposed vCon structure: load the speckit §vCon Data Model + GLOSSARY.md.
- For reviewing code that writes vCon JSON: load the speckit + SPEC_COMPLIANCE_ISSUES.md.
