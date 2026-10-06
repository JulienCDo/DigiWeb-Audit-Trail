# DigiWeb Audit Trail Event Catalog

## 1. Purpose

This document defines the audit event catalog supported by the DigiWeb Audit Platform MVP.

Each event:

- uses the canonical event model defined in `02-event-model.md`;
- has a unique and stable EventType value;
- is associated with an OrganizationId;
- identifies the originating user through UserId;
- contains only the information required for audit and reporting;
- is immutable after acceptance.

---

## 2. Supported Event Types

The MVP supports the following event types:

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

Event types must be written exactly as defined in this catalog.

EventType values are case-sensitive.

---

## 3. Event Definitions

### LOGIN

#### Description

Records a successful or failed login attempt.

#### Required Data

```json
{
  "authenticationMethod": "PASSWORD"
}
```

#### Optional Data

```json
{
  "failureReason": "INVALID_CREDENTIALS"
}
```

#### Rules

- Passwords must never be stored.
- Tokens must never be stored.
- Authentication secrets must never be stored.
- failureReason should be present only when login fails.

#### Reports

- User Activity Audit Report

---

### LOGOUT

#### Description

Records a user logout.

#### Required Data

```json
{
  "logoutReason": "USER_REQUEST"
}
```

#### Supported Values

```text
USER_REQUEST
SESSION_EXPIRED
ADMINISTRATIVE
SYSTEM
```

#### Reports

- User Activity Audit Report

---

### DICTATION_ACCESSED

#### Description

Records a user accessing a dictation.

#### Required Data

```json
{
  "dictationId": "12345",
  "accessType": "VIEW"
}
```

#### Supported Access Types

```text
OPEN
VIEW
```

#### Reports

- Access Audit Report
- User Activity Audit Report

---

### DICTATION_STATUS_CHANGED

#### Description

Records a dictation status change.

#### Required Data

```json
{
  "dictationId": "12345",
  "previousStatus": "RESERVED",
  "newStatus": "COMPLETED"
}
```

#### Rules

Both status values are required.

#### Reports

- Dictation Status History Report
- User Activity Audit Report
- True Productivity Report

---

### DICTATION_PURGED

#### Description

Records the permanent purge of a dictation or audio recording.

#### Required Data

```json
{
  "dictationId": "12345",
  "purgeType": "RETENTION_POLICY",
  "reason": "RETENTION_PERIOD_EXPIRED"
}
```

#### Optional Data

```json
{
  "audioRecordingId": "AUD456"
}
```

#### Supported Purge Types

```text
MANUAL
RETENTION_POLICY
SYSTEM
```

#### Rules

- Audio content must never be stored.
- The affected dictation must always be identified.

#### Reports

- User Activity Audit Report

---

### TRANSCRIPTION_MODIFIED

#### Description

Records a transcription modification.

#### Required Data

```json
{
  "transcriptionId": "TRX789",
  "dictationId": "12345",
  "version": 3,
  "changeType": "CORRECTION"
}
```

#### Supported Change Types

```text
CREATE
CORRECTION
CONTENT_UPDATE
ANNOTATION
```

#### Rules

- Transcription content must never be stored.
- Version must be greater than zero.

#### Reports

- Detailed Transcription Report
- User Activity Audit Report
- True Productivity Report

---

### TRANSCRIPTION_STATUS_CHANGED

#### Description

Records a transcription status change.

#### Required Data

```json
{
  "transcriptionId": "TRX789",
  "dictationId": "12345",
  "previousStatus": "DRAFT",
  "newStatus": "REVIEWED"
}
```

#### Rules

Both status values are required.

#### Reports

- Detailed Transcription Report
- User Activity Audit Report
- True Productivity Report

---

### WORK_SESSION

#### Description

Records user work activity.

#### Required Data

```json
{
  "action": "START",
  "workType": "TRANSCRIPTION"
}
```

or

```json
{
  "action": "STOP",
  "workType": "TRANSCRIPTION",
  "durationSeconds": 1080
}
```

#### Supported Actions

```text
START
STOP
```

#### Supported Work Types

```text
TRANSCRIPTION
REVIEW
CORRECTION
```

#### Rules

- durationSeconds must be non-negative.
- START events should not include durationSeconds.
- STOP events should include durationSeconds.
- Correlation identifiers may be used to associate start and stop events.

#### Reports

- Time Analysis Report
- True Productivity Report
- User Activity Audit Report

---

### REPORT_EXECUTED

#### Description

Records the execution of a report.

#### Required Data

```json
{
  "reportName": "ACCESS_AUDIT"
}
```

#### Optional Data

```json
{
  "executionDurationMs": 1523
}
```

#### Rules

- executionDurationMs must be non-negative.
- Sensitive query parameters must not be stored.
- Clinical information must not be stored.

#### Reports

- Report Usage Report
- User Activity Audit Report

---

## 4. Event-to-Report Mapping

| Event Type | Access Audit | Transcription Report | Dictation Status | User Activity | Time Analysis | Productivity | Report Usage |
|------------|:------------:|:--------------------:|:---------------:|:-------------:|:-------------:|:------------:|:------------:|
| LOGIN | | | | ✓ | | | |
| LOGOUT | | | | ✓ | | | |
| DICTATION_ACCESSED | ✓ | | | ✓ | | | |
| DICTATION_STATUS_CHANGED | | | ✓ | ✓ | | ✓ | |
| DICTATION_PURGED | | | | ✓ | | | |
| TRANSCRIPTION_MODIFIED | | ✓ | | ✓ | | ✓ | |
| TRANSCRIPTION_STATUS_CHANGED | | ✓ | | ✓ | | ✓ | |
| WORK_SESSION | | | | ✓ | ✓ | ✓ | |
| REPORT_EXECUTED | | | | ✓ | | | ✓ |

---

## 5. Event Production Rules

### Asynchronous Persistence

Successful publication of an event does not imply immediate persistence in Azure Cosmos DB.

Events are first published to Azure Storage Queue and are persisted asynchronously by the audit processing pipeline.

### Successful Operations

Business events should be published only after the business operation has been accepted.

### Failed Operations

Failure events may be published when required for security, traceability or operational investigations.

### Duplicate Submission

Applications may retry event publication.

Id is the idempotency key.

Duplicate deliveries must not create duplicate records.

### Immutability

Once accepted, an event must never be modified.

Corrections require a new event.

---

## 6. Events Outside MVP

The following events are not part of the MVP:

```text
USER_CREATED
USER_DISABLED
ROLE_CHANGED
PERMISSION_CHANGED
AUDIO_ACCESSED
AUDIO_DOWNLOADED
REPORT_EXPORTED
AI_ASSISTANCE_REQUESTED
AI_ASSISTANCE_COMPLETED
AI_ASSISTANCE_FAILED
SPEECH_RECOGNITION_STARTED
SPEECH_RECOGNITION_COMPLETED
SPEECH_RECOGNITION_FAILED
```

These events may be introduced in future versions.

---

## 7. Catalog Governance

A new event type may be added only when:

1. its business purpose is documented;
2. its required data is defined;
3. its privacy impact is assessed;
4. its reporting value is identified;
5. its contract version impact is understood;
6. it is approved by the audit platform owner.

Event names must never be reused with a different meaning.

Breaking changes require a new contract version.
