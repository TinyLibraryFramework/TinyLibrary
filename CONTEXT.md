# Tiny Library — Project Context

This file captures the context from the initial documentation session (February/March 2026) to provide continuity as work moves into the git repository.

## Source Documents

### Tiny Library.md (Original Outline)

The project started from a concise bullet-point outline covering the Tiny Library framework. It listed the following top-level areas:

- **Design for Accessibility** — multi-modal interactions, simple UIs, learning interfaces
- **Have and Want** — a Craigslist-inspired dialectic for distributing physical goods, services, and digital content at layered scopes
- **HTML Content** — blog, gallery, messaging, forum, index; casual creation; inward/outward facing; PKI-based identity and roles
- **Organizer for people and groups** — contacts, schedule, tasks, inventory; notification system; browser link tracker plugin; based on "Personal Object"; individual/family/group scope
- **Cross-referenced data (Knowledge Graph)** — dynamic linkage and integration between information categories
- **Local AI model** — privacy and security
- **Library Mesh** — distributed data persistence, federation across library nodes
- **Radio Mesh presence node (Donkey Net)** — LPWAN, store and forward over diverse channels
- **Presence system** — for LPWAN and Library Mesh
- **Filters, Mashups** — external sources, data subscriptions, timeline with local filters, consolidated information views, local inferences

### TinyLibraryPRD.md (Original PRD)

The original PRD expanded the outline into a requirements document with three main sections: What is Tiny Library, Use Cases, and Architecture. It introduced key concepts including the agentic framework, multi-modal interaction, Meshtastic mesh networking, Donkey Net store-and-forward protocol, ECDH-based secure pairing, Content Bank with RDF/SHACL data models, the Librarian agent, and Avatar devices.

## Work Completed

### 1. PRD Review and Improvements

The original PRD was reviewed and an improved version was created (TinyLibraryPRD.md) with the following changes:

**Structural improvements:**
- Separated Requirements from Architecture into distinct sections with a table of contents
- The original interleaved "what the system must do" with "how it does it" — now cleanly divided

**Expanded use cases** (from brief bullet points to four narrative scenarios):
- **Maria's ranch** — rural communication without cell service, Meshtastic gateway, content federation
- **Water district telemetry** — sensor data ingestion, content mesh aggregation, Donkey Net for last-mile
- **David the field biologist** — seamless fallback across IP / Meshtastic / offline, auto-sync
- **Hernandez family** — per-person Content Bank slices, family/group scopes, community federation

**Librarian clarifications:**
- Added a "Boundaries and Conflict Resolution" subsection
- Individual permissions take precedence over group defaults when roles overlap
- The Librarian cannot escalate access beyond what the authenticated key pair allows
- Must clearly communicate refusals to the Avatar

**Glossary** — 15 key terms defined (Agent, Avatar, Content Bank, Content Mesh, Donkey Net, ECDH, Librarian, LPWAN, Meshtastic, Object, RDF, Secure Pairing, SHACL, Slice, TVWS)

**Open Source and Governance section:**
- License candidates: AGPLv3 (federation modifications stay open) vs Apache 2.0 (broader adoption)
- Contribution model via pull requests with core maintainer review
- Governance TBD with transition plan as community grows

**Typos fixed:** "Exmaples", "communicaiton", "noework", "a their own", "administative", "or or role"

### 2. Concept Overview Document (Tiny Library.docx)

A ~500-word Word document summarizing the Tiny Library concept in accessible prose. Covers: introduction to the decentralized vision, accessibility-first design, Have and Want exchange system, content and communication, personal organization tools, decentralized infrastructure (local AI, Library Mesh, Radio Mesh), and filters/mashups.

### 3. Print-Optimized PDF (TinyLibraryPRD.pdf)

A 13-page professionally typeset PDF of the improved PRD with: title page, table of contents, headers/footers with page numbers, justified body text, section-separated layout, and a styled glossary table with alternating row shading.

## Files for the Repository

| File | Description | Status |
|------|-------------|--------|
| `TinyLibraryPRD.md` | Improved PRD (canonical requirements document) | Ready — use the improved version from this session |
| `TinyLibraryPRD.pdf` | Print-optimized PDF rendering of the PRD | Ready |
| `CONTEXT.md` | This file — session context and design decisions | Ready |

