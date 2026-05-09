# Memory Pod Fabric Protocol Specification v0.2 Draft

Status: draft.

Last updated: 2026-05-09.

This document drafts Memory Pod Fabric v0.2 as a continuation of the public MPF v0.1 memory specification and the current verified-action implementation work at amotivv. It is intended to become the bridge between the original open memory protocol and the newer verified-action architecture: a verified-action gateway, a receipt-log protocol layer, a witness network, and MCP gateway integrations.

## Abstract

Memory Pod Fabric v0.1 defined an open protocol for portable AI memory: memory pods, memory objects, capability tokens, and Merkle-verifiable audit trails.

MPF v0.2 extends the same primitives from memory operations to consequential agent actions. The protocol now covers both:

- what an AI system stored, retrieved, or remembered
- what an AI system did, under whose authority, and with what independently verifiable evidence

The core extension is a verified action channel composed of:

- a Gateway that enforces capability-bound permissions
- an Action Registry that declares callable workflows and proof requirements
- a Witness Network that signs protocol, policy, and domain commitments
- a Receipt Log and Certificate Bundle that make the action replayable and verifiable across operator boundaries

MCP tells an agent how to call a tool. MPF v0.2 defines how that tool call is authorized, witnessed, recorded, and verified after the fact.

## 1. Relationship To MPF v0.1

MPF v0.2 is not a replacement for MPF v0.1. It generalizes MPF's trust primitives.

MPF v0.1 remains the memory profile:

- Memory Pod: a logical memory boundary with governance and audit controls
- Memory Object: the stored context unit
- Capability Token: scoped, time-bound authority to operate on memory
- Audit Record: append-only operational history
- Merkle Verification: tamper-evident proof of memory operations
- Memory Card: discovery of pod endpoints and capabilities

MPF v0.2 adds an action profile:

- Verified Action: a consequential operation routed through a certified channel
- Action Registry: signed action surface and proof contract
- Intent Grant: certified permission for an exact action request
- Tool Adapter: execution boundary that verifies capability before side effect
- Witness Network: L1 mechanical, L2 policy, and L3 domain attestations
- Certificate Bundle: portable proof package for recipients, auditors, and counterparties
- Verifier Profile: machine-readable rules for accepting or rejecting a proof claim

Memory remains a first-class MPF object class. Actions become a second object class governed by the same philosophy: capability-scoped access, digest-bound evidence, append-only receipts, and independent verification.

## 2. Design Principles

MPF v0.2 keeps the v0.1 principles and adds action-specific constraints.

### 2.1 Open Protocol, Not Vendor Assertion

Verification is portable only when the relying party can verify artifacts without trusting the operator's dashboard or a single vendor's internal records.

### 2.2 Capability First

Every memory access or consequential action SHOULD be authorized by a scoped, time-bound capability or intent grant. Higher-assurance action profiles require a token-verifying tool boundary.

### 2.3 Receipts Before Claims

A claim that an action was verified is meaningless without receipts, witness evidence, policy bindings, registry authority, and a verifier profile.

### 2.4 Honest Boundaries

The protocol proves what occurred inside the certified channel. It does not prove global absence of shadow channels, model correctness, semantic truth, or legal sufficiency unless separate domain attestations say so.

### 2.5 Historical Verification

Certificates MUST verify against the registries, policies, keys, and profiles valid at action time. Verifiers MUST NOT use mutable current state to rewrite historical meaning.

### 2.6 Digest-First Durable Evidence

Durable certificate bundles SHOULD commit to sensitive content by digest by default. Raw payloads MAY be withheld, encrypted, redacted, or selectively disclosed when the verifier profile permits it.

### 2.7 Live/Durable Channel Separation

Live operational channels MAY carry full content when policy and the verifier profile permit it. Durable verifier channels SHOULD preserve digest-first evidence unless the profile explicitly requires full-content retention. This lets an agent use data during an active workflow without forcing long-term audit bundles to retain sensitive payloads.

### 2.8 Stronger Assurance Is Additive

L1 mechanical witness evidence, L2 policy witness evidence, L3 domain attestation, TEE substrate evidence, and transparency inclusion are separate dimensions. A certificate MUST NOT imply a stronger dimension than it contains.

### 2.9 Common Canonicalization

MPF JSON protocol objects that are hashed, signed, witnessed, included in receipt state-root computation, or otherwise digested for verification MUST be canonicalized using RFC 8785 JSON Canonicalization Scheme (JCS) before digest, signature, or state-root computation.

This applies to: receipt JSON objects, certificate JSON, registry JSON, policy bundle JSON, witness subjects, intent grant claims (when expressed as canonical JSON capability), admission manifests, and any other MPF JSON object the spec instructs an implementation to hash or sign.

This does NOT apply to: raw evidence bytes, binary payloads, file artifacts, encrypted blobs, patches, externally referenced artifacts, JWT/JWS compact serializations (whose signing input is JOSE-defined), COSE messages (whose signing input is COSE-defined), or provider-native receipts (which retain their issuer’s byte semantics). Such non-JSON or non-MPF-protocol bytes are digested as-received.

