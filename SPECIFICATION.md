# Memory Pod Fabric (MPF) Protocol Specification

## Version 0.1 | May 2025 | Draft

**Status:** Draft – For community review and comment

## Table of Contents


 1. [Introduction](#1-introduction)
 2. [Protocol Overview](#2-protocol-overview)
 3. [Core Concepts](#3-core-concepts)
 4. [Memory Card Specification](#4-memory-card-specification)
 5. [API Endpoints](#5-api-endpoints)
 6. [Data Structures](#6-data-structures)
 7. [Authentication and Authorization](#7-authentication-and-authorization)
 8. [Cryptographic Verification](#8-cryptographic-verification)
 9. [Multi-Model Support](#9-multi-model-support)
10. [Implementation Considerations](#10-implementation-considerations)
11. [Security Considerations](#11-security-considerations)
12. [Compliance and Governance](#12-compliance-and-governance)
13. [Integration with Existing Protocols](#13-integration-with-existing-protocols)
14. [Version History](#14-version-history)
15. [Appendixes](#15-appendixes)


---

## 1. Introduction

### 1.1 Purpose

Memory Pod Fabric (MPF) is an open protocol specification for AI memory infrastructure. This protocol defines standardized methods for storing, retrieving, and securing semantic memories across AI applications, agents, and organizations.

MPF addresses the fundamental problem of AI systems lacking persistent, verifiable, and securely shareable memory by providing a standardized protocol layer that can be implemented across diverse environments while maintaining consistent capabilities.

### 1.2 Design Principles

The protocol is built on these foundational principles:


1. **Memory as Infrastructure**: Memory should be a foundational layer for AI systems, not an afterthought
2. **Open and Standard**: Anyone should be able to implement the protocol without proprietary lock-in
3. **Minimal Surface Area**: Focus on core memory operations with clear, stable interfaces
4. **Verifiable Trust**: Enable cryptographic verification of memory integrity
5. **Secure by Design**: Implement fine-grained, capability-based security as a core primitive
6. **Flexible Implementation**: Support implementations ranging from local development to enterprise scale
7. **Future-Proof Architecture**: Accommodate evolving embedding models and AI capabilities
8. **Interoperability**: Work alongside existing protocols like A2A and MCP, not compete with them

### 1.3 Scope

The MPF specification defines:

* Core memory storage and retrieval operations
* Memory discovery and capability advertising
* Capability-based authorization model
* Cryptographic verification mechanisms
* Integration patterns with existing AI protocols
* Reference data structures and formats

The specification does not mandate:

* Specific vector database implementations
* Embedding model selection or parameters
* Internal implementation details beyond protocol compliance
* Business logic for memory processing

### 1.4 Relationship to Other Protocols

MPF is designed to complement, not replace, other AI protocols:

* **Agent2Agent (A2A)**: While A2A standardizes agent communication, MPF standardizes memory persistence. MPF can integrate with A2A through capability advertisement or as an A2A skill.
* **Model Context Protocol (MCP)**: MCP defines how models access tools and context. MPF can serve as a context provider to MCP-compliant systems.

## 2. Protocol Overview

### 2.1 Protocol Stack

MPF uses a layered architecture:


1. **Transport Layer**: Standard HTTPS for secure communication
2. **API Layer**: JSON-based RESTful API endpoints
3. **Authentication Layer**: OAuth 2.0 and capability JWT tokens
4. **Memory Operations Layer**: Store, search, and related context operations
5. **Verification Layer**: Cryptographic audit trails and Merkle anchoring

### 2.2 Core Interactions

MPF defines these fundamental interactions:


1. **Discovery**: A client discovers MPF endpoints and capabilities through a Memory Card
2. **Authentication**: A client obtains appropriate authorization and capability tokens
3. **Storage**: A client stores memory objects with vector embeddings and metadata
4. **Retrieval**: A client retrieves relevant memories through semantic similarity search
5. **Verification**: A client or auditor verifies the integrity of the memory audit trail

### 2.3 Protocol Flow

Typical MPF protocol flow:


1. Discover memory pod capabilities via `.well-known/memory.json`
2. Authenticate and obtain capability tokens for desired operations
3. Store memory content with vectors or request vector embedding
4. Query memory using semantic search parameters
5. Retrieve and use memory content
6. Optionally, verify audit trail integrity through Merkle proofs

## 3. Core Concepts

### 3.1 Memory Pod

A Memory Pod is a logical boundary of memory with its own governance, access controls, and audit trail. A pod represents a collection of memory objects that share common access patterns and security requirements.

Key characteristics:

* Self-contained namespace with unique identifier
* Dedicated capability scope for access control
* Independent audit trail and Merkle anchoring
* Logical isolation from other pods

### 3.2 Memory Object

A Memory Object is the fundamental unit of storage in MPF, containing:

* Unique identifier
* Vector embeddings for semantic search
* Metadata for filtering and organization
* Content (either stored directly or by reference)
* Creation and modification timestamps
* Access control information

### 3.3 Vector Embedding

Vector embeddings represent the semantic meaning of memory content in a high-dimensional space, enabling similarity search. MPF's multi-model approach allows multiple embedding representations to coexist within a single memory object.

### 3.4 Capability Token

Capability tokens are signed JWT tokens that grant specific, time-limited permissions to operate on memory pods or objects. They implement the object-capability security model for fine-grained access control.

Key attributes:

* Time-bound validity
* Specific operation permissions
* Pod and/or object scope limitations
* Delegation chain tracking
* Cryptographic signature verification

### 3.5 Merkle Tree Verification

MPF uses Merkle trees to create cryptographically verifiable audit trails of all memory operations. Regular anchoring of Merkle roots to external systems provides tamper-evident verification of memory history.

## 4. Memory Card Specification

### 4.1 Purpose

A Memory Card is a JSON document that advertises a Memory Pod's endpoints, capabilities, and technical requirements. It serves a similar role to A2A's Agent Card, allowing discovery of memory services.

### 4.2 Location

Memory Cards are available at a standardized location:

```
https://{memory-pod-host}/.well-known/memory.json
```

### 4.3 Format

The Memory Card uses this JSON structure:

```json
{
  "protocol_version": "mpf-0.1",
  "pod_name": "example-memory-pod",
  "pod_id": "urn:mpf:pod:example-org:main",
  "description": "Example organization's main memory pod",
  "vendor": {
    "name": "Example Organization",
    "url": "https://example.org"
  },
  "endpoints": {
    "base_url": "https://memory.example.org/api/v1",
    "contexts": "/contexts",
    "search": "/search",
    "related": "/related",
    "verify": "/verify",
    "embed": "/embed"
  },
  "capabilities": {
    "storage": true,
    "search": true,
    "multi_model": true,
    "verification": true,
    "streaming": false,
    "embedding": true
  },
  "auth": {
    "methods": ["oauth2", "capability_token"],
    "oauth2_endpoint": "https://auth.example.org/oauth2",
    "token_endpoint": "https://memory.example.org/api/v1/token"
  },
  "vector_models": [
    {
      "id": "mx-embed-1024",
      "dimensions": 1024,
      "description": "Default 1024-dimension embedding model"
    },
    {
      "id": "cep-512",
      "dimensions": 512,
      "description": "Compact 512-dimension embedding model"
    }
  ],
  "verification": {
    "merkle_interval": "daily",
    "anchor_type": "s3",
    "public_keys_url": "https://memory.example.org/.well-known/jwks.json",
    "verification_endpoint": "https://memory.example.org/api/v1/verify"
  },
  "documentation_url": "https://docs.example.org/memory-pod"
}
```

### 4.4 Required Fields

These fields are required in all Memory Cards:

* `protocol_version`: MPF protocol version
* `pod_id`: Globally unique identifier for the pod
* `endpoints.base_url`: Base URL for API endpoints
* `endpoints.contexts`: Contexts storage endpoint
* `endpoints.search`: Search endpoint
* `auth.methods`: Supported authentication methods

### 4.5 Optional Fields

These fields are optional but recommended:

* `pod_name`: Human-readable pod name
* `description`: Human-readable pod description
* `vendor`: Information about the implementing organization
* `capabilities`: Detailed feature support flags
* `vector_models`: Available embedding models
* `verification`: Merkle verification details
* `documentation_url`: Link to implementation documentation

## 5. API Endpoints

### 5.1 Core Endpoints

MPF defines these core endpoints:


1. `**POST /contexts**`: Store a memory object
2. `**GET /search**`: Search for relevant memories
3. `**GET /contexts/{id}**`: Retrieve a specific memory
4. `**GET /related/{id}**`: Find memories related to a specific memory
5. `**DELETE /contexts/{id}**`: Delete a specific memory

Additional optional endpoints:


6. `**POST /embed**`: Generate vector embeddings for content
7. `**GET /verify**`: Verify Merkle proof for audit trail
8. `**POST /token**`: Request capability tokens
9. `**GET /stream**`: Stream memory updates (Server-Sent Events)

### 5.2 Contexts Endpoint

#### 5.2.1 Store a Memory

**Request:**

```http

POST /contexts HTTP/1.1

Host: memory.example.org

Authorization: Bearer {capability_token}
Content-Type: application/json

{
  "content": {
    "text": "The customer reported an issue with the login screen on mobile devices.",
    "content_type": "text/plain"
  },
  "metadata": {
    "source": "customer-support",
    "category": "bug-report",
    "priority": "medium",
    "ticket_id": "CS-12345"
  },
  "vectors": {
    "mx-embed-1024": [0.1, 0.2, ..., -0.5],
    "cep-512": [0.3, -0.1, ..., 0.7]
  },
  "embedding_config": {
    "model": "mx-embed-1024",
    "truncation": "end",
    "include_metadata": true
  },
  "references": [
    {
      "id": "c78ef631-2130-4cd5-8e44-31ae78dcf79d",
      "relationship": "follows"
    }
  ],
  "ttl": 2592000
}
```

When `vectors` is omitted, the server generates embeddings according to `embedding_config`.

**Response:**

```http

HTTP/1.1 201 Created

Content-Type: application/json

Location: /contexts/d29fb331-2df0-4ea3-a6c1-63d12e021809

{
  "id": "d29fb331-2df0-4ea3-a6c1-63d12e021809",
  "pod_id": "urn:mpf:pod:example-org:main",
  "created_at": "2025-05-21T14:28:32.511Z",
  "metadata": {
    "source": "customer-support",
    "category": "bug-report",
    "priority": "medium",
    "ticket_id": "CS-12345"
  },
  "vectors": {
    "mx-embed-1024": {
      "model_id": "mx-embed-1024",
      "dimensions": 1024,
      "truncated": false,
      "embedding_id": "e57ba3c2-1f34-4e67-8f22-8cc99feb17a1"
    },
    "cep-512": {
      "model_id": "cep-512",
      "dimensions": 512,
      "truncated": false,
      "embedding_id": "f12cf7a0-7b43-4d24-95b3-8f16d8a27f7e"
    }
  },
  "audit": {
    "operation_id": "op-5467829c-f8b5-4e9a-bc17-3e5091f873a4",
    "transaction_hash": "4c291152cb54ef...d8f94ad21b8"
  }
}
```

#### 5.2.2 Retrieve a Memory

**Request:**

```http

GET /contexts/d29fb331-2df0-4ea3-a6c1-63d12e021809 HTTP/1.1

Host: memory.example.org

Authorization: Bearer {capability_token}
```

**Response:**

```http

HTTP/1.1 200 OK

Content-Type: application/json

{
  "id": "d29fb331-2df0-4ea3-a6c1-63d12e021809",
  "pod_id": "urn:mpf:pod:example-org:main",
  "created_at": "2025-05-21T14:28:32.511Z",
  "content": {
    "text": "The customer reported an issue with the login screen on mobile devices.",
    "content_type": "text/plain"
  },
  "metadata": {
    "source": "customer-support",
    "category": "bug-report",
    "priority": "medium",
    "ticket_id": "CS-12345"
  },
  "vectors": {
    "mx-embed-1024": {
      "model_id": "mx-embed-1024",
      "dimensions": 1024,
      "truncated": false,
      "embedding_id": "e57ba3c2-1f34-4e67-8f22-8cc99feb17a1"
    },
    "cep-512": {
      "model_id": "cep-512",
      "dimensions": 512,
      "truncated": false,
      "embedding_id": "f12cf7a0-7b43-4d24-95b3-8f16d8a27f7e"
    }
  },
  "references": [
    {
      "id": "c78ef631-2130-4cd5-8e44-31ae78dcf79d",
      "relationship": "follows"
    }
  ],
  "audit": {
    "operation_id": "op-5467829c-f8b5-4e9a-bc17-3e5091f873a4",
    "transaction_hash": "4c291152cb54ef...d8f94ad21b8",
    "merkle_proof_id": "merkle-2025-05-21"
  }
}
```

#### 5.2.3 Delete a Memory

**Request:**

```http

DELETE /contexts/d29fb331-2df0-4ea3-a6c1-63d12e021809 HTTP/1.1

Host: memory.example.org

Authorization: Bearer {capability_token}
```

**Response:**

```http

HTTP/1.1 200 OK

Content-Type: application/json

{
  "id": "d29fb331-2df0-4ea3-a6c1-63d12e021809",
  "status": "deleted",
  "audit": {
    "operation_id": "op-9a123b45-e512-4fa3-b975-0db28f4e19a7",
    "transaction_hash": "87f235ac19bd...4f29bc713e4"
  }
}
```

Note: Even when deleted, the audit record of the memory remains in the Merkle tree for verification purposes.

### 5.3 Search Endpoint

#### 5.3.1 Semantic Search

**Request:**

```http

GET /search?query=login%20screen%20issues&model=mx-embed-1024&limit=5 HTTP/1.1

Host: memory.example.org

Authorization: Bearer {capability_token}
```

**Response:**

```http

HTTP/1.1 200 OK

Content-Type: application/json

{
  "query": "login screen issues",
  "model": "mx-embed-1024",
  "results": [
    {
      "id": "d29fb331-2df0-4ea3-a6c1-63d12e021809",
      "similarity": 0.92,
      "content": {
        "text": "The customer reported an issue with the login screen on mobile devices.",
        "content_type": "text/plain"
      },
      "metadata": {
        "source": "customer-support",
        "category": "bug-report",
        "priority": "medium",
        "ticket_id": "CS-12345"
      },
      "created_at": "2025-05-21T14:28:32.511Z"
    },
    {
      "id": "a7c8f631-9e25-4bd7-a614-591827c63de2",
      "similarity": 0.87,
      "content": {
        "text": "Fixed login button alignment issue on mobile browsers.",
        "content_type": "text/plain"
      },
      "metadata": {
        "source": "development",
        "category": "fix",
        "commit_id": "abc123"
      },
      "created_at": "2025-05-20T09:14:18.275Z"
    },
    // Additional results...
  ],
  "total": 12,
  "limit": 5,
  "offset": 0,
  "audit": {
    "operation_id": "op-ef672913-8c25-4d19-9f33-7e1940a8e5c2",
    "transaction_hash": "2f91a48e7bd...5e7a92c61f4"
  }
}
```

#### 5.3.2 Vector Search

**Request:**

```http

POST /search HTTP/1.1

Host: memory.example.org

Authorization: Bearer {capability_token}
Content-Type: application/json

{
  "vector": [0.1, 0.2, ..., -0.5],
  "model": "mx-embed-1024",
  "limit": 5,
  "min_similarity": 0.7,
  "filters": {
    "metadata.category": "bug-report",
    "created_at": {
      "gte": "2025-05-01T00:00:00Z"
    }
  }
}
```

**Response:** Same format as semantic search.

#### 5.3.3 Metadata Search

**Request:**

```http

GET /search?metadata.ticket_id=CS-12345&limit=5 HTTP/1.1

Host: memory.example.org

Authorization: Bearer {capability_token}
```

**Response:** Same format as semantic search but based on metadata match.

### 5.4 Related Endpoint

#### 5.4.1 Find Related Memories

**Request:**

```http

GET /related/d29fb331-2df0-4ea3-a6c1-63d12e021809?limit=5 HTTP/1.1

Host: memory.example.org

Authorization: Bearer {capability_token}
```

**Response:**

```http

HTTP/1.1 200 OK

Content-Type: application/json

{
  "id": "d29fb331-2df0-4ea3-a6c1-63d12e021809",
  "related": [
    {
      "id": "a7c8f631-9e25-4bd7-a614-591827c63de2",
      "relationship": "semantic",
      "similarity": 0.87,
      "content": {
        "text": "Fixed login button alignment issue on mobile browsers.",
        "content_type": "text/plain"
      },
      "metadata": {
        "source": "development",
        "category": "fix",
        "commit_id": "abc123"
      },
      "created_at": "2025-05-20T09:14:18.275Z"
    },
    {
      "id": "c78ef631-2130-4cd5-8e44-31ae78dcf79d",
      "relationship": "reference",
      "similarity": null,
      "content": {
        "text": "Customer initially reported login issues on iPhone devices.",
        "content_type": "text/plain"
      },
      "metadata": {
        "source": "customer-support",
        "category": "bug-report",
        "ticket_id": "CS-12340"
      },
      "created_at": "2025-05-19T11:42:03.819Z"
    },
    // Additional results...
  ],
  "total": 8,
  "limit": 5,
  "offset": 0,
  "audit": {
    "operation_id": "op-2a5f8e31-6d47-4c95-8a29-1fb60a73ef42",
    "transaction_hash": "9a7b3c4d5e...6f7g8h9i0j"
  }
}
```

### 5.5 Embed Endpoint

#### 5.5.1 Generate Embeddings

**Request:**

```http

POST /embed HTTP/1.1

Host: memory.example.org

Authorization: Bearer {capability_token}
Content-Type: application/json

{
  "text": "The customer reported an issue with the login screen on mobile devices.",
  "models": ["mx-embed-1024", "cep-512"],
  "truncation": "end",
  "include_metadata": true,
  "metadata": {
    "source": "customer-support",
    "category": "bug-report"
  }
}
```

**Response:**

```http

HTTP/1.1 200 OK

Content-Type: application/json

{
  "text": "The customer reported an issue with the login screen on mobile devices.",
  "vectors": {
    "mx-embed-1024": [0.1, 0.2, ..., -0.5],
    "cep-512": [0.3, -0.1, ..., 0.7]
  },
  "truncated": false,
  "audit": {
    "operation_id": "op-38f2a901-cd45-4e67-8901-23456c78d9e0",
    "transaction_hash": "5a1b2c3d4e...5f6g7h8i9j"
  }
}
```

### 5.6 Verify Endpoint

#### 5.6.1 Verify Merkle Proof

**Request:**

```http

GET /verify?operation_id=op-5467829c-f8b5-4e9a-bc17-3e5091f873a4 HTTP/1.1

Host: memory.example.org

Authorization: Bearer {capability_token}
```

**Response:**

```http

HTTP/1.1 200 OK

Content-Type: application/json

{
  "operation_id": "op-5467829c-f8b5-4e9a-bc17-3e5091f873a4",
  "operation_type": "store",
  "context_id": "d29fb331-2df0-4ea3-a6c1-63d12e021809",
  "timestamp": "2025-05-21T14:28:32.511Z",
  "transaction_hash": "4c291152cb54ef...d8f94ad21b8",
  "merkle_proof": {
    "merkle_root": "ad7facb2...e982311cf",
    "path": [
      {"position": "left", "hash": "31ab42...789def"},
      {"position": "right", "hash": "98765f...432abc"},
      // Additional path elements...
    ],
    "anchor": {
      "type": "s3",
      "location": "s3://example-merkle-anchors/2025-05-21.json",
      "timestamp": "2025-05-22T00:00:00Z"
    }
  },
  "verification_result": "valid"
}
```

### 5.7 Token Endpoint

#### 5.7.1 Request a Capability Token

**Request:**

```http

POST /token HTTP/1.1

Host: memory.example.org

Authorization: Bearer {oauth_token}
Content-Type: application/json

{
  "operations": ["read", "search"],
  "scope": {
    "type": "pod",
    "filters": {
      "metadata.category": "bug-report"
    }
  },
  "expires_in": 3600,
  "purpose": "Investigating login screen issues"
}
```

**Response:**

```http

HTTP/1.1 200 OK

Content-Type: application/json

{
  "token": "eyJhbGciOiJFUzI1NiIsInR5cCI6IkpXVCJ9...",
  "expires_at": "2025-05-21T15:33:21.511Z",
  "operations": ["read", "search"],
  "scope": {
    "type": "pod",
    "filters": {
      "metadata.category": "bug-report"
    }
  },
  "token_id": "cap-9f82e451-c36a-47d9-b54e-a21fc978e640",
  "audit": {
    "operation_id": "op-76ef8a29-3c54-42b1-8d90-f23e45a6b7c8",
    "transaction_hash": "3a2b1c4d5e...6f7g8h9i0j"
  }
}
```

### 5.8 Stream Endpoint

#### 5.8.1 Stream Memory Updates

**Request:**

```http

GET /stream?filters=metadata.category:bug-report HTTP/1.1

Host: memory.example.org

Authorization: Bearer {capability_token}
Accept: text/event-stream
```

**Response:**

```http

HTTP/1.1 200 OK

Content-Type: text/event-stream

Cache-Control: no-cache

Connection: keep-alive

event: update

data: {"id":"f12ab345-6c7d-8e9f-0a1b-2c3d4e5f6a7b","operation":"store","timestamp":"2025-05-21T14:35:12.721Z"}

event: update

data: {"id":"g23bc456-7d8e-9f0a-1b2c-3d4e5f6a7b8c","operation":"store","timestamp":"2025-05-21T14:36:45.183Z"}

event: delete

data: {"id":"d29fb331-2df0-4ea3-a6c1-63d12e021809","operation":"delete","timestamp":"2025-05-21T14:38:19.547Z"}
```

## 6. Data Structures

### 6.1 Memory Object

The core memory object structure:

```json
{
  "id": "string",
  "pod_id": "string",
  "created_at": "string (ISO 8601)",
  "updated_at": "string (ISO 8601)",
  "content": {
    "text": "string",
    "content_type": "string (MIME type)",
    "uri": "string (optional URI to content)"
  },
  "metadata": {
    "property1": "any",
    "property2": "any",
    "...": "..."
  },
  "vectors": {
    "model_id_1": {
      "model_id": "string",
      "dimensions": "integer",
      "truncated": "boolean",
      "embedding_id": "string"
    },
    "model_id_2": {
      "model_id": "string",
      "dimensions": "integer",
      "truncated": "boolean",
      "embedding_id": "string"
    }
  },
  "references": [
    {
      "id": "string",
      "relationship": "string"
    }
  ],
  "ttl": "integer (seconds, optional)",
  "audit": {
    "operation_id": "string",
    "transaction_hash": "string",
    "merkle_proof_id": "string (optional)"
  }
}
```

### 6.2 Search Request

```json
{
  "query": "string (optional)",
  "vector": "array (optional)",
  "model": "string",
  "limit": "integer",
  "offset": "integer",
  "min_similarity": "float",
  "filters": {
    "property1": "any",
    "property2": {
      "eq": "any",
      "ne": "any",
      "gt": "any",
      "gte": "any",
      "lt": "any",
      "lte": "any",
      "in": "array",
      "exists": "boolean"
    }
  },
  "include_vectors": "boolean",
  "include_content": "boolean"
}
```

### 6.3 Capability Token (JWT)

```json
{
  "iss": "string (issuer)",
  "sub": "string (subject/user)",
  "aud": "string (audience/pod_id)",
  "exp": "integer (expiration timestamp)",
  "iat": "integer (issued at timestamp)",
  "jti": "string (token ID)",
  "mpf": {
    "operations": ["string (operation type)"],
    "scope": {
      "type": "string (pod|context)",
      "context_id": "string (optional)",
      "filters": {
        "property1": "any",
        "property2": "any"
      }
    },
    "parent_token_id": "string (optional)",
    "purpose": "string (optional)"
  }
}
```

### 6.4 Audit Record

```json
{
  "operation_id": "string",
  "timestamp": "string (ISO 8601)",
  "operation_type": "string",
  "user_id": "string",
  "token_id": "string",
  "resource": {
    "type": "string",
    "id": "string"
  },
  "metadata": {
    "property1": "any",
    "property2": "any"
  },
  "transaction_hash": "string",
  "previous_hash": "string",
  "merkle_node_id": "string (optional)"
}
```

### 6.5 Merkle Tree Node

```json
{
  "id": "string",
  "hash": "string",
  "parent_id": "string (optional)",
  "left_child_id": "string (optional)",
  "right_child_id": "string (optional)",
  "data_id": "string (optional)",
  "height": "integer",
  "created_at": "string (ISO 8601)"
}
```

### 6.6 Merkle Root Anchor

```json
{
  "id": "string",
  "merkle_root": "string",
  "timestamp": "string (ISO 8601)",
  "anchor_type": "string",
  "anchor_location": "string",
  "anchor_timestamp": "string (ISO 8601)",
  "signature": "string",
  "previous_anchor_id": "string (optional)"
}
```

## 7. Authentication and Authorization

### 7.1 Authentication Methods

MPF supports these authentication methods:


1. **OAuth 2.0**: Standard OAuth 2.0 flow for user authentication
2. **API Keys**: Simple API key authentication for service accounts
3. **Capability Tokens**: JWT tokens with fine-grained permissions

### 7.2 Capability-Based Security Model

MPF implements the object-capability security model through capability tokens:


1. Each capability token grants specific permissions to specific resources
2. Tokens are time-limited and cryptographically signed
3. Tokens form delegation chains, allowing controlled, traceable re-delegation
4. Tokens follow the principle of least privilege

### 7.3 Capability Operations

Capability tokens can grant these operations:

* `read`: Retrieve a memory object
* `store`: Create a new memory object
* `delete`: Remove a memory object
* `search`: Search for memory objects
* `embed`: Generate vector embeddings
* `verify`: Access verification information
* `delegate`: Create new, more restricted capability tokens

### 7.4 Capability Scopes

Tokens can be scoped to:

* **Pod**: Access to a whole memory pod (with optional filters)
* **Context**: Access to specific memory objects by ID
* **Both**: Combined scopes with different operations for each

### 7.5 Delegation Control

Capability tokens support delegation control:


1. `delegate` operation permission is required to issue new tokens
2. Delegated tokens cannot have more permissions than the parent token
3. The delegation chain is recorded in each token
4. Tokens can be revoked, invalidating entire delegation chains

## 8. Cryptographic Verification

### 8.1 Audit Trail

MPF maintains a complete, append-only audit trail of all operations:


1. Each operation generates an audit record
2. Audit records are cryptographically linked via hash chains
3. The hash chain prevents undetectable modification of past records
4. Audit records are incorporated into a Merkle tree structure

### 8.2 Merkle Tree Construction

The Merkle tree provides efficient verification:


1. Audit records form the leaves of the Merkle tree
2. Each parent node contains the hash of its children
3. The root of the tree represents the cryptographic digest of all operations
4. New operations are added as new leaves, with path updates to the root
5. Periodic snapshots of the tree are taken (e.g., daily or hourly)

### 8.3 Anchoring Options

MPF supports these anchoring methods for Merkle roots:


1. **File System**: Local file with hash and timestamp
2. **Database**: Dedicated table for anchors
3. **Object Storage**: S3, GCS, etc. with versioning
4. **Ledger Database**: AWS QLDB or similar
5. **IOTA Tangle**: Private or public anchoring
6. **Public Blockchain**: Ethereum, Bitcoin, etc.

### 8.4 Verification Process

Verification follows this process:


1. Retrieve the audit record for an operation
2. Validate the hash chain integrity
3. Verify the Merkle path from the record to a root
4. Validate the anchored Merkle root against external systems
5. Confirm the operation details match the audit record

### 8.5 Proof Format

Merkle proofs follow this format:

```json
{
  "merkle_root": "string (root hash)",
  "anchor": {
    "type": "string",
    "location": "string",
    "timestamp": "string (ISO 8601)"
  },
  "path": [
    {
      "position": "string (left|right)",
      "hash": "string"
    }
  ],
  "leaf_hash": "string",
  "leaf_data": "string"
}
```

## 9. Multi-Model Support

### 9.1 Vector Storage Approach

MPF employs a JSONB-based multi-model approach:


1. Each memory object can store multiple vector embeddings
2. Vectors are stored in a structured JSONB field
3. Each vector is associated with a specific model ID
4. Vector dimensions are explicitly recorded for compatibility

### 9.2 Benefits of Multi-Model Storage

This approach provides several advantages:


1. **Future-proofing**: New embedding models can be added without schema changes
2. **A/B testing**: Multiple models can be evaluated in parallel
3. **Migration support**: Smooth transitions between models
4. **Specialized retrieval**: Different models for different query types
5. **Interoperability**: Support for models from various providers

### 9.3 Model Registration

Memory Pods advertise supported models in their Memory Card:

```json
"vector_models": [
  {
    "id": "mx-embed-1024",
    "dimensions": 1024,
    "description": "Default 1024-dimension embedding model"
  },
  {
    "id": "cep-512",
    "dimensions": 512,
    "description": "Compact 512-dimension embedding model"
  }
]
```

### 9.4 Model Selection

Clients can specify models for embedding and search:


1. In store requests via `embedding_config.model`
2. In search requests via the `model` parameter
3. When generating embeddings via the `models` array

### 9.5 Vector Indexing

Implementations should index each model vector separately:


1. Create specialized indexes for each model dimension
2. Optimize each index for its specific dimensionality
3. Support efficient searching across any registered model

## 10. Implementation Considerations

### 10.1 Storage Options

MPF can be implemented on various storage backends:


1. **Postgres + pgVector**: Recommended for self-hosted deployments
2. **Snowflake VECTOR**: Ideal for enterprise deployments
3. **Specialized vector DBs**: Pinecone, Weaviate, Milvus, etc.
4. **Multi-tier storage**: Hot/warm/cold storage separation

### 10.2 Scaling Considerations

Implementations should consider:


1. **Read/write separation**: Optimize for read-heavy workloads
2. **Sharding**: Partition data by pod or time ranges
3. **Caching**: Cache frequent searches and hot memory objects
4. **Index optimization**: Tune vector indexes for specific workloads
5. **Batch operations**: Support efficient batch insertion and querying

### 10.3 Performance Guidelines

Recommendations for implementation performance:


1. Vector search should complete in <100ms for medium-sized pods
2. Storage operations should complete in <200ms
3. Verification operations can be more latent (seconds acceptable)
4. Embed operations should complete in <500ms

### 10.4 Embedding Providers

Implementations can support various embedding sources:


1. **Local models**: Self-hosted embedding models
2. **Cloud APIs**: OpenAI, Anthropic, Google, etc.
3. **Hybrid approach**: Local models with cloud fallback
4. **Custom models**: Organization-specific embedding models

### 10.5 Raw Content Storage

Options for storing raw content:


1. **Direct storage**: Content stored in the memory object
2. **Reference storage**: Content stored externally with URI reference
3. **Chunked storage**: Large content split into manageable chunks
4. **Tiered storage**: Age-based migration between storage tiers

## 11. Security Considerations

### 11.1 Token Security

Best practices for capability tokens:


1. Use ES256 or stronger signature algorithms
2. Implement token revocation mechanisms
3. Maintain short expiration times (hours, not days)
4. Verify the entire delegation chain
5. Follow zero-trust security principles

### 11.2 Data Protection

Recommendations for protecting memory content:


1. Encrypt content at rest
2. Implement field-level encryption for sensitive metadata
3. Support client-side encryption options
4. Apply appropriate retention policies with TTL

### 11.3 API Security

General API security guidelines:


1. Require TLS 1.2+ for all connections
2. Implement rate limiting to prevent abuse
3. Use appropriate HTTP security headers
4. Follow OAuth 2.0 security best practices
5. Apply the principle of least privilege to all operations

### 11.4 Audit and Monitoring

Security monitoring recommendations:


1. Log all access attempts, successful and failed
2. Monitor for unusual access patterns
3. Alert on suspicious delegation chains
4. Track token usage and revoke unused tokens
5. Perform regular security reviews of audit trails

## 12. Compliance and Governance

### 12.1 Regulatory Alignment

MPF is designed to support compliance with:


1. **EU AI Act**: Through verifiable audit trails and provenance
2. **GDPR**: With capability-based access controls and deletion support
3. **HIPAA**: Via secure, auditable memory pods for healthcare data
4. **Financial regulations**: Through tamper-evident record keeping

### 12.2 Governance Models

Recommended governance approaches:


1. **Pod-based isolation**: Separate regulated and non-regulated data
2. **Role-based access**: Define roles with appropriate capabilities
3. **Audit committees**: Regular review of access patterns
4. **Retention policies**: Automated enforcement of data retention rules
5. **Verification checks**: Regular integrity validation of audit trails

### 12.3 Risk Assessment

Common risk areas to address:


1. **Unauthorized access**: Mitigated by capability tokens
2. **Data leakage**: Controlled through fine-grained permissions
3. **Compliance violations**: Tracked via audit trails
4. **Vendor lock-in**: Avoided through the open protocol approach
5. **Data integrity**: Ensured by Merkle verification

### 12.4 Incident Response

Recommended procedures for security incidents:


1. Immediate token revocation capability
2. Audit trail analysis tools
3. Breach notification procedures
4. Forensic investigation support via verification
5. Recovery mechanisms for data integrity issues

## 13. Integration with Existing Protocols

### 13.1 A2A Integration

MPF can integrate with the Agent2Agent (A2A) protocol:


1. **MPF as A2A Skill**: Expose memory capabilities as an A2A skill
2. **A2A Agent Card**: Advertise memory services in an A2A Agent Card
3. **Task delegation**: Accept memory tasks via A2A
4. **Streaming updates**: Provide memory updates via A2A streaming

Example A2A skill for an MPF implementation:

```json
{
  "skill": "memory",
  "description": "Long-term memory services",
  "operations": [
    {
      "name": "store_memory",
      "description": "Store a new memory",
      "parameters": {
        "content": "string",
        "metadata": "object"
      }
    },
    {
      "name": "retrieve_memories",
      "description": "Find relevant memories",
      "parameters": {
        "query": "string",
        "limit": "integer"
      }
    }
  ],
  "auth_requirements": {
    "type": "capability_token",
    "token_endpoint": "https://memory.example.org/api/v1/token"
  }
}
```

### 13.2 MCP Integration

MPF can integrate with the Model Context Protocol (MCP):


1. **Context source**: Serve as an MCP context provider
2. **Tool integration**: Offer memory operations as MCP tools
3. **Two-way communication**: Store MCP interactions in memory

Example MCP schema for MPF integration:

```json
{
  "schema_version": "v1",
  "name": "memory_pod_fabric",
  "description": "Access to semantic memory",
  "tools": [
    {
      "name": "store_memory",
      "description": "Store a new memory in the pod",
      "input_schema": {
        "type": "object",
        "properties": {
          "content": {
            "type": "string",
            "description": "The content to remember"
          },
          "metadata": {
            "type": "object",
            "description": "Additional metadata about the memory"
          }
        },
        "required": ["content"]
      },
      "output_schema": {
        "type": "object",
        "properties": {
          "id": {
            "type": "string",
            "description": "The ID of the stored memory"
          },
          "status": {
            "type": "string",
            "enum": ["success", "error"]
          }
        }
      }
    },
    {
      "name": "search_memories",
      "description": "Find relevant memories by semantic search",
      "input_schema": {
        "type": "object",
        "properties": {
          "query": {
            "type": "string",
            "description": "The search query"
          },
          "limit": {
            "type": "integer",
            "description": "Maximum number of results",
            "default": 5
          }
        },
        "required": ["query"]
      },
      "output_schema": {
        "type": "object",
        "properties": {
          "results": {
            "type": "array",
            "items": {
              "type": "object",
              "properties": {
                "id": {"type": "string"},
                "content": {"type": "string"},
                "similarity": {"type": "number"}
              }
            }
          }
        }
      }
    }
  ]
}
```

### 13.3 LangChain Integration

MPF can integrate with LangChain:


1. **VectorStore implementation**: Custom VectorStore for MPF
2. **Memory implementation**: Custom Memory class for MPF
3. **Retriever implementation**: Custom Retriever for MPF search

Example LangChain integration:

```python

from langchain.vectorstores import BaseVectorStore

from langchain.schema import Document

class MPFVectorStore(BaseVectorStore):
    """Vector store for Memory Pod Fabric."""
    
    def __init__(self, client, pod_id, embedding):
        self.client = client
        self.pod_id = pod_id
        self.embedding = embedding
    
    def add_texts(self, texts, metadatas=None, **kwargs):
        """Add texts to the vector store."""
        ids = []
        for i, text in enumerate(texts):
            metadata = metadatas[i] if metadatas else {}
            
            response = self.client.store_memory(
                content=text,
                metadata=metadata,
                embedding_config={"model": self.embedding.model_name}
            )
            ids.append(response["id"])
        
        return ids
    
    def similarity_search(self, query, k=4, **kwargs):
        """Search for similar documents."""
        response = self.client.search_memories(
            query=query,
            limit=k,
            model=self.embedding.model_name
        )
        
        documents = []
        for result in response["results"]:
            documents.append(
                Document(
                    page_content=result["content"]["text"],
                    metadata={
                        "id": result["id"],
                        "similarity": result["similarity"],
                        **result["metadata"]
                    }
                )
            )
        
        return documents
```

## 14. Version History

### 14.1 Version 0.1 (May 2025)

Initial draft specification with:

* Core API endpoints
* Basic data structures
* Capability token model
* Merkle verification framework
* Multi-model support

### 14.2 Future Versions

Planned future enhancements:

* Streaming APIs for real-time memory updates
* Enhanced delegation mechanisms
* Standardized capability vocabulary
* Cross-pod linking and federation
* Advanced verification optimizations

## 15. Appendixes

### 15.1 JSON Schema

Full JSON Schema definitions for all data structures will be available at: `https://github.com/memory-pod-fabric/mpf-spec/blob/main/schema/v0.1/`

### 15.2 Reference Implementations

Reference implementations are available for:

* Python client SDK
* TypeScript client SDK
* Postgres+pgVector server (Docker)
* Snowflake VECTOR adapter

### 15.3 Capability Token Security

Detailed security considerations for capability tokens:


1. **Token Structure**: Use standard JWT format with ES256 signatures
2. **Delegation Chains**: Record full delegation history in tokens
3. **Token Revocation**: Implement efficient revocation with minimal state
4. **Key Management**: Recommendations for rotation and storage
5. **Authorization Patterns**: Common patterns for different use cases

### 15.4 Merkle Tree Implementation

Technical details on Merkle tree implementation:


1. **Tree Structure**: Binary Merkle tree with SHA-256 hashing
2. **Anchor Scheduling**: Daily anchoring with optional intermediate anchors
3. **Proof Generation**: Efficient path calculation algorithms
4. **Verification Process**: Step-by-step verification walkthrough
5. **Anchoring Security**: Considerations for different anchor types

### 15.5 Multi-Model Implementation

Technical details on multi-model vector storage:


 1. **JSONB Structure**: Efficient representation of multiple vectors
 2. **Index Creation**: Specialized indexes for different vector dimensions
 3. **Search Optimization**: Performance tuning for multi-model retrieval
 4. **Model Transitioning**: Strategies for smooth model migration
 5. **Dimension Reduction**: Techniques for handling high-dimensional vectors
 6. **Storage Efficiency**: JSONB compression techniques for vector data to minimize storage overhead while maintaining query performance. Implementations should consider PG_COMPRESSION or similar technologies for large vector arrays.
 7. **Hybrid Search**: Techniques for combining results from multiple models:
    * Ensemble scoring methods for merging similarity scores
    * Model selection based on query characteristics
    * Performance considerations for parallel vs. sequential searching
 8. **Version Tracking**: Methodology for tracking embedding model versions:

    ```json
    "vectors": {
      "mx-embed-1024": {
        "model_id": "mx-embed-1024",
        "dimensions": 1024,
        "truncated": false,
        "embedding_id": "e57ba3c2-1f34-4e67-8f22-8cc99feb17a1",
        "version": "2025-03-15",
        "provider": "mixedbread-ai"
      }
    }
    ```
 9. **Cross-Model Compatibility**: Techniques for enabling cross-model compatibility:
    * Mapping between different dimensional spaces
    * Transfer functions for score normalization
    * Handling of model-specific metadata
10. **Vector Analysis Tools**: Standard tools for vector analysis:
    * Clustering vectors to identify topic groups
    * Dimensionality visualization for debugging
    * Outlier detection for quality control

### 15.6 Reference Python Client

A minimal reference implementation of an MPF Python client:

```python

import requests

import json

import jwt

import datetime

import uuid

class MPFClient:
    """Client for Memory Pod Fabric protocol."""
    
    def __init__(self, base_url, token=None):
        """Initialize MPF client.
        
        Args:
            base_url: Base URL of the Memory Pod Fabric server
            token: Optional capability token
        """
        self.base_url = base_url.rstrip('/')
        self.token = token
        self.session = requests.Session()
        if token:
            self.session.headers.update({'Authorization': f'Bearer {token}'})
    
    def discover(self):
        """Discover memory pod capabilities via Memory Card."""
        url = f"{self.base_url.split('/api')[0]}/.well-known/memory.json"
        response = self.session.get(url)
        response.raise_for_status()
        return response.json()
    
    def store_memory(self, content, metadata=None, vectors=None, embedding_config=None, 
                    references=None, ttl=None):
        """Store a new memory.
        
        Args:
            content: Text content or dict with text and content_type
            metadata: Optional dict of metadata
            vectors: Optional dict of pre-computed vectors
            embedding_config: Optional embedding configuration
            references: Optional list of related memory references
            ttl: Optional time-to-live in seconds
            
        Returns:
            Dict containing the created memory information
        """
        if isinstance(content, str):
            content = {'text': content, 'content_type': 'text/plain'}
            
        data = {
            'content': content,
            'metadata': metadata or {},
        }
        
        if vectors:
            data['vectors'] = vectors
        
        if embedding_config:
            data['embedding_config'] = embedding_config
            
        if references:
            data['references'] = references
            
        if ttl:
            data['ttl'] = ttl
            
        response = self.session.post(f"{self.base_url}/contexts", json=data)
        response.raise_for_status()
        return response.json()
    
    def get_memory(self, memory_id):
        """Retrieve a specific memory by ID.
        
        Args:
            memory_id: ID of the memory to retrieve
            
        Returns:
            Dict containing the memory information
        """
        response = self.session.get(f"{self.base_url}/contexts/{memory_id}")
        response.raise_for_status()
        return response.json()
    
    def search_memories(self, query=None, vector=None, model=None, limit=10, 
                        offset=0, min_similarity=0.7, filters=None, 
                        include_vectors=False, include_content=True):
        """Search for memories by text query or vector.
        
        Args:
            query: Optional text query
            vector: Optional vector for similarity search
            model: Model ID to use for search
            limit: Maximum number of results
            offset: Number of results to skip
            min_similarity: Minimum similarity threshold
            filters: Optional filters for metadata
            include_vectors: Whether to include vectors in results
            include_content: Whether to include content in results
            
        Returns:
            Dict containing search results
        """
        params = {
            'limit': limit,
            'offset': offset,
            'min_similarity': min_similarity,
            'include_vectors': include_vectors,
            'include_content': include_content
        }
        
        if query and not vector:
            # Text query search
            params['query'] = query
            if model:
                params['model'] = model
            
            if filters:
                for key, value in filters.items():
                    if isinstance(value, dict):
                        for op, op_value in value.items():
                            params[f"filters.{key}.{op}"] = op_value
                    else:
                        params[f"filters.{key}"] = value
                        
            response = self.session.get(f"{self.base_url}/search", params=params)
            
        else:
            # Vector search
            data = {
                'vector': vector,
                'model': model,
                'limit': limit,
                'offset': offset,
                'min_similarity': min_similarity,
                'filters': filters or {},
                'include_vectors': include_vectors,
                'include_content': include_content
            }
            
            if query:
                data['query'] = query
                
            response = self.session.post(f"{self.base_url}/search", json=data)
            
        response.raise_for_status()
        return response.json()
    
    def get_related(self, memory_id, limit=10, offset=0):
        """Find memories related to a specific memory.
        
        Args:
            memory_id: ID of the reference memory
            limit: Maximum number of results
            offset: Number of results to skip
            
        Returns:
            Dict containing related memories
        """
        params = {
            'limit': limit,
            'offset': offset
        }
        
        response = self.session.get(
            f"{self.base_url}/related/{memory_id}", 
            params=params
        )
        response.raise_for_status()
        return response.json()
    
    def delete_memory(self, memory_id):
        """Delete a specific memory.
        
        Args:
            memory_id: ID of the memory to delete
            
        Returns:
            Dict containing deletion confirmation
        """
        response = self.session.delete(f"{self.base_url}/contexts/{memory_id}")
        response.raise_for_status()
        return response.json()
    
    def generate_embeddings(self, text, models=None, truncation="end", 
                           include_metadata=False, metadata=None):
        """Generate vector embeddings for text.
        
        Args:
            text: Text to embed
            models: Optional list of model IDs
            truncation: Truncation strategy ("start", "end", "none")
            include_metadata: Whether to include metadata in embedding
            metadata: Optional metadata to include
            
        Returns:
            Dict containing generated vectors
        """
        data = {
            'text': text,
            'truncation': truncation,
            'include_metadata': include_metadata
        }
        
        if models:
            data['models'] = models
            
        if metadata:
            data['metadata'] = metadata
            
        response = self.session.post(f"{self.base_url}/embed", json=data)
        response.raise_for_status()
        return response.json()
    
    def verify_operation(self, operation_id):
        """Verify an operation with Merkle proof.
        
        Args:
            operation_id: ID of the operation to verify
            
        Returns:
            Dict containing verification results
        """
        response = self.session.get(
            f"{self.base_url}/verify", 
            params={'operation_id': operation_id}
        )
        response.raise_for_status()
        return response.json()
    
    def request_token(self, operations, scope, expires_in=3600, purpose=None):
        """Request a new capability token.
        
        Args:
            operations: List of permitted operations
            scope: Dict describing token scope
            expires_in: Expiration time in seconds
            purpose: Optional purpose description
            
        Returns:
            Dict containing the new token
        """
        data = {
            'operations': operations,
            'scope': scope,
            'expires_in': expires_in
        }
        
        if purpose:
            data['purpose'] = purpose
            
        response = self.session.post(f"{self.base_url}/token", json=data)
        response.raise_for_status()
        return response.json()
```

### 15.7 PostgreSQL Reference Schema

A reference PostgreSQL schema for MPF implementation:

```sql
-- Core table for memory contexts

CREATE TABLE memory_contexts (
    id UUID PRIMARY KEY,
    pod_id TEXT NOT NULL,
    created_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    updated_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    content JSONB NOT NULL,
    metadata JSONB NOT NULL DEFAULT '{}'::JSONB,
    vectors JSONB NOT NULL DEFAULT '{}'::JSONB,
    references JSONB[] DEFAULT ARRAY[]::JSONB[],
    ttl INTEGER,
    expires_at TIMESTAMPTZ
);

-- Create GIN index on metadata for efficient filtering

CREATE INDEX idx_contexts_metadata ON memory_contexts USING GIN (metadata);

-- Create index for time-based queries

CREATE INDEX idx_contexts_created_at ON memory_contexts (created_at);

-- Create trigger for automatically setting expires_at based on ttl

CREATE OR REPLACE FUNCTION set_expires_at() RETURNS TRIGGER AS $$
BEGIN
    IF NEW.ttl IS NOT NULL THEN
        NEW.expires_at := NEW.created_at + (NEW.ttl * INTERVAL '1 second');
    ELSE
        NEW.expires_at := NULL;
    END IF;
    RETURN NEW;
END;
$$ LANGUAGE plpgsql;

CREATE TRIGGER trigger_set_expires_at

BEFORE INSERT OR UPDATE ON memory_contexts

FOR EACH ROW

EXECUTE FUNCTION set_expires_at();

-- Audit trail for all operations

CREATE TABLE audit_records (
    operation_id UUID PRIMARY KEY,
    timestamp TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    operation_type TEXT NOT NULL,
    user_id TEXT NOT NULL,
    token_id TEXT NOT NULL,
    resource_type TEXT NOT NULL,
    resource_id TEXT NOT NULL,
    metadata JSONB NOT NULL DEFAULT '{}'::JSONB,
    transaction_hash TEXT NOT NULL,
    previous_hash TEXT,
    merkle_node_id UUID
);

CREATE INDEX idx_audit_timestamp ON audit_records (timestamp);
CREATE INDEX idx_audit_resource ON audit_records (resource_type, resource_id);
CREATE INDEX idx_audit_token ON audit_records (token_id);

-- Merkle tree nodes

CREATE TABLE merkle_nodes (
    id UUID PRIMARY KEY,
    hash TEXT NOT NULL,
    parent_id UUID REFERENCES merkle_nodes(id),
    left_child_id UUID REFERENCES merkle_nodes(id),
    right_child_id UUID REFERENCES merkle_nodes(id),
    data_id UUID,
    height INTEGER NOT NULL,
    created_at TIMESTAMPTZ NOT NULL DEFAULT NOW()
);

CREATE INDEX idx_merkle_parent ON merkle_nodes(parent_id);
CREATE INDEX idx_merkle_data ON merkle_nodes(data_id);

-- Merkle root anchors

CREATE TABLE merkle_anchors (
    id UUID PRIMARY KEY,
    merkle_root TEXT NOT NULL,
    timestamp TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    anchor_type TEXT NOT NULL,
    anchor_location TEXT NOT NULL,
    anchor_timestamp TIMESTAMPTZ,
    signature TEXT NOT NULL,
    previous_anchor_id UUID REFERENCES merkle_anchors(id)
);

CREATE INDEX idx_merkle_anchors_timestamp ON merkle_anchors(timestamp);

-- Vector extension for pgvector

CREATE EXTENSION IF NOT EXISTS vector;

-- Function to create or update vector indexes for a model

CREATE OR REPLACE FUNCTION create_vector_index(model_name TEXT, dimensions INTEGER) RETURNS VOID AS $$
DECLARE
    index_name TEXT;
    vector_path TEXT;
BEGIN
    index_name := 'idx_vectors_' || model_name;
    vector_path := 'vectors->' || quote_literal(model_name) || '->''vector''';

    -- Check if index already exists
    IF NOT EXISTS (SELECT 1 FROM pg_indexes WHERE indexname = index_name) THEN
        -- Create HNSW index for the vector
        EXECUTE format(
            'CREATE INDEX %I ON memory_contexts USING hnsw (((%s)::vector(%s))) WITH (ef_construction=200, m=16)',
            index_name,
            vector_path,
            dimensions
        );
    END IF;
END;
$$ LANGUAGE plpgsql;

-- Example usage:
-- SELECT create_vector_index('mx-embed-1024', 1024);
-- SELECT create_vector_index('cep-512', 512);
```

### 15.8 Access Control LLM Integration

Example of how an Access Control LLM can be integrated with MPF:

```python

import openai

from mpf_client import MPFClient

class AccessControlLLM:
    """Natural language interface for MPF access control."""
    
    def __init__(self, model_name, mpf_client):
        """Initialize Access Control LLM.
        
        Args:
            model_name: Name of the LLM to use
            mpf_client: MPFClient instance
        """
        self.model_name = model_name
        self.mpf_client = mpf_client
        
    def process_request(self, request, user_info, allowed_operations):
        """Process a natural language access request.
        
        Args:
            request: Natural language access request
            user_info: Dict containing user information
            allowed_operations: List of operations the user can delegate
            
        Returns:
            Dict containing the generated capability token
        """
        # Prepare the prompt for the LLM
        prompt = f"""
        You are an Access Control LLM that generates capability tokens for Memory Pod Fabric.
        
        User information:
        - ID: {user_info['id']}
        - Role: {user_info['role']}
        - Permissions: {', '.join(allowed_operations)}
        
        Natural language request:
        "{request}"
        
        Generate a JSON capability token configuration with the following fields:
        - operations: Array of permitted operations (must be subset of user's permissions)
        - scope: Object describing token scope (type, filters, etc.)
        - expires_in: Expiration time in seconds (max 24 hours)
        - purpose: Brief description of the token's purpose
        
        The response must be valid JSON and operations must be limited to: {', '.join(allowed_operations)}
        """
        
        # Get LLM interpretation of the request
        response = openai.ChatCompletion.create(
            model=self.model_name,
            messages=[
                {"role": "system", "content": "You are an access control assistant that translates natural language requests into precise capability tokens."},
                {"role": "user", "content": prompt}
            ],
            temperature=0.1,
            max_tokens=500
        )
        
        # Extract and parse the token configuration
        token_config = json.loads(response.choices[0].message.content)
        
        # Validate operations (ensure subset of allowed_operations)
        for op in token_config['operations']:
            if op not in allowed_operations:
                raise ValueError(f"Operation {op} not allowed for this user")
        
        # Validate expiration (max 24 hours)
        if token_config.get('expires_in', 86400) > 86400:
            token_config['expires_in'] = 86400
        
        # Request the token
        token_response = self.mpf_client.request_token(
            operations=token_config['operations'],
            scope=token_config['scope'],
            expires_in=token_config['expires_in'],
            purpose=token_config['purpose']
        )
        
        return {
            'token': token_response['token'],
            'expires_at': token_response['expires_at'],
            'interpretation': {
                'operations': token_config['operations'],
                'scope': token_config['scope'],
                'purpose': token_config['purpose']
            }
        }
        
    def explain_token(self, token_data):
        """Generate a natural language explanation of a token.
        
        Args:
            token_data: Token data from MPF
            
        Returns:
            String containing natural language explanation
        """
        prompt = f"""
        Explain this Memory Pod Fabric capability token in natural language:
        
        Token ID: {token_data['token_id']}
        Operations: {', '.join(token_data['operations'])}
        Scope: {json.dumps(token_data['scope'])}
        Expires at: {token_data['expires_at']}
        Purpose: {token_data.get('purpose', 'Not specified')}
        
        Generate a clear, concise explanation that a non-technical person can understand.
        """
        
        response = openai.ChatCompletion.create(
            model=self.model_name,
            messages=[
                {"role": "system", "content": "You are an access control assistant that explains capability tokens in plain language."},
                {"role": "user", "content": prompt}
            ],
            temperature=0.7,
            max_tokens=300
        )
        
        return response.choices[0].message.content

# Example usage:
# client = MPFClient("https://memory.example.org/api/v1", admin_token)
# access_llm = AccessControlLLM("gpt-4-0125-preview", client)
# token = access_llm.process_request(
#     "Allow John's agent to read memories about Project Apollo for 2 hours",
#     {"id": "alice", "role": "admin", "permissions": ["read", "search", "delegate"]},
#     ["read", "search", "delegate"]
# )
```

### 15.9 Compliance Framework Mapping

Table mapping MPF features to regulatory requirements:

| Regulation | Requirement | MPF Feature | Implementation Notes |
|----|----|----|----|
| EU AI Act | Traceability of AI decisions | Merkle verification | Store decision history with tamper-evident verification |
| EU AI Act | Documentation of training data | Content storage | Maintain provenance of data used in AI systems |
| EU AI Act | Access controls | Capability tokens | Fine-grained permission model with audit |
| GDPR | Right to be forgotten | Delete operations | Cryptographic proof of deletion |
| GDPR | Data portability | Multi-model storage | Standardized export format |
| GDPR | Access control | Capability tokens | Granular access with explicit delegation |
| HIPAA | Technical safeguards | Pod isolation | Separate PHI into dedicated pods |
| HIPAA | Audit controls | Audit trail | Complete record of all accesses |
| HIPAA | Access controls | Capability tokens | Role-based access with time limits |
| SOC 2 | Access control | Capability tokens | Principle of least privilege |
| SOC 2 | Audit logging | Audit trail | Comprehensive logging of all operations |
| SOC 2 | Change management | Merkle anchoring | Tamper-evident record of changes |
| SEC | Recordkeeping | Merkle verification | Immutable audit trail of transactions |
| SEC | Supervision | Delegation chains | Traceable oversight of access grants |
| NIST | Zero Trust | Capability tokens | Fine-grained, time-limited permissions |
| NIST | Continuous verification | Merkle proofs | Ongoing validation of data integrity |

### 15.10 Implementation Decision Guide

Decision framework for MPF implementers:


1. **Storage Backend Selection**

| Criteria | Postgres+pgVector | Snowflake VECTOR | Specialized Vector DB |
|----|----|----|----|
| Scale | < 10M vectors | > 10M vectors | Varies by provider |
| Performance | Good | Excellent | Varies |
| Cost | Low | Medium-High | Medium |
| Control | Full | Limited | Limited |
| Compliance | Custom | Built-in | Varies |
| Implementation | Reference available | Adapter needed | Custom adapter |
2. **Anchoring Mechanism Selection**

| Criteria | Local | S3/GCS | AWS QLDB | IOTA | Public Blockchain |
|----|----|----|----|----|----|
| Trust Level | Low | Medium | Medium | High | Highest |
| Cost | Free | Low | Medium | Low/Free | Medium-High |
| Complexity | Low | Low | Medium | Medium | High |
| Latency | Instant | Low | Low | Medium | High |
| Compliance | Limited | Good | Good | Excellent | Excellent |
| Independence | None | Limited | Limited | Good | Best |
3. **Embedding Approach Selection**

| Criteria | Local Models | API-based | Hybrid |
|----|----|----|----|
| Control | High | Low | Medium |
| Cost | Fixed | Variable | Mixed |
| Performance | Consistent | Variable | Mixed |
| Model Quality | Medium | High | High |
| Compliance | High | Medium | High |
| Complexity | High | Low | Medium |
4. **Capability Token Implementation**

| Criteria | Simple JWT | Hierarchical JWT | ZCAP-LD |
|----|----|----|----|
| Complexity | Low | Medium | High |
| Flexibility | Limited | Good | Best |
| Delegation | Basic | Good | Advanced |
| Standards | Well-known | Extended | Emerging |
| Implementation | Simple | Moderate | Complex |

### 15.11 API Status Codes and Error Handling

Standard HTTP status codes and error responses for MPF implementations:

| Status Code | Description | Example Scenario |
|----|----|----|
| 200 OK | Successful request | GET operation completed |
| 201 Created | Resource created | Memory stored successfully |
| 400 Bad Request | Invalid request parameters | Malformed request data |
| 401 Unauthorized | Missing or invalid authentication | No token provided |
| 403 Forbidden | Valid auth but insufficient permissions | Token lacks required operations |
| 404 Not Found | Resource not found | Memory ID doesn't exist |
| 409 Conflict | Resource conflict | Duplicate operation |
| 422 Unprocessable Entity | Valid request but semantic errors | Invalid vector dimensions |
| 429 Too Many Requests | Rate limit exceeded | Too many requests |
| 500 Internal Server Error | Server error | Unexpected failure |
| 503 Service Unavailable | Temporary unavailability | Maintenance mode |

Error response format:

```json
{
  "error": {
    "code": "invalid_token",
    "message": "The provided capability token does not grant the required permissions",
    "details": {
      "required_operations": ["write"],
      "token_operations": ["read", "search"],
      "request_id": "req-1234567890"
    }
  }
}
```

Standard error codes:

| Error Code | Description |
|----|----|
| invalid_request | Malformed request |
| invalid_token | Token validation failed |
| insufficient_permissions | Token lacks required permissions |
| resource_not_found | Requested resource doesn't exist |
| invalid_vector | Vector dimensions or format invalid |
| rate_limited | Too many requests |
| internal_error | Server-side error |
| service_unavailable | Service temporarily unavailable |
| validation_error | Request validation failed |
| merkle_verification_failed | Verification of Merkle proof failed |

### 15.12 Query Language for Search Filtering

Standard query language for MPF search filters:

```
# Basic operators

field = value           # Equality

field != value          # Inequality

field > value           # Greater than

field >= value          # Greater than or equal

field < value           # Less than

field <= value          # Less than or equal

field in [val1, val2]   # In array

field has value         # Contains value (for arrays/objects)
field exists            # Field exists

# Logical operators

condition1 AND condition2

condition1 OR condition2

NOT condition

# Metadata field access

metadata.field = value

metadata.nested.field > 10

# Date ranges

created_at > 2025-05-01T00:00:00Z

created_at between 2025-05-01T00:00:00Z AND 2025-05-31T23:59:59Z

# Examples

metadata.category = "bug-report" AND created_at > 2025-05-01T00:00:00Z

metadata.tags has "important" OR metadata.priority = "high"
```

This query language should be translated to the appropriate filtering mechanism for the underlying storage implementation.

