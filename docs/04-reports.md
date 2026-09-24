# DigiWeb Audit Trail Reports

## 1. Purpose

This document defines the audit reports supported by the DigiWeb Audit Trail MVP.

Each report specifies:

- its business objective;
- the questions it must answer;
- the events it uses;
- the available filters;
- the displayed columns;
- the required permissions;
- the supported export formats;
- its acceptance criteria.

All reports must enforce tenant isolation and role-based access control.

## 2. Common report requirements

All reports must:

- support a date range filter;
- apply the user's tenant scope automatically;
- return only authorized data;
- display timestamps in a consistent timezone;
- use UTC as the storage and processing standard;
- provide deterministic sorting;
- support pagination for large result sets;
- indicate when no data is available;
- avoid exposing sensitive clinical content;
- support CSV export where applicable;
- support Excel export where applicable.

### Common filters

Depending on the report, the following filters may be available:

- date range;
- tenant;
- application;
- user;
- user role;
- event outcome;
- event severity;
- dictation identifier;
- transcription identifier;
- report name;
- site;
- department.

## 3. Report access roles

The initial MVP roles are:

| Role | Access |
|---|---|
| `SUPERVISOR` | Operational audit reports within the authorized scope |
| `ADMINISTRATOR` | Security and operational audit reports within the authorized scope |
| `AUDITOR` | Read-only access to approved audit reports |
| `SYSTEM` | Service-to-service access only |
| Other users | No audit report access unless explicitly authorized |

Report access must be evaluated using both the user's role and the user's tenant or organizational scope.

## 4. Access Audit Report

### Objective

Identify all users who accessed a specific dictation.

### Business question

> Who accessed this dictation, when, and how?

### Events used

- `DICTATION_ACCESSED`

### Filters

- date range;
- dictation identifier;
- user identifier;
- user role;
- access type;
- application;
- outcome;
- site;
- department.

### Columns

| Column | Description |
|---|---|
| Date/Time | Event timestamp |
| Dictation ID | Accessed dictation |
| User ID | Actor identifier |
| User name | Actor display name |
| User role | Role at the time of access |
| Application | Originating application |
| Access type | `OPEN` or `VIEW` |
| Outcome | `SUCCESS` or `DENIED` |
| Workstation | When permitted and available |
| IP address | When permitted and available |

### Security

- `SUPERVISOR`
- `ADMINISTRATOR`
- `AUDITOR`

### Export

- CSV
- Excel

### Acceptance criteria

The report is accepted when it can:

- list all recorded access events for a dictation;
- distinguish opening from viewing when available;
- identify the actor and timestamp;
- show denied access attempts;
- prevent access to data from another tenant.

## 5. Detailed Transcription Report

### Objective

Provide the history of modifications and status changes for a transcription.

### Business questions

> Who modified this transcription?

> How did the transcription status change over time?

### Events used

- `TRANSCRIPTION_MODIFIED`
- `TRANSCRIPTION_STATUS_CHANGED`

### Filters

- date range;
- transcription identifier;
- dictation identifier;
- user identifier;
- user role;
- event type;
- transcription version;
- outcome;
- application.

### Columns

| Column | Description |
|---|---|
| Date/Time | Event timestamp |
| Transcription ID | Affected transcription |
| Dictation ID | Related dictation, when available |
| User ID | Actor identifier |
| User name | Actor display name |
| User role | Role at the time of the event |
| Event type | Modification or status change |
| Change type | Type of modification |
| Previous status | Previous transcription status |
| New status | New transcription status |
| Version | Resulting transcription version |
| Outcome | Operation outcome |
| Application | Originating application |

### Security

- `SUPERVISOR`
- `ADMINISTRATOR`
- `AUDITOR`

### Export

- CSV
- Excel

### Privacy rules

The report must not display:

- full transcription content;
- clinical content;
- authentication data;
- unrestricted request payloads.

### Acceptance criteria

The report is accepted when it can:

- reconstruct the recorded transcription history;
- identify each modifying actor;
- display the resulting version;
- display status transitions in chronological order;
- preserve tenant isolation.

## 6. Dictation Status History Report

### Objective

Provide the complete status history of a dictation.

### Business question

> How did this dictation move through its lifecycle?

### Events used

- `DICTATION_STATUS_CHANGED`

### Filters

