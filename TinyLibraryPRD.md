<!-- TinyLibraryPRD.md -->

Tiny Library Requirements Document

# What is Tiny Library

## Agentic framework
Tiny Library is an agentic framework for people to manage personal, family, and community information. Tiny Library is meant to fill the gaps that social media, phone apps, and PC applications have left in the quality of experience around things like schedule/calendar management, personal inventory, social contacts, and more.

## Multi-Modal Interaction
Tiny Library replaces apps with agentic workflows. Instead of a calendar app, the agent uses a tool that understands the format and semantics of calendar data and can filter for schedule objects in the Content Bank. The agent adapts the interaction around the schedule information to conform to the capabilities of the Avatar or client. This allows for multi-modal interaction.

## As a backup communication and information system
Tiny Library is meant to function when and where other means of communication are not available, for example in rural areas and times when cell service or internet connectivity are not available. Tiny Library can operate in "off-grid" mode and federate over a LPWAN mesh or other fallback network.

## Open Source
Tiny Library is an Open Source project with community contribution encouraged. We intend to build a community of Tiny Library developers and contributors to create a sustaining resource.

## Multilayer mesh
Tiny Library is a node in a multilayer mesh network. Tiny Library provides an IP network bridge and backhaul router connections for meshtastic networks. Tiny Library Agents can pair with Avatar devices through the meshtastic gateway or directly over IP networks. The second mesh layer is a content mesh over which Tiny Libraries federate content through secure proxies and expand the scope and scale of the Tiny Library mesh. The content mesh would typically use IP networks directly but could be switched to meshtastic if IP connections are not available.

## Donkey Net
Tiny Library can use a latency tolerant communication protocol, Donkey Net, that can establish channels that can store and forward information using unreliable, long latency communication methods, with eventual consistency. Exmaples include writing a USB drive at one location and reading at another, enabling literal donkeys, or even carrier pigeons, to physically transport the intermediate storage media. For example, measurements from a unconnected remote sensor or data from a medical device can be transferred to a person's Content Bank in this way.

## Security
The Tiny Library security model is based on secure pairing. Each Avatar device or client device establishes a secure key pair with an agent endpoint, on behalf of a person, group, or role, in one or more Tiny Library nodes. The Avatar or Client device is responsible for authenticating locally with the person, group, or role and can optionally pass credentials on to the agent on behalf of the person, group, or role. 

Each person, group, or or role has a set of Content Bank key pairs that are used to authenticate a person, group, or role to a slice of the Content Bank, which can be considered to be somewhat like a user account. The key pairs are bound to permission levels in the Content Bank, that is, a given key pair establishes a permission level, like read, create, update, execute, etc.

## Data sharing via secure proxy
When content is to be shared or accessed through a remote Tiny Library , the source of the content generates a secure key pair with, and authenticates to, a content proxy, and provides a secure link to an authorized sharer of the content (person, group, or role). The authorized sharer authenticates to the content proxy and establishes a sharing connection for the allowed content and permission level. The proxy then fetches and updates, as allowed, from the source content bank. 

## Data corruption recovery
Optionally, an audit trail of modifications can be maintained by the source Content Bank with a record of which content proxy forwarded the modifications. Furthermore, a git-like commit sequence protocol can be used to enable reversion to previous versions. These policies should be controlled at some TBD granularity in the content bank.

## Deployment
A Tiny Library node can be deployed on a VPS or single board computer. For the most robust deployment, Tiny Library nodes would be deployed on local SBCs or servers with local meshtastic nodes in addition to Internet connections where available. There could also be point to point digital radio links between Tiny Library nodes, or they could be nodes in a TVWS or conventional LPWAN.

# Use cases and scenarios
## Rural Communication where cellular service is not available
- Tiny Library as information and control hub for meshtastic and other LPWAN networks

## Telemetry data from watershed, water systems, remote facilities, infrastructure
- Tiny Library provides a local and distributed storage for telemetry data from watershed elements, remotely situated facilities, and infrastructure components like bridges, pipelines, and roadbeds.

## Seamless experience across diverse network connectivity
- Tiny Library provides a seamless experience as the client or avatar switches from LPWAN to high speed noework connections
- Some clients and workflows can use high speed data access

## 

# Architecture

# Agents and Avatars

# Tools and data models

# Content Bank

