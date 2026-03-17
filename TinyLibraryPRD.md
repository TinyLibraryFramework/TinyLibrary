<!-- TinyLibraryPRD.md -->
<!-- Updated: 2026-03-16 — aligned with design decisions from architecture sessions -->

Tiny Library Requirements Document

# What is Tiny Library

## Agentic framework
Tiny Library is an agentic framework for people to manage personal, family, and community information. Tiny Library is meant to fill the gaps that social media, phone apps, and PC applications have left in the quality of experience around things like schedule/calendar management, personal inventory, social contacts, and more.

## Multi-Modal Interaction
Tiny Library replaces apps with agentic workflows. Instead of a calendar app, the agent uses a tool that understands the format and semantics of calendar data and can filter for schedule objects in the Content Bank. The agent adapts the interaction around the schedule information to conform to the capabilities of the Avatar or client. This allows for multi-modal interaction.

## As a backup communication and information system
Tiny Library is meant to function when and where other means of communication are not available, for example in rural areas and times when cell service or internet connectivity are not available. Tiny Library can operate in "off-grid" mode and federate over a LPWAN mesh or other fallback network.

## Open Source
Tiny Library is an Open Source project licensed under AGPLv3. Community contribution is encouraged. The AGPLv3 license ensures that modifications to Tiny Library, including those deployed as network services, remain open source and available to the community. We intend to build a community of Tiny Library developers and contributors to create a sustaining resource.

## Multilayer mesh
Tiny Library is a node in a multilayer mesh network. Tiny Library provides an IP network bridge and backhaul router connections for meshtastic networks. Tiny Library Agents can pair with Avatar devices through the meshtastic gateway or directly over IP networks. The second mesh layer is a content mesh over which Tiny Libraries federate content through secure proxies and expand the scope and scale of the Tiny Library mesh. The content mesh would typically use IP networks directly but could be switched to meshtastic if IP connections are not available.

## Donkey Net
Tiny Library can use a latency tolerant communication protocol, Donkey Net, that can establish channels that can store and forward information using unreliable, long latency communication methods, with eventual consistency. Examples include writing a USB drive at one location and reading at another, enabling literal donkeys, or even carrier pigeons, to physically transport the intermediate storage media. For example, measurements from an unconnected remote sensor or data from a medical device can be transferred to a person's Content Bank in this way.

## Security
The Tiny Library security model is based on secure pairing. Each Avatar device or client device establishes a secure key pair with an agent endpoint, on behalf of a person, group, or role, in one or more Tiny Library nodes. The Avatar or Client device is responsible for authenticating locally with the person, group, or role and can optionally pass credentials on to the agent on behalf of the person, group, or role.

Each person, group, or role has a set of Content Bank key pairs that are used to authenticate them to their portion of the Content Bank. The key pairs are bound to permission levels in the Content Bank — a given key pair establishes a permission level such as read, create, update, execute, and so on.

## Data sharing via secure proxy
When content is to be shared or accessed through a remote Tiny Library, the source generates a purpose-specific ECDH key pair for the sharing relationship and provides a secure link to the authorized recipient (person, group, or role). The recipient authenticates to the content proxy using that key pair and can pull the specific objects they have been granted access to. The proxy verifies the JWT signed by the recipient's purpose-specific private key against the stored grant, checks that the requested object is in the grant's allowed list, and checks that the grant has not been revoked or expired.

Binary blob data (images, documents, etc.) is not served through the proxy. Recipients receive object metadata including IPFS CIDs and fetch binary blobs independently from the IPFS network.

Sharing grants can optionally allow subscriptions: the recipient registers a callback URL and the source notifies on any change to granted objects. For mesh-connected nodes without a stable HTTP endpoint, a polling fallback is used.

Sharing relationships are revocable at any time. Revocation is recorded as a soft delete on the grant — no key rotation is required, as all sharing keys are purpose-specific and become meaningless once the grant is revoked.

## Data corruption recovery
Optionally, an audit trail of modifications can be maintained by the source Content Bank with a record of which content proxy forwarded the modifications. Furthermore, a git-like commit sequence protocol can be used to enable reversion to previous versions. These policies are controlled at the Content Bank level.

## Deployment
A Tiny Library node can be deployed on a VPS or single board computer. For the most robust deployment, Tiny Library nodes would be deployed on local SBCs or servers with local meshtastic nodes in addition to Internet connections where available. There could also be point to point digital radio links between Tiny Library nodes, or they could be nodes in a TVWS or conventional LPWAN.


# Use Cases and Scenarios