- date range;
- dictation identifier;
- user identifier;
- previous status;
- new status;
- application;
- outcome.

### Columns

| Column | Description |
|---|---|
| Date/Time | Event timestamp |
| Dictation ID | Affected dictation |
| Previous status | Status before the change |
| New status | Status after the change |
| User ID | Actor identifier |
| User name | Actor display name |
| User role | Role at the time of the event |
| Change reason | Business reason, when available |
| Outcome | Operation outcome |
| Application | Originating application |

### Security

- `SUPERVISOR`
- `ADMINISTRATOR`
- `AUDITOR`

### Export

- CSV
- Excel

### Acceptance criteria

The report is accepted when it can:

- list all recorded status transitions;
- display the previous and new status;
- sort transitions chronologically;
- identify the actor or system;
- show only events belonging to the authorized tenant.

## 7. User Activity Audit Report

### Objective

Provide a consolidated view of significant user and system activity.

### Business question

> What significant actions were performed by a user or system during a given period?

### Events used

- `LOGIN`
- `LOGOUT`
- `DICTATION_ACCESSED`
- `DICTATION_STATUS_CHANGED`
- `DICTATION_PURGED`
- `TRANSCRIPTION_MODIFIED`
- `TRANSCRIPTION_STATUS_CHANGED`
- `WORK_SESSION`
- `REPORT_EXECUTED`

### Filters

- date range;
- user identifier;
- user role;
- application;
- event type;
- category;
- target type;
- target identifier;
- outcome;
- severity;
- correlation identifier.

### Columns

| Column | Description |
|---|---|
| Date/Time | Event timestamp |
| Event type | Audit event type |
| Category | Event category |
| Actor ID | User, service, or system identifier |
| Actor name | Actor display name, when available |
| Actor role | Role, when available |
| Target type | Affected entity type |
| Target ID | Affected entity identifier |
| Outcome | Operation outcome |
| Severity | Event severity |
| Application | Originating application |
| Correlation ID | Related operation identifier |

### Security

- `SUPERVISOR`
- `ADMINISTRATOR`
- `AUDITOR`

### Export

- CSV
- Excel

### Acceptance criteria

The report is accepted when it can:

- combine the supported MVP event types;
- filter by user, event type, and date range;
- identify both user and system activity;
- preserve the original event timestamp;
- apply tenant and role restrictions.

## 8. Time Analysis Report

### Objective

Measure the time spent by users working on dictations or transcriptions.

### Business question

> How much time did each user spend working on a dictation or transcription?

### Events used

- `WORK_SESSION`

### Filters

- date range;
- user identifier;
- user role;
- dictation identifier;
- transcription identifier;
- work type;
- application;
- site;
- department.

### Columns

| Column | Description |
|---|---|
| Dictation ID | Related dictation |
| Transcription ID | Related transcription, when available |
| User ID | Actor identifier |
| User name | Actor display name |
| User role | Role at the time of the session |
| Work type | Transcription, review, or correction |
| Start time | Beginning of the work session |
| Stop time | End of the work session |
| Duration | Duration in seconds or formatted time |
| Outcome | Session outcome |
| Application | Originating application |

### Summary values

The report may provide:

- total duration per user;
- total duration per dictation;
- total duration per transcription;
- average session duration;
- number of sessions;
- incomplete sessions;
- duration by work type.

### Security

- `SUPERVISOR`
- `ADMINISTRATOR`
- `AUDITOR`

### Export

- CSV
- Excel

### Rules

- Durations must not be negative.
- Incomplete sessions must be clearly identified.
- The report must not infer work time from unrelated events.
- Only recorded `WORK_SESSION` events may be used for the official duration.

### Acceptance criteria

The report is accepted when it can:

- calculate duration from valid work session data;
- identify the user and target entity;
- distinguish work types;
- show incomplete or invalid sessions separately;
- produce consistent totals.

## 9. True Productivity Report

### Objective

Provide operational productivity indicators based on audit activity.

### Business question

> What productivity indicators can be calculated from recorded work and business events?

### Events used

- `WORK_SESSION`
- `TRANSCRIPTION_MODIFIED`
- `TRANSCRIPTION_STATUS_CHANGED`
- `DICTATION_STATUS_CHANGED`

### Filters

- date range;
- user;
- user role;
- team;
- site;
- department;
- work type;
- application.

