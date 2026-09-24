# DigiWeb Audit Trail Event Model

## 1. Purpose

This document defines the canonical event model used by the DigiWeb Audit Trail.

All audit events published by DigiWeb must comply with this model.

The model is designed to provide:

- consistent event storage;
- reliable audit reporting;
- multi-tenant isolation;
- event immutability;
- correlation across business operations;
- controlled contract evolution;
- compatibility with Azure Cosmos DB.

## 2. Canonical event structure

An audit event contains:

- a unique identifier;
- a contract version;
- the event timestamp;
- the tenant context;
- the originating application;
- the event type;
- the actor;
- the target entity;
- the result;
- optional correlation and session information;
- event-specific data.

Example:

```json
{
  "id": "8e3a7d7c-9d67-4dc9-a337-cf1d4e44dc5",
  "eventVersion": 1,
  "timestampUtc": "2026-09-23T15:30:22.000Z",
  "tenantId": "TENANT001",
  "application": "DigiWeb",
  "eventType": "DICTATION_ACCESSED",
  "category": "Dictation",
  "outcome": "SUCCESS",
  "severity": "INFO",
  "actor": {
    "type": "USER",
    "id": "USR123",
    "displayName": "Jane Doe",
    "role": "TRANSCRIPTIONIST"
  },
  "target": {
    "type": "DICTATION",
    "id": "DICT456"
  },
  "sessionId": "4e3a7d7c-9d67-4dc9-a337-cf1d4e44dc5",
  "correlationId": "7b2a3c4d-5e6f-7890-abcd-ef1234567890",
  "data": {
    "accessType": "VIEW"
  }
}
```

## 3. Field definitions

| Field | Type | Required | Description |
|---|---|---:|---|
| `id` | UUID | Yes | Unique identifier of the audit event. Used as the idempotency key. |
| `eventVersion` | Integer | Yes | Version of the event contract. |
| `timestampUtc` | DateTime | Yes | Time at which the business action occurred, in UTC. |
| `tenantId` | String | Yes | Tenant to which the event belongs. |
| `application` | String | Not for V1 | Application that generated the event, such as `DigiWeb` or `DigiConsole`. |
| `eventType` | String | Yes | Canonical event name from the event catalog. |
| `category` | String | Yes | Functional category of the event. |
| `outcome` | String | No | Result of the operation. |
| `severity` | String | No | Business or security importance of the event. |
| `actor` | Object | Yes | User, service, or system responsible for the action. |
| `target` | Object | No | Business entity affected by the action. |
| `sessionId` | UUID | No | User session associated with the event. |
| `correlationId` | UUID | No | Identifier used to link events belonging to the same operation. |
| `data` | JSON object | No | Event-specific information required for reporting or investigation. |

## 4. Actor

The `actor` identifies who or what performed the action.

### Actor structure

| Field | Type | Required | Description |
|---|---|---:|---|
| `type` | String | Yes | `USER`, `SERVICE`, `SYSTEM`, or `SCHEDULED_PROCESS`. |
| `id` | String | Yes | Identifier of the user, service, or process. |
| `displayName` | String | No | Display name. Must not contain unnecessary sensitive information. |
| `role` | String | No | User role at the time of the event. |

### User actor example

```json
{
  "type": "USER",
  "id": "USR123",
  "displayName": "Jane Doe",
  "role": "TRANSCRIPTIONIST"
}
```

### System actor example

```json
{
  "type": "SYSTEM",
  "id": "RetentionService",
  "displayName": "Retention Service"
}
```

A system-generated event must use a non-user actor type. It must not use a fake or placeholder user identifier.

## 5. Target

The `target` identifies the business entity affected by the event.

### Target structure

| Field | Type | Required | Description |
|---|---|---:|---|
| `type` | String | Yes | Type of the affected entity. |
| `id` | String | Yes | Identifier of the affected entity. |

Example:

```json
{
  "type": "TRANSCRIPTION",
  "id": "TRX789"
}
```

