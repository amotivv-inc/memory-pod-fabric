# MPF Implementations

This document tracks known implementations of the Memory Pod Fabric protocol, their conformance status, and publicly available evidence of conformance.

Implementations may target MPF v0.1 (memory operations, released) or MPF v0.2 (action operations, in development). Each implementation is registered with the protocol version it conforms to, the maintainer, and links to evidence a third party can independently verify.

---

## amotivv MPF v0.2 Reference Implementation

| Field | Value |
|---|---|
| **Codebase** | Strata (internal identifier) |
| **Status** | Production-grade pilot deployment |
| **Conformance target** | [MPF v0.2 draft](SPECIFICATION-v0.2-draft.md) (action operations) |
| **Maintainer** | [amotivv, inc](https://amotivv.com) |
| **License** | Proprietary implementation; protocol conformance under MPF Apache 2.0 |

The first reference implementation of MPF v0.2, extending MPF's core primitives to consequential AI agent actions. The implementation produces certificate bundles for actions invoked through the Model Context Protocol (MCP), with capability-gated execution, multi-witness signature flows, and downstream verifiability.

### Architecture (v0.2 Mapping)

| MPF v0.2 Primitive | Implementation Component |
|---|---|
| Action Gateway scope | Gateway service (policy + capability + orchestration boundary) |
| Certificate Bundle | Portable, durable artifact published to immutable storage with stable URL |
| Intent Grant | Capability-bound token issued by the Gateway after policy resolution |
| Certificate-Bundled Receipt Chain | Per-action signed bundle containing intent grant, witness signatures, policy reference, registry epoch hashes, execution receipt, substrate attestation |
| Action Registry | Signed, versioned registry of action definitions with witness tier, policy bundle, schema, adapter target |
| Witness Registry | Signed registry of authorized witnesses with public keys and validity periods |
| Policy Registry | Signed registry of policy bundles cited per-certificate |
| Operator Registry | Signed registry of authorized operators with attestation profile and jurisdiction |
| Multi-Witness Trust Tiers | L1 mechanical (enclave-attested), L2 policy (rule compliance), L3 domain-attested (optional expert sign-off) |

### Live Conformance Evidence

A reference certificate bundle is publicly available for independent verification:

- **Bundle URL:** *(to be inserted by maintainer at publication time)*
- **Verifier endpoint:** `https://strata-certificate-verifier.fly.dev/verify`
- **Latest verifier result against the reference bundle:** 50 pass · 0 warn · 0 fail (bundle URL forthcoming)

Anyone can fetch the bundle, submit it to the verifier endpoint, and independently confirm:

1. All signatures match published public keys
2. Registry epoch hashes resolve to the registries cited
3. Witness signatures meet the quorum specified by the action's policy bundle
4. Execution receipt hashes match the bundle's claimed action outputs
5. Operator attestation is admissible for the action's jurisdiction at the certificate's timestamp

The verifier returns a structured result identifying every check performed, every check's outcome, and the source of any failure. Verification is reproducible and does not depend on amotivv's cooperation.

### Deployment Profile

The reference implementation supports the three deployment profiles specified in the v0.2 architecture:

- **Developer Sandbox** — public Gateway, sample Action Registry, mechanical witnesses, demo certificates
- **Enterprise Private Gateway** — Gateway hosted inside the customer's perimeter, private Action Registry, customer-controlled policy bundles and adapters
- **Cross-Trust Verified Gateway** — customer-hosted or enclave-hosted Gateway with public registry commitments, independent witness network, historical witness registry, and domain attestations

### Patent Status

The implementation methods are covered by a U.S. provisional patent application: *Cryptographically Verified Gateway for Policy-Gated AI-Mediated Actions Using Intent Grants and Chained Receipts* (Application No. 64/055,645).

The MPF protocol itself is licensed Apache 2.0 with explicit patent grants for protocol implementation. The patent application covers operator-grade implementation methods that go beyond the protocol minimums; protocol conformance does not require the patented methods.

---

## Other Implementations

No other public implementations of MPF v0.1 or v0.2 are currently registered. Implementations targeting either version of the protocol are welcome — see [CONTRIBUTING.md](CONTRIBUTING.md) for the registration process.

---

## How to Register an Implementation

To register an implementation of MPF (v0.1 or v0.2) in this document:

1. The implementation must be running and producing protocol-conformant artifacts (memory objects for v0.1, certificate bundles for v0.2)
2. The implementation must publish at least one publicly verifiable artifact a third party can independently check
3. Open a pull request adding an entry to this document with: codebase identifier, maintainer, conformance target, deployment status, conformance evidence URL, and any relevant licensing notes

The bar for registration is that another party can confirm the implementation works without contacting the maintainer. Live evidence is preferred over claims.

---

*This document is a living registry. Last updated alongside the v0.2 reference implementation reactivation.*