## Rural communication where cellular service is not available
Maria runs a ranch in a rural area with no reliable cell service. Her Tiny Library node acts as an information and control hub for her Meshtastic network. Neighbors share emergency contacts, weather observations, and resource availability through the community scope of her Content Bank. When connectivity to the internet is briefly available, her node replicates changes to a cloud-hosted peer node so the information is not lost if her local hardware fails.

## Telemetry data from watershed, water systems, remote facilities, infrastructure
A water district deploys Tiny Library nodes at remote monitoring stations along a watershed. Sensor telemetry is ingested into Content Bank as time-series objects, replicated across nodes for redundancy. Donkey Net provides a last-resort path for stations that are intermittently unreachable over IP or Meshtastic. District staff access aggregated data through their own agents scoped to the appropriate group.

## Seamless experience across diverse network connectivity
David is a field biologist who moves between areas with LTE, Meshtastic coverage, and no connectivity at all. His Avatar device maintains a local cache and syncs with his Tiny Library node whenever a connection is available. Data he captures in the field — notes, photos, sensor readings — is stored locally and replicated to his Content Bank on reconnect. His experience is seamless regardless of connectivity.

## Multi-user household
The Hernandez family uses a single Tiny Library deployment. Each family member has their own Agent and can store private Individual-scoped objects in the shared Content Bank invisible to other family members. A Family-scoped ScopeGroup gives all members access to shared calendar events, household inventory, and community announcements. Parents can post Community-scoped items visible to their neighborhood group. Each member's private data is physically stored on the same node but enforced as inaccessible to others at the application and database layers.


# Requirements

## Functional requirements

### Content storage
- Content Bank must support multiple co-resident users (Agents) in a single deployment
- Each Object must have an owner (Agent or ScopeGroup) and a visibility scope (Individual, Family, Group, or Community)
- Scope must be equal to or broader than ownership — an object cannot be invisible to its own owner
- Objects must support zero or more binary blob attachments, each identified by an IPFS CID, MIME type, and role (primary, thumbnail, raw, transcript, preview)
- SHACL validation must be enforced on every write before data reaches storage

### Capability system
- Horizontal functionality must be partitioned into Capabilities (Calendar, Inventory, Have/Need, Contacts, Tasks, etc.)
- Each Capability must be a self-contained plug-in bundle: agent prompt, tool definitions (JSON Schema), SHACL shapes, and manifest
- The Workflow layer must discover Capabilities dynamically at runtime from manifests
- Tool definitions must be compatible with LLM function-calling APIs (Anthropic/OpenAI format)

### Replication
- Each node must hold a complete copy of its Content Bank (no sharding)
- Replication must be peer-to-peer with no central coordinator
- Topology may be fully or partially meshed; each node maintains an explicit peer list
- Default sync mode is pull at a configurable interval
- Changes to ScopeGroup membership must be causally ordered — no node may process a write referencing a ScopeGroup state it has not yet received
- Conflict resolution must be deterministic (last-write-wins by updated timestamp and originating node ID as tiebreaker)

### Data sharing
- Object-granularity sharing must be possible between Content Banks on different nodes
- Sharing must be controlled by purpose-specific ECDH key pairs (not Agent identity keys)
- Sharing grants must be revocable at any time per sharing relationship
- Shared objects must not include binary blobs — recipients fetch blobs from IPFS separately
- Push-on-change subscriptions must be optional per grant

### Security
- Agent identity must be based on ECDH key pairs
- All replication and proxy requests must be authenticated with JWT signed by the node or agent key
- Scope access rules must be enforced at the application layer on every read path; PostgreSQL row-level security provides an additional backstop

## Non-functional requirements
- Must deploy on ARM single-board computers (Raspberry Pi 4 class) and x86 cloud instances
- Must operate within approximately 8GB RAM per node (shared with database, IPFS, and agent stack)
- Must operate in off-grid mode with no internet connectivity
- Must tolerate temporary loss of peer nodes without data loss


# Architecture

## Agents and Avatars
- Avatars are physical devices or client software that a person uses to interact with Agents
- An Avatar connects to a Tiny Library node using secure pairing with ECDH keys
- A person, group, or role uses a workflow layer key pair to connect to a data set in the Content Bank
- An Agent uses tools to send and retrieve messages to communicate with an Avatar
- An Avatar has a default agent that handles the communication endpoint and keeps the interaction history
- An Avatar Agent may spawn sub-agents for specialized tasks and tools within a workflow

## Librarian
- There is a base Agent, the Librarian, that can access the content bank for management and administrative tasks
- The Librarian agent is the default interface for Tiny Library and handles the front end for connections and handles the back end for Content Bank access
- The Librarian works primarily with local context for people, groups, or roles, and has access to information in the Content Bank that is proxied for access
- Each person, group, or role has their own private interactions with the Librarian
- The Librarian may provide a completely separate context for each person, group, or role
- The Librarian cannot escalate access beyond what the authenticated key pair allows
- The Librarian must clearly communicate refusals to the Avatar when a request exceeds permission level

