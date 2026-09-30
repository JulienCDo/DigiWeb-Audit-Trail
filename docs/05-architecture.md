# DigiWeb Audit Trail Architecture

## 1. Purpose

This document defines the target architecture for the DigiWeb Audit Platform MVP.

The architecture provides:

- centralized audit event ingestion;
- authentication and identity extraction;
- asynchronous event processing;
- immutable event storage;
- organization isolation;
- idempotent processing;
- authorized reporting;
- future extensibility.

The architecture supports DigiWeb, DigiConsole and future approved applications.

---

## 2. Architecture Principles

### Single Source of Truth

Azure Cosmos DB is the authoritative source of audit data.

---

### Centralized Ownership

Applications publish audit events exclusively through the Audit Service.

Applications never write directly to Azure Cosmos DB.

---

### Asynchronous Processing

Audit publication must not noticeably impact business operations.

Accepted events are processed asynchronously.

---

### Immutability

Audit events are append-only.

Once accepted, events cannot be modified.

Any correction must be represented by a new event.

---

### Organization Isolation

OrganizationId is the primary isolation boundary of the platform.

Isolation must be enforced:

- during ingestion;
- during persistence;
- during querying;
- during reporting.

---

### Contract-First Integration

All producers must use the approved versioned gRPC contract.

---

### Least Privilege

Each component receives only the permissions it requires.

---

### Privacy by Design

Audit events contain only information required for:

- traceability;
- investigations;
- reporting.

Sensitive information must never be stored.

---

## 3. Target Architecture

```text
Application
    │
    ▼
Audit gRPC API
    │
    ▼
Authentication
Token Validation
Identity Extraction
    │
    ▼
Azure Storage Queue
    │
    ▼
AuditQueueProcessor
    │
    ▼
Azure Cosmos DB
    │
    ▼
Reporting Layer
    │
    ▼
Authorized Users
```

---

## 4. Architecture Flow

### Request Processing

```text
CreateAuditEventRequest
        │
        ▼
Validate AuthenticationToken
        │
        ▼
Extract:
    OrganizationId
    GroupId
    UserId
        │
        ▼
Validate Event
        │
        ▼
Create AuditQueueMessage
        │
        ▼
Publish Azure Storage Queue
        │
        ▼
Return Accepted
```

---

### Persistence Processing

```text
Azure Storage Queue
        │
        ▼
AuditQueueProcessor
        │
        ▼
Persist Cosmos DB
        │
        ▼
Delete Queue Message
```

Queue messages are deleted only after:

- successful persistence;
- confirmed duplicate detection.

---

## 5. Components

### 5.1 Audit Producers

Initial producers:

- DigiWeb;
- DigiConsole.

Responsibilities:

- identify business actions;
- create audit requests;
- generate AuditEventId;
- provide AuthenticationToken;
- submit events through gRPC;
- retry transient failures.

Producers must never:

- access Cosmos DB directly;
- access Azure Storage Queue directly;
- submit OrganizationId;
- submit GroupId;
- submit UserId.

Identity information is obtained exclusively from AuthenticationToken.

---

### 5.2 Audit gRPC API

The Audit gRPC API is the only ingestion entry point.

Responsibilities:

- receive audit requests;
- validate AuthenticationToken;
- extract OrganizationId;
- extract GroupId;
- extract UserId;
- validate event contracts;
- validate EventType;
- validate payloads;
- publish queue messages;
- return SynnefoAPIStatus.

---

### Accepted Semantics

Accepted means:

```text
Validation Succeeded
AND
Queue Publication Succeeded
```

Accepted does NOT mean:

```text
Persisted in Cosmos DB
```

Persistence remains asynchronous.

---

### gRPC Contract

Request:

```text
AuditEventId
EventVersion
TimestampUtc
AuthenticationToken
ApplicationId
EventType
Data
```

Response:

```text
SynnefoAPIStatus
```

AuditEventId is not returned.

---

### 5.3 Azure Storage Queue

Azure Storage Queue provides:

- durable buffering;
- retry support;
- asynchronous processing;
- temporary failure protection.

Responsibilities
