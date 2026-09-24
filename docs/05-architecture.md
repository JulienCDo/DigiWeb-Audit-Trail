# DigiWeb Audit Trail Architecture

## 1. Purpose

This document defines the target architecture for the DigiWeb Audit Trail MVP.

The architecture must provide:

- centralized audit event ingestion;
- reliable event validation;
- asynchronous processing;
- immutable event storage;
- multi-tenant isolation;
- authorized event search;
- audit report generation;
- future extensibility.

The architecture supports DigiWeb, DigiConsole, and approved future applications.

## 2. Architecture principles

### Single source of truth

Azure Cosmos DB is the source of truth for accepted audit events during the MVP.

### Centralized ownership

Applications must publish events through the Audit Service. They must not write directly to Azure Cosmos DB.

### Asynchronous processing

Audit processing should be asynchronous to minimize the impact on business operations.

### Immutability

Accepted events are append-only and cannot be modified through normal application APIs.

### Multi-tenant isolation

Every event belongs to exactly one tenant. Tenant isolation must be enforced during ingestion, storage, search, and reporting.

### Contract-first integration

All producers must use the versioned event contract and the approved event catalog.

### Least privilege

Each component must have only the permissions required to perform its responsibilities.

### Privacy by design

Audit events must contain only the data required for traceability, investigation, and reporting.

## 3. Target architecture

```text
┌──────────────────────┐
│ DigiWeb              │
│ DigiConsole          │
│ Future Applications  │
└──────────┬───────────┘
           │
           │ Authenticated gRPC
           ▼
┌──────────────────────┐
│ Audit gRPC API       │
│ Ingestion endpoint   │
└──────────┬───────────┘
           │
           ▼
┌──────────────────────┐
│ Audit Service        │
│ - Authentication     │
│ - Validation         │
│ - Tenant checks      │
│ - Idempotency        │
└──────────┬───────────┘
           │
           │ Durable asynchronous processing
           ▼
┌──────────────────────┐
│ Internal Queue       │
│ Retry and buffering  │
└──────────┬───────────┘
           │
           ▼
┌──────────────────────┐
│ Audit Persistence    │
│ Azure Cosmos DB      │
└──────────┬───────────┘
           │
           ▼
┌──────────────────────┐
│ Reporting Layer      │
│ Search and reports   │
└──────────┬───────────┘
           │
           ▼
┌──────────────────────┐
│ Authorized consumers │
│ UI, API, exports     │
└──────────────────────┘
```

## 4. Components

## 4.1 Audit producers

Audit producers are applications or services that generate audit events.

Initial producers:

- DigiWeb;
- DigiConsole.

Future producers may be added after approval.

Producer responsibilities:

- identify the business action;
- create an event using the canonical event model;
- provide the correct tenant context;
- provide the actor and target when applicable;
- generate a unique event identifier;
- provide a correlation identifier when applicable;
- submit the event through the Audit gRPC API;
- retry transient failures safely.

Producers must not:

- write directly to Cosmos DB;
- modify an accepted event;
- generate event types outside the approved catalog;
- include passwords, tokens, clinical content, or full transcription content.

## 4.2 Audit gRPC API

The Audit gRPC API is the authenticated entry point for event publication.

Responsibilities:

- authenticate the calling application;
- authorize the application for the requested tenant;
- receive audit events;
- validate the request envelope;
- forward valid events to the Audit Service;
- return an acknowledgment or a clear error;
- support idempotent retries.

The API must not expose direct database access.

### Suggested operations

```protobuf
service AuditService {
  rpc PublishEvent(PublishEventRequest) returns (PublishEventResponse);
  rpc PublishEvents(PublishEventsRequest) returns (PublishEventsResponse);
}
```

Batch publication may be supported to reduce network overhead, but each event must be validated and deduplicated independently.

### Publication response

The response should distinguish at least:

```text
ACCEPTED
DUPLICATE
REJECTED
RETRYABLE_FAILURE
```

A producer must retry only retryable failures.

## 4.3 Audit Service

The Audit Service owns the audit event lifecycle.

Responsibilities:

- validate event contracts;
- validate event-specific data;
- validate tenant context;
- enforce application authorization;
- enforce idempotency;
- enqueue accepted events;
- coordinate persistence;
- expose operational metrics;
- ensure that events are not modified after acceptance.

The Audit Service is the only component allowed to publish events to the audit persistence layer.

## 4.4 Internal queue

The internal queue decouples event publication from event persistence.

Responsibilities:

- buffer events during temporary storage issues;
- support asynchronous processing;
- support retries;
- preserve the event identifier;
- avoid event loss;
- expose queue depth and processing latency;
- route permanently failed messages to a dead-letter mechanism.

The queue implementation is intentionally technology-agnostic at this stage. The selected technology must provide:

- durable delivery;
- retry policies;
- dead-letter handling;
- monitoring;
- encryption;
- access control;
- appropriate throughput.

