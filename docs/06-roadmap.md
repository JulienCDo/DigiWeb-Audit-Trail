# DigiWeb Audit Trail Roadmap

## 1. Overview

This roadmap defines the delivery plan for the DigiWeb Audit Platform MVP and its future evolution.

The roadmap follows a staged approach:

1. Define and validate the architecture.
2. Implement reliable event ingestion.
3. Implement event persistence.
4. Deliver operational reporting.
5. Deliver productivity reporting.
6. Prepare future analytics capabilities.

The MVP focuses on reliability, traceability, organization isolation and operational reporting.

Long-term analytics, Microsoft Fabric and advanced reporting are intentionally excluded from the MVP.

---

## 2. MVP Goal

The MVP must provide enough audit traceability to answer the key operational and compliance questions of DigiWeb and DigiConsole.

The MVP must deliver:

- reliable event collection;
- centralized storage;
- organization isolation;
- immutable audit records;
- operational reporting;
- productivity reporting support;
- CSV and Excel exports;
- idempotent event processing.

---

# 3. Phase 1 — Architecture and Design

## Objective

Define and validate the audit platform architecture.

## Scope

- define requirements;
- define the event model;
- define the event catalog;
- define reporting requirements;
- define the gRPC contract;
- define authentication strategy;
- define identity extraction strategy;
- define Azure Storage Queue architecture;
- define Azure Cosmos DB architecture;
- define idempotency behavior;
- define observability requirements.

## Deliverables

- approved requirements;
- approved event model;
- approved event catalog;
- approved reporting specifications;
- approved architecture;
- approved queue architecture;
- approved Cosmos DB design.

## Status

✅ Completed

---

# 4. Phase 2 — Event Ingestion

## Objective

Enable reliable audit event publication.

## Scope

- gRPC ingestion endpoint;
- request validation;
- AuthenticationToken validation;
- OrganizationId extraction;
- GroupId extraction;
- UserId extraction;
- queue publication;
- SynnefoAPIStatus responses.

## Deliverables

- CreateAuditEvent endpoint;
- validation framework;
- queue publisher;
- accepted/rejected responses;
- Application Insights telemetry.

## Exit Criteria

The phase is complete when:

- applications can publish events;
- invalid events are rejected;
- valid events are accepted;
- accepted events are published to Azure Storage Queue;
- identity extraction functions correctly.

## Status

✅ Completed

---

# 5. Phase 3 — Persistence

## Objective

Persist accepted events to Azure Cosmos DB.

## Scope

- Azure Cosmos DB implementation;
- queue processing;
- document persistence;
- idempotency handling;
- duplicate detection;
- retry handling;
- queue message deletion.

## Deliverables

- CosmosAuditEventRepository;
- AuditQueueProcessor;
- CosmosInitializationService;
- duplicate detection;

## Exit Criteria

The phase is complete when:

- queue messages are persisted;
- events appear in Cosmos DB;
- duplicate deliveries are detected;
- HTTP 409 conflicts are handled correctly;
- queue messages are deleted after successful processing.

## Status

✅ Completed

---

# 6. Phase 4 — Event Querying

## Objective

Provide access to persisted audit data.

## Scope

- event search APIs;
- organization-scoped filtering;
- pagination;
- sorting;
- query optimization.

## Deliverables

- GetAuditEvents;
- GetUserAuditEvents;
- GetDictationAuditEvents;
- continuation token support.

## Exit Criteria

The phase is complete when:

- queries return correct results;
- organization isolation is enforced;
- query performance is acceptable.

## Status

⏳ Planned

---

# 7. Phase 5 — Operational Audit Reporting

## Objective

Deliver the first operational reports.

## Reports

- Access Audit Report;
- Detailed Transcription Report;
- Dictation Status History Report;
- User Activity Audit Report.

## Deliverables

- reporting services;
- authorization rules;
- CSV export;
- Excel export.

## Exit Criteria

The phase is complete when:

- all reports are functional;
- exports are available;
- organization isolation is validated.