A verifier profile MAY define a different canonicalization scheme for specific MPF JSON objects only if the profile names the alternate scheme explicitly. A profile that uses an alternate canonicalization MUST NOT claim baseline `mpf-0.2` conformance for the affected objects; such a profile is treated as a profile-specific extension, not the default portable v0.2 wire behavior.

The purpose of this rule is interoperability: two independent MPF v0.2 implementations operating on the same JSON object MUST produce the same digest and the same signature input, so that signatures and receipts are portable across implementations without requiring a shared canonicalization library beyond JCS.

## 3. Terminology

### 3.1 Memory Pod

A logical boundary of memory, actions, governance, access controls, and audit trails. A pod MAY expose only memory operations, only action operations, or both.

### 3.2 Memory Object

The stored context unit from MPF v0.1. It may contain content, references, metadata, vectors, lifecycle rules, and audit bindings.

### 3.3 Verified Action

A consequential operation that enters an MPF certified action channel and produces a certificate bundle. Also called an attested action when emphasizing witness signatures.

Verified actions divide into two classes by initiation:

- **Agent-initiated actions** are requested through the gateway by a client (typically an LLM-mediated agent) and certified under the applicable verifier profile. When the profile requires policy evaluation or capability-token enforcement, the certificate binds that evidence. The certificate proves the request, authorization or attestation, execution or observation, and boundaries required by the profile; it MUST NOT claim causation or control beyond those artifacts.
- **Observed actions** include the case where the gateway has no causal control over the underlying process and only certifies its own observation of a published artifact. The actor is external to MPF (for example, a deterministic public pipeline producing a periodically refreshed output). The certificate MUST NOT imply the gateway controlled or policy-gated the upstream process; it certifies that the observer fetched specific bytes at a specific time, validated declared invariants, and produced a witnessed receipt for that observation.

Agent-initiated observed actions and external-pipeline observations both use observed-action profiles when tool-side capability enforcement is unavailable.

### 3.4 Gateway

The runtime boundary that receives requests, validates registry and policy requirements, coordinates witnesses, issues capability tokens where required, records receipts, and assembles certificate bundles.

### 3.5 Action Registry

A signed, versioned, digest-addressed manifest that declares which actions exist, how they may be called, what policy and witness evidence they require, and how resulting certificates should be verified.

### 3.6 Witness Network

The set of registry-authorized witnesses and attestors that sign canonical commitments. MPF v0.2 defines three witness classes:

| Tier | Name | Signs | Does Not Claim |
|---|---|---|---|
| L1 | Mechanical Witness | Canonical protocol subjects, intent grants, observations, boundaries, checkpoints | Semantic correctness or policy wisdom |
| L2 | Policy Witness | Deterministic policy decisions over a policy bundle and action commitment | Domain truth or legal sufficiency |
| L3 | Domain Attestor | Independent domain judgments over an action, certificate, or evidence bundle | Replacement for L1/L2 evidence |

Witness tier and witness-operator independence are separate dimensions. A certificate may have a 2-of-3 L1 quorum where all witnesses are operated by the same party, or a 2-of-3 L1 quorum with administratively independent operators. Verifier profiles MUST define how operator independence affects assurance claims.

### 3.7 Intent Grant

The gateway's certified authorization of an exact proposed action under a declared session state, policy digest, request digest, audience, method, and expiry.

### 3.8 Capability Token

The concrete authority object presented to a tool or adapter. In capability-enforced profiles, the tool MUST verify the token before execution.

### 3.9 Receipt Log

An ordered, append-only, signed, hash-chained sequence of protocol receipts for a memory operation, action, session, denial, or workflow segment.

### 3.10 Certificate Bundle

The portable proof package containing the certificate summary and every artifact required by the verifier profile.

### 3.11 Verifier Profile

A machine-readable policy describing which artifacts, algorithms, witness tiers, registries, policy evidence, storage guarantees, taints, and substrate evidence are required for a claim to pass.

### 3.12 Evidence Store

The storage system for raw, redacted, encrypted, or bulky evidence referenced by digest from receipts and certificates.

### 3.13 Substrate Attestation

Evidence about runtime, image, configuration, key binding, and deployment posture. TEE evidence is one substrate-attestation profile.

### 3.14 Admission Manifest

A signed declaration binding a certified session to operator identity, tenant/workflow scope, active action surface, policy digest, verifier profile, runtime/config evidence, and MCP or API authorization context. It is the operator-side admission evidence that verifiers use to determine whether the gateway was authorized to run the claimed workflow.

For observation profiles (§9.4.1) the Admission Manifest additionally binds: observer code digest, validator code digest, source URL, cadence, expected schema (or schema digest), and pipeline identity. There is no agent-intent layer in observation profiles; the manifest plus the cadence declaration is the equivalent admission evidence.

## 4. Protocol Roles

### 4.1 Client

An agent, application, MCP client, user interface, or service that stores memory, searches memory, or requests an action.

### 4.2 Pod Operator

The organization or person operating a pod, gateway, memory store, policy registry, or connector.

### 4.3 Gateway Operator

The operator responsible for the gateway runtime. The gateway operator may be the pod operator, customer, vendor, enclave operator, or consortium member.

### 4.4 Policy Authority

The party that authors and signs a policy bundle or policy registry record.

### 4.5 Witness Operator

The party operating a witness node or domain attestor. Witness independence MUST be disclosed and verifier-profile-defined.