### Indicators

The report may provide:

- number of completed dictations;
- number of completed transcriptions;
- total recorded work time;
- average work session duration;
- average processing time;
- number of transcription modifications;
- number of reviewed transcriptions;
- number of approved transcriptions;
- volume processed per user;
- productivity by team;
- productivity by site.

### Columns

| Column | Description |
|---|---|
| User ID | User identifier |
| User name | User display name |
| User role | User role |
| Completed dictations | Number of completed dictations |
| Completed transcriptions | Number of completed transcriptions |
| Total work time | Sum of valid work sessions |
| Average work time | Average duration per work item |
| Modified transcriptions | Number of modification events |
| Productivity rate | Calculated operational indicator |
| Period | Reporting period |

### Security

- `SUPERVISOR`
- `ADMINISTRATOR`
- `AUDITOR`

### Export

- CSV
- Excel

### Rules

Productivity indicators must be clearly identified as calculated metrics.

The report must document:

- the source events;
- the calculation period;
- the calculation formula;
- how incomplete sessions are handled;
- how duplicate events are excluded;
- how missing data affects the result.

The report must not be used as the sole source for employee evaluation without appropriate business validation.

### Acceptance criteria

The report is accepted when it can:

- calculate indicators from documented event types;
- provide reproducible results for the same input period;
- exclude duplicate events;
- identify incomplete or insufficient data;
- display the calculation period and source definitions.

## 10. Report Usage Report

### Objective

Identify which reports were executed, by whom, and when.

### Business questions

> Which reports are being used?

> Who executed a report and how long did it take?

### Events used

- `REPORT_EXECUTED`

### Filters

- date range;
- user identifier;
- user role;
- report name;
- application;
- outcome;
- minimum or maximum execution duration.

### Columns

| Column | Description |
|---|---|
| Date/Time | Event timestamp |
| Report name | Executed report |
| User ID | Actor identifier |
| User name | Actor display name |
| User role | Role at execution time |
| Application | Originating application |
| Execution duration | Duration in milliseconds |
| Outcome | Execution result |
| Correlation ID | Related execution identifier |
| Parameter summary | Sanitized summary, when available |

### Security

- `ADMINISTRATOR`
- `AUDITOR`

Access for `SUPERVISOR` may be granted according to customer configuration.

### Export

- CSV
- Excel

### Privacy rules

The report must not display:

- full query payloads;
- passwords or tokens;
- clinical content;
- unrestricted report parameters;
- data from another tenant.

### Acceptance criteria

The report is accepted when it can:

- list report executions;
- identify the executing user;
- display duration and outcome;
- filter by report name and date range;
- prevent unauthorized access to execution history.

## 11. Report export behavior

Exports must follow the same authorization rules as interactive reports.

Each export should:

- apply the active report filters;
- preserve the selected tenant scope;
- use stable column names;
- include the export timestamp;
- use UTC timestamps or clearly state the display timezone;
- avoid including hidden or unauthorized fields;
- protect against formula injection in spreadsheet formats.

Export auditing is outside the MVP event catalog. It may be implemented later through a dedicated `REPORT_EXPORTED` event.

## 12. Pagination and sorting

Reports returning event-level data must support pagination.

The default sort order is:

1. `timestampUtc` descending;
2. event identifier descending as a deterministic tie-breaker.

Historical reports requiring chronological reconstruction may use ascending order.

The API must return enough metadata for clients to continue pagination safely.

## 13. Empty and incomplete data

Reports must distinguish between:

- no matching events;
- incomplete event data;
- invalid event data;
- unavailable historical data;
- unauthorized data.

The reporting layer must not silently invent or infer missing audit events.

When calculated values cannot be reliably produced, the report must show the value as unavailable and explain the reason.

## 14. MVP report acceptance checklist

The reporting layer is considered ready when:

- all seven MVP reports are implemented;
- each report uses only documented event types;
- filters are tenant-safe;
- role-based authorization is enforced;
- pagination is available for large result sets;
- CSV export works where applicable;
- Excel export works where applicable;
- timestamps are displayed consistently;
- reports do not expose clinical content unnecessarily;
- calculated metrics are reproducible;
- duplicate events do not inflate totals;
- empty and incomplete data are clearly handled;
- report performance has been measured and accepted.
