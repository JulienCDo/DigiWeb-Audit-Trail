# Synnefo Audit Project Context

## Source of Truth

Document precedence:

1. Architecture-Decisions.md
2. 05-architecture.md
3. 02-event-model.md
4. 03-event-catalog.md
5. 04-reports.md
6. 01-requirements.md
7. 06-roadmap.md

## Current Status

Completed:

- gRPC ingestion
- Authentication token validation
- Identity extraction
- Azure Storage Queue publication
- AuditQueueProcessor
- Cosmos DB persistence
- Idempotency handling
- CosmosInitializationService

Current phase:

Phase 4 - Event Querying

## Approved Architecture

Application
↓
Audit gRPC API
↓
Azure Storage Queue
↓
AuditQueueProcessor
↓
Azure Cosmos DB
↓
Reports

## Approved Decisions

- Azure Storage Queue
- Azure Cosmos DB
- Partition Key = /organizationId
- Idempotency via document id
- Queue publication is the acknowledgement boundary
- Asynchronous persistence
- Organization isolation

## Technical Debt

- Managed Identity for Cosmos DB
- Dedicated Worker Service
- Dead Letter Queue
- Application Insights observability

## Important Notes

- Identity comes from AuthenticationToken.
- Applications never write directly to Cosmos.
- AuthenticationToken is never stored.
- Cosmos DB is the system of record.