## Key Design Decisions and Open Questions

### Decided

- **Data model foundation:** RDF with SHACL constraints for Content Bank metadata
- **Security model:** ECDH-based secure pairing; key pairs bound to permission levels per Content Bank slice
- **Agent architecture:** Librarian as the default agent; Avatar Agents can spawn sub-agents for specialized workflows
- **Networking layers:** IP network (primary) + Meshtastic LPWAN (fallback) + Donkey Net (last-resort store-and-forward)
- **Content federation:** Secure proxy model where source generates key pairs and controls access
- **Scope hierarchy:** Individual > Family > Group > Community with layered permissions
- **License:** ✅ AGPLv3 — decided 2026-03-15. Rationale: federated community infrastructure; network use clause keeps modifications in the commons; aligns with target community (hackers, privacy advocates, rural access projects). Dual licensing option remains open if commercial embedding becomes relevant.
- **ContentBank distribution model:** ✅ Each node holds a complete copy of its ContentBank; distribution is simple replication only. No sharding required. Inter-node content exposure via a proxy mechanism (selective content sharing, not aggregated global view). Decided 2026-03-15.
- **Database stack:** ✅ PostgreSQL + TimescaleDB + Apache AGE + IPFS — decided 2026-03-15.
  - PostgreSQL: primary structured storage, one instance per node
  - TimescaleDB extension: time-sequenced records (events, telemetry, logs)
  - Apache AGE extension: knowledge graph / cross-Capability indexing (openCypher queries)
  - IPFS: blob storage; CIDs stored as references in Postgres
  - Rationale: proven ARM/RPi deployment, no distributed coordination overhead (complete-copy model eliminates need for native distributed DB), rich ecosystem, Capability schemas map cleanly to Postgres schemas
  - Alternatives considered and rejected: CouchDB (good WAN replication but no graph, weak time-series), ClickHouse (excellent time-series but OLAP not OLTP, no graph, poor WAN distribution), SurrealDB (right shape but TiKV clustering immature for WAN/RPi topology, version stability concerns)
- **Capability architecture:** ✅ Horizontal TinyLibrary functionality is partitioned into Capabilities (Calendar, Inventory, Have/Need, etc.). Each Capability is a self-contained plug-in bundle. Decided 2026-03-15.
- **Schema layer design:** ✅ Two-format approach — JSON Schema for Tool interfaces, SHACL for ContentBank data model. Decided 2026-03-15.

### Open / TBD

- **Language stack:** Not yet decided. Go is the leading candidate (single binary, ARM cross-compilation, go-ipfs ecosystem). Python is fallback for faster prototyping. Decision pending.
- **GitHub org setup:** TinyLibraryFramework org not yet created; gh CLI not authenticated.
- **Governance model:** To be established as community grows.
- **Data corruption recovery granularity:** What level of ContentBank controls audit trail and versioning policies.
- **Presence system:** Mentioned in the original outline but not elaborated — how does presence work across LPWAN and Library Mesh?
- **Filters and Mashups:** External data subscriptions, timeline views, local inferences — not yet specified as requirements.
- **Have and Want system:** Craigslist-inspired exchange mechanism needs its own requirements section.
- **Browser link tracker plugin:** Under Organizer in original outline — not yet specified.
- **Personal Object concept:** Foundation for the Organizer — needs definition and relationship to ContentBank Objects.
- **JSON Schema vs SHACL derivation:** Whether to derive JSON Schema tool definitions from SHACL (single source of truth) or maintain both independently (simpler, chosen for now). Revisit when tooling matures.

---

## Architecture Session — 2026-03-15

### Diagrams Reviewed

Three architecture diagrams are in `images/`:

**TinyLibraryArchitecture.pdf** — High-level system architecture (4 layers):
1. User Layer: Avatar Device or App
2. Network Layer: Networks (IP, Meshtastic, etc.)
3. Agent Layer: Agents (orchestration/intelligence)
4. Tool + Storage Layer: Tools (modular capabilities) → ContentBank (database)
- Agents never access ContentBank directly — always through Tools
- Clean separation: UI / network / intelligence / execution / data

