# DigiWeb Audit Platform

Centralized audit and reporting platform for DigiWeb, DigiConsole, and future applications.

---

# Overview

The DigiWeb Audit Platform provides a centralized, multi-tenant audit solution designed to:

- Track user activity
- Support customer audit requirements
- Enable compliance investigations
- Provide operational reporting
- Support future cross-application auditing
- Minimize storage and operational complexity

The platform uses:

- gRPC for communication
- Asynchronous event processing
- Azure Cosmos DB as the single source of truth
- A lightweight business-oriented audit model

---

# Architecture

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

# Key Architectural Decisions

| Decision | Selection |
|-----------|-----------|
| Audit Platform | Centralized |
| Communication | gRPC |
| Processing | Asynchronous |
| Storage | Azure Cosmos DB |
| Source of Truth | Cosmos DB |
| Multi-Tenant Support | Yes |
| Reporting Source | Cosmos DB |
| Microsoft Fabric | Future Phase |
| Direct Database Access | Not Allowed |

---

# Audit Event Catalog

Version 1 intentionally contains only the events required to satisfy customer requirements.

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

---

# Audit Reports

Version 1 supports:

- Access Audit Report
- Detailed Transcription Report
- Dictation Status History Report
- User Activity Audit Report
- Time Analysis Report
- True Productivity Report
- Report Usage Report

---

# Document Structure

## Master Planning

### [DigiWeb-Audit-Master-Roadmap.md](./DigiWeb-Audit-Master-Roadmap.md)

Primary planning document.

Contains:

- Project vision
- Architecture roadmap
- Delivery phases
- MVP scope
- Future evolution strategy

---

## Requirements

### [DigiWeb-Audit-Trail-and-Reporting-Requirements.md](./DigiWeb-Audit-Trail-and-Reporting-Requirements.md)

Defines:

- Customer requirements
- Audit objectives
- Reporting requirements
- Compliance expectations
- Success criteria

---

## Architecture

### [DigiWeb-Audit-Trail-Architecture.md](./DigiWeb-Audit-Trail-Architecture.md)

Defines:

- Target architecture
- System components
- Event flow
- Security model
- Multi-tenant strategy
- Scalability considerations

---

## Data Model

### [DigiWeb-Audit-Data-Model.md](./DigiWeb-Audit-Data-Model.md)

Defines:

- Canonical audit event structure
- Required fields
- Optional fields
- Multi-tenant design
- Cosmos DB data model

---

## Event Catalog

### [DigiWeb-Audit-Event-Catalog.md](./DigiWeb-Audit-Event-Catalog.md)

Defines:

- Supported audit events
- Event purposes
- Event examples
- Customer requirement mapping

---

## Coverage Analysis

### [DigiWeb-Audit-Coverage-Matrix.md](./DigiWeb-Audit-Coverage-Matrix.md)

Defines:

- Functional coverage
- Event coverage
- Report coverage
- Gap analysis
- Requirement traceability

---

## Prioritization

### [DigiWeb-Audit-Prioritization.md](./DigiWeb-Audit-Prioritization.md)

Defines:

- Delivery priorities
- MVP scope
- Future phases
- Implementation strategy

---

## Reporting Specifications

### [DigiWeb-Audit-Reports-Specifications.md](./DigiWeb-Audit-Reports-Specifications.md)

Defines:

- Supported audit reports
- Filters
- Report columns
- Data sources
- Business questions answered

---

# Current Status

## Analysis

- [x] Customer requirements analyzed
- [x] Audit scope defined
- [x] Report inventory completed

## Architecture

- [x] Architecture defined
- [x] Storage strategy defined
- [x] Event catalog simplified
- [x] Multi-tenant approach validated
- [x] Cosmos DB selected as source of truth

## Design

- [x] Data model completed
- [x] Coverage matrix completed
- [x] Prioritization completed
- [x] Report specifications completed

## Next Phase

- [ ] Detailed technical design
- [ ] gRPC contract definition
- [ ] Cosmos DB container design
- [ ] Development planning
- [ ] MVP implementation

---

# MVP Success Criteria

Version 1 is considered successful when the platform can reliably answer the following questions:

1. Who logged in?
2. Who logged out?
3. Who accessed a dictation?
4. Who modified a transcription?
5. Who changed a dictation status?
6. Who changed a transcription status?
7. How much time was spent transcribing?
8. Who purged a dictation recording?
9. Who executed a report?

---

# Future Evolution

Potential future enhancements include:

- Microsoft Fabric integration
- Advanced analytics
- Cross-application reporting
- Security administration auditing
- Audio access auditing
- AI usage auditing
- Long-term archival strategy

These capabilities are intentionally outside the Version 1 scope.

---

# Summary

The DigiWeb Audit Platform delivers a centralized and reusable audit solution built around:

- A lightweight event model
- Azure Cosmos DB
- Multi-tenant support
- gRPC integration
- Asynchronous processing
- Customer-focused reporting

The current architecture prioritizes simplicity, maintainability, scalability, and rapid delivery while keeping a clear path for future growth.