Supported target types may include:

```text
DICTATION
TRANSCRIPTION
AUDIO_RECORDING
REPORT
USER
TENANT
```

The target is optional for events such as a login attempt where no business entity is affected.

## 6. Controlled values

### Event categories

The following categories are supported by the MVP:

```text
Authentication
Dictation
Transcription
Productivity
Reporting
```

Additional categories may be added through a versioned catalog update.

### Event types

The MVP event types are:

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

Event types must be written exactly as defined in the event catalog.

### Outcomes

```text
SUCCESS
FAILURE
PARTIAL_SUCCESS
DENIED
```

Examples:

- `LOGIN` with outcome `SUCCESS`;
- `LOGIN` with outcome `FAILURE`;
- `DICTATION_ACCESSED` with outcome `DENIED`;
- `REPORT_EXECUTED` with outcome `SUCCESS`.

### Severities

```text
INFO
WARNING
ERROR
CRITICAL
```

Severity must represent the importance of the event, not the technical status of the API request.

### Actor types

```text
USER
SERVICE
SYSTEM
SCHEDULED_PROCESS
```

## 7. Event-specific data

The `data` object contains only fields specific to the event type.

It must not contain:

- passwords;
- access tokens;
- authentication secrets;
- complete transcription content;
- audio content;
- unnecessary clinical information;
- complete request or response payloads.

### LOGIN

```json
{
  "data": {
    "authenticationMethod": "PASSWORD",
    "failureReason": "INVALID_CREDENTIALS"
  }
}
```

`failureReason` is required only when the login fails.

### LOGOUT

```json
{
  "data": {
    "logoutReason": "USER_REQUEST"
  }
}
```

Supported logout reasons may include:

```text
USER_REQUEST
SESSION_EXPIRED
ADMINISTRATIVE
SYSTEM
```

### DICTATION_ACCESSED

```json
{
  "data": {
    "accessType": "VIEW"
  }
}
```

Supported access types may include:

```text
OPEN
VIEW
```

### DICTATION_STATUS_CHANGED

```json
{
  "data": {
    "previousStatus": "RESERVED",
    "newStatus": "COMPLETED"
  }
}
```

Both status values are required.

### DICTATION_PURGED

```json
{
  "data": {
    "purgeType": "MANUAL",
    "reason": "RETENTION_POLICY",
    "audioRecordingId": "AUD456"
  }
}
```

Supported purge types may include:

```text
MANUAL
RETENTION_POLICY
SYSTEM
```

### TRANSCRIPTION_MODIFIED

```json
{
  "data": {
    "version": 3,
    "changeType": "CORRECTION"
  }
}
```

The event must record the resulting transcription version.

Supported change types may include:

```text
CREATE
CORRECTION
CONTENT_UPDATE
ANNOTATION
```

### TRANSCRIPTION_STATUS_CHANGED

```json
{
  "data": {
    "previousStatus": "DRAFT",
    "newStatus": "REVIEWED"
  }
}
```

Both status values are required.

### WORK_SESSION

```json
{
  "data": {
    "action": "START",
    "workType": "TRANSCRIPTION",
    "startedAtUtc": "2026-09-23T15:30:22.000Z",
    "stoppedAtUtc": "2026-09-23T15:48:22.000Z",
    "durationSeconds": 1080
  }
}
```

Required fields depend on the action:

- `START` requires `startedAtUtc`;
- `STOP` requires `stoppedAtUtc` and `durationSeconds`;
- `durationSeconds` must be a non-negative integer.

A work session should use the same `correlationId` for its start and stop events when both events are emitted separately.

### REPORT_EXECUTED

```json
{
  "data": {
    "reportName": "ACCESS_AUDIT",
    "executionDurationMs": 1523,
    "parameterSummary": {
      "dateRange": "2026-09-01/2026-09-23",
      "dictationId": "DICT456"
    }
  }
}
```

Report parameters must be minimized and sanitized.

