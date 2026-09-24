# DigiWeb Audit Platform
# Master Roadmap

Version: 1.0  
Phase: Architecture

---

# 1. Purpose

This document defines the overall roadmap for the DigiWeb Audit Platform.

It serves as the master planning document and provides:

- Strategic objectives
- Architecture direction
- Delivery phases
- MVP scope
- Future evolution path
- Traceability across all audit-related documents

The platform is intended to become a centralized audit solution reusable across DigiWeb, DigiConsole, and future applications.

---

# 2. Project Vision

Create a centralized audit platform capable of:

- Recording user activity
- Supporting customer audit requirements
- Providing operational reporting
- Supporting compliance investigations
- Scaling across multiple applications
- Supporting multi-tenant deployments

The platform must remain simple, maintainable, and cost-efficient.

---

# 3. Architecture Vision

## Target Architecture

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

## Key Architectural Decisions

| Decision | Selection |
|-----------|-----------|
| Audit Model | Centralized Platform |
| Communication Protocol | gRPC |
| Processing Model | Asynchronous |
| Storage Platform | Azure Cosmos DB |
| Source of Truth | Cosmos DB |
| Multi-Tenant Support | Yes |
| Reporting Source | Cosmos DB |
| Microsoft Fabric Required for V1 | No |
| Application Direct DB Access | Not Allowed |

---

# 4. Business Objectives

The platform must answer the following questions reliably:

1. Who logged in?
2. Who logged out?
3. Who accessed a dictation?
4. Who modified a transcription?
5. Who changed a dictation status?
6. Who changed a transcription status?
7. How much time was spent transcribing?
8. Who permanently purged a dictation recording?
9. Who executed a report?

These questions define the minimum viable audit platform.

---

# 5. Audit Event Catalog

Version 1 includes only business-critical events.

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

The catalog intentionally excludes low-value technical and user-interface events.

---

# 6. Audit Data Model

The platform uses a lightweight audit structure.

## Required Fields

```text
id
ts
tenantId
app
event
userId
```

## Optional Fields

```text
targetId
data
```

## Example

```json
{
  "id": "GUID",
  "ts": "2026-09-23T15:30:22Z",
  "tenantId": "TENANT001",
  "app": "DigiWeb",
  "event": "DICTATION_ACCESSED",
  "userId": "USR123",
  "targetId": "DICT001"
}
```

---

# 7. Delivery Strategy

The project will be delivered incrementally.

Each phase should provide demonstrable business value.

Customer demonstrations should occur regularly throughout development.

---

# 8. Phase 1 - Platform Foundation

## Objective

Build the audit platform foundation.

---

## Deliverables

### Architecture

- Audit gRPC API
- Audit Service
- Internal Queue
- Azure Cosmos DB integration
- Multi-tenant support

---

### Core Infrastructure

- Authentication between applications and Audit Platform
- Event validation
- Event persistence
- Error handling
- Operational logging

---

### Audit Events

```text
LOGIN
LOGOUT
DICTATION_ACCESSED
```

---

### Report

```text
Access Audit Report
```

---

## Success Criteria

Able to answer:

> Who accessed a dictation?

---

# 9. Phase 2 - Operational Auditing

## Objective

Support auditability of core transcription workflows.

---

## Audit Events

```text
TRANSCRIPTION_MODIFIED
TRANSCRIPTION_STATUS_CHANGED
DICTATION_STATUS_CHANGED
```

---

## Reports

```text
Detailed Transcription Report
Dictation Status History Report
```

---

## Success Criteria

Able to answer:

- Who modified a transcription?
- Who changed a status?
- What workflow transitions occurred?

---

# 10. Phase 3 - Productivity Reporting

## Objective

Provide productivity and effort tracking.

---

## Audit Events

```text
WORK_SESSION
```

---

## Reports

```text
Time Analysis Report
True Productivity Report
```

---

## Success Criteria

Able to answer:

- How much time was spent transcribing?
- Which transcriptionists worked on a dictation?
- What productivity metrics can be calculated?