### 4.6 Relying Party

A recipient, auditor, counterparty, regulator, customer system, or downstream agent that consumes and verifies a certificate bundle.

### 4.7 Verifier

The software component that decides whether a bundle satisfies a declared claim under explicit trust roots and a verifier profile.

## 5. Discovery

MPF v0.2 preserves `.well-known/memory.json` for v0.1 compatibility and introduces `.well-known/mpf.json` for combined memory and action discovery.

### 5.1 MPF Card Location

```text
https://{pod-host}/.well-known/mpf.json
```

### 5.2 Required MPF Card Fields

```json
{
  "protocol_version": "mpf-0.2-draft",
  "pod_id": "urn:mpf:pod:example-org:main",
  "pod_name": "Example Pod",
  "endpoints": {
    "base_url": "https://pod.example.org/api/v1",
    "contexts": "/contexts",
    "search": "/search",
    "actions": "/actions",
    "certificates": "/certificates",
    "verify": "/verify"
  },
  "capabilities": {
    "memory": true,
    "actions": true,
    "verification": true,
    "mcp_projection": true
  },
  "auth": {
    "methods": ["oauth2", "capability_token"],
    "oauth2_authorization_server": "https://pod.example.org/.well-known/oauth-authorization-server",
    "protected_resource": "https://pod.example.org/.well-known/oauth-protected-resource"
  }
}
```

### 5.3 Recommended MPF Card Fields

The card SHOULD also advertise registry URLs, active profile IDs, public verifier URLs, memory vector models, MCP endpoint, evidence modes, and supported assurance profiles.

```json
{
  "registries": {
    "action_registry_current": "https://pod.example.org/registries/actions/current",
    "witness_registry_current": "https://pod.example.org/registries/witness/current",
    "policy_registry_current": "https://pod.example.org/registries/policies/current"
  },
  "mcp": {
    "endpoint": "https://pod.example.org/mcp",
    "protocol_version": "2025-11-25"
  },
  "verification": {
    "verifier_url": "https://verifier.example.org",
    "default_profile": "mpf.profile.action.policy-witnessed-l2.v0.2",
    "bundle_media_type": "application/json"
  },
  "vector_models": [
    {
      "id": "example-embed-1024",
      "dimensions": 1024
    }
  ]
}
```

## 6. Operation Classes

MPF v0.2 defines two top-level operation classes.

### 6.1 Memory Operations

Memory operations are the MPF v0.1 operations:

- `memory.store`
- `memory.read`
- `memory.search`
- `memory.related`
- `memory.delete`
- `memory.embed`
- `memory.verify`
- `memory.delegate`

Memory operations MAY be verified using the original Merkle-audit profile or the v0.2 receipt-log profile.

### 6.2 Action Operations

Action operations are consequential invocations mediated by a gateway:

- `action.request`
- `action.policy.evaluate`
- `action.intent.grant`
- `action.tool.execute`
- `action.observe`
- `action.deny`
- `action.abort`
- `action.certify`
- `action.verify`

Any action claimed as verified or certified MUST use an Action Registry entry and MUST produce a receipt log. Uncertified local/demo calls MAY run without a registry only if they do not produce a verified-action certificate.

Receipts MUST identify the actor of the certified operation. The following `actor.type` values are standardized in v0.2:

| `actor.type` | Meaning |
|---|---|
| `agent` | An LLM-mediated or autonomous agent client requesting an action through the gateway. |
| `human` | A human operator requesting an action through the gateway (for example, an approver or initiator). |
| `service` | A non-agent automated client (scheduler, workflow runner, integration service) requesting an action through the gateway. |
| `external_pipeline` | An external code-only process whose published output is observed by the gateway. The gateway has no causal control over execution. Used only with observed-action profiles. |
| `code` | Generic alias for `external_pipeline` when the observed actor is not specifically a published-artifact pipeline (for example, a third-party deterministic computation observed at the API boundary). |

Profiles MAY define additional actor types. Verifier profiles MUST specify which actor types they admit and what assurance claims each implies.

## 7. Core APIs

MPF v0.2 implementations MAY expose REST, MCP, SDK, or A2A bindings. The REST shape below is the recommended draft HTTP binding for interoperable implementations.

### 7.1 Memory APIs

The following endpoints remain compatible with MPF v0.1:

```text
POST /v1/contexts
GET /v1/contexts/{id}
DELETE /v1/contexts/{id}
GET /v1/search
POST /v1/search
GET /v1/related/{id}
POST /v1/embed
```

### 7.2 Action APIs

```text
GET /v1/action-registry/current
GET /v1/action-registry/epochs/{epoch_id}
POST /v1/sessions
POST /v1/actions
POST /v1/sessions/{session_id}/end
GET /v1/sessions/{session_id}/receipts
GET /v1/certificates/{certificate_id}
GET /v1/certificates/{certificate_id}/bundle
POST /v1/verify
```

### 7.3 Registry APIs

```text
GET /v1/registries/actions/current
GET /v1/registries/actions/epochs/{epoch_id}
GET /v1/registries/witness/current
GET /v1/registries/witness/epochs/{epoch_id}
GET /v1/registries/policies/current
GET /v1/registries/policies/epochs/{epoch_id}
GET /v1/registries/operators/current
```

