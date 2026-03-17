---
marp: true
theme: default
paginate: true
backgroundColor: #ffffff
color: #1a1a1a
style: |
  section {
    font-family: 'Georgia', serif;
    font-size: 1.15rem;
    padding: 2.5rem 3rem;
  }
  h1 {
    font-size: 2rem;
    color: #2c3e50;
    border-bottom: 2px solid #3498db;
    padding-bottom: 0.4rem;
  }
  h2 {
    font-size: 1.5rem;
    color: #2c3e50;
  }
  h3 {
    font-size: 1.1rem;
    color: #555;
  }
  code {
    background: #f4f6f8;
    border-radius: 4px;
    padding: 0.1em 0.3em;
    font-size: 0.9em;
  }
  pre {
    background: #f4f6f8;
    border-left: 4px solid #3498db;
    padding: 1rem;
    font-size: 0.8rem;
  }
  ul li {
    margin-bottom: 0.35rem;
  }
  .small {
    font-size: 0.85rem;
  }
---

# Tiny Library

### Decentralized Personal and Community Infrastructure

*Local AI · Mesh Networking · Federated Content Storage*

---
*TinyLibraryFramework — Open Source, AGPLv3*

---

## The Problem

Modern digital life is fragmented across:

- **Cloud silos** — your data in someone else's infrastructure
- **App monocultures** — one app per task, no shared context
- **Connectivity assumptions** — everything requires internet
- **Accessibility gaps** — rural, offline, low-bandwidth communities left out

> Social media, phone apps, and PC applications have left significant gaps in quality of experience around calendar management, personal inventory, social contacts, and community coordination.

---

## What is Tiny Library?

An **agentic framework** for managing personal, family, and community information — designed to work where other systems fail.

- **Replaces apps with agentic workflows** — instead of a calendar app, an agent uses tools that understand calendar semantics
- **Multi-modal by design** — adapts to the capabilities of the device, not the other way around
- **Works off-grid** — operates without internet via LPWAN mesh networks
- **User-sovereign** — your data, your hardware, your rules
- **Open Source** — AGPLv3; modifications stay in the commons

---

## Core Concepts

| Concept | What it means |
|---------|---------------|
| **Avatar** | The device or app you interact with — your face to the system |
| **Agent** | The AI that acts on your behalf, using tools to access information |
| **Capability** | A plug-in module for a specific domain (Calendar, Inventory, etc.) |
| **Content Bank** | The persistent, federated content store — the system's memory |
| **Librarian** | The default agent — your primary interface to Tiny Library |
| **Library Mesh** | The network of Tiny Library nodes sharing content |
| **Donkey Net** | A store-and-forward protocol for unreliable, high-latency links |

---

## Use Case: Rural Ranch

**Maria** runs a ranch with no reliable cell service.

- Her Tiny Library node is the **information hub** for her Meshtastic network
- Neighbors share emergency contacts, weather observations, resource availability through the **community scope** of her Content Bank
- When a brief internet connection is available, her node **replicates** to a cloud peer — nothing is lost if local hardware fails
- A Donkey Net relay on a USB drive carries sensor data from a remote field station back to her node once a week

> *No internet required. No cloud dependency. No data loss.*

---

## Use Case: Multi-User Household

**The Hernandez family** uses a single Tiny Library deployment.

- Each family member has their own **Agent** and **private Individual-scoped objects** — invisible to other family members
- A **Family ScopeGroup** gives all members access to shared calendar events, household inventory, and community announcements
- Parents post **Community-scoped** items visible to their neighborhood group
- All data lives on one local node, but access is strictly enforced at every layer

> *One node. Multiple users. No data leakage.*

---

## The Agent Stack

Three layers between the person and their information:

```
Person / Intention
        ↓
  ┌─────────────────┐
  │    Avatar       │  ← How to interact with the person
  │                 │     (personality, intent parsing, device adaptation)
  ├─────────────────┤
  │    Workflow     │  ← Planning and provisioning
  │                 │     (orchestration, task decomposition)
  ├─────────────────┤
  │   Capability    │  ← Access information and take action
  │                 │     (tool execution, data access, side effects)
  └─────────────────┘
        ↓
Information / Action
```

Agents **never access Content Bank directly** — always through Capability tools.

---

## Capability Architecture

Each Capability is a **self-contained plug-in bundle**:

```
capability/
  calendar/
    manifest.json      ← Identity, version, dependencies
    tools.json         ← JSON Schema tool definitions (LLM-compatible)
    shapes.ttl         ← SHACL shapes for Content Bank objects
    agent-prompt.md    ← Agent specialization context
```