### Processing rules

- transient failures must be retried;
- retries must be idempotent;
- permanently invalid events must not be retried indefinitely;
- failed messages must be retained for investigation;
- queue processing must preserve the original `timestampUtc`;
- queue processing time must not replace the business event timestamp.

## 4.5 Azure Cosmos DB

Azure Cosmos DB stores accepted audit events for the MVP.

Responsibilities:

- durable event storage;
- tenant-scoped queries;
- event retrieval by identifier;
- filtering by supported reporting fields;
- support for operational reporting workloads.

Recommended document fields:

```text
id
eventVersion
timestampUtc
tenantId
application
eventType
category
outcome
severity
actor
target
sessionId
correlationId
data
```

### Partitioning

The initial partition key recommendation is:

```text
/tenantId
```

This recommendation must be validated against:

- expected event volume;
- number of tenants;
- tenant size distribution;
- report query patterns;
- cross-tenant administrative requirements;
- retention and archival requirements.

If a tenant can produce a very high event volume, a hierarchical or composite strategy may be considered. Any alternative must preserve tenant isolation.

### Indexing

The indexing policy should support the primary query patterns:

- date range;
- event type;
- actor identifier;
- target identifier;
- category;
- outcome;
- severity;
- correlation identifier.

Indexing must be reviewed against:

- write throughput;
- storage cost;
- report latency;
- query frequency.

The final Cosmos DB indexing policy must be defined before implementation.

## 4.6 Reporting Layer

The Reporting Layer provides authorized access to audit data and generates reports.

Responsibilities:

- authenticate report consumers;
- authorize report access;
- apply tenant and organizational scope;
- validate report filters;
- query audit data;
- apply pagination and sorting;
- calculate documented metrics;
- generate CSV and Excel exports;
- avoid exposing unauthorized or sensitive data.

The Reporting Layer must not bypass the Audit Service security model.

### Suggested reporting operations

```text
SearchEvents
GetEventById
GenerateAccessAuditReport
GenerateTranscriptionReport
GenerateDictationStatusReport
GenerateUserActivityReport
GenerateTimeAnalysisReport
GenerateProductivityReport
GenerateReportUsageReport
```

The exact API protocol for reporting may be REST, gRPC, or another approved interface. It must not expose Cosmos DB directly to clients.

## 4.7 Monitoring and operations

The platform must provide operational telemetry for:

- accepted events;
- rejected events;
- duplicate submissions;
- retryable failures;
- permanently failed events;
- queue depth;
- processing latency;
- persistence latency;
- query latency;
- report generation latency;
- authentication failures;
- tenant authorization failures;
- Cosmos DB throttling;
- storage availability;
- event volume by application and tenant.

Logs must not contain:

- passwords;
- access tokens;
- full transcription content;
- audio content;
- unnecessary clinical information;
- complete sensitive request payloads.

## 5. Event ingestion flow

### Successful event flow

```text
1. A business operation occurs in DigiWeb or DigiConsole.
2. The application creates an AuditEvent.
3. The application sends the event through the authenticated gRPC API.
4. The API authenticates the application.
5. The Audit Service validates the event.
6. The tenant context is validated.
7. The event identifier is checked for idempotency.
8. The valid event is placed in the durable queue.
9. The producer receives an acceptance response.
10. A worker reads the event from the queue.
11. The worker persists the event in Azure Cosmos DB.
12. The event becomes available to authorized reporting queries.
```

### Important acknowledgment rule

The system must define whether `ACCEPTED` means:

- accepted into a durable queue; or
- persisted in Cosmos DB.

The MVP must use one consistent definition.

The recommended behavior is:

> `ACCEPTED` means that the event has been validated and placed in a durable processing mechanism. The producer must be able to distinguish this from final persistence if required by the business.

If the business requires immediate persistence confirmation, the API must expose a separate status or use a synchronous persistence mode for selected events.

## 6. Validation flow

Validation occurs before an event is accepted for processing.

The Audit Service must validate:

- event identifier format;
- event contract version;
- timestamp format and UTC value;
- tenant identifier;
- calling application;
- event type;
- category;
- outcome;
- severity;
- actor;
- target when required;
- event-specific data;
- prohibited sensitive data;
- payload size limits.

Invalid events must be rejected with a stable error code.

Example error categories:

```text
INVALID_EVENT_FORMAT
UNSUPPORTED_EVENT_VERSION
UNKNOWN_EVENT_TYPE
MISSING_REQUIRED_FIELD
INVALID_TENANT_CONTEXT
UNAUTHORIZED_APPLICATION
INVALID_EVENT_DATA
PAYLOAD_TOO_LARGE
SENSITIVE_DATA_NOT_ALLOWED
```

## 7. Idempotency and duplicate handling

The event `id` is the idempotency key.

When an event is submitted:

