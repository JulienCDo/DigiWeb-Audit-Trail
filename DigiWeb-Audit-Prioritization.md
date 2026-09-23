# DigiWeb Audit Prioritization

## Purpose

Define the implementation priorities for the DigiWeb Audit Platform.

This prioritization aims to:

- Satisfy customer audit requirements
- Deliver business value early
- Reduce implementation risk
- Establish a reusable audit platform for DigiWeb, DigiConsole, and future applications
- Maintain a minimal and sustainable audit footprint

---

# Must Have (Version 1)

## Description

These capabilities are required to satisfy the audit and reporting requirements explicitly identified by the customer.

Without these items, the audit platform cannot meet the project's primary objectives.

---

## Authentication Audit

### Events

- LOGIN
- LOGOUT

### Justification

Provides traceability of user access to the platform.

Supports:

- User Activity Audit

---

## Dictation Access Audit

### Events

- DICTATION_ACCESSED

### Justification

Directly answers the customer requirement:

> Who accessed a dictation?

Supports:

- Access Audit Report

---

## Dictation Status History

### Events

- DICTATION_STATUS_CHANGED

### Justification

Provides a complete history of dictation workflow transitions.

Supports:

- Dictation Status History Report
- User Activity Audit

---

## Transcription Audit

### Events

- TRANSCRIPTION_MODIFIED
- TRANSCRIPTION_STATUS_CHANGED

### Justification

Provides traceability of user activity on transcriptions.

Supports:

- Detailed Transcription Report
- User Activity Audit

Directly answers:

> Who modified a transcription?

---

## Productivity Tracking

### Events

- WORK_SESSION

### Justification

Captures effective transcription work duration.

Supports:

- Time Analysis Report
- True Productivity Report

Directly answers:

> How much time was spent transcribing?

---

## Dictation Retention Audit

### Events

- DICTATION_PURGED

### Justification

Tracks permanent deletion of dictation recordings.

Supports:

- User Activity Audit
- Compliance investigations

---

## Report Usage Audit

### Events

- REPORT_EXECUTED

### Justification

Tracks report usage within the system.

Supports:

- User Activity Audit
- Report Usage Reporting

Directly answers:

> Who executed a report?

---

# Should Have (Version 1.1)

## Description

Additional audit capabilities that may provide operational value but are not required to satisfy current customer requirements.

---

## Security Administration Audit

### Potential Events

- USER_CREATED
- USER_DISABLED
- ROLE_CHANGED
- PERMISSION_CHANGED

### Justification

Provides traceability of administrative security actions.

Not currently required by customer audit reports.

---

## Audio Access Audit

### Potential Events

- AUDIO_ACCESSED
- AUDIO_DOWNLOADED

### Justification

Provides visibility into access to audio recordings.

May become relevant for future compliance or privacy requirements.

---

## Report Export Audit

### Potential Events

- REPORT_EXPORTED

### Justification

Tracks data extraction activities and report distribution.

---

# Nice To Have (Phase 2)

## Description

Capabilities intended for future enhancement once production usage patterns and audit volumes are better understood.

---

## Artificial Intelligence Audit

### Potential Events

- AI_ASSISTANCE_REQUESTED
- AI_ASSISTANCE_COMPLETED
- AI_ASSISTANCE_FAILED

### Justification

Provides analytics and governance for AI-assisted workflows.

---

## Advanced Analytics

### Solution

Microsoft Fabric

### Potential Use Cases

- Historical trend analysis
- Cross-application reporting
- Operational KPIs
- Productivity dashboards
- Compliance dashboards
- Anomaly detection
- Long-term analytics

### Notes

Microsoft Fabric is not required for the initial audit platform release.

The Version 1 solution must be fully operational using Azure Cosmos DB as the source of truth.

---

# MVP Scope

## Included in Version 1

### Audit Events

- LOGIN
- LOGOUT
- DICTATION_ACCESSED
- DICTATION_
