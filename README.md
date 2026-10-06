# DigiWeb Audit Trail

Centralized audit platform for DigiWeb, DigiConsole and future Synnefo applications.

The objective is to provide reliable traceability of user and system actions, support audit reporting, and prepare the platform for future compliance and analytics capabilities.

---

# Status

**Current phase: MVP implementation**

| Area | Status |
|--------|--------|
| Functional requirements | ✅ Defined |
| Event model | ✅ Defined |
| MVP event catalog | ✅ Defined |
| MVP reports | ✅ Defined |
| Technical architecture | ✅ Validated |
| Authentication strategy | ✅ Validated |
| Queue architecture | ✅ Validated |
| Cosmos DB model | ✅ Validated |
| Implementation | 🔄 In Progress |
| Long-term retention | 📌 Future phase |
| Microsoft Fabric | 📌 Future phase |

---

# MVP Objectives

The MVP must reliably answer the following questions:

1. Who logged in or logged out?
2. Who accessed a dictation?
3. Who changed a dictation status?
4. Who modified a transcription?
5. Who changed a transcription status?
6. Who purged a dictation or audio recording?
7. How much time did a user spend working on a dictation?
8. Which reports were executed?

---

# MVP Scope

The MVP is based on the following audit events:

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

Events are:

- centralized in a shared audit service;
- immutable after creation;
- organization-scoped;
- available to authorized reporting features;
- stored in Azure Cosmos DB;
- published through a gRPC API;
- processed asynchronously.

---

# Target Architecture

```text
Application
    │
    ▼
Audit gRPC API
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
Reports
```

---

# Architecture Principles

## Single source of truth

Azure Cosmos DB is the authoritative source of audit data.

## Asynchronous processing

Audit publication must not noticeably slow down business operations.

Applications receive acknowledgement once the event is successfully published to the queue.

## Immutability

Audit events are never modified after creation.

Any correction produces a new event.

## Organization isolation

Organizations can access only their own audit data.

Organization isolation is enforced throughout the platform.

## Centralized publication

Applications never write directly to Azure Cosmos DB.

All events are published through the Audit Service.

## Identity extraction

Authentication is validated at the gRPC boundary.

Identity information is extracted from the authentication token:

- OrganizationId
- GroupId
- UserId

The authentication token is never:

- stored;
- queued;
- logged;
- persisted.

---

# Identity Model

The platform uses the following identity model:

```text
OrganizationId
GroupId
UserId
```

OrganizationId is the official partitioning and isolation boundary.

---

# Reports

The following reports are planned for the MVP:

## Access Audit Report

Users who accessed a dictation.

## Detailed Transcription Report

History of transcription modifications and status changes.

## Dictation Status History Report

History of dictation status changes.

## User Activity Audit Report

History of logins, logouts and key user actions.

## Time Analysis Report

Work time recorded per user and per dictation.

## True Productivity Report

Productivity indicators calculated from activity events.

## Report Usage Report

History of executed reports.

CSV and Excel exports may be supported in future iterations.

---

# Documentation Structure

```text
README.md

docs/
  01-requirements.md
  02-event-model.md
  03-event-catalog.md
  04-reports.md
  05-architecture.md
  06-roadmap.md
```

---

# Documents

- Functional requirements
- Event model
- Event catalog
- Report specifications
- Technical architecture
- Roadmap

---

# MVP Technical Decisions

## Transport

gRPC only.

## Queue

Azure Storage Queue.

## Database

Azure Cosmos DB.

## Cosmos Partition Key

```text
/organizationId
```

## Authentication

AuthenticationToken is validated only at the gRPC endpoint.

The token is expanded into:

- OrganizationId
- GroupId
- UserId

Only those values continue through the system.

## Queue Contract

The internal queue message contains:

- AuditEventId
- TimestampUtc
- OrganizationId
- GroupId
- UserId
- ApplicationId
- EventType
- Data

## Response Contract

CreateAuditEventResponse returns only:

```text
SynnefoAPIStatus
```

Accepted means:

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

# Out of Scope

The following capabilities are planned for future versions:

- detailed role and permission auditing;
- user administration auditing;
- detailed audio access auditing;
- report export auditing;
- AI assistance auditing;
- long-term archival;
- Microsoft Fabric;
- Power BI;
- anomaly detection;
- advanced analytics.

---

# Important Rules

## No Direct Storage Access

Applications never access Cosmos DB directly.

## No Clinical Content

Audit events contain only traceability information.

Clinical data and transcription content must never be stored in the audit trail.

## Immutability

Events are append-only.

## Versioning

The event contract must remain versioned to support future evolution.

---

# Current Priorities

1. Validate end-to-end processing.
2. Add Application Insights observability.
3. Implement poison message handling.
4. Implement the Access Audit Report.
5. Implement audit search capabilities.
6. Extract AuditQueueProcessor into a dedicated worker service.
5. Implement the Access Audit Report.
6. Validate end-to-end processing.
