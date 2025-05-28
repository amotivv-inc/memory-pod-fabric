# Memory Pod Fabric (MPF): Open Protocol for AI Memory Infrastructure

![Status: Draft Specification](https://img.shields.io/badge/Status-Draft%20Specification-yellow)
![License: Apache 2.0](https://img.shields.io/badge/License-Apache%202.0-blue.svg)
![Version: 0.1](https://img.shields.io/badge/Version-0.1-orange)

Memory Pod Fabric (MPF) is an open protocol specification that defines standardized methods for storing, retrieving, and securing semantic memories across AI applications, agents, and organizations.

## 🌟 Overview

As AI systems become more sophisticated and persistent, the need for standardized, verifiable, and securely shareable memory becomes critical. MPF addresses this challenge by providing a standardized protocol layer that can be implemented across diverse environments while maintaining consistent capabilities.

MPF serves as the essential third pillar in the open agent protocol stack:
- **A2A** lets agents talk to each other
- **MCP** lets models use tools
- **MPF** lets everyone remember

## 📋 Table of Contents

- [Key Features](#key-features)
- [Protocol Specification](#protocol-specification)
- [Quick Start](#quick-start)
- [Core Concepts](#core-concepts)
- [Implementation Guidance](#implementation-guidance)
- [Reference Implementations](#reference-implementations)
- [Contributing](#contributing)
- [Governance](#governance)
- [License](#license)
- [Security](#security)
- [Community](#community)

## ✨ Key Features

- **Standardized API** for memory storage and retrieval
- **Capability-based security** with fine-grained permissions
- **Cryptographic verification** for regulatory compliance
- **Multi-model vector support** for future-proof embeddings
- **Designed for interoperability** with existing AI protocols

## 📚 Protocol Specification

The complete protocol specification is available in the [SPECIFICATION.md](SPECIFICATION.md) file. This specification defines:

- Core memory storage and retrieval operations
- Memory discovery and capability advertising
- Capability-based authorization model
- Cryptographic verification mechanisms
- Integration patterns with existing AI protocols
- Reference data structures and formats

## 🚀 Quick Start

To implement MPF in your project:

1. Review the [SPECIFICATION.md](SPECIFICATION.md) to understand the protocol
2. Explore the [examples](examples/) directory for implementation samples (coming soon)
3. Choose a reference implementation or create your own following the specification
4. Implement the core endpoints required for MPF compliance

## 🧠 Core Concepts

### Memory Pod

A Memory Pod is a logical boundary of memory with its own governance, access controls, and audit trail. A pod represents a collection of memory objects that share common access patterns and security requirements.

### Memory Object

A Memory Object is the fundamental unit of storage in MPF, containing vector embeddings, metadata, content, and access control information.

### Capability Tokens

MPF uses signed JWT tokens that implement the object-capability security model for fine-grained access control with specific, time-limited permissions.

### Merkle Verification

MPF uses cryptographic audit trails through Merkle trees, providing tamper-evident verification for regulatory compliance and trust.

## 🛠️ Implementation Guidance

MPF can be implemented on various storage backends:

- **Postgres + pgVector**: Recommended for self-hosted deployments
- **Snowflake VECTOR**: Ideal for enterprise deployments
- **Specialized vector DBs**: Pinecone, Weaviate, Milvus, etc.
- **Multi-tier storage**: Hot/warm/cold storage separation

For detailed implementation guidance, see the [implementation guide](docs/implementation-guide.md).

## 📦 Reference Implementations

Official reference implementations will be available in multiple languages:

- [Python SDK](https://github.com/amotivv/memory-pod-sdk-py)
- [TypeScript SDK](https://github.com/amotivv/memory-pod-sdk-ts)
- [Postgres Reference Server](https://github.com/amotivv/memory-pod-postgres)

## 🤝 Contributing

We welcome contributions to the MPF specification and reference implementations. Please see our [CONTRIBUTING.md](CONTRIBUTING.md) for guidelines on how to participate.

## 🏛️ Governance

The MPF protocol is governed by a community working group that includes representatives from various organizations. For more information on governance and the decision-making process, see [GOVERNANCE.md](GOVERNANCE.md).

## 📄 License

This specification is licensed under the [Apache License 2.0](LICENSE).

The Apache 2.0 license provides patent protections that make it ideal for an open protocol:
- Explicit patent grant from contributors
- Defensive termination provisions
- Compatible with commercial use
- Industry standard for open protocols

## 🔒 Security

For information about the security model and best practices, see [SECURITY.md](SECURITY.md). For reporting security vulnerabilities, please follow the process outlined in the security document.

## 👥 Community

- **Twitter/X**: Follow [@amotivv](https://twitter.com/amotivv) for updates
- **Meetings**: Regular community working group meetings (see [GOVERNANCE.md](GOVERNANCE.md))

---

<p align="center">
  <em>Memory Pod Fabric: The Memory Protocol for AI Agents</em><br>
  <a href="https://amotivv.com">amotivv, inc</a>
</p>