### 7.4 Witness APIs

L1 witnesses SHOULD expose:

```text
GET /health
GET /v1/public-key
GET /v1/witness-info
POST /v1/sign
POST /v1/sign-request
```

L2 policy witnesses SHOULD expose:

```text
GET /health
GET /v1/public-key
GET /v1/policy
POST /v1/evaluate
```

## 8. Action Registry

Every verified action MUST reference an action ID declared in an Action Registry Epoch.

### 8.1 Registry Epoch Requirements

An Action Registry Epoch MUST include:

- schema version
- registry ID
- epoch ID
- validity window
- canonical digest
- workflow ID or scope
- profile ID
- action entries
- policy bindings
- witness requirements
- signatures or trusted publication evidence

### 8.2 Action Entry Requirements

Each action entry MUST include:

- stable `action_id`
- optional `mcp_tool_name`
- title and description
- input schema
- output or certificate schema reference
- adapter or upstream target class
- audience and method bindings
- assurance mode
- capability-token requirement
- required L1, L2, and L3 evidence
- policy bundle or policy class binding
- evidence mode
- verifier profile binding

### 8.3 Example Action Entry

```json
{
  "action_id": "verified_email_send.v1",
  "mcp_tool_name": "email_send_verified",
  "workflow_id": "email.send",
  "risk_tier": "external_side_effect",
  "input_schema_ref": "schema://mpf/actions/verified_email_send.v1/input",
  "adapter": {
    "type": "provider_api",
    "audience": "tool:email-resend-adapter",
    "method": "POST /emails"
  },
  "assurance": {
    "mode": "policy_witnessed",
    "capability_token_enforced": true,
    "mechanical_witness_threshold": 2,
    "mechanical_witness_set": "l1.email.send.default",
    "policy_witness_threshold": 2,
    "policy_witness_set": "l2.email.policy.default"
  },
  "policy": {
    "policy_class": "email.send",
    "policy_bundle_digest": "sha256:..."
  },
  "evidence": {
    "payload_mode": "digest_only",
    "provider_metadata_required": true,
    "durable_bundle_required": true
  },
  "verifier_profile_id": "mpf.profile.action.policy-witnessed-l2.v0.2"
}
```

## 9. Capability And Intent Model

MPF v0.2 extends v0.1 capability tokens from memory access to action execution.

### 9.1 Capability Claims

A capability token or equivalent signed capability object MUST bind:

- token ID
- issuer key ID
- subject or actor
- audience
- operation or action ID
- method
- request digest
- session ID where applicable
- step index where applicable
- policy digest where applicable
- action registry epoch digest where applicable
- expiry
- nonce or idempotency key
- maximum uses, default `1` for side effects

Side-effect capabilities SHOULD be single-use by default. Idempotent capabilities MAY allow safe retries only when the Action Registry or verifier profile declares idempotent semantics. Idempotent capability claims MUST bind the same request digest, idempotency key, audience, method, retry window, and maximum successful executions.

### 9.2 Encoding Profiles

MPF v0.2 allows two encoding profiles during draft status:

| Profile | Use | Requirement |
|---|---|---|
| JWT/JWS capability | v0.1 compatibility and API access | Claims MUST preserve MPF semantics. JWS signing input is JOSE-defined and is not subject to §2.9 JCS canonicalization. Any MPF JSON object that embeds the capability claims or capability digest is itself JCS-canonicalized per §2.9. |
| Canonical JSON capability | receipt-bound action grants | Claims are JSON capability claims canonicalized via RFC 8785 JCS per §2.9 and signed with the declared domain separator. |

Implementations MUST expose enough token claims or token digests in the certificate bundle for the verifier profile to validate the action claim.

### 9.3 Intent Grant Flow

A capability-enforced action SHOULD follow this flow:

```text
1. Client calls action through MCP, REST, SDK, or A2A.
2. Gateway authenticates the client and validates action registry entry.
3. Gateway binds or creates the admission manifest for operator, tenant, workflow, profile, and policy context.
4. Gateway canonicalizes the proposed request and computes request digest.
5. Gateway records tool.request or action.request receipt.
6. Gateway obtains L2 policy decision when required.
7. Gateway creates intent grant and capability token for the exact request.
8. Gateway obtains required L1 witness signatures over the grant subject.
9. Tool adapter verifies token, executes action, and signs execution receipt.
10. Gateway records tool execution and observation receipts.
11. Gateway checkpoints and publishes certificate bundle.
```

### 9.4 Observed-Only Flow

Some systems can observe actions but cannot force tool-side token verification. These MUST use an observed-action profile.

Observed profiles MUST declare:

- `capability_token_enforced=false`
- observation boundary
- known taints
- what was observed before execution
- what was observed after execution
- why tool-side enforcement is not claimed

Verifiers MUST reject observed-only certificates that overclaim controlled execution.

### 9.4.1 Observation Of External Pipelines

A further variant of the observed-action profile applies when the actor is an external deterministic process (for example, a public data pipeline) that the gateway cannot influence at all. In this variant the gateway is purely an observer of a published artifact. The certificate MUST be framed as verified observation, not certification of upstream execution.

Observation profiles MUST declare:

- `capability_token_enforced=false`
- `actor.type` set to `external_pipeline` or `code`
- source identity binding: source URL, expected schema or schema digest, pipeline identity (repo, commit, or other stable identifier where available), and any provider/storage identity required by the verifier profile
- observer code identity: digest of the observer/fetcher implementation
- validator code identity: digest of any deterministic invariant validator that produced `validation.result` receipts
- cadence: the schedule under which the observer polls the source (and the guard-key semantics for recurring observations, profile-defined)
- retention mode: whether the raw fetched bytes are retained, an immutable copy is stored, or only the digest plus URL is preserved (lower-assurance profiles MAY use digest+URL; higher-assurance profiles SHOULD retain bytes since public URLs are mutable over time)
- failure-mode handling: how missed polls, source-unavailable conditions, validation failures, validator exceptions, and bytes-changed-mid-interval are represented as receipts

Observation profiles MUST NOT require an `intent.attested` receipt. Scheduled non-agent observations have no agent-intent layer; the equivalent admission evidence is the Admission Manifest plus the cadence declaration.

A recommended receipt flow for a single observation cycle is:

```text
session.start
  -> data.request          (observer issues fetch against source URL)
  -> data.response         (observer commits to fetched bytes by digest)
  -> validation.result     (validator commits to declared-invariant outcomes)
  -> observation           (witnesses sign the observation subject)
  -> session.end
  -> checkpoint            (gateway publishes certificate bundle)
```

L1 witnesses sign observation/validation subjects. L2 policy witnesses are optional and typical uses are source URL pinning, max artifact size, schema version enforcement, allowed validator code digests, and cadence rules.

Whether witnesses must independently fetch the source URL (in addition to signing the observer’s digest) is a verifier-profile decision. Profiles requiring witness-independent fetch produce stronger observation evidence at the cost of bandwidth and witness-side state.

Guard-key semantics for recurring observations are profile-defined. Common choices include `(source_url, cadence_bucket)`, `(source_url, observed_timestamp)`, or a monotonically increasing sequence number bound to the gateway-source pair.

Verifiers MUST reject observation certificates that claim policy-gated execution, capability-token enforcement, or upstream-process control.

## 10. Receipt Log

Every verified action certificate MUST bind a receipt log or a receipt-log digest.

### 10.1 Receipt Requirements

Each receipt MUST include:

- schema version
- receipt kind
- session ID or operation ID
- sequence or step index
- previous state root
- actor
- body
- issued timestamp
- signature set
- resulting state root

### 10.2 Common Receipt Kinds

```text
session.start
input.received
memory.store
memory.search
memory.read
data.request
data.response
tool.request
policy.request
policy.decision
intent.grant
intent.attested
tool.execution
tool.execution.observed
observation
observation.request
validation.result
source.unavailable
validation.failed
validator.exception
bytes.changed_in_interval
human.approval.request
human.approval.response
abort
dissent.notice
output.stamped
session.end
```

### 10.3 State Root

The receipt state root MUST be recomputable from the previous state root, receipt payload digest, and receipt signatures under the declared canonicalization profile. For baseline `mpf-0.2` conformance, the receipt payload digest is computed over the JCS-canonicalized receipt JSON per §2.9.

### 10.4 Observation Rule

An output-producing action MUST be followed by an observation receipt before the next material certified action advances unless the verifier profile explicitly allows an abort, timeout, or in-flight state.

## 11. Witness Network

### 11.1 L1 Mechanical Witness

L1 witnesses sign canonical protocol subjects. The subject digest is computed over the JCS-canonicalized subject JSON per §2.9. They MUST maintain anti-equivocation state for guard keys defined by profile.

L1 witnesses MUST refuse a different digest for the same guard key.

When a guard-key conflict is detected, the witness MUST persist conflict evidence in its anti-equivocation store and return a structured error to the gateway. Higher-assurance profiles SHOULD require the conflict evidence to be exported through a transparency surface, guard log, or verifier-visible witness evidence artifact.

### 11.2 L2 Policy Witness

L2 witnesses evaluate a canonical action commitment against a policy bundle. They MUST sign the policy bundle digest, policy epoch, action payload digest, decision, and rule results. Action payload digests, policy bundle digests, and L2 witness signature inputs over JSON objects are JCS-canonicalized per §2.9.

### 11.3 L3 Domain Attestor

L3 attestors sign domain-specific judgments. L3 attestation MAY address legal, medical, financial, audit, safety, or vertical-specific claims. L3 MUST NOT substitute for L1 mechanical or L2 policy evidence.

### 11.4 Witness Registry

Witness signatures count only if the witness key was authorized by a signed Witness Registry Epoch at signing time.

A Witness Registry Epoch MUST include:

- registry ID and epoch ID
- witness ID and key ID
- public key
- tier
- authorized workflows
- authorized policy hashes or scopes
- validity windows
- status events
- signatures

Verifiers MUST distinguish expired or deprecated keys from revoked or compromised keys.

### 11.5 Operator Independence

Witness operator independence is profile-defined and separate from witness tier, quorum threshold, and runtime substrate. A verifier SHOULD report the independence posture of each counted witness signature.

Independence dimensions MAY include administrative control, legal entity, infrastructure account, deployment pipeline, key custody, funding/control relationship, TEE or HSM custody, policy authority, and audit obligations.