Current Capabilities: **Calendar**, **Inventory**

Planned: Have/Need, Contacts, Tasks, Notifications

The Workflow layer discovers Capabilities **dynamically at runtime** from manifests — no hardcoded tool knowledge.

---

## Networking Layers

Tiny Library operates across three network layers, with automatic fallback:

```
┌─────────────────────────────────────────────┐
│  IP Network (primary)                        │
│  HTTP / HTTPS · ContentBank API · Replication│
├─────────────────────────────────────────────┤
│  Meshtastic LPWAN (fallback)                 │
│  LoRa radio · Long range · Low bandwidth     │
├─────────────────────────────────────────────┤
│  Donkey Net (last resort)                    │
│  Store-and-forward · USB · any physical media│
│  "Eventually consistent" — even carrier pigeon│
└─────────────────────────────────────────────┘
```

Tiny Library provides **IP backhaul** for Meshtastic networks and acts as a gateway between layers.

---

## Security Model

Security is based on **ECDH key pairs** throughout:

- **Agent identity** — P-256 key pair per agent; public key stored in Content Bank
- **Authentication** — Challenge/response: agent signs a server-issued nonce with their private key; node issues a signed JWT on success
- **Secure pairing** — Avatar devices pair with agent endpoints via ECDH
- **Sharing** — Purpose-specific key pairs per sharing relationship; private key stays with recipient, never touches the source node
- **Scope enforcement** — Read access checked against ownership and group membership on every request; PostgreSQL row-level security as backstop

> *The private key never leaves the device that holds it.*

---

## Content Bank: The Data Layer

Content Bank is Tiny Library's **content-addressable persistent storage**.

### Core design principles

- **One complete copy per node** — no sharding, no distributed coordination
- **Object-centric** — every piece of data is a rich RDF object with typed metadata
- **Binary data separate** — blobs stored in IPFS, linked by CID; the graph stores metadata and references
- **Schema-driven** — SHACL is the single source of truth; all validation enforced at write time
- **Multi-tenant** — multiple users share one deployment with strict isolation

---

## The Content Bank Object Model

Every object has **two layers**:

**Graph layer** (PostgreSQL + Apache AGE)
```
Object ID:    urn:cb:photograph:550e8400-...
Type slug:    photograph
Owner:        urn:cb:agent:alice-uuid
Scope:        urn:cb:scope_group:family-uuid
Timestamps:   created_at, updated_at
Content hash: sha256 of canonical serialization
Capability metadata: { title, location, camera, ... }
```

**Blob layer** (IPFS)
```
Blob: cid=bafybeig..., role=primary, mime=image/jpeg, size=4.2MB
Blob: cid=bafybeix..., role=thumbnail, mime=image/jpeg, size=42KB
```

Objects are **queryable**. Blobs are **fetched on demand**.

---

## Scope and Ownership

Ownership and visibility are **separate concerns**:

| Scope | Who can read | Order |
|-------|-------------|-------|
| `Individual` | Owner only | Most restrictive |
| `ScopeGroup` (Family) | Named family members | ↑ |
| `ScopeGroup` (Group) | Named group members | ↓ |
| `Community` | All agents in the deployment | Least restrictive |

**Key rule:** Scope must be equal to or *broader* than ownership.
An object owned by Alice can be scoped to Community — Alice retains ownership/quota, but everyone can read it.

Two SHACL-SPARQL constraints enforce this at write time on every object.

---

## Replication

Peer-to-peer replication with **no central coordinator**:

- Each node holds a **complete copy** of its Content Bank
- Peers sync via **pull at configurable intervals** — reconnecting nodes catch up automatically
- Topology is **fully or partially meshed** — each node has an explicit peer list stored as Content Bank objects (so peer config replicates too)
- **Conflict resolution:** last-write-wins by `(updated_at, source_node_id)` — deterministic, auditable

**ScopeGroup causal ordering:**
When a group membership changes, subsequent writes referencing that group carry a causal dependency tag `(dep_node, dep_seq)`. Receiving nodes **hold those writes** until the group change has arrived — no node processes a write against a group state it hasn't seen yet.

---

## Data Sharing

Cross-node sharing uses **purpose-specific key pairs** — not agent identity keys:

```
1. Grantor generates a fresh ECDH key pair for this sharing relationship
2. Grantor creates a SharingGrant:
     grant_key = recipient's purpose-specific public key
     granted_objects = [urn:cb:photograph:abc, urn:cb:event:def]
     allow_subscribe = true
     expires_at = optional

3. Grantor shares the purpose-specific private key out-of-band
4. Recipient authenticates via signed JWT using that key
5. Recipient pulls metadata via GET /proxy/objects/{id}
6. Recipient fetches blobs directly from IPFS using CIDs
```

**Revocation** sets `revoked_at` on the grant — no key rotation needed, purpose keys are meaningless once revoked.

---

## Implementation Technology

### Runtime Stack

| Component | Technology |
|-----------|-----------|
| Language | Python 3.11+ |
| API server | FastAPI + uvicorn |
| Data validation | pydantic v2 |
| RDF / SHACL | rdflib + pySHACL |
| Structured storage | PostgreSQL 15+ |
| Time-series | TimescaleDB extension |
| Graph queries | Apache AGE (openCypher) |
| Blob storage | IPFS (Kubo) |
| Auth / crypto | P-256 ECDSA, JWT (ES256) |
| HTTP client | httpx (async) |
| DB migrations | Alembic |
| Schema generation | rdflib (SHACL → JSON Schema) |

---

## Implementation Technology (continued)

### Development & Deployment

| Concern | Approach |
|---------|---------|
| Schema source of truth | SHACL (`.ttl` files) — generates JSON Schema at build time |
| API schema | JSON Schema generated from SHACL annotations |
| DB schema | SQLAlchemy ORM + Alembic migrations |
| Testing | pytest + pytest-asyncio |
| Linting | ruff |
| Packaging | pyproject.toml (setuptools) |
| Deployment target | ARM SBC (RPi 4+), x86 VPS, Docker |
| License | AGPLv3 |
| Repository | github.com/TinyLibraryFramework |

---

## ContentBank API Surface

**40+ endpoints across 8 modules**, all JWT-authenticated:

```
Auth        register · challenge · token
Agents      profile · update display name
Groups      create · members · delete
Objects     create · get · update · delete · list
            (SHACL validated · scope enforced · cursor paginated)
Calendar    events: create · get · update · delete · list
Inventory   items + collections: full CRUD
Replication GET /sync (node JWT · pull protocol)
Proxy       grants: create · view · add objects · revoke
            objects: pull shared object (grant JWT)
            subscriptions: register push callback
```

Schema → API pipeline: `make generate` runs SHACL → JSON Schema, produces `_create`, `_update`, `_response`, `_list_params` schemas per type.

---

## Deployment and Operations

### Getting started

```bash
# 1. Generate node identity
contentbank keygen
# Prints CB_NODE_PUBLIC_KEY and CB_NODE_PRIVATE_KEY for .env

# 2. Configure environment
export CB_NODE_ID="urn:cb:node:$(uuidgen | tr A-Z a-z)"
export CB_DATABASE_URL="postgresql+asyncpg://..."
# (plus node keys from keygen)

# 3. Provision database
alembic upgrade head

# 4. Start server
contentbank serve
# FastAPI on port 8000; replication worker starts automatically
```

Replication peers are configured by creating `ReplicationPeer` objects via the API — peer config replicates automatically across the mesh.

---

## Roadmap

### Near term
- **Have/Need Capability** — Craigslist-inspired community exchange
- **Contacts Capability** — Personal Object, relationship graph
- **IPFS integration** — Wire Kubo HTTP API for blob storage
- **Tool definitions** — Generate `tools.json` manifests from SHACL shapes
- **AGE knowledge graph** — Cross-Capability graph queries

### Longer term
- **Presence system** — Cross-LPWAN, Library Mesh presence
- **Filters and Mashups** — External data subscriptions, timeline views
- **Push replication** — Async push on write (complement to pull)
- **Conflict flagging** — Surface conflicted objects for agent review
- **SmartCapture** — Capture and classify external content into Content Bank
- **Glyph** — Avatar interface layer

---

## Summary

**Tiny Library** is decentralized personal and community infrastructure built for the real world — including the parts without internet.

- **Agentic** — AI agents use typed tools; no app silos
- **Federated** — nodes replicate and share with fine-grained control
- **Off-grid capable** — Meshtastic + Donkey Net fallback layers
- **User-sovereign** — your hardware, your keys, your data
- **Schema-driven** — SHACL as single source of truth, all the way down
- **Open** — AGPLv3; network modifications stay open

**Content Bank v0.1** is complete and available at:
`github.com/TinyLibraryFramework/ContentBank`

Framework documentation:
`github.com/TinyLibraryFramework/TinyLibrary`