The event must not store sensitive data or unrestricted query payloads.

## 8. Required field rules

The following rules apply to all events:

1. `id` must be a valid UUID.
2. `tenant id` must be unique within the tenant.
3. `eventVersion` must be a positive integer.
4. `timestampUtc` must be a valid UTC timestamp.
5. `tenantId` must be present and must match the authenticated application context.
6. `application` must be an approved application identifier.
7. `eventType` must exist in the approved event catalog.
8. `category`, `outcome`, and `severity` must contain supported values.
9. `actor.type` and `actor.id` must always be present.
10. `target` must be present when required by the event definition.
11. `data` must comply with the event-specific contract.
12. Unknown required fields must cause validation failure.
13. Sensitive data must be rejected or removed before persistence.
14. Events must be immutable after acceptance.

## 9. Timestamp and ordering

`timestampUtc` represents the time at which the business action occurred. It must not be replaced by the time at which the event is received or stored.

The system should also record processing metadata outside the business event when required, such as:

- ingestion time;
- processing time;
- retry count.

These technical values must not replace `timestampUtc`.

When two events have the same timestamp, consumers should order them using:

1. `timestampUtc`;
2. ingestion sequence, if available;
3. `id` as a deterministic fallback.

## 10. Idempotency

The event `id` is the idempotency key.

If the same event is submitted more than once:

- the first valid submission may be persisted;
- subsequent submissions must not create duplicate records;
- the service should return the existing event status;
- a submission with the same `id` but different content must be rejected.

## 11. Immutability

Audit events are append-only.

The following operations are not permitted through normal application APIs:

- update an event;
- overwrite an event;
- delete an event;
- change the tenant;
- change the actor;
- change the target;
- change the event timestamp.

If new information becomes available, the system must record a new event.

Retention-related deletion is a separate governance operation and must not be exposed as a normal event modification operation.

## 12. Versioning

The event contract is versioned through `eventVersion`.

### Compatible changes

The following changes may be introduced without incrementing the major contract version:

- adding an optional field;
- adding optional event-specific data;
- adding a non-breaking enum value after consumer validation.

### Breaking changes

The following changes require a new contract version:

- renaming a field;
- changing a field type;
- removing a field;
- changing the meaning of an existing field;
- making an optional field mandatory;
- changing the interpretation of an event.

Producers and consumers must support the versions agreed for the deployment.

## 13. Azure Cosmos DB considerations

Azure Cosmos DB is the source of truth for accepted audit events.

Recommended document properties:

```text
id              Event identifier
tenantId        Tenant partition key
timestampUtc    Business event timestamp
eventType       Event type
category        Event category
actor.id        Actor identifier
target.id       Target identifier
outcome         Operation outcome
severity        Event severity
```

The recommended partition key is:

```text
/tenantId
```

The final partitioning strategy must be validated against:

- expected event volume;
- tenant distribution;
- cross-tenant access restrictions;
- report query patterns;
- retention requirements.

Applications must not access Cosmos DB directly. All writes and authorized reads must go through the appropriate audit or reporting service.

## 14. Minimal valid event

```json
{
  "id": "8e3a7d7c-9d67-4dc9-a337-cf1d4e44dc5",
  "eventVersion": 1,
  "timestampUtc": "2026-09-23T15:30:22.000Z",
  "tenantId": "TENANT001",
  "application": "DigiWeb",
  "eventType": "LOGIN",
  "category": "Authentication",
  "outcome": "SUCCESS",
  "severity": "INFO",
  "actor": {
    "type": "USER",
    "id": "USR123"
  }
}
```

## 15. Design principles

The event model follows these principles:

- keep the common envelope small;
- keep event-specific data inside `data`;
- do not store clinical content unnecessarily;
- identify both the actor and target where applicable;
- use UTC for all timestamps;
- prefer additive, backward-compatible changes;
- treat accepted events as immutable;
- make duplicate submission safe;
- enforce tenant isolation at every layer.
