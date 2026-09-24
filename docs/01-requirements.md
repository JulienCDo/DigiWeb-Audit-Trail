# DigiWeb Audit Trail Requirements

## 1. Purpose

The DigiWeb Audit Trail provides a centralized and reliable record of significant user and system activities across DigiWeb and DigiConsole.

The audit trail must support:

- operational investigations;
- access traceability;
- compliance and customer audits;
- transcription and dictation history;
- productivity reporting;
- security investigations;
- future analytics capabilities.

The audit trail is the official transactional source for audit data. Microsoft Fabric and other analytics platforms may be integrated in a future phase, but they must not replace the transactional audit service.

## 2. Scope

The MVP covers the following applications:

- DigiWeb only;

All applications must publish audit events through the centralized Audit Service.

Applications must not write directly to the audit database or modify existing audit events.

## 3. MVP business questions

The MVP must reliably answer the following questions:

1. Who logged in or logged out?
2. Who accessed a dictation?
3. Who changed a dictation status?
4. Who modified a transcription?
5. Who changed a transcription status?
6. Who purged a dictation or its associated audio recording?
7. How much time did a user spend working on a dictation?
8. Which reports were executed?

## 4. MVP event requirements

The MVP includes the following event types:

| Event | Purpose | Required for |
|---|---|---|
| `LOGIN` | Records a successful or failed login attempt | Security and user activity |
| `LOGOUT` | Records a user logout | Security and user activity |
| `DICTATION_ACCESSED` | Records access to a dictation | Access audit |
| `DICTATION_STATUS_CHANGED` | Records a dictation status transition | Dictation history |
| `DICTATION_PURGED` | Records the permanent purge of a dictation or audio recording | Retention and compliance |
| `TRANSCRIPTION_MODIFIED` | Records a transcription modification | Transcription history |
| `TRANSCRIPTION_STATUS_CHANGED` | Records a transcription status transition | Transcription history |
| `WORK_SESSION` | Records the start or end of a work session | Time and productivity analysis |
| `REPORT_EXECUTED` | Records the execution of an audit or operational report | Report usage audit |

Each event must use the canonical event model defined in [`02-event-model.md`](./02-event-model.md).

## 5. Functional requirements

### FR-001 — Centralized event ingestion

The system must provide a centralized Audit Service for receiving events from DigiWeb.

### FR-002 — Application authentication

The Audit Service must authenticate calling applications before accepting audit events.

### FR-003 — Event validation

The Audit Service must validate each event against:

- the canonical event model;
- the event type;
- the required fields for that event;
- the tenant context;
- the supported event version.

Invalid events must be rejected and logged for technical investigation.

### FR-004 — Tenant association

Every event must be associated with a tenant.

The service must prevent an application from publishing an event for a tenant it is not authorized to access.

### FR-005 — Actor identification

Each event must identify the actor responsible for the action when applicable.

The actor may be:

- a user;
- a service;
- a scheduled process;
- a system operation.

System-generated events must not require a human user identifier.

### FR-006 — Target identification

Events concerning a business object must identify the target entity whenever applicable.

Examples include:

- dictation;
- transcription;
- audio recording;
- report;
- user.

### FR-007 — Immutability

Once accepted, an audit event must not be modified or deleted through normal application operations.

Any correction or additional context must be represented by a new event.

### FR-008 — Correlation

Events belonging to the same business operation must support correlation through a shared correlation identifier.

This allows investigators to reconstruct a complete workflow across services.

### FR-009 — Event ordering

The system must preserve the event timestamp supplied by the originating application and provide a deterministic ordering strategy for events with identical timestamps.

The storage sequence must not replace the original business event timestamp.

### FR-010 — Event search

Authorized consumers must be able to search audit events using supported filters, including:

- tenant;
- date range;
- event type;
- actor;
- target entity;
- correlation identifier.

### FR-011 — Report generation

The reporting layer must use audit events to generate the reports defined in [`04-reports.md`](./04-reports.md).

### FR-012 — Access control

Audit data and reports must be accessible only to authorized users and services.

Access must be evaluated according to:

- tenant;
- user role;
- report permissions;
- requested data scope.

### FR-013 — Export

The reporting layer should support, at minimum:

- CSV export;
- Excel export.

Export functionality must respect the same authorization rules as on-screen report access.

## 6. Non-functional requirements

### NFR-001 — Performance

Audit publication must not noticeably slow down normal DigiWeb or DigiConsole operations.

The preferred implementation is asynchronous processing through an internal queue.

### NFR-002 — Availability

The Audit Service must be available independently from the business services that publish events.

Temporary storage or processing failures must not silently result in lost events.

### NFR-003 — Durability

Accepted events must be durably stored in Azure Cosmos DB.

An event must not be acknowledged as successfully accepted before the system has persisted it or placed it in a durable processing mechanism.

### NFR-004 — Security

