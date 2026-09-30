# Current Architecture Decisions

## Architecture

Approved processing flow:

```text
Application
    ↓
Audit gRPC API
    ↓
Azure Storage Queue
    ↓
AuditQueueProcessor
    ↓
Azure Cosmos DB
```

Architecture principles:

- gRPC only.
- Azure Storage Queue used for durable asynchronous processing.
- Cosmos DB is the system of record.
- Queue publication is the acknowledgement boundary.
- Audit processing must never block business operations.
- Processing remains asynchronous.

---

## Identity

Official identity model:

- OrganizationId
- GroupId
- UserId

Rules:

- TenantId removed from the domain model.
- TenantId == OrganizationId.
- OrganizationId is the official identifier.
- Identity is derived exclusively from AuthenticationToken.
- OrganizationId, GroupId and UserId are extracted at the gRPC boundary.
- Clients never submit OrganizationId, GroupId or UserId directly.

---

## Authentication

AuthenticationToken is validated only at the gRPC endpoint.

After validation:

- OrganizationId is extracted.
- GroupId is extracted.
- UserId is extracted.

AuthenticationToken must never:

- be stored;
- be logged;
- be published to Azure Storage Queue;
- be persisted to Cosmos DB;
- be transmitted to AuditQueueProcessor.

The processor has no dependency on authentication services.

---

## Queue

AuditQueueMessage contains:

- AuditEventId
- TimestampUtc
- OrganizationId
- GroupId
- UserId
- ApplicationId
- EventType
- Data

The queue message never contains AuthenticationToken.

---

## Event Catalog

Approved MVP event types:

```text
LOGIN
LOGOUT
DICTATION_ACCESSED
DICTATION_STATUS_CHANGED
DICTATION_PURGED
TRANSCRIPTION_MODIFIED
TRANSCRIPTION_STATUS_CHANGED
WORK_SESSION
REPORT_EXECUTED
```

Rules:

- EventType is stored as a string.
- EventType is validated against the centralized catalog.
- ApplicationId remains a protobuf enum.

---

## Cosmos

Database:

```text
SynnefoAudit
```

Container:

```text
AuditEvents
```

Partition Key:

```text
/organizationId
```

Document Id:

```text
AuditEventId
```

Minimum document fields:

- id
- timestampUtc
- organizationId
- groupId
- userId
- app
- eventType
- data

---

## gRPC

CreateAuditEventRequest contains:

- AuditEventId
- EventVersion
- TimestampUtc
- AuthenticationToken
- ApplicationId
- EventType
- Data

CreateAuditEventResponse returns only:

```text
SynnefoAPIStatus
```

AuditEventId is not returned because it is already known by the client.

---

## Acceptance Semantics

Accepted = true means:

```text
Validation succeeded
AND
Queue publication succeeded
```

Accepted does not mean:

```text
Persisted in Cosmos DB
```

Persistence remains asynchronous.

---

## Idempotency

AuditEventId is the idempotency key.

Persistence uses:

```text
Document Id = AuditEventId
```

HTTP 409 Conflict means:

```text
Duplicate Delivery
```

and is not considered an error.

Duplicate events:

- must not be reinserted;
- must not inflate reports;
- must be logged for observability.

Queue messages may be deleted when:

- persistence succeeds;
- duplicate delivery is confirmed.

---

## Current Implementation

Current processor implementation:

```csharp
AddHostedService<AuditQueueProcessor>()
```

Current processor dependencies are singletons.

Do not create:

```csharp
IServiceScope
```

inside AuditQueueProcessor.

A future release may move the processor into a dedicated Worker Service.

---

## Current Priorities

1. Implement CosmosAuditRepository.SaveAsync().
2. Persist queue events to Cosmos DB.
3. Complete idempotency handling.
4. Delete queue messages after successful processing.
5. Add Application Insights observability.
6. Implement the Access Audit Report.
