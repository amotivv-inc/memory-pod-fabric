# Memory Pod Fabric (MPF): Open Protocol for AI Memory Infrastructure

![Status: v0.1 Released · v0.2 In Development](https://img.shields.io/badge/Status-v0.1%20Released%20%C2%B7%20v0.2%20In%20Development-blue)
![License: Apache 2.0](https://img.shields.io/badge/License-Apache%202.0-blue.svg)

Memory Pod Fabric (MPF) is an open protocol specification that defines standardized methods for storing, retrieving, and securing semantic memories across AI applications, agents, and organizations.

## 🌟 Overview

As AI systems become more sophisticated and persistent, the need for standardized, verifiable, and securely shareable memory becomes critical. MPF addresses this challenge by providing a standardized protocol layer that can be implemented across diverse environments while maintaining consistent capabilities.

MPF serves as the essential third pillar in the open agent protocol stack:

- **A2A** lets agents talk to each other
- **MCP** lets models use tools
- **MPF** lets everyone remember

## 📍 Status

**MPF v0.1** is released. The specification defines the core memory operations: pods, objects, capability tokens, Merkle-anchored audit trails, and Memory Card discovery. It is published as an open standard under Apache 2.0.

**MPF v0.2** is in active development. v0.2 extends MPF's core primitives — capability tokens, Merkle anchoring, pod-scoped governance — from memory operations to action operations. Where v0.1 specified how AI systems remember, v0.2 specifies how AI systems act: capability-gated intent grants, witness-attested execution, certificate-bundled receipt chains, and downstream verification by counterparties.

For the v0.2 scope and conceptual mapping from v0.1 primitives, see [ROADMAP.md](ROADMAP.md).

For the current reference implementation and conformance evidence, see [IMPLEMENTATIONS.md](IMPLEMENTATIONS.md).

## 📋 Table of Contents

- [Key Features](#key-features)
- [Protocol Specification](#protocol-specification)
- [Quick Start](#quick-start)
- [Core Concepts](#core-concepts)
- [Implementations](#implementations)
- [Roadmap](#roadmap)
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

The complete v0.1 protocol specification is available in [SPECIFICATION.md](SPECIFICATION.md). This specification defines:

- Core memory storage and retrieval operations
- Memory discovery and capability advertising
- Capability-based authorization model
- Cryptographic verification mechanisms
- Integration patterns with existing AI protocols
- Reference data structures and formats

The v0.2 draft specification (action operations) is published in [SPECIFICATION-v0.2-draft.md](SPECIFICATION-v0.2-draft.md). Scope, conceptual mapping from v0.1 primitives, and conformance levels are tracked in [ROADMAP.md](ROADMAP.md).

## 🚀 Quick Start

To implement MPF in your project:

1. For memory operations, review [SPECIFICATION.md](SPECIFICATION.md) (v0.1, stable)
2. For action operations, review [SPECIFICATION-v0.2-draft.md](SPECIFICATION-v0.2-draft.md) (v0.2, draft)
3. Review [IMPLEMENTATIONS.md](IMPLEMENTATIONS.md) to see a live reference implementation
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

## 🛠️ Implementations

A reference implementation of MPF v0.2 is in active development at amotivv. It produces certificate bundles that are independently verifiable against the protocol's primitives, with a live verifier endpoint and conformance evidence registry.

For the full implementations registry — including conformance status, evidence, and verification surfaces — see [IMPLEMENTATIONS.md](IMPLEMENTATIONS.md).

## 🗺️ Roadmap

The roadmap covers v0.2 scope (action operations), conceptual mapping from v0.1 primitives, conformance criteria, and ongoing protocol governance. See [ROADMAP.md](ROADMAP.md).

## 🤝 Contributing

We welcome contributions to the MPF specification and reference implementations. Please see our [CONTRIBUTING.md](CONTRIBUTING.md) for guidelines on how to participate.

## 🏛️ Governance

The MPF protocol is stewarded by amotivv as the original author of the specification. Governance for v0.2 — including the working group structure, contribution model, and decision-making process — is documented in [GOVERNANCE.md](GOVERNANCE.md) and will evolve alongside v0.2 publication.

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
- **Repository**: [github.com/amotivv-inc/memory-pod-fabric](https://github.com/amotivv-inc/memory-pod-fabric)

---

*Memory Pod Fabric: The Memory Protocol for AI Agents*

[amotivv, inc](https://amotivv.com/)