The system must provide:

- authenticated application-to-service communication;
- encrypted communication;
- role-based access to reports;
- tenant isolation;
- least-privilege access to storage;
- protection against unauthorized modification.

### NFR-005 — Privacy

Audit events must contain only the data required for traceability and reporting.

The following data must not be stored unnecessarily:

- full transcription content;
- clinical content;
- audio content;
- passwords;
- authentication secrets;
- access tokens;
- sensitive request payloads.

IP addresses and workstation information may be recorded only when permitted by applicable customer policies and regulations.

### NFR-006 — Scalability

The solution must support increasing event volumes without requiring direct access to the audit database from business applications.

The ingestion and reporting paths should be scalable independently.

### NFR-007 — Compatibility

The event contract must be versioned.

Adding an optional field must not break existing producers or consumers.

Breaking changes must require a new event contract version.

### NFR-008 — Retention

Audit retention must be configurable according to customer, contractual, legal, and regulatory requirements.

The detailed retention and archival strategy is outside the MVP implementation scope and must be documented before production deployment.

### NFR-009 — Time standard

All event timestamps must be stored in UTC using ISO 8601 format.

### NFR-010 — Data integrity

The system must provide mechanisms to detect:

- malformed events;
- missing required fields;
- duplicate events;
- invalid tenant associations;
- unsupported event versions;
- incomplete processing.

## 7. Nice To Have

### NTH-001 — Audit service observability

The Audit Service must provide operational telemetry for:

- accepted events;
- rejected events;
- duplicate events;
- processing failures;
- queue depth;
- processing latency;
- storage failures;
- tenant or application identification.

### NTH-002 — Idempotency

The Audit Service must support idempotent event submission.

If the same event is submitted more than once, the service must not create duplicate audit records.

The event identifier must be used as the idempotency key.

### NTH-003 — Failure handling

If an audit event cannot be accepted, the originating application must receive a clear technical response.

The failure must not expose sensitive event data.

The system must provide a reliable retry mechanism for transient failures.

### NTH-004 — Event ordering

The system must preserve the event timestamp supplied by the originating application and provide a deterministic ordering strategy for events with identical timestamps.

The storage sequence must not replace the original business event timestamp.
  
## 8. MVP reports

The MVP reporting layer must support:

### Access Audit Report

Answers:

> Who accessed a specific dictation?

Uses:

- `DICTATION_ACCESSED`

### Detailed Transcription Report

Answers:

> Who created, modified, reviewed, approved, or changed the status of a transcription?

Uses:

- `TRANSCRIPTION_MODIFIED`
- `TRANSCRIPTION_STATUS_CHANGED`

### Dictation Status History Report

Answers:

> How did the status of a dictation change over time?

Uses:

- `DICTATION_STATUS_CHANGED`

### User Activity Audit Report

Answers:

> What significant actions were performed by a user?

Uses:

- `LOGIN`
- `LOGOUT`
- `DICTATION_ACCESSED`
- `DICTATION_STATUS_CHANGED`
- `TRANSCRIPTION_MODIFIED`
- `TRANSCRIPTION_STATUS_CHANGED`
- `DICTATION_PURGED`
- `WORK_SESSION`
- `REPORT_EXECUTED`

### Time Analysis Report

Answers:

> How much time did each user spend working on a dictation?

Uses:

- `WORK_SESSION`

### True Productivity Report

Answers:

> What productivity indicators can be calculated from audit activity?

Uses:

- `WORK_SESSION`
- `TRANSCRIPTION_MODIFIED`
- `TRANSCRIPTION_STATUS_CHANGED`
- `DICTATION_STATUS_CHANGED`

### Report Usage Report

Answers:

> Which reports were executed, by whom, and when?

Uses:

- `REPORT_EXECUTED`

## 8. Out of scope for the MVP

The following features are excluded from the MVP:

- detailed role and permission auditing;
- user administration auditing;
- detailed audio playback auditing;
- separate audio download auditing;
- report export auditing;
- AI assistance auditing;
- speech recognition auditing;
- Microsoft Fabric integration;
- Power BI dashboards;
- anomaly detection;
- advanced historical analytics;
- long-term archival implementation;
- customer-specific retention exceptions.
- DigiConsole can publish events through the Audit Service;
- duplicate submissions are handled safely;
- transient processing failures can be retried;
- performance impact has been measured and accepted;
- retention and archival rules have been approved before production deployment.

These features may be considered in future phases.

## 9. MVP acceptance criteria

The MVP is considered functionally complete when:

- all nine MVP event types are defined;
- each event has a documented contract;
- DigiWeb can publish events through the Audit Service;
- events are validated before storage;
- events are associated with a tenant;
- accepted events are immutable;
- authorized users can search events;
- the seven MVP reports can be generated;
- CSV and Excel exports are available where applicable;
- tenant isolation has been tested;
