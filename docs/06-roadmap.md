# DigiWeb Audit Trail Roadmap

## 1. Overview

This roadmap defines the phased delivery plan for the DigiWeb Audit Trail MVP and its future evolution.

The roadmap follows a staged approach:

- first, centralize audit event capture and storage;
- then, support operational reporting and investigations;
- later, expand to productivity analytics and advanced analytics.

The MVP focuses on reliability, traceability, and reporting value. It deliberately excludes long-term analytics and advanced data warehousing work.

## 2. MVP goal

The MVP must provide enough audit traceability to answer the key operational and compliance questions of DigiWeb and DigiConsole.

The MVP must deliver:

- reliable event collection;
- centralized storage;
- tenant isolation;
- immutable audit records;
- operational report generation;
- productivity measurement support;
- reporting export capabilities.

## 3. Phase 1 — Foundation

### Objective

Establish the foundation for all future audit features.

### Scope

- define the canonical event model;
- define the event catalog;
- define the gRPC event ingestion contract;
- define the Audit Service responsibilities;
- define application authentication and authorization rules;
- define tenant isolation policies;
- define idempotency rules;
- define storage strategy in Azure Cosmos DB;
- define event validation and rejection handling.

### MVP events in Phase 1

- `LOGIN`
- `LOGOUT`
- `DICTATION_ACCESSED`
- `DICTATION_STATUS_CHANGED`
- `DICTATION_PURGED`
- `TRANSCRIPTION_MODIFIED`
- `TRANSCRIPTION_STATUS_CHANGED`
- `WORK_SESSION`
- `REPORT_EXECUTED`

### Deliverables

- approved event model;
- approved event catalog;
- approved gRPC contract;
- approved storage design;
- validation rules;
- initial observability design.

### Exit criteria

The project can move to Phase 2 only when:

- the event model is approved;
- the event catalog is approved;
- the gRPC contract is approved;
- the storage strategy is approved;
- tenant isolation is defined;
- validation and rejection rules are defined.

## 4. Phase 2 — Core audit operations

### Objective

Enable the first operational reporting and investigation capabilities.

### Scope

- events are published through the Audit Service;
- Azure Cosmos DB stores accepted events;
- reporting layer supports event searches;
- report generation is available to authorized users;
- all operations are tenant-aware;
- report exports are available in CSV and Excel.

### Reports

- Access Audit Report
- Detailed Transcription Report
- Dictation Status History Report
- User Activity Audit Report

### Deliverables

- operational reporting APIs;
- event search functionality;
- report authorization controls;
- CSV and Excel exports;
- performance testing for event ingestion and report generation.

### Exit criteria

The phase is complete when:

- reports can be generated for authorized users only;
- event search is stable and tenant-aware;
- exports are working;
- performance is acceptable;
- the audit trail is operational for the approved use cases.

## 5. Phase 3 — Productivity and analytics support

### Objective

Support operational productivity and time measurement.

### Scope

- `WORK_SESSION` events are fully implemented;
- productivity calculations are standardized;
- time tracking is validated against operational practices;
- productivity reports are generated reliably;
- incomplete or invalid sessions are clearly handled.

### Reports

- Time Analysis Report
- True Productivity Report
- Report Usage Report

### Deliverables

- validated work session model;
- productivity calculations;
- performance metrics;
- reporting and filtering enhancements;
- calculated metric governance.

### Exit criteria

This phase is complete when:

- work session timing is reliable;
- productivity numbers are reproducible;
- calculation rules are documented;
- the reports are accepted by operational stakeholders.

## 6. Phase 4 — Future extensions

### Objective

Prepare for future product enhancements without disrupting the MVP.

### Scope

- detailed role and permission auditing;
- user administration auditing;
- detailed audio access auditing;
- report export auditing;
- AI assistance auditing;
- speech recognition auditing;
- long-term retention governance;
- Microsoft Fabric ingestion;
- Power BI or equivalent reporting;
- anomaly detection;
- advanced analytics.

### Notes

These features are not part of the MVP and should be implemented only after:

- retention rules are defined;
- privacy impact is assessed;
- business value is validated;
- operational stakeholders confirm the requirements;
- the cost and complexity of the solution are understood.

## 7. Dependencies

The roadmap depends on the following decisions:

- validated event catalog;
- validated event model;
- validated storage strategy;
- approval of the gRPC contract;
- selection of the queue technology;
- definition of Azure Cosmos DB partitioning and indexing;
- approval of authentication and authorization standards;
- approval of retention requirements;
- definition of performance and throughput requirements.

## 8. Delivery principles

The roadmap follows these principles:

- keep the MVP narrow and valuable;
- avoid building analytical complexity before operational traceability;
- separate transactional audit storage from future analytics workloads;
- keep the event model stable and versioned;
- prioritize reproducibility and auditability over feature breadth;
- design for future extension without breaking the MVP.

## 9. Current status

### Status summary

| Area | Status |
|---|---|
| Event model | ✅ Defined |
| Event catalog | ✅ Defined |
| Requirements | ✅ Defined |
| Reports | ✅ Defined |
| Architecture | 🔄 In progress |
| MVP implementation | ⏳ Planned |
| Long-term retention | 📌 Future |
| Microsoft Fabric | 📌 Future |

## 10. Definition of done

The project is ready for implementation when:

- the MVP event catalog is approved;
- the event model is approved;
- the requirements are approved;
- the report definitions are approved;
- the architecture is approved;
- the gRPC contract is approved;
- the queue and persistence technologies are selected;
- the retention policy is defined;
- operational stakeholders approve the MVP scope;
- performance and security reviews are completed.

## 11. Future watchlist

The following items deserve continued attention:

- retention and archival regulation changes;
- privacy law and contractual obligations;
- new operational reporting needs;
- emerging Azure platform capabilities;
- changing customer expectations around reporting and traceability;
- future application integrations;
- growth in audit event volume.

## 12. Roadmap summary

The project is intentionally designed to be simple and focused.

The success of the MVP will be measured by the ability to provide trustworthy audit answers to the organization, without prematurely building a long-term analytics platform.

The next major milestone is the approval of the architecture and the event contract before implementation begins.