Profiles SHOULD state whether same-operator witnesses are acceptable, warn-only, or invalid for the claimed assurance level.

## 12. Policy

### 12.1 Policy Bundle

A Policy Bundle is the versioned, digest-addressed ruleset evaluated by L2 policy witnesses.

Policy Bundles SHOULD include:

- policy ID and epoch ID
- workflow and tenant scope
- rule schema
- evaluator profile
- rules
- test vectors where practical
- validity window
- signatures or registry publication evidence

### 12.2 Policy Decisions

Policy decisions MUST bind:

- decision, such as `allow` or `deny`
- action payload digest
- policy bundle digest
- policy epoch ID
- rule results
- issuing witness key ID
- timestamp
- signature

### 12.3 Denials

A policy denial is a valid protocol outcome. Denials SHOULD produce denial receipts or denial certificates when the workflow requires refusal auditability.

A denial certificate MUST NOT include `intent.grant`, capability token release, tool execution, or observation of a side effect unless a side effect actually occurred under a separate profile.

## 13. Certificate Bundle

The certificate is the summary. The bundle is the proof.

### 13.1 Certificate Requirements

The certificate digest is computed over the JCS-canonicalized certificate JSON per §2.9. Bundle artifact digests for JSON artifacts (`receipts.jsonl` line-by-line, `keyring.json`, `action-registry.json`, `witness-registry-epoch.json`, `policy-bundle.json`, `policy-decision.json`, `operator-registry.json`, `admission-manifest.json`, `checkpoint.json`, `verification.json`, `replay.json`, and any other JSON artifact in the bundle) are computed over their JCS-canonicalized form. Non-JSON artifacts (binary evidence, encrypted payloads, externally referenced files, JWS compact serializations, COSE messages, transparency-log entries with their own canonicalization) are digested as-received.

A certificate MUST include:

- certificate schema version
- certificate ID
- certificate digest
- issued time
- pod ID
- action ID or memory operation ID
- upstream system identity and adapter/provider identity where an external system is invoked
- assurance mode
- outcome state
- verifier profile ID
- receipt root or receipt log digest
- checkpoint reference or digest for verified action certificates
- Merkle or anchor proof for memory-audit certificates where required by profile
- registry epoch IDs and digests
- policy digest where required
- operator or admission binding where required
- witness quorum summaries
- capability-token enforcement status
- evidence mode
- warnings, taints, and limitations

### 13.2 Bundle Requirements

A bundle SHOULD include these artifacts when required by profile:

```text
certificate.json
receipts.jsonl
keyring.json
action-registry.json
witness-registry-epoch.json
policy-bundle.json
policy-decision.json
operator-registry.json
admission-manifest.json
checkpoint.json
transparency-log.jsonl
substrate-attestation.json
domain-attestation.json
verification.json
replay.json
```

### 13.3 Evidence Modes

MPF v0.2 defines standard evidence modes:

| Mode | Bundle Contains | Use |
|---|---|---|
| `digest_only` | digest, size, type, metadata | sensitive evidence, recipient recomputation |
| `redacted` | digest plus redacted payload or preview | operational review without full disclosure |
| `encrypted` | encrypted payload and key disclosure policy | regulated archive with controlled access |
| `full` | complete canonical payload | low-sensitivity or private audit archives |
| `external_ref` | immutable digest-bound pointer | customer-controlled evidence stores |

### 13.4 Outcome And Upstream Identity

Action certificates SHOULD distinguish transport status from logical outcome. A gateway MUST NOT treat HTTP success as action success when the upstream protocol reports a logical error.

For MCP-backed upstreams, gateways MUST inspect MCP-level error indicators such as `isError: true` and record the action as a logical error unless the verifier profile explicitly defines another interpretation.

Action outcome fields SHOULD identify:

- `transport_status`, such as HTTP status or provider transport result
- `logical_status`, such as success, policy_denied, upstream_error, adapter_error, timeout, unknown, or aborted
- `upstream_error_indicator`, such as MCP `isError`, provider error code, or response-body error marker
- `side_effect_status`, such as not_attempted, accepted, completed, failed, unknown, or not_applicable
- `upstream_system_identity`, such as provider name, project/account/tenant ID, region, endpoint origin, or connector ID

The upstream system identity does not prove the upstream provider was honest. It binds the certificate claim to the system or connector the gateway actually invoked so relying parties can reason about provider trust, scope, and safety features.

## 14. Verification

### 14.1 Verifier Inputs

A verifier takes:

- certificate bundle
- verifier profile
- trust roots
- verification time
- optional online registry resolution policy
- optional local relying-party policy

### 14.2 Required Checks

The verifier MUST recompute rather than trust embedded claims.

Common checks include:

- certificate digest
- artifact digests
- receipt log parse and state-root continuity
- signatures and key resolution
- session boundaries
- intent grant or observed intent semantics
- capability token claims where required
- tool execution receipt and observation
- witness quorum and witness registry authority
- policy decision and policy bundle digest
- action registry entry and epoch digest
- operator/admission evidence where required
- upstream system identity and action outcome semantics where applicable
- checkpoint and transparency evidence where required
- substrate attestation where required
- domain attestation where required
- evidence mode and taints
- assurance overclaim rejection

### 14.3 Verification Results

