# DigiWeb Audit Trail Reports

## 1. Purpose

This document defines the reports supported by the DigiWeb Audit Platform MVP.

Each report specifies:

- its business objective;
- the questions it answers;
- the events it uses;
- the supported filters;
- the displayed information;
- the required permissions;
- the available export formats;
- the acceptance criteria.

All reports must enforce organization isolation.

---

## 2. Common Report Requirements

All reports must:

- support a date range filter;
- apply OrganizationId filtering automatically;
- return only authorized data;
- display timestamps consistently;
- use UTC as the storage standard;
- provide deterministic sorting;
- support pagination;
- clearly indicate when no data exists;
- avoid exposing sensitive information;
- support CSV export where applicable;
- support Excel export where applicable.

---

## 3. Common Filters

Depending on the report, the following filters may be available:

- date range;
- organization;
- application;
- user;
- group;
- event type;
- dictation identifier;
- transcription identifier;
- report name.

---

## 4. Report Access

Initial MVP access:

| Role | Access |
|--------|--------|
| SUPERVISOR | Operational reports |
| ADMINISTRATOR | Operational and audit reports |
| AUDITOR | Read-only audit reports |
| SYSTEM | Service-to-service access |
| Other users | No access unless explicitly granted |

All report access must remain organization-scoped.

---

# 5. Access Audit Report

## Objective

Identify all users who accessed a dictation.

## Business Question

> Who accessed this dictation, when, and how?

## Events Used

- DICTATION_ACCESSED

## Filters

- date range;
- dictation identifier;
- user identifier;
- group identifier;
- access type;
- application.

## Columns

| Column | Description |
|----------|----------|
| Timestamp | Event timestamp |
| Dictation ID | Accessed dictation |
| User ID | User identifier |
| Group ID | Group identifier |
| Application | Originating application |
| Access Type | OPEN or VIEW |

## Exports

- CSV
- Excel

## Acceptance Criteria

The report is accepted when it can:

- list all recorded accesses;
- identify the user;
- identify the dictation;
- distinguish access types;
- enforce organization isolation.

---

# 6. Detailed Transcription Report

## Objective

Provide the history of transcription modifications and status changes.

## Business Questions

> Who modified this transcription?

> How did the transcription status change over time?

## Events Used

- TRANSCRIPTION_MODIFIED
- TRANSCRIPTION_STATUS_CHANGED

## Filters

- date range;
- transcription identifier;
- dictation identifier;
- user identifier;
- event type.

## Columns

| Column | Description |
|----------|----------|
| Timestamp | Event timestamp |
| Transcription ID | Affected transcription |
| Dictation ID | Related dictation |
| User ID | User identifier |
| Event Type | Modification or Status Change |
| Version | Resulting version |
| Change Type | Type of modification |
| Previous Status | Previous status |
| New Status | New status |
| Application | Originating application |

## Privacy Rules

The report must never display:

- transcription content;
- clinical content;
- authentication information;
- tokens.

## Acceptance Criteria

The report is accepted when it can:

- reconstruct transcription history;
- identify modifying users;
- display modification versions;
- display status transitions;
- enforce organization isolation.

---

# 7. Dictation Status History Report

## Objective

Provide the complete status history of a dictation.

## Business Question

> How did this dictation move through its lifecycle?

## Events Used

- DICTATION_STATUS_CHANGED

## Filters

- date range;
- dictation identifier;
- user identifier;
- previous status;
- new status.

## Columns

| Column | Description |
|----------|----------|
| Timestamp | Event timestamp |
| Dictation ID | Dictation identifier |
| Previous Status | Previous value |
| New Status | New value |
| User ID | User identifier |
| Application | Originating application |

## Acceptance Criteria

The report is accepted when it can:

- list status transitions;
- display the previous and new status;
- sort chronologically;
- identify the responsible user;
- enforce organization isolation.

---

# 8. User Activity Audit Report

## Objective

Provide a consolidated view of user activity.

## Business Question

> What significant actions were performed by a user during a given period?

## Events Used

- LOGIN
- LOGOUT
- DICTATION_ACCESSED
- DICTATION_STATUS_CHANGED
- DICTATION_PURGED
- TRANSCRIPTION_MODIFIED
- TRANSCRIPTION_STATUS_CHANGED
- WORK_SESSION
- REPORT_EXECUTED