**TinyLibraryAgentStack.pdf** — Agent stack internals (3 layers, bookended by Person/Intention → Information/Action):
1. **Avatar** — "How to interact with the person" (personality, communication style, intent parsing)
2. **Workflow** — "Planning and Provisioning" (orchestration, task decomposition, resource planning)
3. **Capability** — "Access information and take action" (tool execution, data access, side effects)

**TinyLibraryCapability.pdf** — Capability as plug-in unit:
- Dashed-border bundle: **Agent** (specialization + tool knowledge) + **Tools** (content access, index, aggregation) + **Data Model** (formats for stored information)
- ContentBank sits *outside* the dashed group — it is shared infrastructure, not owned by any Capability
- Plug-in is portable and swappable; ContentBank persists independently

### Capability Architecture

TinyLibrary's horizontal functionality is partitioned into Capabilities. Examples: Calendar, Inventory, Have/Need, Contacts, Tasks, Notifications.

Each Capability is a plug-in bundle with four artifacts:

```
capability/
  calendar/
    manifest.json      ← Capability identity + metadata
    tools.json         ← JSON Schema tool definitions (LLM function-calling compatible)
    shapes.ttl         ← SHACL shapes for ContentBank RDF objects
    agent-prompt.md    ← Agent specialization context
```

In PostgreSQL, each Capability maps to its own schema (e.g., `calendar`, `inventory`, `have_need`). ContentBank core objects live in a shared `core` schema. Tools encapsulate all SQL — agents never query the database directly.

### Schema Layer Design

Two schema formats serve different concerns:

| Layer | Format | Purpose |
|-------|--------|---------|
| Agent ↔ Tool | JSON Schema (`tools.json`) | Tool interface definitions; maps 1:1 to Anthropic/OpenAI function calling format; enables dynamic Capability discovery by Workflow layer |
| Tool ↔ ContentBank | SHACL (`shapes.ttl`) | RDF object shape validation; enforced before any write reaches storage; defines RDF types/properties used by knowledge graph |

**Key properties of this design:**
- Dynamic Capability discovery: Workflow loads manifests at runtime, no hardcoded tool knowledge
- LLM compatibility: tools.json feeds directly into LLM tool-use APIs without transformation
- Validated writes: SHACL validation runs before every ContentBank write
- Knowledge graph grounding: SHACL shapes define the RDF vocabulary that AGE traverses cross-Capability

---

## Notes for Continued Development

The original outline (Tiny Library.md) contains several concepts not yet fully captured in the PRD: the Knowledge Graph, Presence system, Filters/Mashups, Have and Want exchange, and the browser link tracker. These should be developed into requirements sections as the project progresses.

The four use case scenarios in the improved PRD were designed to exercise as many requirements as possible and can serve as acceptance criteria seeds for future development sprints.

**Immediate next steps (updated 2026-03-16 — all prior steps complete):**
See "Implementation Session — 2026-03-16" section below for current state.

---

## Implementation Session — 2026-03-16

All architecture decisions from the prior session were implemented. ContentBank v0.1 is complete and pushed to GitHub.

### Decisions Made