Standard result states SHOULD include:

```text
verified
verified_with_warnings
verified_denial
in_flight_verified_to_head
unverifiable
failed
unsupported
```

### 14.4 Relying-Party Policy

MPF verification determines whether a certificate satisfies the declared profile. A relying party still decides whether the operator, workflow, assurance profile, warnings, or evidence mode are acceptable for its local risk policy.

## 15. Assurance Profiles

MPF v0.2 defines named profiles so deployments can select appropriate proof strength without changing the core protocol.

| Profile | Intended Use | Minimum Claim |
|---|---|---|
| `mpf.profile.local-dev.v0.2` | demos and local testing | protocol shape works locally |
| `mpf.profile.memory-audit.v0.2` | v0.1-style memory audit | memory operations are hash/Merkle-audited |
| `mpf.profile.action.observed-l1.v0.2` | observed agent actions | intent and observation were witnessed, no tool token enforcement claimed |
| `mpf.profile.action.controlled-l1.v0.2` | controlled side effects | token-verifying tool executed exact granted request |
| `mpf.profile.action.policy-witnessed-l2.v0.2` | policy-enforced workflows | action was allowed or denied under signed policy quorum |
| `mpf.profile.action.domain-attested-l3.v0.2` | high-trust vertical workflows | independent domain attestation is present in addition to L1/L2 |
| `mpf.profile.regulated-high-assurance.v0.2` | regulated/adversarial settings | independent witnesses, durable evidence, transparency, substrate evidence, and historical registries required |

Profiles MUST define mandatory artifacts, allowed algorithms, witness thresholds, registry requirements, evidence modes, taint severity, retention expectations, and online/offline verification rules.

The profile IDs above are protocol-level names. amotivv's current P0-P4 deployment matrix is a deployment-profile crosswalk over these protocol profiles. For example, P0 maps to `mpf.profile.local-dev.v0.2`, P1 maps to memory-audit or observed-L1 profiles with operator-hosted evidence, P2/P3 map to policy-witnessed L2 with stronger registry/storage/substrate requirements, and P4 maps to regulated-high-assurance.

## 16. MCP Integration

MPF v0.2 treats MCP as the preferred agent-facing projection, not the trust root.

### 16.1 MCP Tool Projection

Action Registry entries SHOULD project to MCP `tools/list` entries. MCP tool descriptions guide the model, but gateway enforcement remains authoritative.

### 16.2 MCP Tool Calls

MCP `tools/call` SHOULD map to MPF action requests. The tool result SHOULD include normal operational output plus proof metadata.

Example proof metadata:

```json
{
  "certificate_url": "https://pod.example.org/certificates/cert_123/bundle",
  "certificate_digest": "sha256:...",
  "action_id": "verified_email_send.v1",
  "receipt_root": "sha256:...",
  "witness_quorum": "2-of-3",
  "policy_status": "allow",
  "verified": true
}
```

### 16.3 MCP Resources

MPF MCP servers SHOULD expose:

```text
mpf://action-registry/current
mpf://witness-registry/current
mpf://policy/current
mpf://verifier-profile/current
mpf://certificate/latest
mpf://certificate/{id}
```

### 16.4 OAuth And Client Auth

Remote MCP deployments SHOULD use OAuth 2.1, Dynamic Client Registration where appropriate, PKCE, refresh-token rotation, and bearer-token validation. OAuth authorizes the client to reach the gateway; it does not replace MPF action authorization or proof.

### 16.5 Client Hints

MCP gateways SHOULD expose client hints for skill authors and agent clients. Client hints are guidance, not trust roots. Gateway enforcement and certificate verification remain authoritative.

Connector-style gateways that expose client hints MUST use an object shaped like:

```json
{
  "schema_versions": {},
  "upstream_capabilities": [],
  "upstream_tool_mappings": {},
  "evidence_mode_per_tool": {},
  "error_taxonomy": {}
}
```

Minimum fields:

- `schema_versions`: active request, commitment, certificate, and evidence schema versions
- `upstream_capabilities`: capability classes available through the connector, such as `sql:read` or `docs:search`
- `upstream_tool_mappings`: mapping from MPF tool names to upstream tool names
- `evidence_mode_per_tool`: live response mode and durable bundle evidence mode
- `error_taxonomy`: stable error categories for client-side control flow

Optional fields:

- `semantic_input_hints`: field semantics that may not be obvious from schema names
- `tool_result_modes`: whether live tool responses can include full content, redacted samples, summaries, or digest-only results
- `upstream_safety_features`: upstream safety semantics preserved by the gateway, such as untrusted-data boundaries
- `connector_constraints`: fixed connector constraints the client cannot override, such as project, account, feature set, or read-only mode
- `profile_hints`: recommended verifier profile IDs or assurance expectations for returned certificates

Client hints MUST NOT include bearer tokens, connector secrets, private policy internals, or raw evidence unless the same disclosure is allowed by policy for ordinary tool results.

## 17. Memory Profile Compatibility

MPF v0.2 memory implementations MUST preserve v0.1-compatible behavior for:

- `.well-known/memory.json`
- memory pod ID
- memory object shape or mapped equivalent
- `POST /contexts`, `POST /v1/contexts`, or a documented mapped equivalent
- `GET /search`, `GET /v1/search`, or a documented mapped equivalent
- capability-scoped memory access
- audit records and verification endpoint

