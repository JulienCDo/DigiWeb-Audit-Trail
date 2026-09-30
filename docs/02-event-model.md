# DigiWeb Audit Trail Event Model

## 1. Purpose

This document defines the canonical event model used by the DigiWeb Audit Platform.

All audit events published by DigiWeb, DigiConsole and future applications must comply with this model.

The model is designed to provide:

- consistent event storage;
- reliable audit reporting;
- organization isolation;
- event immutability;
- contract versioning;
- idempotent processing;
- compatibility with Azure Cosmos DB.

---

## 2. Design Principles

### Identity Source of Truth

Identity information is derived exclusively from the validated AuthenticationToken.

The following values are extracted during gRPC request processing:

- OrganizationId
- GroupId
- UserId

Clients must never provide those values directly.

The AuthenticationToken is not stored, queued or persisted.

---

### Immutability

Audit events are append-only.

Once accepted, an event is never modified.

Any correction or additional information must be represented by a new event.

---

### Idempotency

Each audit event must have a unique identifier.

The identifier is used as the idempotency key.

Duplicate submissions must not create duplicate audit records.

---

### Organization Isolation

OrganizationId is the primary isolation boundary of the platform.

All audit queries and reports must be organization-scoped.

---

## 3. Canonical Event Structure

The external event contract contains:

- auditEventId
- eventVersion
- timestampUtc
- authenticationToken
- applicationId
- eventType
- data

Example:

```json
{
  "auditEventId": "8e3a7d7c-9d67-4dc9-a337-cf1d4e44dc5",
  "eventVersion": "1.0",
  "timestampUtc": "2026-09-23T15:30:22.000Z",
  "authenticationToken": "token",
  "applicationId": "DIGI_WEB",
  "eventType": "DICTATION_ACCESSED",
  "data": {
    "dictationId": "12345",
    "accessType": "VIEW"
  }
}
```

---

## 4. Internal Audit Event

After authentication and validation, the platform produces an internal audit event.

Example:

```json
{
  "auditEventId": "8e3a7d7c-9d67-4dc9-a337-cf1d4e44dc5",
  "timestampUtc": "2026-09-23T15:30:22.000Z",
  "organizationId": "6c8cb3a5-022a-4779-89f8-5d2806379b8f",
  "groupId": "2785c132-7c39-4fe2-b4bd-8550478a17c0",
  "userId": "0eb2266f-6c91-4dce-a116-17fae7339cf0",
  "applicationId": "DIGI_WEB",
  "eventType": "DICTATION_ACCESSED",
  "data": {
    "dictationId": "12345",
    "accessType": "VIEW"
  }
}
```

---

## 5. Queue Model

The Azure Storage Queue message contains:

| Field | Type | Required | Description |
|---------|---------|---------|---------|
| AuditEventId | Guid | Yes | Unique audit identifier. |
| TimestampUtc | DateTime | Yes | Business event timestamp. |
| OrganizationId | Guid | Yes | Organization isolation boundary. |
| GroupId | Guid | Yes | Group associated with the actor. |
| UserId | Guid | Yes | User associated with the actor. |
| ApplicationId | Enum | Yes | Originating application. |
| EventType | String | Yes | Event catalog value. |
| Data | Dictionary | No | Event-specific information. |

The queue message never contains AuthenticationToken.

---

## 6. Cosmos Document

Events are persisted in Azure Cosmos DB.

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

Example document:

```json
{
  "id": "8e3a7d7c-9d67-4dc9-a337-cf1d4e44dc5",
  "timestampUtc": "2026-09-23T15:30:22.000Z",
  "organizationId": "6c8cb3a5-022a-4779-89f8-5d2806379b8f",
  "groupId": "2785c132-7c39-4fe2-b4bd-8550478a17c0",
  "userId": "0eb2266f-6c91-4dce-a116-17fae7339cf0",
  "app": "DIGI_WEB",
  "eventType": "DICTATION_ACCESSED",
  "data": {
    "dictationId": "12345",
    "accessType": "VIEW"
  }
}
```

---

## 7. Field Definitions

| Field | Type | Required | Description |
|---------|---------|---------|---------|
| id | Guid | Yes | Unique event identifier. |
| timestampUtc | DateTime | Yes | Business event timestamp in UTC. |
| organizationId | Guid | Yes | Organization isolation boundary. |
| groupId | Guid | Yes | Group identifier. |
| userId | Guid | Yes | User identifier. |
| app | String | Yes | Application identifier. |
| eventType | String | Yes | Event catalog value. |
| data | Object | No | Event-specific information. |

---

## 8. Application Identifiers

Supported applications:

```text
DIGI_WEB
DIGI_CONSOLE
```

Applications are represented by the ApplicationId protobuf enum.

---

## 9. Event Types

Supported event types:

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

EventType is stored as a string.

Validation is performed against the centralized event catalog.

---

## 10. Event Data Rules

The Data object contains only information required for:

- traceability;
- reporting;
- investigations.

The following information must never be stored:

- passwords;
- authentication tokens;
- authentication secrets;
- access tokens;
- clinical content;
- transcription content;
- audio content;
- unrestricted request payloads;
- unrestricted response payloads.

---

## 11. Correlation

Events belonging to the same business operation may share a CorrelationId within the Data object.

Example:

```json
{
  "data": {
    "correlationId": "3f43c6a0-48ab-4149-95dc-35ef42cf58cd"
  }
}
```

Correlation identifiers are optional in V1.

---

## 12. Versioning

All events must contain an EventVersion.

The version identifies the external contract version used by the client application.

Adding optional fields must not break existing producers or consumers.

Breaking changes require a new contract version.