## Filters

- date range;
- user identifier;
- group identifier;
- application;
- event type.

## Columns

| Column | Description |
|----------|----------|
| Timestamp | Event timestamp |
| Event Type | Audit event |
| User ID | User identifier |
| Group ID | Group identifier |
| Application | Originating application |
| Event Data | Event-specific data |

## Acceptance Criteria

The report is accepted when it can:

- combine all supported MVP events;
- filter by user;
- filter by event type;
- filter by period;
- preserve timestamps;
- enforce organization isolation.

---

# 9. Time Analysis Report

## Objective

Measure the recorded work time of users.

## Business Question

> How much time did users spend working on a dictation?

## Events Used

- WORK_SESSION

## Filters

- date range;
- user identifier;
- dictation identifier;
- transcription identifier;
- work type.

## Columns

| Column | Description |
|----------|----------|
| Dictation ID | Related dictation |
| Transcription ID | Related transcription |
| User ID | User identifier |
| Work Type | Type of work |
| Duration Seconds | Recorded duration |
| Application | Originating application |

## Summary Values

The report may provide:

- total duration per user;
- total duration per dictation;
- total duration per transcription;
- average duration;
- session count.

## Rules

- durations must not be negative;
- only WORK_SESSION events are authoritative;
- incomplete sessions must be identified.

## Acceptance Criteria

The report is accepted when it can:

- calculate durations correctly;
- identify users;
- identify work items;
- identify incomplete sessions;
- provide consistent totals.

---

# 10. True Productivity Report

## Objective

Provide productivity indicators based on recorded activity.

## Events Used

- WORK_SESSION
- TRANSCRIPTION_MODIFIED
- TRANSCRIPTION_STATUS_CHANGED
- DICTATION_STATUS_CHANGED

## Filters

- date range;
- user;
- group;
- application.

## Indicators

The report may provide:

- completed dictations;
- completed transcriptions;
- total work time;
- average work session duration;
- transcription modifications;
- reviewed transcriptions;
- approved transcriptions.

## Rules

Calculated values must:

- document their source events;
- document formulas;
- document assumptions;
- exclude duplicate events.

## Acceptance Criteria

The report is accepted when it can:

- calculate reproducible metrics;
- identify insufficient data;
- exclude duplicates;
- preserve organization isolation.

---

# 11. Report Usage Report

## Objective

Identify which reports were executed and by whom.

## Business Questions

> Which reports are being used?

> Who executed a report?

## Events Used

- REPORT_EXECUTED

## Filters

- date range;
- user identifier;
- report name;
- application.

## Columns

| Column | Description |
|----------|----------|
| Timestamp | Event timestamp |
| Report Name | Executed report |
| User ID | User identifier |
| Application | Originating application |
| Execution Duration | Execution duration in milliseconds |

## Acceptance Criteria

The report is accepted when it can:

- list report executions;
- identify executing users;
- display execution duration;
- filter by date range;
- filter by report name;
- enforce organization isolation.

---

# 12. Export Behavior

Exports must:

- respect report permissions;
- respect OrganizationId isolation;
- preserve active filters;
- use stable column names;
- include export timestamps;
- avoid hidden fields;
- protect against spreadsheet formula injection.

Export auditing is outside MVP scope.

---

# 13. Pagination and Sorting

Event-based reports must support pagination.

Default sorting:

1. Timestamp descending
2. Event identifier descending

Historical reports may use ascending chronological ordering.

Pagination must support safe continuation.

---

# 14. Empty and Incomplete Data

Reports must distinguish:

- no matching events;
- incomplete event data;
- invalid data;
- unavailable historical data;
- unauthorized data.

The reporting layer must never invent missing audit information.

Calculated values must clearly indicate when required data is unavailable.

---

# 15. MVP Acceptance Checklist

The reporting layer is considered ready when:

- all seven MVP reports are implemented;
- all reports enforce OrganizationId isolation;
- authorization is enforced;
- pagination is available;
- CSV export is available where applicable;
- Excel export is available where applicable;
- timestamps are displayed consistently;
- duplicate events do not inflate totals;
- incomplete data is handled correctly;
- report performance is acceptable.