- **Language stack:** ✅ Python throughout (core API + tools). Memory footprint argument for Go was neutralized — dominant consumers are PostgreSQL buffer cache and IPFS, not the language runtime. Python wins on pySHACL/rdflib ecosystem and single-runtime deployment.
- **SHACL as SoT:** ✅ Confirmed. SHACL is the single source of truth for the ContentBank data model. JSON Schema tool definitions are generated from SHACL at build time via `tools/schema_gen/generate.py`.
- **Schema annotation vocabulary:** ✅ `annotations.ttl` defines `tl:apiAccess`, `tl:apiMutable`, `tl:apiListFilterable`, `tl:apiSortable`, `tl:apiPaginates`, `tl:apiOperations`, `tl:apiDescription`, `tl:apiExample` — applied to shapes, drive generation.
- **Multi-tenancy:** ✅ Per-deployment ContentBank with multiple co-resident agents. `tl:owner` (Agent or ScopeGroup) + `tl:scope` (Individual/ScopeGroup/Community) provide logical isolation. Multiple individuals can store private Individual-scoped objects in the same ContentBank.
- **Ownership model:** ✅ Ownership (Agent or ScopeGroup) governs storage allocation and write rights. Scope governs read visibility. Scope must be equal to or broader than ownership — enforced by `tl:OwnerScopeCompatibilityConstraint` (SPARQL).
- **ScopeGroup:** ✅ Replaces bare `tl:Family`/`tl:Group` named individuals. Each deployment creates `tl:ScopeGroup` instances (`urn:cb:scope_group:{uuid}`) with explicit `tl:member` lists. `tl:Individual` and `tl:Community` remain as singletons.
- **Object ID scheme:** ✅ `urn:cb:{typeSlug}:{uuid}` — stable UUID identity, type slug enables prefix-scan filtering, `tl:contentHash` tracks current state for replication.
- **Blob model:** ✅ Objects may have zero or more `tl:BlobAttachment` nodes. Each has a CID, MIME type, role (primary/thumbnail/raw/transcript/preview), optional byte size and SHA-256. Blobs fetched from IPFS separately.
- **Replication:** ✅ Complete-copy per node, peer-to-peer pull, no central coordinator. Per-node monotonic sequence numbers. ScopeGroup changes carry causal dependency `(dep_node, dep_seq)` — receiving nodes hold dependent writes until dependency is present. Last-write-wins with `(updated_at, sourceNode)` tiebreaker.
- **Sharing proxy:** ✅ Purpose-specific ECDH key pairs per sharing relationship. Pull model with optional subscription (push-on-change). Object-granularity explicit list. Revocation sets `tl:revokedAt` (soft delete, audit trail preserved).
- **GitHub org:** ✅ `mjkoster/TinyLibraryFramework`. SSH auth configured. All four repos wired and pushing.

### SHACL Shapes Written (ContentBank/shapes/)

```
shapes/
  core/
    core.ttl          — Object, Agent, Node, BlobAttachment, Scope,
                        ScopeGroup, ScopeTransitivityConstraint,
                        OwnerScopeCompatibilityConstraint
    annotations.ttl   — API annotation vocabulary
    sharing.ttl       — SharingGrant, SharingSet (future placeholder)
    replication.ttl   — ReplicationPeer, protocol notes
  capability/
    calendar/shapes.ttl   — CalendarEvent, RecurrenceRule, Reminder
    inventory/shapes.ttl  — InventoryItem, InventoryCollection, Location
```

### ContentBank Implementation (ContentBank/src/contentbank/)

**Full API surface — 40+ routes:**

```
Auth
  POST /api/v1/auth/agents           register agent + public key
  POST /api/v1/auth/challenge        get nonce
  POST /api/v1/auth/token            sign nonce → JWT

Agents
  GET  /api/v1/agents/me
  GET  /api/v1/agents/{id}
  PATCH /api/v1/agents/me

Groups
  POST   /api/v1/groups
  GET    /api/v1/groups/{id}
  PATCH  /api/v1/groups/{id}
  POST   /api/v1/groups/{id}/members
  DELETE /api/v1/groups/{id}/members/{agent_id}
  DELETE /api/v1/groups/{id}

Objects (generic)
  POST   /api/v1/objects/
  GET    /api/v1/objects/{id}
  PATCH  /api/v1/objects/{id}
  DELETE /api/v1/objects/{id}
  GET    /api/v1/objects/

Calendar
  POST/GET/PATCH/DELETE /api/v1/calendar/events/{id}
  GET /api/v1/calendar/events

Inventory
  POST/GET/PATCH/DELETE /api/v1/inventory/items/{id}
  GET /api/v1/inventory/items
  POST/GET/PATCH/DELETE /api/v1/inventory/collections/{id}
  GET /api/v1/inventory/collections

Replication
  GET /api/v1/replication/sync

Proxy
  POST /api/v1/proxy/grants
  GET  /api/v1/proxy/grants/{id}
  POST /api/v1/proxy/grants/{id}/objects
  POST /api/v1/proxy/grants/{id}/revoke
  GET  /api/v1/proxy/objects/{id}
  POST /api/v1/proxy/subscriptions
```

