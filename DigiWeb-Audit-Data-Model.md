# DigiWeb Audit Platform
# Audit Data Model

Version: 1.0
Status: Approved
Phase: Architecture

---

# 1. Purpose

This document defines the canonical audit event data model for the DigiWeb Audit Platform.

The objective of the model is to:

- Support all current customer audit and reporting requirements.
- Minimize storage costs.
- Reduce event payload size.
- Simplify reporting and querying.
- Support multi-tenant environments.
- Enable future reuse by DigiConsole and other applications.
- Provide a stable foundation for future analytics integration.

The platform uses Azure Cosmos DB as the audit event datastore and source of truth.

---

# 2. Design Principles

The audit model follows the following principles:

## Principle 1 - Store Only What Is Needed

Audit events should only contain information required to:

- Produce audit reports
- Support security investigations
- Meet compliance requirements

Fields without a clear reporting or audit use case must not be stored.

---

## Principle 2 - Minimize Event Volume

Only meaningful business and security events are audited.

Technical events such as:

- Button clicks
- Window navigation
- Screen focus changes
- UI interactions

are not recorded.

---

## Principle 3 - Immutable Events

Audit events are immutable.

Once written to Cosmos DB:

- Events cannot be modified.
- Events cannot be deleted individually.

---

## Principle 4 - Multi-Tenant by Design

Every audit event belongs to a tenant.

Tenant isolation must be enforced for:

- Storage
- Querying
- Reporting
- Security

---

## Principle 5 - Application Agnostic

The model must support:

- DigiWeb
- DigiConsole
- Future applications

without requiring schema changes.

---

# 3. Audit Event Structure

## Canonical Audit Event

```json
{
  "id": "8e3a7d7c-9d67-4dc9-a337-cf1d4e44dc5",

  "ts": "2026-09-23T15:30:22Z",

  "tenantId": "TENANT001",

  "app": "DigiWeb",

  "event": "DICTATION_ACCESSED",

  "userId": "USR123",

  "targetId": "DICT456",

  "data": {}
}
```

# 4. Field Definitions
|Field|Required|Description|
|----|----|----|
|id|Yes|Unique audit event identifier|
|ts|Yes|UTC timestamp|
|tenantId|Yes|Tenant identifier|
|app|Yes|Source application|
|event|Yes|Audit event type|
|userId|Yes|User responsible for the action|
|targetId|No|Business object impacted by the action|
|data|No|Event-specific metadata|

---

# 5. Field Details
## id
Globally unique identifier.  
``
Exemple: 8e3a7d7c-9d67-4dc9-a337-cf1d4e44dc5  
``
## ts
UTC timestamp representing when the audited action occurred.  
``
Exemple:  
2026-09-23T15:30:22Z  
``
## tenantId
Tenant that owns the event.
## app
Application that generated the event.  
``
Exemple:  
DigiWeb  
DigiConsole
``
## event
Business audit event identifier.  
``
Exemple:  
LOGIN  
DICTATION_ACCESSED  
REPORT_EXECUTED  
``
## userId
Unique identifier of the user performing the action.  

## targetId
Identifier of the primary business object affected by the event.  
## data
Optional event-specific information.  
Present only when additional information is required.  
``
Exemple:
{
  "from": "Draft",
  "to": "Completed"
}
``
