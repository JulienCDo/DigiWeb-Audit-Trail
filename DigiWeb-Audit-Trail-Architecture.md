# DigiWeb Audit Platform
# Audit Trail Architecture

Phase: Architecture

---

# 1. Purpose

This document defines the target architecture for the DigiWeb Audit Platform.

The platform provides centralized audit capabilities for:

- DigiWeb
- DigiConsole
- Future applications

The objective is to deliver:

- Regulatory audit capabilities
- Customer-required audit reports
- Multi-tenant support
- Centralized audit management
- Reusable architecture across applications
- Scalable long-term audit storage

---

# 2. Architectural Goals

The architecture must:

- Support customer audit requirements
- Support multiple applications
- Minimize operational complexity
- Minimize infrastructure components
- Support asynchronous processing
- Avoid direct database access from applications
- Provide a single audit source of truth
- Scale across tenants and applications

---

# 3. Architectural Principles

## Centralized Audit Platform

Audit functionality is implemented as a shared platform service.

Applications must not implement their own audit repositories.

```text
DigiWeb
DigiConsole
Future Applications
        │
        ▼
Audit Platform
```

---

## Asynchronous Processing

Business operations must never be blocked by audit persistence.

Audit events are submitted asynchronously.

```text
Business Action
       │
       ├── Success Response
       │
       ▼
Audit Event Queued
```

Audit failures must not impact business transactions.

---

## Single Source of Truth

Azure Cosmos DB is the authoritative audit datastore.

No secondary audit storage layer exists.

No synchronization between multiple repositories is required.

```text
Audit Event
      │
      ▼
Azure Cosmos DB
```

---

## Immutable Events

Audit records are immutable.

Once persisted:

- Events cannot be modified
- Events cannot be deleted individually
- Corrections are represented by new events

---

## Multi-Tenant Design

Every audit event belongs to a tenant.

All queries and reports must be tenant-aware.

---

# 4. Target Architecture

```text
Applications
├── DigiWeb
├── DigiConsole
└── Future Applications

        │
        ▼

Audit gRPC API

        │
        ▼

Audit Service

        │
        ▼

Internal Queue

        │
        ▼

Azure Cosmos DB
(Source of Truth)

        │
        ▼

Reporting APIs

        │
        ▼

Audit Reports
```

---

# 5. Components

## Applications

Applications generate audit events when business actions occur.

Examples:

- User login
- Dictation access
- Transcription modification
- Status changes
- Report execution

Applications never write directly to Cosmos DB.

---

## Audit gRPC API

The Audit Platform exposes a gRPC interface.

All consuming applications communicate through this API.

Responsibilities:

- Receive audit events
- Validate requests
- Authenticate applications
- Forward events for processing

Benefits:

- Strongly typed contracts
- Consistency across applications
- High performance
- Alignment with existing DigiWeb architecture

---

## Audit Service

The Audit Service acts as the core platform component.

Responsibilities:

- Event validation
- Metadata enrichment
- Event transformation
- Queue publishing
- Error handling

The Audit Service is the only component authorized to persist audit events.

---

## Internal Queue

The internal queue decouples applications from storage operations.

Responsibilities:

- Buffer incoming events
- Support retry mechanisms
- Smooth traffic spikes
- Protect Cosmos DB from bursts

Benefits:

- Non-blocking architecture
- Better scalability
- Improved reliability

---

## Azure Cosmos DB

Azure Cosmos DB serves as the authoritative audit repository.

Responsibilities:

- Store all audit events
- Support reporting queries
- Support future analytics integration
- Provide tenant isolation

Cosmos DB replaces:

- Relational audit indexes
- Blob-based audit storage

---

## Reporting Layer

The reporting layer retrieves audit information from Cosmos DB.

Responsibilities:

- Audit report generation
- Filtering
- Search capabilities
- Export capabilities

Reports may be exposed through:

- DigiWeb
- DigiConsole
- Future administrative consoles

---

# 6. Audit Event Flow

## Event Creation

```text
User Action
      │
      ▼
Application
      │
      ▼
Audit gRPC API
```

Example:

```text
User accesses a dictation
```

Application generates:

```text
DICTATION_ACCESSED
```

