# vCon Ecosystem Glossary

One-line definitions with pointers to the canonical section. When a term is defined in detail elsewhere in the spec kit, the definition here is the short form and the pointer is authoritative.

## Core Data Model

**vCon** — Virtual Conversation. An IETF-standardized container object for the content, metadata, and analyses of a conversation. See [speckit §vCon Data Model](vcon-ecosystem-speckit.md#2-vcon-data-model-canonical-reference).

**dialog** — A single conversation segment: a `recording`, `text`, `transfer`, or `incomplete`. The array in a vCon can hold many dialogs, and cross-dialog linkage is expressed via transfer dialogs. See [speckit §Dialog Object](vcon-ecosystem-speckit.md#dialog-object).

**party** — A participant in one or more dialogs. Identified by `tel`, `sip`, `mailto`, `did`, or `uuid`; may also carry name, validation, location. See [speckit §Party Object](vcon-ecosystem-speckit.md#party-object).

**analysis** — Machine-produced output derived from one or more dialogs (transcript, summary, sentiment, translation, etc.). MUST include `vendor`. See [speckit §Analysis Object](vcon-ecosystem-speckit.md#analysis-object).

**attachment** — Out-of-band data associated with a vCon, party, or dialog (documents, tags, lawful-basis records). `purpose`, `party`, and `dialog` are required. See [speckit §Attachment Object](vcon-ecosystem-speckit.md#attachment-object).

**tags** — Key-value metadata stored as an attachment with `purpose: "tags"` and JSON-encoded body. Use `party: 0, dialog: 0` for vCon-level tags. See [speckit §Tags Convention](vcon-ecosystem-speckit.md#tags-convention).

**redacted** — A pointer on a vCon indicating it is a less-detailed derivative of a prior vCon (e.g. PII stripped for LLM processing). Mutually exclusive with `amended`.

**amended** — A pointer on a vCon indicating it extends a prior vCon (e.g. appending new analysis). Mutually exclusive with `redacted`. **Note:** vcon-mcp DB column is incorrectly named `appended`; see [SPEC_COMPLIANCE_ISSUES.md](SPEC_COMPLIANCE_ISSUES.md).

**content_hash** — Integrity hash for externally referenced content. Format: `sha512-` + Base64Url of the SHA-512 digest. Required whenever `url` is used.

**extension** — A named set of additional fields or semantics layered on top of core vCon. Listed in the top-level `extensions` array. See [speckit §Extension System](vcon-ecosystem-speckit.md#8-extension-system).

**critical extension** — An extension listed in the `critical` array. Implementations that do not support a critical extension MUST refuse to process the vCon. **Note:** vcon-mcp DB column is incorrectly named `must_support`; see [SPEC_COMPLIANCE_ISSUES.md](SPEC_COMPLIANCE_ISSUES.md).

**UUIDv8** — Version 8 UUID (timestamp-based with FQHN hash). Preferred UUID format for vCons. See [speckit §UUID Convention](vcon-ecosystem-speckit.md#uuid-convention).

**ISO 8601** — Timestamp format used throughout the spec. MUST include timezone (`Z` or explicit offset). See [speckit §Timestamp Convention](vcon-ecosystem-speckit.md#timestamp-convention).

## Pipeline Concepts

**link** — A stateless processor in vcon-server that reads a vCon from Redis, modifies it, and writes it back. Implements `async def run(vcon_uuid, link_config) -> str | None`. See [speckit §Processing Link Development](vcon-ecosystem-speckit.md#9-processing-link-development).

**chain** — An ordered sequence of links executed against an ingested vCon. Configured per-deployment in pipeline YAML.

**ingress** — The entry point where external sources (SIPREC, audio files, screen captures, fax) become vCons. Handled by adapters.

**adapter** — A service that watches a source (file system, SIP recorder, screen/audio capture) and produces vCons on the ingress side. See [speckit §Adapters (Ingress Pattern)](vcon-ecosystem-speckit.md#34-adapters-ingress-pattern).

**redaction** — The process of producing a `redacted` copy of a vCon with PII stripped before sending to external LLM providers. Original is preserved.

**DLQ** — Dead Letter Queue. Redis queue where fatally errored vCons are pushed for manual review; non-fatal errors return `None` to stop the chain instead.

**VconRedis** — Python helper in vcon-server providing `get_vcon()` / `set_vcon()` over Redis-backed vCon storage.

**Conserver** — Colloquial name for vcon-server (the conversation-server processing pipeline).

**Portal** — SvelteKit analytics web UI for browsing vCons, running AI chat, and managing users.

**MCP tool** — A named tool exposed by vcon-mcp via Model Context Protocol. Input validated with Zod; errors returned via `McpError`. See [speckit §vcon-mcp MCP Tools](vcon-ecosystem-speckit.md#vcon-mcp-mcp-tools-30).

## Telephony & Identity

**SIPREC** — SIP Recording (RFC 7866). The metadata/media format emitted by carrier session recorders; ingested by vcon-siprec-adapter.

**STIR/SHAKEN** — Telephony caller-ID authentication framework. `stir` on a Party carries a PASSporT JWS Compact token.

**PASSporT** — Persona Assertion Token (RFC 8225). Signed assertion binding a caller identity to an originating call.

**PIDF-LO** — Presence Information Data Format — Location Object. Format for `gmlpos` location on a Party.

**GEOPRIV** — IETF geographic privacy WG convention. `civicaddress` on a Party uses GEOPRIV field names (country, a1, a2, …).

## Extensions & Formats

**lawful basis** — GDPR lawful-basis extension ([draft-howe-vcon-lawful-basis](https://datatracker.ietf.org/doc/draft-howe-vcon-lawful-basis/)). Carries consent / legal-obligation metadata. See [speckit §Lawful Basis Extension](vcon-ecosystem-speckit.md#lawful-basis-extension-gdpr-compliance).

**WTF** — World Transcription Format. Compact transcription schema produced by vcon-mac-wtf and vfun. See [speckit §WTF Extension](vcon-ecosystem-speckit.md#wtf-extension-world-transcription-format).

**JWS** — JSON Web Signature. A signed vCon is wrapped as JWS General JSON Serialization.

**JWE** — JSON Web Encryption. An encrypted vCon is a JWE whose plaintext is a signed vCon.

**Zod** — TypeScript schema validator used in vcon-mcp for MCP tool input validation.

**base64url** — URL-safe base64 (no padding). Encoding for binary inline content in `body`.
