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

### Open / TBD

- **License selection:** AGPLv3 vs Apache 2.0 — needs decision before public repository
- **Governance model:** To be established as community grows
- **Data corruption recovery granularity:** What level of the Content Bank controls audit trail and versioning policies
- **Knowledge Graph implementation:** The original outline mentions cross-referenced data and dynamic linkage — the PRD doesn't yet detail how the knowledge graph layer interacts with the Content Bank's RDF objects
- **Presence system:** Mentioned in the original outline but not elaborated in the PRD — how does presence work across LPWAN and Library Mesh?
- **Filters and Mashups:** External data subscriptions, timeline views, and local inferences are in the outline but not yet specified as requirements
- **Have and Want system:** The Craigslist-inspired exchange mechanism from the original outline needs its own requirements section
- **Browser link tracker plugin:** Mentioned in the original outline under Organizer — not yet specified
- **Personal Object concept:** Referenced as the foundation for the Organizer — needs definition and relationship to Content Bank Objects

## Notes for Continued Development

The original outline (Tiny Library.md) contains several concepts not yet fully captured in the PRD: the Knowledge Graph, Presence system, Filters/Mashups, Have and Want exchange, and the browser link tracker. These should be developed into requirements sections as the project progresses.

The four use case scenarios in the improved PRD were designed to exercise as many requirements as possible and can serve as acceptance criteria seeds for future development sprints.
