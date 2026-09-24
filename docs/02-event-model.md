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
