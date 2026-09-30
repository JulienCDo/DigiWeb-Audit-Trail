# Current Architecture Decisions

## Identity

- TenantId removed.
- OrganizationId is the official identifier.
- Identity derived from AuthenticationToken.
- OrganizationId, GroupId and UserId extracted at gRPC boundary.
- AuthenticationToken never reaches Queue or Cosmos.

## Queue

AuditQueueMessage contains:

- AuditEventId
- TimestampUtc
- OrganizationId
- GroupId
- UserId
- ApplicationId
- EventType
- Data

## Cosmos

Database: SynnefoAudit
Container: AuditEvents
Partition Key: /organizationId

## gRPC

CreateAuditEventResponse returns only SynnefoAPIStatus.