## Status

⏳ Planned

---

# 8. Phase 6 — Productivity Reporting

## Objective

Provide productivity and work-time analysis.

## Reports

- Time Analysis Report;
- True Productivity Report;
- Report Usage Report.

## Deliverables

- duration calculations;
- productivity metrics;
- report filtering enhancements.

## Exit Criteria

The phase is complete when:

- productivity calculations are reproducible;
- duplicate events do not inflate metrics;
- metrics are accepted by stakeholders.

## Status

⏳ Planned

---

# 9. Phase 7 — Production Hardening

## Objective

Prepare the platform for production deployment.

## Scope

- Application Insights;
- operational dashboards;
- retry validation;
- poison message handling;
- backup validation;
- disaster recovery review;
- performance validation.

## Deliverables

- operational monitoring;
- runbooks;
- alerting;
- production readiness checklist.

## Exit Criteria

The phase is complete when:

- monitoring is operational;
- failures are observable;
- recovery procedures are documented;
- performance targets are met.

## Status

⏳ Planned

---

# Production Backlog

## Security

- Replace Cosmos DB connection strings with Managed Identity.
- Introduce Azure RBAC for Cosmos DB access.

## Architecture

- Move AuditQueueProcessor into a dedicated worker service.
- Allow independent scaling of ingestion and persistence.

## Reliability

- Add dead-letter queue support.
- Improve poison message handling.

## Observability

- Application Insights integration.
- Queue depth monitoring.
- Cosmos Request Unit monitoring.
- Processing latency metrics.

---

# 10. Future Extensions

Future versions may include:

- role auditing;
- permission auditing;
- user administration auditing;
- audio auditing;
- report export auditing;
- AI auditing;
- speech recognition auditing;
- Microsoft Fabric integration;
- Power BI integration;
- anomaly detection;
- advanced analytics;
- long-term archival.

These features are outside MVP scope.

---

# 11. Dependencies

Current architecture assumes:

- gRPC transport;
- Azure Storage Queue;
- Azure Cosmos DB;
- OrganizationId-based isolation;
- AuthenticationToken validation at the gRPC boundary;
- asynchronous processing.

---

# 12. Delivery Principles

The roadmap follows these principles:

- keep the MVP narrow and valuable;
- prioritize traceability over advanced analytics;
- preserve immutable audit records;
- prioritize organization isolation;
- use asynchronous processing;
- maintain contract compatibility;
- design for future extensibility.

---

# 13. Current Status

| Area | Status |
|--------|--------|
| Requirements | ✅ Complete |
| Event Model | ✅ Complete |
| Event Catalog | ✅ Complete |
| Reports | ✅ Complete |
| Architecture | ✅ Complete |
| Authentication Strategy | ✅ Complete |
| Queue Architecture | ✅ Complete |
| Cosmos DB Design | ✅ Complete |
| Event Ingestion | ✅ Complete |
| Persistence | ✅ Complete |
| Reporting | ⏳ Planned |
| Production Hardening | ⏳ Planned |
| Microsoft Fabric | 📌 Future |

---

# 14. Definition of Done

The MVP is complete when:

- events can be published through gRPC;
- AuthenticationToken is validated;
- OrganizationId, GroupId and UserId are extracted successfully;
- events are published to Azure Storage Queue;
- events are persisted to Azure Cosmos DB;
- duplicate deliveries are handled safely;
- organization isolation is enforced;
- operational reports are available;
- CSV exports are available;
- Excel exports are available;
- operational logging is available;
- performance is acceptable.

---

# 15. Current Priorities

1. Validate end-to-end processing.
2. Add Application Insights observability.
3. Implement poison message handling.
4. Implement audit search APIs.
5. Implement the Access Audit Report.
6. Validate Cosmos query patterns.

---

# 16. Roadmap Summary

The architecture is validated.

The platform is now in the implementation phase.

The end-to-end ingestion and persistence path is implemented.

Current focus is shifting toward:

- event querying;
- operational reporting;
- observability;
- production hardening.