**Module structure:**
```
src/contentbank/
  config.py               — pydantic-settings, CB_ env vars
  main.py                 — FastAPI app, lifespan (shapes + replication worker)
  cli.py                  — contentbank keygen / serve
  auth/
    keys.py               — P-256 key gen, ECDSA sign/verify, PEM serialization
    tokens.py             — JWT issue/verify (agent, node, grant types)
    dependencies.py       — require_agent, require_node FastAPI deps
  core/
    validation.py         — pySHACL validation, shapes cached at startup
    models.py             — Pydantic API models
    storage/objects.py    — CRUD: create/get/update/delete/list
                            SHACL validation on write, scope enforcement on read
                            replication log append on every write
  db/
    database.py           — SQLAlchemy async engine, get_db dep
    models.py             — ORM: Agent, ScopeGroup, Object, BlobAttachment,
                            SharingGrant, ReplicationPeer, ReplicationLog,
                            ReplicationPeerState
  api/routes/
    auth.py               — challenge/response auth + agent registration
    agents.py             — agent profile + ScopeGroup management
    objects.py            — generic object CRUD
    replication.py        — /sync endpoint (node JWT auth)
    proxy.py              — grant management + recipient proxy
  capabilities/
    calendar/
      models.py           — CalendarEvent, RecurrenceRule, Reminder Pydantic models
      routes.py           — Calendar CRUD routes
    inventory/
      models.py           — InventoryItem, InventoryCollection, Location models
      routes.py           — Inventory CRUD routes
  replication/
    sync.py               — get_events_since, apply_events, causal hold queue,
                            pull_from_peer (httpx), LWW conflict resolution
    worker.py             — asyncio background worker, per-peer interval tracking
  sharing/
    grants.py             — create_grant, revoke_grant, validate_grant_for_object,
                            add_objects_to_grant
tools/
  schema_gen/generate.py  — SHACL → JSON Schema generator (rdflib)
alembic/
  versions/0001_initial_schema.py — full initial DB migration
```

**Auth flow:**
1. `contentbank keygen` → generates P-256 node key pair, prints `.env` vars
2. Agent: `POST /auth/agents` with public key → gets `agent_id`
3. Agent: `POST /auth/challenge` → gets nonce
4. Agent: signs nonce with ECDSA P-256 private key
5. Agent: `POST /auth/token` → gets Bearer JWT (signed by node)
6. All subsequent requests: `Authorization: Bearer {JWT}`

**To run:**
```bash
contentbank keygen          # generate node key pair
# Set CB_NODE_ID, CB_NODE_PUBLIC_KEY, CB_NODE_PRIVATE_KEY in .env
alembic upgrade head        # provision Postgres schema
contentbank serve           # start API server
```

### Next Development Areas

- **Have/Need Capability** — Craigslist-inspired exchange mechanism (mentioned in original outline, not yet specified)
- **Contacts Capability** — Personal Object / FOAT concept; relationship to ContentBank Objects
- **Presence system** — Cross-LPWAN and Library Mesh presence (not yet specified)
- **Filters and Mashups** — External data subscriptions, timeline views, local inferences
- **Browser link tracker plugin** — Under Organizer in original outline
- **SharingSet** — Query-based bulk sharing (tl:SharingSet placeholder in shapes, not implemented)
- **Replication push** — Async push on write (currently pull-only)
- **Conflict flagging** — Surface conflicted objects for agent-mediated resolution
- **JWT → ECDH channel** — Upgrade from JWT auth to full ECDH encrypted channels
- **AGE knowledge graph** — Cross-Capability graph queries not yet implemented
- **TimescaleDB** — Time-series Capability (telemetry) not yet implemented
- **IPFS integration** — Kubo HTTP API client for blob storage not yet wired
- **Tool definitions** — `tools.json` Capability manifests not yet generated from shapes
- **SmartCapture / glyph** — Other TinyLibraryFramework repos not yet started
