# vCon Ecosystem — Spec Kit for Code Generation & Maintenance

> **Spec target:** IETF `draft-ietf-vcon-vcon-core-05`. The `vcon` syntax parameter is `"0.5.0"` (deprecated as of RFC publication; retained for parser compatibility). Vendored schema and CDDL: [schema/](schema/). Core-05 rules were taken from the core-04 to core-05 change analysis; the narrative below was last fully re-verified against core-02 and is being brought forward. Where this kit and the schema disagree, the schema and the draft text win.

> Use this document as context when generating, reviewing, or maintaining code across the vCon ecosystem. It captures architecture, conventions, data models, integration points, and standards compliance requirements.

---

## 1. Ecosystem Overview

The vCon ecosystem implements the IETF vCon (Virtual Conversation) standard across a set of interconnected repositories. Together, they provide end-to-end conversation capture, processing, analysis, storage, and privacy-compliant access.

### Repository Map

This map covers the public repositories in the [`vcon-dev`](https://github.com/vcon-dev) GitHub organization (the canonical open-source ecosystem). Forked upstream projects are listed at the end of this subsection for reference. The proprietary [`VCONIC`](https://github.com/VCONIC) organization repositories — commercial conserver builds, customer deployments, apps, and ops tooling — are listed separately under "VCONIC repositories" below; they build on the open-source ecosystem but are not open source.

#### Core libraries (canonical implementations)

| Repository | Language | Purpose |
|---|---|---|
| **[vcon-lib](https://github.com/vcon-dev/vcon-lib)** | Python 3.12+ | Canonical vCon object library |
| **[vcon-js](https://github.com/vcon-dev/vcon-js)** | TypeScript | TypeScript vCon library |
| **[pydantic-vcon](https://github.com/vcon-dev/pydantic-vcon)** | Python | Pydantic models for vCon validation |

#### Server, MCP, and tooling

| Repository | Language | Purpose |
|---|---|---|
| **[vcon-server](https://github.com/vcon-dev/vcon-server)** | Python 3.12+ | Event-driven processing pipeline with pluggable links and storage backends |
| **[vcon-mcp](https://github.com/vcon-dev/vcon-mcp)** | TypeScript 5.9+ | Model Context Protocol server for AI agent access to vCon data |
| **[vcon-server-cli](https://github.com/vcon-dev/vcon-server-cli)** | — | CLI to manage vcon-server Docker containers |
| **[vcon-admin](https://github.com/vcon-dev/vcon-admin)** | — | Admin interface for vCon operators |
| **[vcon-app-template](https://github.com/vcon-dev/vcon-app-template)** | — | Generic application template for vCon stores |
| **[vcon-info](https://github.com/vcon-dev/vcon-info)** | — | vcon.info site |
| **[vcon-desk-viewer](https://github.com/vcon-dev/vcon-desk-viewer)** | — | Desktop vCon viewer |
| **[vscode-vcon-viewer](https://github.com/vcon-dev/vscode-vcon-viewer)** | — | VS Code extension for inspecting vCon files |
| **[vcon-zip](https://github.com/vcon-dev/vcon-zip)** | — | VCON ZIP implementation |
| **[vcon-mcp-proxy](https://github.com/vcon-dev/vcon-mcp-proxy)** | — | Proxy that captures MCP sessions as vCons and posts them to a conserver |
| **[vcon-mcp-adapters](https://github.com/vcon-dev/vcon-mcp-adapters)** | — | Convert AI-agent framework traces into vCon containers with MCP session data; CLI for post-hoc conversion plus SDK |

#### Capture / ingest adapters

| Repository | Language | Purpose |
|---|---|---|
| **[vcon-siprec-adapter](https://github.com/vcon-dev/vcon-siprec-adapter)** | Python | SIPREC (RFC 7866) sessions → vCons |
| **[vcon-twilio-adapter](https://github.com/vcon-dev/vcon-twilio-adapter)** | Python | Twilio recordings → vCons |
| **[vcon-telephony-adapters](https://github.com/vcon-dev/vcon-telephony-adapters)** | Python | Twilio webhook receiver that creates vCons |
| **[vcon-audio-adapter](https://github.com/vcon-dev/vcon-audio-adapter)** | Python | Directory watcher for audio files; emits vCons |
| **[vcon-eleven-labs-adapter](https://github.com/vcon-dev/vcon-eleven-labs-adapter)** | — | ElevenLabs → vCon adapter |
| **[vcon-laptop](https://github.com/vcon-dev/vcon-laptop)** | Python | Records laptop screen, audio, webcam into vCons |
| **[sippy-conserver-adapter](https://github.com/vcon-dev/sippy-conserver-adapter)** | — | Sippy Soft → vCon |
| **[signalwire_adapter](https://github.com/vcon-dev/signalwire_adapter)** | — | SignalWire → vCon |
| **[vcon-fadapter](https://github.com/vcon-dev/vcon-fadapter)** | Python | Filesystem watcher for fax images; extracts sender/receiver from filenames and creates vCons |
| **[vcon-email-adapter](https://github.com/vcon-dev/vcon-email-adapter)** | — | Convert email (`.eml`, `.mbox`, or folders) into spec-compliant vCons |
| **[vcon-vac-adapter](https://github.com/vcon-dev/vcon-vac-adapter)** | — | Convert AI-agent session transcripts (Claude Code, Anthropic Messages, OpenAI Responses/Agents SDK) into vCons |
| **[vcon-adapter-template](https://github.com/vcon-dev/vcon-adapter-template)** | — | Scaffold for building a new source-platform → vCon adapter |

#### Transcription and WTF (World Transcription Format)

| Repository | Language | Purpose |
|---|---|---|
| **[vcon-mac-wtf](https://github.com/vcon-dev/vcon-mac-wtf)** | Python/FastAPI | MLX Whisper on Apple Silicon; outputs WTF format |
| **[wtf-server](https://github.com/vcon-dev/wtf-server)** | — | Transcription server with NVIDIA NIM ASR (Parakeet/Canary) and WTF output |
| **[wtf-transcript-converter](https://github.com/vcon-dev/wtf-transcript-converter)** | — | Convert transcript JSONs to/from WTF (Whisper, Deepgram, AssemblyAI, Rev.ai, Canary, Parakeet) |
| **[speechmatics-link](https://github.com/vcon-dev/speechmatics-link)** | Python | vcon-server link for Speechmatics transcription with WTF output |

#### Storage and sync

| Repository | Language | Purpose |
|---|---|---|
| **[vcon-s3-loader](https://github.com/vcon-dev/vcon-s3-loader)** | — | S3 loader for vCons |
| **[mongo-redis-sync](https://github.com/vcon-dev/mongo-redis-sync)** | Python | _Archived_ — automatic Redis → MongoDB sync for vCons |

#### Search and analytics (open-source apps)

| Repository | Language | Purpose |
|---|---|---|
| **[conversational_search](https://github.com/vcon-dev/conversational_search)** | Python/Streamlit | Streamlit app to search vCons in Elasticsearch |
| **[conversation_gpt](https://github.com/vcon-dev/conversation_gpt)** | — | _Archived_ — OpenAI Assistants ↔ vCons demo |

#### Privacy, SCITT, and lifecycle

| Repository | Language | Purpose |
|---|---|---|
| **[vcon-right-to-know](https://github.com/vcon-dev/vcon-right-to-know)** | Python/Streamlit | _Archived_ — GDPR Right to Access / Right to Erasure demo |
| **[scittles](https://github.com/vcon-dev/scittles)** | — | SCRAPI-compatible SCITT transparency service backed by SQLite |
| **[scitt-action](https://github.com/vcon-dev/scitt-action)** | — | GitHub Action for the DataTrails SCITT implementation |
| **[vcon-vac](https://github.com/vcon-dev/vcon-vac)** | — | Verifiable Agent Conversations (VAC): vCon extension for agent auditability and interaction traceability |

#### Test data, demos, and examples

| Repository | Language | Purpose |
|---|---|---|
| **[fake-vcons](https://github.com/vcon-dev/fake-vcons)** | — | Synthetic vCons for testing and demos |
| **[vcon_faker](https://github.com/vcon-dev/vcon_faker)** | — | Faker library for generating vCons |
| **[vcon-sample-link](https://github.com/vcon-dev/vcon-sample-link)** | — | Sample external vcon-server link that adds custom attachments |
| **[ietf-meeting-vcons](https://github.com/vcon-dev/ietf-meeting-vcons)** | — | IETF working group sessions as vCons (meetings 110-124, 2021-2025) |
| **[ietf2vcon](https://github.com/vcon-dev/ietf2vcon)** | — | Convert IETF meeting sessions to vCon format |
| **[tadhack-2025](https://github.com/vcon-dev/tadhack-2025)** | — | Synthetic conversations for TADHACK 2025, Island Edition |
| **[vcon-the-hacks](https://github.com/vcon-dev/vcon-the-hacks)** | — | TADHack 2026 hackathon submissions (15 projects with transcripts and summaries) |
| **[load_test](https://github.com/vcon-dev/load_test)** | — | Load testing utilities |
| **[matrix_vcon_emitter](https://github.com/vcon-dev/matrix_vcon_emitter)** | — | Matrix → vCon emitter |

#### Documentation and meta

| Repository | Purpose |
|---|---|
| **[vcon](https://github.com/vcon-dev/vcon)** | Home repo for vCons |
| **[vcon-speckit](https://github.com/vcon-dev/vcon-speckit)** | This kit — spec context for AI code generation |
| **[vcon-docs](https://github.com/vcon-dev/vcon-docs)** | Documentation |
| **[vcon-background-docs](https://github.com/vcon-dev/vcon-background-docs)** | Background documentation |
| **[docs](https://github.com/vcon-dev/docs)** | _Archived_ — superseded placeholder; new docs live in `vcon-docs` and `vcon-background-docs` |
| **[vcon-dev.github.io](https://github.com/vcon-dev/vcon-dev.github.io)** | GitHub Pages for the org |
| **[awesome-vcon](https://github.com/vcon-dev/awesome-vcon)** | Awesome list for vCon |
| **[homebrew-vcon](https://github.com/vcon-dev/homebrew-vcon)** | Homebrew tap for vCon tools |
| **[homebrew-tap](https://github.com/vcon-dev/homebrew-tap)** | Homebrew tap for vcon-dev tools |

#### Upstream forks (vendored / referenced, not vcon-dev–originated)

These are forks the org maintains for reference or local patching; they are upstream projects, not vCon implementations.

| Repository | Upstream purpose |
|---|---|
| **[modelcontextprotocol](https://github.com/vcon-dev/modelcontextprotocol)** | MCP specification and documentation (referenced by vcon-mcp) |
| **[pyVoIP](https://github.com/vcon-dev/pyVoIP)** | Pure-Python VoIP/SIP/RTP library |
| **[rd-apmm-python-lib-rtp](https://github.com/vcon-dev/rd-apmm-python-lib-rtp)** | RTP packet decoding/encoding |
| **[langchain](https://github.com/vcon-dev/langchain)** | LLM composability framework |
| **[whisper](https://github.com/vcon-dev/whisper)** | OpenAI Whisper speech recognition |
| **[TTS](https://github.com/vcon-dev/TTS)** | Coqui TTS deep-learning text-to-speech toolkit |

### VCONIC repositories (proprietary)

These repositories live in the [`VCONIC`](https://github.com/VCONIC) GitHub organization. They are the commercial layer built on top of the open-source ecosystem above: licensed conserver builds, customer deployments, applications, realtime services, and ops tooling. They are not open source and are out of scope for open-source code generation, but are listed here for context and cross-referencing. Where a README was absent, the purpose below is inferred from the repository name and should be verified before relying on it.

#### Conserver, links, and pipeline

| Repository | Purpose |
|---|---|
| **conserver** | VCONIC conserver deployment (no README; verify) |
| **vcon-server-enterprise** | Commercial build of `vcon-server` that enforces a per-vCon license check before processing; same Docker image as open source |
| **conserver-link-check-consent** | vcon-server link that gates a vCon on the validity of its `lawful_basis` attachment |
| **conserver-link-consentify** | vcon-server link that produces a `lawful_basis` attachment on each vCon |
| **conserver-link-consig** | vCon link that processes Consig request attachments and enriches vCons with metadata and analysis |
| **conserver-pipeline-config** | Documentation, config, and Claude Code skills for the pipeline on the Strolid GPU host |
| **vconic-conserver-link-check-consent** | Appears to be a renamed/placeholder copy of `conserver-link-check-consent` (empty README; verify) |
| **vconic-conserver-link-consentify** | Appears to be a renamed/placeholder copy of `conserver-link-consentify` (empty README; verify) |

#### Adapters and capture

| Repository | Purpose |
|---|---|
| **vcon-anthropic-chats** | Convert Claude AI chats into IETF vCons |
| **vconic-adapter-nice** | NICE platform → vCon adapter (no README; verify) |
| **vcon-m5-stack-audio** | Records audio from the M5Stack Core2 microphone and publishes each as a vCon |
| **link-wellsky-records-to-vcons** | Links WellSky records to vCons (empty README; verify) |
| **customer-frontline-wellsky-report-downloader** | WellSky report downloader for the Frontline customer |

#### Applications and portals

| Repository | Purpose |
|---|---|
| **vKong** | vCon-native revenue intelligence platform (a Gong equivalent without the vendor lock-in) |
| **vconic-app-saleshub** | AI sales acceleration platform built on the vCon standard |
| **vconic-app-portal** | White-label SvelteKit portal: conversation analytics, user management, GPT-powered chat |
| **vconic-app-iot-portal** | Full-stack portal (React + Vite frontend, Express backend) managing vCon uploads from IoT recording devices |
| **vconic-r2d2** | Conversational tag manager for vCons; web app on a vcon-mcp server using Claude as the tag engine |
| **vconic-pov-voc** | Six-screen demo built on the Strolid vCon corpus |

#### Realtime

| Repository | Purpose |
|---|---|
| **vcon-realtime** | Self-hosted speech-to-text streaming service with vCon lifecycle support |
| **vconic-realtime-starter-app** | Starter application for the BDS WO4 realtime direction |
| **vconic-realtime-patent** | Realtime patent work (no README; verify) |
| **vconic-core-vfun** | Core vFun component (README is code-only; verify) |

#### Knowledge, docs, and brand

| Repository | Purpose |
|---|---|
| **vconic-docs** | VCONIC knowledge base: what is deployed per customer, platform capabilities, performance |
| **vconic-kb** | VCONIC knowledge base (overlaps with `vconic-docs`; verify which is canonical) |
| **vconic-design-system** | Vconic brand and document/design system |
| **claude-skills** | Shared Claude Code skills for the VCONIC organization |

#### Ops and deployment

| Repository | Purpose |
|---|---|
| **vconic-ops-deploy** | Deployment tooling (no README; verify) |
| **vconic-ops-deployment-manager** | SvelteKit app with Google Workspace OAuth and domain restrictions for deployments |
| **vconic-ops-runbook** | Deployment config, operational scripts, and Claude Code skills for the pipeline |
| **vconic-ops-test-environments** | Test environment definitions (no README; verify) |
| **vconic-performance** | Proprietary performance and intelligence layer for the vCon ecosystem |

#### Data, corpora, and customer deployments

| Repository | Purpose |
|---|---|
| **example-vcons** | Example vCons (no README; verify) |
| **vconic-case-management-conversations** | Fully synthetic test corpus of medical case-management conversations |
| **ai-human-annotation** | AI and human annotation tooling/data (no README; verify) |
| **merkle-tree-viz** | Merkle-tree visualization (no README; verify) |
| **customer-bds** | BDS customer pipeline config, docs, and Claude Code skills |
| **customer-bds-recordings** | BDS customer recordings (no README; verify) |
| **customer-dt** | Deutsche Telekom POC deployment config, documentation, and operational scripts |

### IETF Standards Documents

| Document | Status | Scope |
|---|---|---|
| **[draft-ietf-vcon-vcon-core](https://github.com/ietf-wg-vcon/draft-ietf-vcon-vcon-core)** | Working Group Draft | Core vCon specification (authoritative) |
| **[draft-ietf-vcon-vcon-overview](https://github.com/vcon-dev/draft-ietf-vcon-vcon-overview)** | Working Group Draft | vCon — conversational data exchange overview |
| **[draft-ietf-vcon-privacy-primer](https://github.com/vcon-dev/draft-ietf-vcon-privacy-primer)** | Working Group Draft | Privacy guidance for developers |
| **[draft-howe-vcon-lawful-basis](https://github.com/vcon-dev/draft-howe-vcon-lawful-basis)** | Individual Draft | GDPR lawful basis extension |
| **[draft-howe-vcon-lifecycle](https://github.com/vcon-dev/draft-howe-vcon-lifecycle)** | Individual Draft | Lifecycle management via SCITT |
| **[draft-howe-vcon-wtf-extension](https://github.com/vcon-dev/draft-howe-vcon-wtf-extension)** | Individual Draft | World Transcription Format extension |
| **[draft-howe-vcon-agent-session](https://github.com/vcon-dev/draft-howe-vcon-agent-session)** | Individual Draft | vCon Agent Session extension (successor to the earlier MCP-session draft) |
| **[draft-howe-vcon-provenance](https://github.com/vcon-dev/draft-howe-vcon-provenance)** | Individual Draft | vCon generation provenance extension |
| **[draft-howe-vcon-sip-signaling](https://github.com/vcon-dev/draft-howe-vcon-sip-signaling)** | Individual Draft | SIP signaling and STIR/SHAKEN data |
| **[draft-howe-sipcore-mcp-extension](https://github.com/vcon-dev/draft-howe-sipcore-mcp-extension)** | Individual Draft | SIPcore MCP extension |
| **[draft-birkholz-verifiable-agent-conversations](https://datatracker.ietf.org/doc/draft-birkholz-verifiable-agent-conversations/)** | Individual Draft (external) | Verifiable Agent Conversations; basis for the `vcon-vac` extension |

---

## 2. vCon Data Model (Canonical Reference)

All repositories MUST conform to this data model. When in doubt, defer to the IETF spec (`draft-ietf-vcon-vcon-core`).

### Core Structure

```json
{
  "vcon": "0.5.0",
  "uuid": "string (UUID)",
  "created_at": "ISO 8601 datetime (REQUIRED)",
  "updated_at": "ISO 8601 datetime (optional)",
  "subject": "string (optional)",
  "extensions": ["string (extension names; MUST be present if any extension is used)"],
  "critical": ["string (incompatible extension names; MUST be present if any incompatible extension is used)"],
  "redacted_from": { "uuid": "string", "type": "string", "url": "string", "content_hash": "string (sha512-base64url)" },
  "amended_from": { "uuid": "string", "url": "string", "content_hash": "string" },
  "redacted_strings": [],
  "sessions": [],
  "parties": [],
  "events": [],
  "dialog": [],
  "analysis": [],
  "attachments": []
}
```

**Notes (core-05)**:
- `uuid` and `created_at` are the only top-level parameters the JSON Schema requires. `vcon` is optional there, with the constant `"0.5.0"`. It is DEPRECATED as of RFC publication; write it as `"0.5.0"` for now. Never write `"0.4.0"` in new output.
- `extensions`: MUST list every extension that defines any parameter, Object or parameter value not in the core schema (core-04 said SHOULD). Every name in `critical` MUST also be in `extensions`.
- `critical`: incompatible extensions. An implementation that does not recognize or support a listed extension MUST NOT process the vCon except to reject it or report unsupported content.
- `redacted_from` (renamed from `redacted`) and `amended_from` (renamed from `amended`) are mutually exclusive; the JSON Schema rejects a vCon that has both. `group` is reserved; do not use. `redacted_from` references a prior, less redacted vCon; `amended_from` references the prior vCon instance version (original, redacted, or previously amended) to which the amendments were applied. A new vCon instance version MUST be created for an amendment. If `redacted_from` has a `url`, `content_hash` MUST be included and access to the URL MUST be restricted.
- `sessions`: at least one Session Object MUST exist if `dialog` or `events` contains Objects (the JSON Schema enforces this with a non-empty `sessions` check).
- `redacted_strings`: new. Substitutes `__REDACTED_REF_<n>` in text for each redacted substring; an entry is required for every unique substring redacted, with no more than 99999 per vCon.

**vCon forms**: Unsigned (top-level object), signed (JWS General JSON Serialization with payload, signatures), encrypted (JWE with signed vCon as plaintext). Core-05 adds `sessions` and `events` to the members that identify the unsigned form. The JWS `alg` parameter MUST NOT be `"none"`; it MUST be present in either the JWS Protected Header or the Header Object (SHOULD be the protected header), and a header parameter MUST NOT appear in both. The JWS Header Object MUST include at least one of `x5c` or `x5u`. The JWE `alg` and `enc` MUST each appear in exactly one permitted header location. Externally referenced files MUST use HTTPS and include `content_hash`. Inline files MUST have `body` and `encoding` (`base64url`, `json`, `none`, or a value defined in an extension).

The JSON Schema in [schema/](schema/) validates the unsigned form only. Validate a signed vCon through its decoded payload.

### Session Object

A Session scopes dialog and events and can group them into sub-conversations. A simple conversation has one Session referencing all of its Dialogs.

```json
{
  "start": "ISO 8601 datetime (SHOULD be present)",
  "duration": "number (seconds; absent means the session has no end)",
  "parties": "UnsignedInt[] (the RECEIVERS; SHOULD be present)",
  "originator": "number (party index; only when the first party in parties is NOT the originator)",
  "session_id": "SessionId ({local, remote})",
  "sessions": "Session_Reference[] ({session, type, description?})",
  "dialog": "UnsignedInt[] (indices into dialog)"
}
```

A Session Object with no parameters is permitted. All Dialog Objects SHOULD be referenced by at least one Session.

### Event Object

An event is a point in time. A period such as being on hold is a pair of events, not a duration. Event Objects may appear in any order; `time` gives the sequence.

```json
{
  "type": "join | drop | hold | unhold | mute | unmute | keydown | keyup | consultation-start | blind-transfer | consultative-transfer | transfer-abandoned | channel-contributors | (extension value)",
  "time": "ISO 8601 datetime (REQUIRED)",
  "sessions": "UnsignedInt | UnsignedInt[] (sessions the event applies to)",
  "party": "number (party index; the Transferor for transfer types)",
  "dialog": "number (SHOULD NOT be present for party and key events; required for channel-contributors)",
  "button": "string (keydown/keyup)",
  "channel": "number", "contributors": "UnsignedInt[]",
  "transferee": "number", "transfer_target": "number | number[]",
  "consultation": "number | number[]", "target_session": "number"
}
```

`sessions`, `party`, and `dialog` rules vary by event type; see the parameter-applicability table in section 4.3.1.5 of the draft and the Event definition in [schema/vcon_json_schema.json](schema/vcon_json_schema.json). Events replace Dialog `party_history` and the `transfer` Dialog type.

### Party Object

```json
{
  "tel": "string (E.164, tel: prefix optional)",
  "sip": "string (SIP URI)",
  "stir": "string (PASSporT JWS Compact)",
  "mailto": "string (mailto: prefix optional)",
  "name": "string (display name; 'anonymous' for unknown)",
  "did": "string (Decentralized Identifier)",
  "validation": "string (SHOULD provide if name provided)",
  "gmlpos": "string (PIDF-LO gml:pos format)",
  "civicaddress": "object (GEOPRIV: country, a1, a2, etc.)",
  "uuid": "string (cross-vCon tracking)",

  "jcard": "array (RFC 7095 vCard) — EXTENSION ONLY",
  "timezone": "string — EXTENSION ONLY",
  "role": "string — EXTENSION ONLY",
  "meta": "object — EXTENSION ONLY"
}
```

**Core party fields** (first 10 above) are defined in the authoritative spec. The last four (`jcard`, `timezone`, `role`, `meta`) are extension fields — they MUST be listed in the top-level `extensions` array when used.

**UUID (core-05)**: SHOULD use version 8 UUID (timestamp-based with FQHN hash for uniqueness).

### Dialog Object

```json
{
  "type": "recording | text | incomplete | (extension value). Required unless the Dialog is empty ({})",
  "start": "ISO 8601 datetime",
  "duration": "number (seconds)",
  "parties": "UnsignedInt | UnsignedInt[] | (UnsignedInt | UnsignedInt[])[] (CONTRIBUTORS only)",
  "mediatype": "string (MIME type)",
  "filename": "string",
  "body": "string, or any JSON value when encoding is json",
  "encoding": "base64url | json | none | (extension value)",
  "url": "string (external reference)",
  "content_hash": "ContentHash | ContentHash[] (sha512-base64url)",
  "disposition": "string (REQUIRED for incomplete)",
  "session_id": "SessionId | SessionId[] ({local: UUID, remote: UUID})",
  "application": "string",
  "message_id": "string",
  "redacted_segments": "array of {start, end, parties?} (SHOULD be present in a redacted vCon)"
}
```

**Dialog types**: `recording`, `text`, `incomplete`. The `transfer` and `recording-set` types, and the `originator`, `party_history`, `recordings`, `recording_set`, `transferee`, `transferor`, `transfer_target`, `original`, `consultation` and `target_dialog` parameters, are gone in core-05. Transfers are Event Objects, recording sets are Session Objects, joins/drops/holds/mutes/keys are Event Objects, and `originator` lives on the Session.
**Contributors and receivers**: Dialog `parties` lists only the contributors. The receivers are in the Session Object `parties`. A text message's Dialog `parties` is the sender only; the Session `parties` includes the sender and all recipients.
**Empty Dialog**: `{}` is allowed and has no meaning. Use it to hold a position so other dialog indices stay valid, for example when a Dialog is removed during redaction.
**Incomplete dispositions**: `no-answer`, `congestion`, `failed`, `busy`, `hung-up`, `voicemail-no-message`, or an extension value.

### Analysis Object

```json
{
  "type": "string (report, sentiment, summary, transcript, translation, tts, etc.)",
  "dialog": "number or array (dialog indices, optional)",
  "sessions": "number or array (session indices, optional)",
  "attachment": "number or array (optional)",
  "analysis": "number or array (optional; MUST NOT reference itself)",
  "mediatype": "string (MIME type)",
  "filename": "string",
  "vendor": "string (REQUIRED - who produced this)",
  "product": "string (model name/version)",
  "schema": "string (schema identifier - NOT schema_version)",
  "body": "string, or any JSON value when encoding is json",
  "encoding": "base64url | json | none | (extension value)",
  "url": "string (external reference)",
  "content_hash": "ContentHash | ContentHash[]"
}
```

**Analysis types (core-05)**: `report`, `sentiment`, `summary`, `transcript`, `translation`, `tts`. At least one of `dialog`, `sessions`, `attachment` or `analysis` SHOULD be present to show the input scope, unless the analysis used data outside the vCon. `vendor` is provided even when `mediatype` fully identifies the format. If the Analysis contains or references content, enough information to interpret its format SHOULD be provided (`mediatype`, or `schema` with `vendor` and `product`).

### Attachment Object

```json
{
  "purpose": "string (free text, e.g. 'tags', 'lawful_basis', 'wtf_transcription')",
  "start": "ISO 8601 datetime (REQUIRED)",
  "party": "number (party index, REQUIRED)",
  "dialog": "number (dialog index)",
  "session": "number | number[] (session indices)",
  "mediatype": "string (MIME type; MUST be provided for inline files)",
  "filename": "string",
  "body": "string, or any JSON value when encoding is json",
  "encoding": "base64url | json | none | (extension value)",
  "url": "string",
  "content_hash": "ContentHash | ContentHash[]"
}
```

**Note (core-05)**: `party` is REQUIRED (provenance: the party that contributed or distributed the attachment, which need not be a conversation participant; the entity that adds the attachment SHOULD be represented as a Party Object). `dialog` is no longer required on its own: at least one of `dialog` or `session` MUST be present (the JSON Schema enforces this). The core spec uses `purpose` (not `type`); `purpose` has no restricted value set.

### Critical Spec Compliance Rules

These corrections apply across ALL repositories. Violations are spec-breaking bugs:

1. **Analysis `schema` field** - Use `schema`, NOT `schema_version`
2. **Analysis `vendor` is REQUIRED** - Every analysis MUST include a vendor string
3. **Inline `body` with `encoding: "json"`** - `body` is any JSON value; for `base64url` and `none` it is a string. Do not double-encode with `json.dumps()` when you can write the value directly
4. **Party `uuid` and `did` fields exist** - For cross-vCon party tracking and decentralized identifiers
5. **Dialog includes `session_id`, `application`, `message_id`** - `session_id` is `{local: UUID, remote: UUID}` or an array thereof
6. **Sessions** - If `dialog` or `events` has any Objects, `sessions` MUST have at least one Session. Dialog `parties` are contributors only; receivers go in the Session `parties`. Do not write `originator` on a Dialog
7. **Events replace `party_history` and `transfer` dialogs** - Write join/drop/hold/mute/key and transfer activity as Event Objects (`type`, `time`, `sessions`, `party`). Do not write the removed Dialog parameters or types
8. **`redacted_from` and `amended_from`** - Use these names; `redacted` and `amended` are the core-04 names. They are mutually exclusive. `redacted_from.url` requires `content_hash`
9. **Attachment needs `party` and a scope** - `party` is REQUIRED, and at least one of `dialog` or `session` MUST be present. Use `purpose` (not `type`)
10. **JWS `alg` MUST NOT be `"none"`** - Re-sign any vCon that used it with a permitted algorithm (SHOULD be `RS256`)
11. **`extensions` MUST list every extension used** - Any parameter, Object or parameter value not in core needs its extension name listed; every `critical` entry MUST also be in `extensions`
12. **`critical` extensions** - An implementation that does not support a listed extension MUST NOT process the vCon except to reject it or report unsupported content
13. **Externally referenced files** - MUST have both `url` and `content_hash`. ContentHash format: `sha512-` + Base64Url of SHA-512 digest
14. **`vcon` parameter** - DEPRECATED; write `"0.5.0"`, never `"0.4.0"`. The extension mechanism replaces schema versioning
15. **Removing an array element during redaction** - Leave an empty placeholder so indices hold; if an element is removed entirely, every index into that array MUST be revised
16. **Unknown values** - An unrecognized Dialog, Event or Session_Reference type, or encoding, SHOULD cause that Object to be ignored while the rest of the vCon is processed; the ignored Object stays in its array so indices do not shift (unless its extension is in `critical`)

### Tags Convention

Tags are stored as attachments with `purpose: "tags"` (or `type: "tags"` in legacy output) and a JSON body. Attachments require `party` and at least one of `dialog` or `session`; for vCon-level tags use `party: 0` and `dialog: 0` (or a `session` index) when no specific party or dialog applies:

```json
{
  "purpose": "tags",
  "start": "2025-01-15T10:30:00Z",
  "party": 0,
  "dialog": 0,
  "mediatype": "application/json",
  "body": {"department": "sales", "priority": "high"},
  "encoding": "json"
}
```

---

## 3. Architecture Patterns

### 3.1 vcon-server (Python Processing Pipeline)

**Pattern**: Event-driven pipeline with pluggable links and storage backends.

```
Ingress → [Link1] → [Link2] → [Link3] → [Storage1, Storage2] → Egress
           (tag)    (analyze)  (transcribe)  (postgres, s3)
```

**Core concepts**:
- **Links**: Processing modules that transform vCons (add tags, run analysis, transcribe, etc.)
- **Chains**: Ordered sequences of links defined in YAML config
- **Storages**: Pluggable backends (Redis, PostgreSQL, S3, MongoDB, Elasticsearch, Milvus, File, SFTP)
- **Ingress/Egress Queues**: Redis-backed queues for inter-service communication
- **Dead Letter Queue**: Failed processing goes to `DLQ:{ingress_list}`

**Link interface** — every link module must implement:

```python
async def run(vcon_uuid: str, link_config: dict) -> str | None:
    """Process a vCon. Return vcon_uuid to continue pipeline, None to stop."""
    pass
```

**Storage interface** — every storage module must extend:

```python
class Storage(ABC):
    async def save(self, vcon: dict) -> None: ...
    async def get(self, uuid: str) -> dict | None: ...
    async def delete(self, uuid: str) -> None: ...
```

**Configuration format** (YAML):

```yaml
links:
  my_link:
    module: links.my_link
    options:
      api_key: "${MY_API_KEY}"
      model: "claude-sonnet-4-20250514"

storages:
  postgres:
    module: storage.postgres
    options:
      connection_string: "${DATABASE_URL}"

chains:
  main_pipeline:
    links:
      - my_link
    storages:
      - postgres
    ingress_lists:
      - default
    egress_lists:
      - output
```

### 3.2 vcon-mcp (TypeScript MCP Server)

**Pattern**: Layered architecture with MCP protocol compliance.

```
Transport (STDIO/HTTP) → Server → Tool Handlers → Database (Supabase + Redis cache)
                                → Resource Handlers
                                → Prompt Handlers
```

**Key abstractions**:
- **ToolHandlerRegistry**: Centralized registration of 30+ MCP tools
- **PluginManager**: Lifecycle hooks (beforeCreate, afterCreate, beforeRead, afterRead, etc.)
- **TenantConfig**: Multi-tenant RLS via attachment-based tenant extraction
- **VConValidator**: Zod-based schema validation with IETF compliance checking

**Tool naming convention**: `snake_case` (e.g., `create_vcon`, `search_vcons_semantic`)

**Database**: Supabase PostgreSQL with:
- pgvector for semantic search (384-dimension embeddings)
- Full-text search via tsvector
- Row Level Security for multi-tenancy
- 41 migrations tracking schema evolution

### 3.3 vcon-lib (Python Library)

**Pattern**: Object-oriented data model with extension framework.

```python
from vcon import Vcon
from vcon.party import Party
from vcon.dialog import Dialog

v = Vcon.build_new()
v.add_party(Party(tel="+15551234567", name="Alice"))
v.add_party(Party(tel="+15559876543", name="Bob"))
v.add_dialog(Dialog(type="text", start="2025-01-15T10:30:00Z", parties=[0, 1], body="Hello world", encoding="none"))
# add_analysis is keyword-only; type, dialog, vendor, body are required
v.add_analysis(type="summary", dialog=0, vendor="OpenAI", product="GPT-4", body="Summary text", encoding="none")
v.to_json()
```

> `Party` and `Dialog` import from their submodules (`vcon.party`, `vcon.dialog`); only `Vcon` is exported from the package root. Prefer these helpers over writing `vcon_dict[...]` directly.

**Extension framework**:
- `ExtensionRegistry` for registration and discovery
- `ExtensionValidator` base class for validation
- `ExtensionProcessor` base class for processing
- Built-in extensions: Lawful Basis (GDPR), WTF (World Transcription Format)

**Property handling modes**: `"default"` (keep all), `"strict"` (remove non-standard), `"meta"` (move non-standard to meta object)

### 3.4 Adapters (Ingress Pattern)

All adapters follow the same pattern:

```
Source (files, SIP, screen capture) → Parse/Convert → Build vCon → POST to conserver API
```

**POST target**: `POST /vcon?ingress_list={name}` with `x-conserver-api-token` header.

---

## 4. Technology Stack Reference

### Python Repositories

| Concern | Standard Choice |
|---|---|
| Web framework | FastAPI + Uvicorn |
| Database ORM | Peewee (vcon-server), direct SQL (others) |
| Cache/Queue | Redis (via `redis` package, redis-stack for JSON) |
| LLM providers | OpenAI SDK, Anthropic SDK, LiteLLM, Groq |
| Transcription | Deepgram SDK, HuggingFace Whisper, MLX Whisper, NeMo |
| Async | anyio, trio |
| Retry logic | tenacity (exponential backoff) |
| Config | python-dotenv + YAML |
| Logging | python-json-logger, structured JSON to stderr |
| Observability | OpenTelemetry (traces + metrics), Sentry (errors), Datadog (metrics) |
| Testing | pytest, pytest-asyncio, pytest-cov, Faker |
| Code quality | black (120 char lines), flake8, ruff |
| Packaging | Poetry (preferred), setuptools fallback |
| Python version | 3.12+ |

### TypeScript Repository (vcon-mcp)

| Concern | Standard Choice |
|---|---|
| Runtime | Node.js 18+ |
| Module system | ES modules (`"type": "module"`) |
| Framework | @modelcontextprotocol/sdk |
| Database | @supabase/supabase-js |
| Cache | ioredis |
| Validation | zod |
| Logging | pino (with OpenTelemetry context injection) |
| Observability | @opentelemetry/* |
| Testing | vitest |
| Build target | ES2022 |
| Strict mode | Enabled |

---

## 5. Naming Conventions

### Cross-Repository Standards

| Element | Convention | Examples |
|---|---|---|
| Database columns | snake_case | `created_at`, `vcon_version`, `tenant_id` |
| Python modules | snake_case | `vcon_redis.py`, `llm_client.py` |
| Python classes | PascalCase | `Vcon`, `Storage`, `VConPeeWee` |
| Python functions | snake_case | `get_vcon()`, `add_tag()` |
| TypeScript types | PascalCase | `VCon`, `Dialog`, `Analysis` |
| TypeScript functions | camelCase | `getVcon()`, `addAnalysis()` |
| MCP tool names | snake_case | `create_vcon`, `search_vcons_semantic` |
| API endpoints | RESTful snake_case | `/vcon/{uuid}/analysis` |
| Environment vars | UPPER_SNAKE_CASE | `REDIS_URL`, `SUPABASE_URL` |
| Config keys | snake_case | `ingress_lists`, `api_key` |
| Docker services | lowercase hyphenated | `conserver`, `redis-stack` |
| IETF fields | snake_case | `created_at`, `content_hash`, `redacted_from` |

### UUID Convention

- vcon-lib generates UUID v8 (Unix timestamp-based) for new vCons
- All repositories accept UUIDs with or without dashes
- UUIDs are the primary identifier for all vCon operations

### Timestamp Convention

- Always ISO 8601 format with timezone info
- Example: `"2025-01-15T10:30:00Z"` or `"2025-01-15T10:30:00+00:00"`
- Database storage: `timestamptz` in PostgreSQL

---

## 6. API Patterns

### vcon-server REST API

**Auth**: `x-conserver-api-token` header (configurable via `CONSERVER_HEADER_NAME`)

| Method | Endpoint | Purpose |
|---|---|---|
| POST | `/vcon` | Submit vCon to pipeline |
| POST | `/vcon?ingress_list={name}` | Submit to specific ingress |
| GET | `/vcon` | List vCon UUIDs (paginated) |
| GET | `/vcon/{uuid}` | Retrieve vCon |
| PUT | `/vcon/{uuid}` | Update vCon |
| DELETE | `/vcon/{uuid}` | Delete vCon |
| GET | `/vcon/{uuid}/analysis` | Get analysis results |
| POST | `/vcon/external-ingress?ingress_list={name}` | External partner submission |
| GET | `/vcon/egress?egress_list={name}&limit=10` | Pull from egress queue |
| GET | `/vcon/dlq/{ingress_list}` | Dead Letter Queue access |

**External ingress auth**: Scoped API keys per ingress list in config YAML.

### vcon-mcp MCP Tools (30+)

**CRUD**: `create_vcon`, `get_vcon`, `update_vcon`, `delete_vcon`, `add_analysis`, `add_dialog`, `add_attachment`, `create_vcon_from_template`

**Search (4 modes)**:
- `search_vcons` — Structured field queries
- `search_vcons_content` — Full-text keyword search
- `search_vcons_semantic` — AI embedding similarity (384-dim vectors)
- `search_vcons_hybrid` — Combined keyword + semantic

**Tags**: `manage_tag`, `get_tags`, `remove_all_tags`, `search_by_tags`, `get_unique_tags`

**Analytics**: `get_database_analytics`, `get_monthly_growth_analytics`, `get_attachment_analytics`, `get_tag_analytics`, `get_content_analytics`, `get_database_health_metrics`

**Database inspection**: `get_database_shape`, `get_database_stats`, `analyze_query`, `get_database_size_info`, `get_smart_search_limits`

**Schema/Templates**: `get_schema`, `get_examples`

**MCP Resources** (URI-based):
- `vcon://v1/vcons/recent` — Most recent vCons
- `vcon://v1/vcons/{uuid}` — Full vCon
- `vcon://v1/vcons/{uuid}/metadata` — Metadata only
- `vcon://v1/vcons/{uuid}/parties|dialog|analysis|attachments|transcript|summary|tags`

---

## 7. Database Schemas

### vcon-mcp (Supabase PostgreSQL)

```sql
-- Core tables (8 main tables, 25+ indexes)

CREATE TABLE vcons (
  id SERIAL PRIMARY KEY,
  uuid UUID UNIQUE NOT NULL,
  vcon_version TEXT,
  subject TEXT,
  created_at TIMESTAMPTZ DEFAULT NOW(),
  updated_at TIMESTAMPTZ DEFAULT NOW(),
  extensions JSONB,
  critical JSONB,      -- spec field; legacy `must_support` column still exists, deprecated, exposed via `vcons_legacy` view
  redacted JSONB,      -- storage column as shipped by vcon-mcp; core-05 field name is redacted_from
  amended JSONB,       -- spec field; legacy `appended` column still exists, deprecated, exposed via `vcons_legacy` view
  tenant_id TEXT
);

CREATE TABLE parties (
  id SERIAL PRIMARY KEY,
  vcon_id INTEGER REFERENCES vcons(id) ON DELETE CASCADE,
  party_index INTEGER NOT NULL,
  tel TEXT, sip TEXT, stir TEXT, mailto TEXT,
  name TEXT, did TEXT, uuid TEXT,
  validation TEXT, gmlpos TEXT, civicaddress JSONB,
  jcard JSONB,    -- extension field (not in core spec)
  timezone TEXT   -- extension field (not in core spec)
);

CREATE TABLE dialog (
  id SERIAL PRIMARY KEY,
  vcon_id INTEGER REFERENCES vcons(id) ON DELETE CASCADE,
  dialog_index INTEGER NOT NULL,
  type TEXT NOT NULL,
  start TIMESTAMPTZ, duration NUMERIC,
  parties JSONB, originator INTEGER,
  mediatype TEXT, filename TEXT,
  body TEXT, encoding TEXT, url TEXT,
  content_hash TEXT, disposition TEXT,
  session_id TEXT, application TEXT,
  message_id TEXT, party_history JSONB
);

CREATE TABLE analysis (
  id SERIAL PRIMARY KEY,
  vcon_id INTEGER REFERENCES vcons(id) ON DELETE CASCADE,
  analysis_index INTEGER NOT NULL,
  type TEXT NOT NULL,
  dialog JSONB, mediatype TEXT, filename TEXT,
  vendor TEXT NOT NULL,
  product TEXT, schema TEXT,
  body TEXT, encoding TEXT, url TEXT,
  content_hash TEXT,
  embedding VECTOR(384)  -- pgvector for semantic search
);

CREATE TABLE attachments (
  id SERIAL PRIMARY KEY,
  vcon_id INTEGER REFERENCES vcons(id) ON DELETE CASCADE,
  attachment_index INTEGER NOT NULL,
  type TEXT,  -- core uses purpose; type retained for legacy/ecosystem
  start TIMESTAMPTZ,
  party INTEGER, dialog INTEGER,
  mediatype TEXT, filename TEXT,
  body TEXT, encoding TEXT, url TEXT,
  content_hash TEXT, created_at TIMESTAMPTZ
);
```

> **vcon-mcp legacy column names** (historical): vcon-mcp originally used `appended` and `must_support` (inherited from the older container draft). The current schema and TypeScript types use the spec-correct `amended` and `critical`; the legacy columns persist as deprecated and remain readable through the `vcons_legacy` view for back-compat. See [SPEC_COMPLIANCE_ISSUES.md](SPEC_COMPLIANCE_ISSUES.md) (Closed).

### vcon-server (PostgreSQL via Peewee)

```python
class VConPeeWee(Model):
    id = UUIDField(primary_key=True)
    uuid = UUIDField(index=True)
    vcon = TextField()           # Raw JSON string
    vcon_json = BinaryJSONField() # JSONB for querying
    created_at = DateTimeField()
    updated_at = DateTimeField()
    subject = TextField(null=True)
```

### Redis Data Model (vcon-server)

```
vcon:{uuid}              → JSON document (full vCon)
vcons                    → Sorted set (uuid → timestamp score)
context:{vcon_uuid}      → OpenTelemetry trace context
DLQ:{ingress_list}       → List (failed vCon UUIDs)
{ingress_list}           → List (pending vCon UUIDs)
{egress_list}            → List (processed vCon UUIDs)
```

---

## 8. Extension System

### Lawful Basis Extension (GDPR Compliance)

Implemented in vcon-lib, referenced by IETF draft.

```python
v = Vcon.build_new()
v.add_lawful_basis_attachment(
    lawful_basis="consent",
    expiration="2026-01-15T10:00:00Z",   # REQUIRED — ISO 8601, or None for indefinite
    purpose_grants=[{
        "purpose": "quality_assurance",
        "granted": True,
        "granted_at": "2025-01-15T10:00:00Z",   # REQUIRED per grant
        "conditions": ["Recording for training purposes"]  # optional array of strings
    }],
    party_index=0,
)
# Optional proof: proof_mechanisms=[{"proof_type": "verbal_confirmation",
#   "timestamp": "...", "proof_data": {...}}] — the helper expects ProofMechanism
#   objects, so for dict input build the body per the draft and append directly.

# Check permission
has_permission = v.check_lawful_basis_permission("quality_assurance", party_index=0)
```

**Lawful bases**: `consent`, `contract`, `legal_obligation`, `vital_interests`, `public_task`, `legitimate_interests`

### WTF Extension (World Transcription Format)

Standardized transcription format with multi-provider support. Transcripts default to `analysis[]`, not `attachments[]` — prefer `add_wtf_transcription_analysis()` (vcon-lib ≥ 0.9.2) for new code.

```python
v.add_wtf_transcription_analysis(
    transcript={
        "segments": [
            {"start": 0.0, "end": 2.5, "text": "Hello", "speaker": "Speaker_0", "confidence": 0.95}
        ],
        "speakers": [{"id": "Speaker_0", "name": "Agent"}],
        "metadata": {"provider": "whisper", "model": "large-v3"}
    },
    dialog_index=0
)
```

`add_wtf_transcription_attachment()` (same signature) remains available for the legacy `attachments[]` placement, but new pipelines should treat transcripts as derived analysis output.

**Provider adapters**: Whisper, Deepgram, AssemblyAI
**Export formats**: SRT, WebVTT

---

## 9. Processing Link Development

### Creating a New Link (vcon-server)

```python
# server/links/my_link/link.py

import logging
from lib.logging_utils import init_logger

logger = init_logger(__name__)

default_options = {
    "model": "claude-sonnet-4-20250514",
    "prompt": "Summarize this conversation."
}

async def run(vcon_uuid: str, link_config: dict) -> str | None:
    """
    Process a vCon through this link.

    Args:
        vcon_uuid: UUID of the vCon to process
        link_config: Configuration options from YAML

    Returns:
        vcon_uuid to continue pipeline, None to stop
    """
    from lib.vcon_redis import VconRedis
    vcon_redis = VconRedis()

    options = {**default_options, **link_config.get("options", {})}

    vcon = await vcon_redis.get_vcon(vcon_uuid)
    if not vcon:
        logger.error(f"vCon not found: {vcon_uuid}")
        return None

    # Process the vCon...
    result = do_analysis(vcon, options)

    # Add analysis result
    vcon["analysis"].append({
        "type": "summary",
        "body": result,
        "vendor": "MyCompany",
        "product": options["model"],
        "encoding": "none"
    })

    await vcon_redis.set_vcon(vcon_uuid, vcon)
    return vcon_uuid
```

### Creating a New Storage Backend (vcon-server)

```python
# server/storage/my_storage/storage.py

from storage.base import Storage

class MyStorage(Storage):
    def __init__(self, options: dict):
        self.connection = connect(options["connection_string"])

    async def save(self, vcon: dict) -> None:
        self.connection.insert(vcon["uuid"], vcon)

    async def get(self, uuid: str) -> dict | None:
        return self.connection.find(uuid)

    async def delete(self, uuid: str) -> None:
        self.connection.remove(uuid)
```

---

## 10. Testing Patterns

### Python Testing (pytest)

```python
# Standard test structure
import pytest
from vcon import Vcon
from vcon.party import Party

class TestVconCreation:
    def test_build_new(self):
        v = Vcon.build_new()
        assert v.uuid is not None
        assert v.parties == []

    def test_add_party(self):
        v = Vcon.build_new()
        v.add_party(Party(tel="+15551234567"))
        assert len(v.parties) == 1

    @pytest.mark.anyio
    async def test_async_operation(self):
        result = await async_function()
        assert result is not None
```

**Markers**: `@pytest.mark.slow`, `@pytest.mark.integration`, `@pytest.mark.anyio`
**Fixtures**: Use `conftest.py` for shared test setup, mock vCons via `generate_mock_vcon()`

### TypeScript Testing (vitest)

```typescript
import { describe, it, expect, vi } from 'vitest';

describe('createVcon', () => {
  it('should create a vcon with required fields', async () => {
    const result = await createVcon({
      parties: [{ name: 'Alice', tel: '+15551234567' }],
      subject: 'Test call'
    });
    expect(result.uuid).toBeDefined();
    expect(result.parties).toHaveLength(1);
  });
});
```

**Config**: `vitest.config.ts` with Node environment, dotenv, v8 coverage.
**Separation**: Unit tests run by default; E2E tests via `test:e2e` script.

---

## 11. Observability & Logging

### Structured Logging

**Python** (pino-style via python-json-logger):
```python
from lib.logging_utils import init_logger
logger = init_logger(__name__)
logger.info("Processing vCon", extra={"vcon_uuid": uuid, "chain": chain_name})
```

**TypeScript** (pino):
```typescript
import { logger } from './observability/logger';
logger.info({ vconUuid: uuid, tool: 'create_vcon' }, 'Creating vCon');
```

### OpenTelemetry

Both vcon-server and vcon-mcp support OpenTelemetry with:
- **Traces**: Distributed tracing across pipeline links
- **Metrics**: Counters, gauges, histograms
- **Logs**: Automatic trace context correlation
- **Exporters**: Console (JSON) or OTLP

Environment variables:
```bash
OTEL_ENABLED=true
OTEL_EXPORTER_TYPE=otlp        # or "console"
OTEL_ENDPOINT=http://collector:4318
OTEL_SERVICE_NAME=vcon-server
```

---

## 12. Deployment & Infrastructure

### Docker Compose (Production Pipeline)

```yaml
services:
  conserver:
    build: ./vcon-server
    environment:
      - REDIS_URL=redis://redis
      - CONSERVER_CONFIG_FILE=./config.yml
    depends_on: [redis]
    deploy:
      replicas: 4

  api:
    build: ./vcon-server
    command: ["uvicorn", "server.api:app", "--host", "0.0.0.0", "--port", "8000"]
    ports: ["8000:8000"]

  redis:
    image: redis/redis-stack:latest
    volumes: [redis_data:/data]
    ports: ["6379:6379"]
```

### Environment Variables (Common)

```bash
# Database
SUPABASE_URL=https://xxx.supabase.co
SUPABASE_SERVICE_ROLE_KEY=eyJ...
REDIS_URL=redis://localhost:6379

# Auth
CONSERVER_API_TOKEN=your-api-token

# LLM
OPENAI_API_KEY=sk-...
ANTHROPIC_API_KEY=sk-ant-...

# Observability
OTEL_ENABLED=true
LOG_LEVEL=INFO
SENTRY_DSN=https://...

# Multi-tenancy
RLS_ENABLED=false
TENANT_ATTACHMENT_TYPE=tenant
TENANT_JSON_PATH=tenant_id
```

---

## 13. Error Handling Patterns

### Python (vcon-server, links)

```python
# Return None to stop pipeline (non-fatal)
if not vcon:
    logger.warning(f"vCon not found: {vcon_uuid}")
    return None

# Dead Letter Queue for fatal errors
try:
    result = await process(vcon)
except Exception as e:
    logger.error(f"Processing failed: {e}", extra={"vcon_uuid": vcon_uuid})
    await dlq.push(vcon_uuid, str(e))
    return None
```

### TypeScript (vcon-mcp)

```typescript
import { McpError, ErrorCode } from '@modelcontextprotocol/sdk/types.js';

// MCP protocol errors
throw new McpError(ErrorCode.InvalidParams, 'UUID is required');
throw new McpError(ErrorCode.InternalError, 'Database connection failed');

// Validation errors (via Zod)
const result = schema.safeParse(input);
if (!result.success) {
  throw new McpError(ErrorCode.InvalidParams, result.error.message);
}
```

---

## 14. Security Considerations

### Authentication

- **vcon-server API**: Token-based via `x-conserver-api-token` header
- **External ingress**: Scoped API keys per ingress list
- **vcon-mcp**: Relies on MCP transport security (local STDIO or authenticated HTTP)

### Multi-Tenancy

- vcon-mcp supports PostgreSQL Row Level Security (RLS)
- Tenant ID extracted from vCon attachments (configurable path)
- All queries automatically filtered by tenant context

### Privacy

- Redaction link strips PII from vCons
- Lawful Basis extension tracks GDPR consent
- Right-to-Know portal provides data access/deletion
- Content hashing for integrity verification
- Digital signatures supported in vcon-lib

---

## 15. Code Generation Guidelines

When generating code for this ecosystem:

1. **Always check the spec** — Use the data model in Section 2 as the canonical reference (draft-ietf-vcon-vcon-core). Never use `schema_version` (it's `schema`). Always include `vendor` in analysis objects. Use `purpose` for attachment purpose (not `type` in core).

2. **Follow existing patterns** — Match the architecture of the target repository. Links return `vcon_uuid | None`. MCP tools use Zod validation. Python uses `init_logger(__name__)`.

3. **Use the right stack** — Python repos use FastAPI + Poetry + pytest. TypeScript uses ESM + Zod + Vitest. Don't mix conventions.

4. **Tags are attachments** — Tags are stored as `purpose: "tags"` attachments with a JSON body. Legacy `type: "tags"` may still appear. Attachments require `party` and at least one of `dialog` or `session` (core-05).

5. **Handle encoding properly** — Body content uses `base64url`, `json`, or `none` encoding. Audio/video is typically base64url. JSON data uses `json` encoding. Plain text uses `none`.

6. **UUIDs everywhere** — All vCon operations are UUID-keyed. Accept with or without dashes. Generate v8 in vcon-lib.

7. **ISO 8601 timestamps** — Always include timezone. Use `Z` suffix or explicit offset.

8. **Structured logging** — Use key-value pairs, not string interpolation. Include `vcon_uuid` in all log entries related to vCon processing.

9. **Test thoroughly** — Unit tests for validation, integration tests for database operations, E2E tests for full workflows.

10. **Respect the pipeline** — In vcon-server, links are stateless processors. They read from Redis, modify, write back. Never hold state between invocations.
