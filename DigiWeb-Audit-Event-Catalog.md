# DigiWeb Audit Platform  
# Audit Event Catalog

Version: 1.0  
Status: Approved  
Phase: Architecture

---

# 1. Purpose

This document defines the audit events recorded by the DigiWeb Audit Platform.

The catalog contains only events required to satisfy:

- Customer audit requirements
- Security investigations
- Productivity reporting
- Compliance reporting

Events without a demonstrated business, compliance, or reporting value are intentionally excluded.

---

# 2. Event Design Principles

## Business-Oriented Events

Events represent meaningful business or security actions.

Examples:

- User login
- Dictation access
- Transcription modification
- Status change
- Report execution

---

## Minimal Data Collection

Each event contains only the information required to answer audit and reporting questions.

Additional metadata is stored only when necessary.

---

# 3. Event Catalog

---

# LOGIN

## Description

A user successfully authenticates to the application.

## Purpose

Supports:

- User Activity Audit
- Security investigations

## Target

None

## Example

```json
{
  "event": "LOGIN"
}
```

---

# LOGOUT

## Description

A user ends an authenticated session.

## Purpose

Supports:

User Activity Audit
Security investigations  

## Target

None

## Example
```json
{
"event": "LOGOUT"
}
```

---

# DICTATION_ACCESSED

## Description

A user accesses or opens a dictation.

## Purpose

Supports:

Access Audit Report
Security investigations

## Target

Dictation

## Example
```json
{
"event": "DICTATION_ACCESSED",
"targetId": "DICT001"
}
```

---

# DICTATION_STATUS_CHANGED

## Description

The status of a dictation changes.

## Purpose

Supports:

User Activity Audit
Operational reporting
Workflow tracking

## Target

Dictation

## Data Attributes
```json
{
"from": "New",
"to": "Assigned"
}
```

## Example
```json
{
"event": "DICTATION_STATUS_CHANGED",
"targetId": "DICT001",
"data": {
"from": "New",
"to": "Assigned"
}
}
```

---

# DICTATION_PURGED

## Description

A dictation recording is permanently deleted or purged.

## Purpose

Supports:

User Activity Audit
Compliance investigations

## Target

Dictation

## Example
```json
{
"event": "DICTATION_PURGED",
"targetId": "DICT001"
}
```

---

# TRANSCRIPTION_MODIFIED

## Description

A user modifies the contents of a transcription.

## Purpose

Supports:

Transcription Detailed Report
Audit investigations

## Target

Transcription

## Example
```json
{
"event": "TRANSCRIPTION_MODIFIED",
"targetId": "TR001"
}
```

---

# TRANSCRIPTION_STATUS_CHANGED

## Description

The workflow status of a transcription changes.

## Purpose

Supports:

User Activity Audit
Workflow reporting
Operational reporting

## Target

Transcription

## Data Attributes
```json
{
"from": "Draft",
"to": "Completed"
}
```

## Example
```json
{
"event": "TRANSCRIPTION_STATUS_CHANGED",
"targetId": "TR001",
"data": {
"from": "Draft",
"to": "Completed"
}
}
```

---

# WORK_SESSION

## Description

Represents a completed transcription work session performed by a user.

A work session records the effective time spent working on a dictation.

## Purpose

Supports:

Time Analysis Report
True Productivity Report

## Target

Dictation

## Data Attributes
```json
{
"minutes": 18
}
```

## Example
```json
{
"event": "WORK_SESSION",
"targetId": "DICT001",
"data": {
"minutes": 18
}
}
```

---

# REPORT_EXECUTED

## Description

A user executes an audit, operational, or management report.

## Purpose

Supports:

User Activity Audit
Report Usage Audit
Administrative monitoring

## Target

Report

## Example
```json
{
"event": "REPORT_EXECUTED",
"targetId": "AccessAuditReport"
}
```

---

# 4. Traceability to Customer Requirements

---

| Customer Requirement                   | Event                          |
| -------------------------------------- | ------------------------------ |
| User log on/off                        | LOGIN, LOGOUT                  |
| List of users who accessed a dictation | DICTATION\_ACCESSED            |
| Users who changed transcriptions       | TRANSCRIPTION\_MODIFIED        |
| Change of dictation status             | DICTATION\_STATUS\_CHANGED     |
| Change of transcription status         | TRANSCRIPTION\_STATUS\_CHANGED |
| Dictation recording purges             | DICTATION\_PURGED              |
| Running of a report                    | REPORT\_EXECUTED               |
| Time spent transcribing                | WORK\_SESSION                  |
| True productivity measures             | WORK\_SESSION                  |

---

# 6. Summary

The Version 1 audit catalog intentionally remains minimal.

The platform records only nine high-value business and security events:

LOGIN  
LOGOUT  
DICTATION_ACCESSED  
DICTATION_STATUS_CHANGED  
DICTATION_PURGED  
TRANSCRIPTION_MODIFIED  
TRANSCRIPTION_STATUS_CHANGED  
WORK_SESSION  
REPORT_EXECUTED  

This event set satisfies the current customer audit requirements while minimizing storage, complexity, and long-term maintenance costs.