## Capability plug-in bundles
Horizontal Tiny Library functionality is partitioned into Capabilities. Each Capability is a self-contained plug-in bundle:

```
capability/
  {name}/
    manifest.json      — Capability identity and metadata
    tools.json         — JSON Schema tool definitions (LLM function-calling compatible)
    shapes.ttl         — SHACL shapes for ContentBank RDF objects
    agent-prompt.md    — Agent specialization context
```

Current Capabilities: Calendar, Inventory. Planned: Have/Need, Contacts, Tasks, Notifications.

In PostgreSQL, each Capability maps to its own schema. ContentBank core objects live in a shared `core` schema. Tools encapsulate all SQL — agents never query the database directly.

## Schema layer
SHACL is the single source of truth for the Content Bank data model. JSON Schema tool definitions are generated from SHACL shapes at build time.

| Layer | Format | Purpose |
|-------|--------|---------|
| Agent ↔ Tool | JSON Schema (`tools.json`) | Tool interface definitions; maps to LLM function-calling format; enables dynamic Capability discovery |
| Tool ↔ Content Bank | SHACL (`shapes.ttl`) | RDF object shape validation; enforced before every write; defines the RDF vocabulary for graph traversal |

SHACL shapes carry API annotations (access mode, mutability, filter/sort flags) that drive schema generation. The generator emits per-type create, update, response, and list-params schemas.

## Content Bank
- Content Bank provides application and administrative storage for Tiny Library
- A Content Bank deployment supports multiple tenants (Agents and ScopeGroups) with data isolation enforced by owner and scope properties on every Object
- The atomic unit of storage in Content Bank is an Object: an RDF subgraph with descriptive metadata, optionally with binary blobs attached via IPFS CIDs
- Binary blobs are stored in IPFS; the Content Bank graph holds CID references, MIME types, roles, and sizes — not the binary data itself
- Content Bank uses PostgreSQL as primary structured storage, TimescaleDB for time-series records, Apache AGE for knowledge graph traversal across Capabilities, and IPFS for blob storage

## Technical stack
- **Language:** Python throughout (core API server and Capability tools)
- **API server:** FastAPI with async PostgreSQL (asyncpg/SQLAlchemy)
- **Validation:** pySHACL + rdflib (shapes loaded and cached at startup)
- **Database:** PostgreSQL + TimescaleDB + Apache AGE
- **Blob storage:** IPFS (Kubo)
- **Auth:** ECDH key pairs, JWT (ES256)
- **Schema generation:** rdflib-based build-time pipeline (SHACL → JSON Schema)


# Glossary

| Term | Definition |
|------|-----------|
| Agent | A software entity that acts on behalf of a person, group, or role. Agents use tools to access the Content Bank. |
| Avatar | A physical device or client application through which a person interacts with their Agent. |
| Capability | A plug-in bundle providing a specific domain of functionality (Calendar, Inventory, etc.). Each bundle includes tools, SHACL shapes, an agent prompt, and a manifest. |
| Content Bank | The content-addressable persistent storage layer for Tiny Library. Stores RDF object graphs with IPFS blob attachments. |
| Content Mesh | The federation layer enabling Tiny Library nodes to share content via secure proxies. |
| Donkey Net | A latency-tolerant store-and-forward protocol for unreliable, high-latency communication paths. |
| ECDH | Elliptic Curve Diffie-Hellman. Used for secure pairing and purpose-specific sharing key pairs. |
| Librarian | The default Agent in a Tiny Library deployment. Manages content access and the communication front end. |
| LPWAN | Low-Power Wide-Area Network. Includes Meshtastic and other long-range low-bandwidth radio protocols. |
| Meshtastic | An open-source LPWAN mesh networking protocol used as a fallback communication layer. |
| Object | The atomic unit of Content Bank storage. An RDF subgraph with metadata fields and optional IPFS blob attachments. |
| RDF | Resource Description Framework. The graph data model used for Content Bank metadata. |
| Scope | The visibility boundary of a Content Bank Object. Ordered from least to most restrictive: Community, Group, Family, Individual. |
| ScopeGroup | A named group of Agents (family or group type) used as a scope and ownership value. Membership is an explicit list. |
| SHACL | Shapes Constraint Language. Used to define and validate the structure of RDF objects in Content Bank. SHACL is the single source of truth for the data model. |
| SharingGrant | A Content Bank object that authorizes a recipient (identified by a purpose-specific key pair) to pull specific objects from a remote Content Bank via the sharing proxy. |
