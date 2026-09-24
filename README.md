# DigiWeb Audit Trail

Centralized audit system for DigiWeb and DigiConsole.

The objective is to provide reliable traceability of user and system actions, support audit reporting, and prepare the platform for future compliance and analytics capabilities.

## Status

**Current phase: MVP architecture design**

| Area | Status |
|---|---|
| Functional requirements | ✅ Defined |
| Event model | ✅ Defined |
| MVP event catalog | ✅ Defined |
| MVP reports | ✅ Defined |
| Technical architecture | 🔄 To be validated |
| Implementation | ⏳ Planned |
| Long-term retention | 📌 Future phase |
| Microsoft Fabric | 📌 Future phase |

## MVP objectives

The MVP must reliably answer the following questions:

1. Who logged in or logged out?
2. Who accessed a dictation?
3. Who changed a dictation status?
4. Who modified a transcription?
5. Who changed a transcription status?
6. Who purged a dictation or audio recording?
7. How much time did a user spend working on a dictation?
8. Which reports were executed?

## MVP scope

The MVP is based on the following events:

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

- centralized in a single audit service;
- associated with a tenant;
- immutable after creation;
- available to authorized reporting features;
- stored in Azure Cosmos DB;
- published by applications through a gRPC API.

## Target architecture

```text
DigiWeb / DigiConsole
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
          │
          ▼
   Reporting API / Reports
```

### Architecture principles

- **Single source of truth**: Azure Cosmos DB.
- **Centralized publication**: applications do not access the database directly.
- **Asynchronous processing**: audit recording must not noticeably slow down business operations.
- **Immutability**: a recorded event cannot be modified.
- **Multi-tenant isolation**: a tenant can access only its own events.
- **Contract validation**: events must comply with the defined model and catalog.
- **Technical traceability**: events support correlation across related operations.

## Reports

The following reports are defined for the MVP and will be available once the reporting layer is implemented and validated:

- **Access Audit Report**  
  Users who accessed a dictation.

- **Detailed Transcription Report**  
  History of transcription modifications and status changes.

- **Dictation Status History Report**  
  History of dictation status changes.

- **User Activity Audit Report**  
  History of logins, logouts, and key user actions.

- **Time Analysis Report**  
  Work time recorded per user and per dictation.

- **True Productivity Report**  
  Productivity indicators calculated from activity events.

- **Report Usage Report**  
  History of executed reports.

CSV and Excel exports may be supported by the reporting layer.

## Documentation structure

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

### Documents

- [Functional requirements](./docs/01-requirements.md)
- [Event model](./docs/02-event-model.md)
- [Event catalog](./docs/03-event-catalog.md)
- [Report specifications](./docs/04-reports.md)
- [Technical architecture](./docs/05-architecture.md)
- [Roadmap](./docs/06-roadmap.md)

## Out of scope for the MVP

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

## Important rules

### No direct storage access

Applications must publish events through the Audit Service. They must never access Azure Cosmos DB directly.

### No unnecessary clinical data

Audit events must contain only the information required for traceability and reporting. Clinical content and full transcription content must not be stored in the audit trail.

### Immutability

Events are created once. Any functional correction must produce a new event rather than modify an existing event.

### Versioning

The event contract must be versioned to allow the system to evolve without breaking existing applications.

## Next steps

1. Validate the scope of the nine MVP events.
2. Validate the canonical `AuditEvent` model.
3. Validate the gRPC contract.
4. Define Azure Cosmos DB partitioning and indexes.
5. Define security and tenant-isolation rules.
6. Implement the Audit Service.
7. Implement the first reports.
8. Perform a performance assessment.
9. Define the long-term retention and archival strategy outside the MVP.