---

## Event Processing

```text
Audit gRPC API
      │
      ▼
Audit Service
      │
      ▼
Internal Queue
```

Validation occurs before persistence.

Invalid events may be rejected.

---

## Event Persistence

```text
Internal Queue
      │
      ▼
Cosmos DB
```

A single document is created for each audit event.

No duplicate persistence process exists.

---

## Report Generation

```text
Report Request
      │
      ▼
Reporting API
      │
      ▼
Cosmos DB Query
      │
      ▼
Audit Report
```

---

# 7. Audit Data Model

The platform uses a lightweight audit structure.

## Canonical Event

```json
{
  "id": "GUID",
  "ts": "2026-09-23T15:30:22Z",
  "tenantId": "TENANT001",
  "app": "DigiWeb",
  "event": "DICTATION_ACCESSED",
  "userId": "USR123",
  "targetId": "DICT456",
  "data": {}
}
```

---

## Required Fields

```text
id
ts
tenantId
app
event
userId
```

---

## Optional Fields

```text
targetId
data
```

---

# 8. Supported Audit Events

Version 1 supports the following events:

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

The event catalog is intentionally minimal.

Only events required to satisfy customer reporting requirements are included.

---

# 9. Security Model

## Application Authentication

Applications authenticate with the Audit Platform.

Examples:

```text
DigiWeb
DigiConsole
Future Applications
```

Users do not authenticate directly to the Audit Platform.

---

## Authorization

Only trusted applications are authorized to submit audit events.

The Audit Service validates caller permissions before accepting requests.

---

## Tenant Isolation

All audit events contain:

```text
tenantId
```

Reports and queries must be restricted to the authorized tenant context.

---

# 10. Scalability

The platform is designed to scale horizontally.

Scaling dimensions include:

- Number of applications
- Number of tenants
- Event volume
- Report volume

---

## Cosmos DB Partition Strategy

Recommended partition key:

```text
tenantId
```

Benefits:

- Logical tenant isolation
- Efficient tenant-specific queries
- Better scalability
- Predictable throughput consumption

---

# 11. Availability and Reliability

The architecture favors reliability through:

- Asynchronous processing
- Internal queue buffering
- Retry capabilities
- Decoupled services

Failures in audit persistence must not interrupt business operations.

---

# 12. Reporting Architecture

Version 1 supports:

- Access Audit Report
- Detailed Transcription Report
- Dictation Status History Report
- User Activity Audit Report
- Time Analysis Report
- True Productivity Report

All reports are generated directly from Cosmos DB.

---

# 13. Future Evolution

The architecture is intentionally designed to evolve.

Future enhancements may include:

## Additional Audit Events

Examples:

```text
USER_CREATED
ROLE_CHANGED
PERMISSION_CHANGED
AUDIO_ACCESSED
REPORT_EXPORTED
```

---

## Microsoft Fabric Integration

Potential future capabilities:

- Historical analytics
- Enterprise dashboards
- Operational KPIs
- Trend analysis
- Cross-application reporting
- Executive reporting

Microsoft Fabric is not required for Version 1.

---

## Long-Term Retention Strategy

A future archival strategy may be introduced if:

- Audit volume significantly increases
- Storage costs justify optimization
- Regulatory requirements evolve

The initial architecture stores all audit events exclusively in Cosmos DB.

---

# 14. Architecture Decisions

| Decision | Result |
|-----------|---------|
| Audit platform model | Centralized |
| Communication protocol | gRPC |
| Processing model | Asynchronous |
| Source of truth | Azure Cosmos DB |
| Multi-tenant support | Yes |
| Event model | Minimalist |
| Reporting source | Cosmos DB |
| Microsoft Fabric required for V1 | No |
| Direct database access from applications | Not allowed |

---

# 15. Summary

The DigiWeb Audit Platform architecture provides:

- A centralized audit service
- A lightweight event model
- Azure Cosmos DB as the single source of truth
- Multi-tenant support
- Asynchronous processing
- Support for customer-required audit reports
- Future compatibility with DigiConsole and additional applications

The architecture intentionally prioritizes simplicity, maintainability, and business value while remaining extensible for future analytics and reporting requirements.