MPF v0.2 memory operations MAY additionally produce receipt logs and certificate bundles. A memory write can therefore be both a v0.1 memory object operation and a v0.2 verified operation.

## 18. Privacy, Redaction, And Retention

MPF v0.2 separates proof commitments from payload custody.

Implementations MUST declare:

- whether raw payloads are stored
- where raw payloads are stored
- whether evidence is digest-only, redacted, encrypted, full, or externally referenced
- retention period and retention mode
- deletion semantics for content versus immutable audit records
- whether public bundle URLs reveal sensitive metadata

Immutable audit records MAY outlive deleted content when required for compliance. The certificate MUST distinguish content deletion from proof deletion.

## 19. Substrate Attestation

Substrate attestation is optional in lower profiles and required in higher profiles.

When claimed, substrate evidence MUST identify:

- component name and type
- platform
- image or binary digest
- config digest where available
- debug or staging mode
- public key binding where claimed
- attestation bundle or verification result
- issue time and validity

TEE evidence proves runtime measurement, not witness independence, policy correctness, or absence of all side channels. Reports MUST display substrate status separately from protocol and governance status.

## 20. Conformance

### 20.1 MPF v0.2 Memory Conformant

An implementation is memory conformant if it supports v0.1 memory discovery and operations, capability-scoped access, and auditable operation verification.

### 20.2 MPF v0.2 Action Observed Conformant

An implementation is action observed conformant if it supports action registry binding, receipt logs, witnessed intent or intent attestation, observed execution evidence, certificate bundles, and verifier reports that do not claim tool-side token enforcement.

### 20.3 MPF v0.2 Action Controlled Conformant

An implementation is action controlled conformant if it additionally supports intent grants, capability tokens, token-verifying tools or adapters, tool execution receipts, observations, and verifier rejection of missing or invalid grants.

### 20.4 MPF v0.2 Policy Witnessed Conformant

An implementation is policy witnessed conformant if it additionally supports policy bundles, L2 policy witnesses, signed policy decisions, policy-denial certificates where configured, and policy-digest verification.

### 20.5 MPF v0.2 Cross-Boundary Conformant

An implementation is cross-boundary conformant if it additionally supports signed registries, historical verification, independent verifier operation, durable bundles, public transparency-log inclusion or equivalent independently verifiable anti-equivocation evidence, and disclosure of witness/operator/substrate independence.

## 21. Security Considerations

Implementations MUST address:

- token forgery
- token replay
- registry substitution
- policy drift
- witness key compromise
- gateway omission of uncertified activity
- tool execution outside the gateway
- evidence URL mutation
- raw payload leakage
- direct upstream bypass
- prompt injection through data-source results
- confusing observed profiles with controlled profiles
- treating transport-success responses as logical success without inspecting upstream error indicators
- stale current pointers
- verifier trust-root drift

Critical failures MUST fail closed unless the verifier profile explicitly permits partial verification.

## 22. Current Reference Artifacts

amotivv's current verified-action implementation demonstrates MPF v0.2 draft concepts in working form:

- Verified Email MCP Gateway: capability-enforced, L1 witnessed, L2 policy-witnessed email side effects
- Supabase MCP Governance Proxy: curated MCP gateway pattern with connector credential custody and digest-only evidence; full email-style L1/L2 receipt parity and formal Supabase verifier checks are still in progress
- Tinfoil L1 Witnesses: TEE-attested mechanical witnesses with S3 Object Lock anti-equivocation guard evidence
- Signed Witness Registry Pointer: immutable registry epochs selected by signed current pointer
- Durable Certificate Bundles: complete bundle JSON published through S3/CloudFront no-overwrite storage
- Verifier Web App and CLI: independent bundle verification with pass/warn/fail checks

## 23. Open Questions For v0.2 Finalization

- Should `.well-known/mpf.json` replace or only supplement `.well-known/memory.json`?
- Should action capability tokens standardize on JWT/JWS, canonical JSON, COSE, or profile-defined encodings?
- Which artifact set is mandatory for all action certificates?
- Should action registries be mandatory for memory-only deployments?
- Should policy denials always produce durable bundles?
- How should private policy bundles be selectively disclosed while preserving verifiability?
- Which transparency log profile should be normative for high-assurance v0.2?
- Should MCP be a required binding or preferred binding?
- Should certificates self-declare which conformance profile they satisfy, or should conformance be only a verifier-side determination?
- What public terminology should be used: Verified Actions, Attested Actions, or Certified Actions?
- What minimum independence evidence is required before a witness counts as third-party?
- Should recipient verification receipts become a first-class MPF object?

## 24. Version History

### 24.1 MPF v0.1, May 2025

Initial public draft for AI memory infrastructure, including Memory Pods, Memory Objects, capability tokens, memory discovery, semantic search, multi-model vectors, and Merkle-verifiable audit trails.

### 24.2 MPF v0.2 Draft, May 2026

Extends MPF from verifiable memory to verified action. Adds Gateway, Action Registry, Witness Network, Intent Grant, Receipt Log, Certificate Bundle, Verifier Profile, MCP projection, action assurance profiles, and historical registry verification.
