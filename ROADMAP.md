# MPF Roadmap

This document describes the current direction of the Memory Pod Fabric protocol. It complements the formal [SPECIFICATION.md](SPECIFICATION.md) by naming what is being worked on, what is in scope for the next version, and how implementations are tracked.

## Released Versions

### v0.1 — Memory Operations (May 2025)

The initial specification, defining MPF's core primitives:

- Memory pods as logical governance boundaries
- Memory objects as the fundamental unit of storage
- Capability tokens implementing the object-capability security model
- Merkle-anchored audit trails for tamper-evident verification
- Memory Card discovery via `.well-known/memory.json`
- Integration patterns with A2A and MCP

The v0.1 specification is published in [SPECIFICATION.md](SPECIFICATION.md) and remains the reference for memory-shaped operations.

## In Development

### v0.2 — Action Operations

The v0.2 specification extends MPF to consequential AI agent actions. The conceptual move is direct: the same protocol family that governs how AI systems remember can govern how AI systems act, with cryptographic accountability and operator-independent verification.

v0.2 is part inheritance from v0.1, part new architectural surface required by the action use case. Both are valuable for different reasons. The inherited primitives carry forward the conceptual framework and priority date. The new primitives address what consequential actions require that memory operations did not.

### What v0.2 inherits from v0.1

These primitives extend cleanly without forcing:

| Primitive | v0.1 use | v0.2 use |
|---|---|---|
| Capability tokens (object-capability security model) | Authorize memory pod access | Authorize specific action execution under specific policy (intent grants) |
| Pod-scoped governance | Memory pod boundaries with explicit access policy | Operator admission scope, workflow authorization, policy binding |
| Discovery via signed metadata | `.well-known/memory.json` | Action registry, witness registry, policy registry, operator registry |
| Apache 2.0 + explicit patent grants | Open protocol with defensive patent termination | Same |

The capability token primitive is the strongest inheritance. The signed, time-limited, scope-bound token shape that authorizes memory access in v0.1 authorizes action execution in v0.2 unchanged. The same is true for pod-style governance: a pod's "logical boundary with explicit policy" pattern generalizes directly to an operator's admission to a tenant under a workflow and policy hash.

### What v0.2 introduces

The action use case requires architectural surface that v0.1 did not specify:

- **Witness networks** — multi-tier independent witnesses (mechanical, policy, optional domain) with quorum semantics. v0.1 does not have witnesses; v0.2's witness model is net new.
- **Enclave attestation requirements** — gateways and L1 witnesses run in attested TEEs (e.g., Tinfoil, AWS Nitro Enclaves), with verifier checks against published code measurements. This adds a new trust primitive: not just *who signed* but *what code signed*.
- **Multi-registry authorization** — four signed registries (action, witness, policy, operator) cross-referenced at action time, with epoch rotation and pointer-backed registry delivery for operational maturity. v0.1's discovery is single-channel; v0.2 separates concerns across multiple signed authorities.
- **Certificate bundles** — portable artifacts containing hash-chained receipts, witness signatures, registry epoch references, attestation evidence, and operator identity binding. Unlike memory objects (designed to be retrieved and used), certificate bundles are designed to be *verified*, by parties who do not necessarily trust the operator.
- **Recipient verification receipts** — independently signed confirmation that content received by a recipient matches the certificate's commitment. Closes the loop from producer to consumer with cryptographic accountability rather than operator assertion.
- **Conformance levels** — assurance profiles ranging from local development through regulated/adversarial deployment, defined by the combination of witness independence, attestation requirements, retention mode, and transparency anchoring.

The witness network and certificate bundle are the two largest new pieces of architecture. Both exist because consequential actions impose verification requirements that memory operations did not: the recipient of an action must be able to confirm not just that the operator's records say something happened, but that multiple independent parties witnessed it happening under signed rules at the time of execution.

### v0.2 Scope (specification deliverables)

The v0.2 specification will define:

- The action gateway protocol — the entry point at which consequential actions enter the certified channel
- The witness model — tier definitions, quorum semantics, registry-anchored authorization
- The certificate bundle format — required components, schemas, hash-chaining requirements, attestation evidence
- The four-registry model — action, witness, policy, operator registries, with signing, pinning, and rotation requirements
- Operator identity binding — proof of which operator was authorized to process which class of action under which admission manifest at the time of action
- Recipient verification receipts — the format and protocol for independently checkable confirmation of received content
- Assurance profiles — conformance levels with explicit infrastructure requirements

### Milestones

- **v0.2 draft specification** — first public draft of the action-extension specification
- **First reference implementation** — see [IMPLEMENTATIONS.md](IMPLEMENTATIONS.md)
- **Working group cadence** — established alongside v0.2 draft publication
- **Conformance test suite** — verifier package and test bundles for implementation validation
- **v0.2 release candidate** — feature-complete draft for community review

Specific dates are intentionally not set. The v0.2 specification will release when the structural property holds end-to-end across the reference implementation and the conformance tests pass against authorities the operator does not control.

## Forward-Looking

Areas of interest for v0.3 and beyond, in no particular order:

- **Verifiable deliberation** — extending intent grants to multi-step reasoning chains
- **Verifiable delegation** — agent-to-agent capability handoff with chain-of-custody
- **Cross-protocol composition** — patterns for MPF + A2A and MPF + MCP coordination
- **Domain-specific profiles** — financial services, healthcare, government, regulated AI deployment
- **Public transparency anchoring** — Rekor/Trillian/SCITT-style logs for action histories

These are areas of interest, not commitments. The shape of v0.3 will be determined by what implementations and adopters demand.

## Working Group

The MPF working group exists to evolve the specification with input from implementers and adopters. See [GOVERNANCE.md](GOVERNANCE.md) for participation details. Working group activities include:

- Specification review and consensus building
- Implementation registry maintenance
- Conformance test suite development
- Cross-implementation interoperability testing
- Public meetings (cadence to be established alongside v0.2 draft publication)

## How to Get Involved

- **Implementers**: register your implementation in [IMPLEMENTATIONS.md](IMPLEMENTATIONS.md)
- **Reviewers**: open issues against the v0.2 draft when published
- **Adopters**: open issues describing your use case and constraints
- **Contributors**: see [CONTRIBUTING.md](CONTRIBUTING.md)

---

*This roadmap is a living document. It will be updated as v0.2 work progresses.*