- if the identifier is new, the event may be accepted;
- if the identifier already exists with identical content, the request is treated as a duplicate;
- if the identifier exists with different content, the request is rejected as a conflict.

Duplicate handling must prevent:

- duplicate report rows;
- inflated productivity metrics;
- repeated status transitions;
- repeated purge records.

## 8. Failure handling

### Transient failures

Examples:

- temporary Cosmos DB throttling;
- temporary queue unavailability;
- network interruption;
- service unavailability.

Transient failures must be retried according to a bounded retry policy.

### Permanent failures

Examples:

- invalid event contract;
- unknown event type;
- invalid tenant;
- unsupported event version;
- prohibited sensitive data.

Permanent failures must not be retried indefinitely.

The system must:

- record the failure reason;
- preserve enough technical context for investigation;
- notify the producer;
- route the message to an appropriate dead-letter or rejection store when necessary.

## 9. Security architecture

### Application-to-service security

Applications must authenticate with the Audit gRPC API using the approved enterprise authentication mechanism.

The implementation must provide:

- encrypted communication;
- application identity;
- credential rotation;
- least-privilege authorization;
- protection against replay where applicable.

### User-to-report security

Report consumers must be authenticated and authorized before accessing audit data.

Authorization must consider:

- tenant;
- user role;
- organizational scope;
- report type;
- requested entity scope.

### Storage security

Azure Cosmos DB access must be restricted to authorized platform services.

Applications and end users must not receive direct Cosmos DB credentials or connection details.

## 10. Data protection

The platform must protect audit data through:

- encryption in transit;
- encryption at rest;
- restricted network access;
- managed identities or equivalent secure credentials;
- secret rotation;
- role-based access control;
- tenant-aware query enforcement;
- data minimization;
- operational monitoring.

Audit events must not include:

- passwords;
- access tokens;
- private keys;
- full audio files;
- complete transcription content;
- unnecessary clinical information.

## 11. Performance and scalability

The architecture must minimize the impact of auditing on business operations.

Performance measures must include:

- event publication latency;
- validation latency;
- queue processing latency;
- persistence latency;
- report query latency;
- report generation latency;
- throughput by tenant;
- throughput by application.

The following strategies should be used:

- asynchronous event processing;
- bounded event payloads;
- batch publication where appropriate;
- efficient Cosmos DB indexing;
- pagination for search and reports;
- separate scaling of ingestion and reporting workloads;
- backpressure when downstream services are unavailable.

Performance targets must be defined and validated before production deployment.

## 12. Immutability and lifecycle

Accepted events are append-only.

Normal APIs must not support:

- updating events;
- overwriting events;
- deleting events;
- moving events between tenants;
- changing event ownership.

Retention and deletion are governance operations and must be implemented separately.

Before production deployment, the project must define:

- retention periods;
- legal holds;
- customer-specific retention rules;
- archival requirements;
- deletion authorization;
- deletion audit requirements;
- recovery procedures.

Long-term archival and Microsoft Fabric integration are outside the MVP architecture.

## 13. Availability and disaster recovery

The production deployment must define:

- availability targets;
- backup strategy;
- restore procedures;
- regional redundancy requirements;
- recovery point objective;
- recovery time objective;
- queue recovery behavior;
- dead-letter recovery procedures.

A disaster recovery test must verify that:

- accepted events are not silently lost;
- duplicate handling remains safe after recovery;
- tenant isolation remains enforced;
- reports remain consistent with stored events.

## 14. API boundaries

The following boundaries are mandatory:

```text
Business applications
    → Audit gRPC API
    → Audit Service
    → Internal Queue
    → Cosmos DB

Report consumers
    → Reporting API
    → Reporting Layer
    → Audit data access layer
    → Cosmos DB
```

The following access patterns are forbidden:

```text
Business application → Cosmos DB
End user → Cosmos DB
Business application → internal queue
End user → internal queue
```

## 15. Future extensions

The architecture must allow future support for:

- role and permission auditing;
- user administration auditing;
- detailed audio access auditing;
- report export auditing;
- AI assistance auditing;
- speech recognition auditing;
- long-term archival;
- Microsoft Fabric;
- Power BI;
- anomaly detection;
- advanced analytics.

Future extensions must reuse the canonical event model and must not bypass the Audit Service.

## 16. Architecture acceptance criteria

The architecture is considered ready for implementation when:

- the gRPC ingestion contract is approved;
- the event validation rules are approved;
- the event catalog is approved;
- the acknowledgment semantics are defined;
- the internal queue technology is selected;
- the Cosmos DB partition key is validated;
- the Cosmos DB indexing policy is defined;
- tenant isolation has been designed and tested;
- authentication and authorization mechanisms are approved;
- retry and dead-letter behavior is defined;
- observability requirements are defined;
- performance targets are agreed;
- retention and deletion governance is approved;
- disaster recovery expectations are documented.