---

# 11. Phase 4 - Administrative Activity

## Objective

Complete customer-required user activity auditing.

---

## Audit Events

```text
DICTATION_PURGED
REPORT_EXECUTED
```

---

## Reports

```text
User Activity Audit Report
Report Usage Report
```

---

## Success Criteria

Able to answer:

- Who purged a dictation recording?
- Who executed a report?

---

# 12. MVP Scope

## Included

### Platform

- Audit gRPC API
- Audit Service
- Internal Queue
- Azure Cosmos DB
- Multi-tenant support

### Event Catalog

- LOGIN
- LOGOUT
- DICTATION_ACCESSED
- DICTATION_STATUS_CHANGED
- DICTATION_PURGED
- TRANSCRIPTION_MODIFIED
- TRANSCRIPTION_STATUS_CHANGED
- WORK_SESSION
- REPORT_EXECUTED

### Reports

- Access Audit Report
- Detailed Transcription Report
- Dictation Status History Report
- User Activity Audit Report
- Time Analysis Report
- True Productivity Report
- Report Usage Report

---

# 13. Out of Scope (Version 1)

## Events

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
```

---

## Capabilities

```text
Microsoft Fabric
Advanced Analytics
Cross-Application Reporting
Long-Term Archiving
AI Usage Analytics
```

---

# 14. Phase 2 Evolution Opportunities

The following capabilities may be considered after production rollout.

---

## Security Administration Auditing

Potential events:

```text
USER_CREATED
USER_DISABLED
ROLE_CHANGED
PERMISSION_CHANGED
```

---

## Audio Access Auditing

Potential events:

```text
AUDIO_ACCESSED
AUDIO_DOWNLOADED
```

---

## Audit Export Tracking

Potential events:

```text
REPORT_EXPORTED
```

---

## AI Usage Auditing

Potential events:

```text
AI_ASSISTANCE_REQUESTED
AI_ASSISTANCE_COMPLETED
AI_ASSISTANCE_FAILED
```

---

# 15. Analytics Roadmap

## Future Analytics Platform

Potential future integration:

```text
Microsoft Fabric
```

Potential use cases:

- Executive dashboards
- Historical trends
- Productivity analytics
- Cross-application reporting
- Compliance dashboards
- Anomaly detection

Fabric is explicitly outside the Version 1 scope.

---

# 16. Storage Roadmap

## Version 1

```text
Azure Cosmos DB
```

Stores:

- All audit events
- All reporting data

Acts as:

- Operational datastore
- Reporting datastore
- Source of truth

---

## Future Considerations

Future evaluation may include:

- Long-term archival
- Cost optimization strategies
- Historical analytics stores
- Data lifecycle policies

No archival mechanism is planned for Version 1.

---

# 17. Success Metrics

The audit platform will be considered successful when:

- Audit events are generated consistently
- Reports provide accurate results
- Multi-tenant isolation is enforced
- Business transactions remain non-blocking
- Customer audit requirements are satisfied
- Platform adoption is achieved across DigiWeb and future applications

---

# 18. Project Status

## Completed

- [x] Audit requirements analysis
- [x] Audit data model definition
- [x] Audit event catalog definition
- [x] Coverage matrix definition
- [x] Prioritization completed
- [x] Report specifications completed
- [x] Architecture definition completed

---

## Next Steps

- [ ] Detailed technical design
- [ ] gRPC contract definition
- [ ] Cosmos DB schema design
- [ ] Development planning
- [ ] Implementation
- [ ] Testing
- [ ] Customer demonstrations
- [ ] Production rollout

---

# 19. Summary

The DigiWeb Audit Platform delivers a centralized, multi-tenant audit solution built around:

- A minimal audit event model
- Azure Cosmos DB as the single source of truth
- gRPC-based integration
- Asynchronous processing
- Customer-focused audit reporting

The Version 1 roadmap intentionally prioritizes simplicity, business value, and rapid delivery while preserving a clear path for future analytics, administration auditing, and enterprise-scale reporting.
