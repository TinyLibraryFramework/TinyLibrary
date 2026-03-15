<!-- TinyLibraryPRD.md -->

# Tiny Library Product Requirements Document

## Table of Contents

1. [What is Tiny Library](#what-is-tiny-library)
2. [Requirements](#requirements)
3. [Use Cases and Scenarios](#use-cases-and-scenarios)
4. [Architecture](#architecture)
5. [Open Source and Governance](#open-source-and-governance)
6. [Glossary](#glossary)

---

# What is Tiny Library

## Agentic Framework

Tiny Library is an agentic framework for people to manage personal, family, and community information. Tiny Library is meant to fill the gaps that social media, phone apps, and PC applications have left in the quality of experience around things like schedule/calendar management, personal inventory, social contacts, and more.

## Multi-Modal Interaction

Tiny Library replaces apps with agentic workflows. Instead of a calendar app, the Agent uses a tool that understands the format and semantics of calendar data and can filter for schedule Objects in the Content Bank. The Agent adapts the interaction around the schedule information to conform to the capabilities of the Avatar or client. This allows for multi-modal interaction across diverse devices and connectivity conditions.

## Backup Communication and Information System

Tiny Library is meant to function when and where other means of communication are not available, for example in rural areas and times when cell service or internet connectivity are not available. Tiny Library can operate in "off-grid" mode and federate over a LPWAN mesh or other fallback network.

## Multilayer Mesh

Tiny Library is a node in a multilayer mesh network. Tiny Library provides an IP network bridge and backhaul router connections for Meshtastic networks. Tiny Library Agents can pair with Avatar devices through the Meshtastic gateway or directly over IP networks. The second mesh layer is a content mesh over which Tiny Libraries federate content through secure proxies and expand the scope and scale of the Tiny Library mesh. The content mesh would typically use IP networks directly but could be switched to Meshtastic if IP connections are not available.

## Donkey Net

Tiny Library can use a latency-tolerant communication protocol, Donkey Net, that can establish channels that store and forward information using unreliable, long-latency communication methods, with eventual consistency. Examples include writing a USB drive at one location and reading it at another, enabling literal donkeys, or even carrier pigeons, to physically transport the intermediate storage media. For example, measurements from an unconnected remote sensor or data from a medical device can be transferred to a person's Content Bank in this way.

---

# Requirements

## Security

### Secure Pairing

The Tiny Library security model is based on secure pairing. Each Avatar device or client device establishes a secure key pair with an Agent endpoint, on behalf of a person, group, or role, in one or more Tiny Library nodes. The Avatar or client device is responsible for authenticating locally with the person, group, or role and can optionally pass credentials on to the Agent on behalf of the person, group, or role.

### Content Bank Authorization

Each person, group, or role has a set of Content Bank key pairs that are used to authenticate to a slice of the Content Bank, which can be considered somewhat like a user account. The key pairs are bound to permission levels in the Content Bank — a given key pair establishes a permission level such as read, create, update, execute, and so on.

### Data Sharing via Secure Proxy

When content is to be shared or accessed through a remote Tiny Library, the source of the content generates a secure key pair with, and authenticates to, a content proxy, and provides a secure link to an authorized sharer of the content (person, group, or role). The authorized sharer authenticates to the content proxy and establishes a sharing connection for the allowed content and permission level. The proxy then fetches and updates, as allowed, from the source Content Bank.

### Data Corruption Recovery

Optionally, an audit trail of modifications can be maintained by the source Content Bank with a record of which content proxy forwarded the modifications. Furthermore, a git-like commit sequence protocol can be used to enable reversion to previous versions. These policies should be controlled at some TBD granularity in the Content Bank.

## Deployment

A Tiny Library node can be deployed on a VPS or single board computer. For the most robust deployment, Tiny Library nodes would be deployed on local SBCs or servers with local Meshtastic nodes in addition to Internet connections where available. There could also be point-to-point digital radio links between Tiny Library nodes, or they could be nodes in a TVWS or conventional LPWAN.

---

# Use Cases and Scenarios

## Rural Communication Where Cellular Service Is Not Available

Maria runs a cattle ranch 40 miles from the nearest cell tower. A Tiny Library node on a single board computer at her homestead connects to a small Meshtastic network covering her property and neighboring ranches. When a neighbor needs to coordinate a shared water delivery, they send a message from a handheld Meshtastic device. The message reaches Maria's Tiny Library node, where her Librarian agent stores it and presents it the next time she checks in from her own Avatar device. If her satellite internet link is active, the content mesh federates the message to the neighbor's Tiny Library node for confirmation. If not, the message waits — the system is resilient to intermittent connectivity.

**Key requirements demonstrated:** off-grid operation, Meshtastic gateway, content federation, store-and-forward resilience.

## Telemetry Data from Watershed, Water Systems, and Remote Infrastructure

A rural water district uses low-power sensors along a creek to monitor water levels, flow rate, and temperature. Each sensor periodically transmits readings over LPWAN to a nearby Tiny Library node, which stores them as Objects in the Content Bank. A district manager's Librarian agent aggregates telemetry from multiple nodes via the content mesh, flags anomalies, and surfaces alerts. Historical data is versioned using the commit sequence protocol so that accidental overwrites can be reverted. In the most remote locations where even LPWAN coverage is spotty, a technician on a monthly visit writes sensor logs to a USB drive and physically carries it to the nearest node — a Donkey Net channel.

**Key requirements demonstrated:** telemetry ingestion, Content Bank storage, content mesh aggregation, data corruption recovery, Donkey Net for last-mile gaps.

## Seamless Experience Across Diverse Network Connectivity

David is a field biologist who moves between a research station with broadband, a base camp with only Meshtastic coverage, and remote survey sites with no connectivity at all. At the research station, his laptop Avatar connects over IP to his Tiny Library node and he works with rich media — photo galleries, detailed reports, and real-time collaboration with colleagues via the content mesh. At base camp, his handheld Avatar falls back to Meshtastic. The Librarian agent adapts the interaction: instead of a photo gallery, it provides text summaries and compact data. At a survey site with no network at all, David logs observations locally on his device. When he returns to Meshtastic range, the Avatar syncs the new Objects to his Content Bank automatically.

**Key requirements demonstrated:** multi-modal interaction, Avatar adaptability, seamless fallback across IP / Meshtastic / offline, automatic sync on reconnection.

## Family and Community Organization

The Hernandez family uses a shared Tiny Library node at home. Each family member has their own Content Bank slice with private data — personal contacts, schedules, and notes — secured by individual key pairs. A shared family slice holds the grocery list, household inventory, chore schedule, and a shared photo gallery. The Librarian agent knows the difference: when 14-year-old Sofia asks "what's for dinner tonight?" it draws from the family meal plan in the shared slice, but when she asks "show me my homework list" it accesses only her private slice. A neighborhood group also federates through the family's node, sharing a community event calendar and a "Have and Want" board for exchanging tools and produce.

**Key requirements demonstrated:** per-person Content Bank slices, role-based authorization, family and group scopes, Librarian context isolation, community federation.

---

# Architecture

## Agents and Avatars

- Avatars are physical devices or client software that a person uses to interact with Agents.
- An Avatar connects to a Tiny Library node using secure pairing with ECDH keys.
- A person, group, or role uses a workflow-layer key pair to connect to a data set in the Content Bank.
- An Agent uses tools to send and retrieve messages to communicate with an Avatar.
- An Avatar has a default Agent that handles the communication endpoint and maintains the interaction history.
- An Avatar Agent may spawn sub-agents for specialized tasks and tools within a workflow.

## Librarian

- There is a base Agent, the Librarian, that can access the Content Bank for management and administrative tasks.
- The Librarian agent is the default interface for Tiny Library. It handles the front end for Avatar connections and the back end for Content Bank access.
- The Librarian works primarily with local context for people, groups, or roles, and has access to information in the Content Bank that is proxied for access.
- Each person, group, or role has their own private interactions with the Librarian.
- The Librarian provides a completely separate context for each person, group, or role, ensuring that private data and interaction history are never shared across contexts.

### Librarian Boundaries and Conflict Resolution

- When operating across multiple roles (e.g., a person who is also a member of a group), the Librarian resolves conflicts by deferring to the narrower scope — individual permissions take precedence over group defaults.
- The Librarian enforces Content Bank permission levels and cannot escalate access beyond what the authenticated key pair allows.
- The Librarian may refuse or defer requests that exceed the current role's authorization, and must clearly communicate the reason to the Avatar.

## Tools and Data Models

- Tools and data models work together to provide a consistent interface for Agents to use the Content Bank.
- Data models are based on RDF with SHACL constraints that determine the shape and types of metadata in the Content Bank.
- Tools can create, read, update, and delete data in the Content Bank and can execute other operations like fetching external URLs.
- Tools and data models are specialized to work with particular workflows.
- Agents are scoped to a set of tools and data models.

## Content Bank

- Content Bank provides application and administrative storage for Tiny Library.
- Content Bank is divided into slices which are bound to people, groups, or roles using authorization and secure pairing.
- The atomic unit of storage in Content Bank is an Object, each Object consisting of an RDF subgraph with descriptive metadata, and optionally opaque binary objects bound to the metadata that can be handled by content-specific tools or handlers specified by the Object metadata.
- Content Bank can replicate Object data to other Tiny Library nodes for redundancy, and can chunk and replicate the opaque data across multiple Tiny Library nodes for improved bandwidth and redundancy.

---

# Open Source and Governance

Tiny Library is an open source project with community contribution encouraged. The project intends to build a sustaining community of developers and contributors.

- **License:** TBD — candidates include AGPLv3 (to ensure federation modifications remain open) or Apache 2.0 (for broader adoption flexibility).
- **Contribution model:** Contributions are welcome via pull requests. A core maintainer group reviews and merges changes.
- **Governance:** TBD — initial development is led by the founding contributors. As the community grows, a more formal governance model (e.g., a steering committee or benevolent dictator for life model) should be established.
- **Code of conduct:** The project will adopt a standard code of conduct to ensure an inclusive and respectful community.

---

# Glossary

| Term | Definition |
|------|-----------|
| **Agent** | An AI-powered process that performs tasks on behalf of a person, group, or role. Agents use tools to interact with the Content Bank and communicate with Avatars. |
| **Avatar** | A physical device (e.g., handheld radio, tablet) or client software through which a person interacts with an Agent. Avatars adapt their presentation to match device capabilities and connectivity. |
| **Content Bank** | The primary data store for a Tiny Library node. Divided into slices, it holds Objects consisting of RDF metadata and optional binary data. |
| **Content Mesh** | The overlay network through which Tiny Library nodes federate and share content via secure proxies. Typically runs over IP but can fall back to LPWAN. |
| **Donkey Net** | A latency-tolerant store-and-forward protocol for communication over unreliable or physical transport channels (e.g., USB drives, radio bursts). |
| **ECDH** | Elliptic Curve Diffie-Hellman — a key agreement protocol used for secure pairing between Avatars and Agents. |
| **Librarian** | The default Agent on a Tiny Library node. It manages Avatar connections, Content Bank access, and provides context-isolated interactions for each person, group, or role. |
| **LPWAN** | Low-Power Wide-Area Network — a class of wireless technologies (e.g., LoRa, Meshtastic) designed for long-range, low-bandwidth communication. |
| **Meshtastic** | An open source project for LoRa-based mesh networking, used by Tiny Library as a LPWAN transport layer. |
| **Object** | The atomic unit of storage in the Content Bank. An RDF subgraph with descriptive metadata and optional opaque binary attachments. |
| **RDF** | Resource Description Framework — a standard model for data interchange on the web, used as the metadata foundation for Content Bank Objects. |
| **Secure Pairing** | The process by which an Avatar or client establishes a trusted, encrypted connection with an Agent endpoint using ECDH key pairs. |
| **SHACL** | Shapes Constraint Language — used to define and validate the structure of RDF data in the Content Bank. |
| **Slice** | A partition of the Content Bank bound to a person, group, or role via authorization key pairs. Analogous to a user account. |
| **TVWS** | TV White Spaces — unused broadcast television frequencies that can be used for broadband wireless communication in rural areas. |
