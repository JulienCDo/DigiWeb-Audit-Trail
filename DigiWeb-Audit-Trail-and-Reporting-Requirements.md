# DigiWeb Audit Platform
# Audit Trail and Reporting Requirements

Phase: Architecture

---

# 1. Purpose

This document defines the audit trail and reporting requirements for the DigiWeb Audit Platform.

The objective is to establish a centralized audit capability that:

- Supports customer audit requirements
- Provides traceability of user activity
- Supports compliance investigations
- Enables operational reporting
- Provides a reusable audit platform for DigiWeb, DigiConsole, and future applications

The audit solution is designed to balance compliance requirements, business value, and storage efficiency.

---

# 2. Scope

The audit platform applies to:

- DigiWeb
- DigiConsole
- Future applications integrated with the centralized Audit Platform

The platform records only meaningful business and security events required for reporting, audits, and investigations.

Low-value technical events are intentionally excluded.

---

# 3. Business Requirements

The customer requires the ability to audit user activity and system activity through reports similar to those currently available in Digi and Med Console.

Supervisors must be able to generate audit reports that answer key operational, security, and compliance questions.

---

# 4. Audit Objectives

The platform must provide the ability to determine:

- Who logged into the system
- Who logged out of the system
- Who accessed a dictation
- Who modified a transcription
- Who changed a dictation status
- Who changed a transcription status
- Who permanently removed a dictation recording
- Who executed a report
- How much time users spent transcribing
- Productivity metrics based on actual transcription work

---

# 5. User Activity Audit Requirements

The platform must record and retain audit information for the following activities.

## Authentication Activity

The platform must record:

- User login
- User logout

---

## Dictation Activity

The platform must record:

- Access to a dictation
- Changes to dictation status
- Permanent purge of a dictation recording

---

## Transcription Activity

The platform must record:

- Modification of a transcription
- Changes to transcription status
- Time spent working on a transcription

---

## Reporting Activity

The platform must record:

- Execution of reports

---

# 6. Audit Event Requirements

Version 1 of the audit platform shall support the following events.

---

## LOGIN

Records successful user authentication.

### Purpose

Supports:

- User Activity Audit
- Security investigations

---

## LOGOUT

Records user sign-out activity.

### Purpose

Supports:

- User Activity Audit
- Session tracking

---

## DICTATION_ACCESSED

Records access to a dictation.

### Purpose

Supports:

- Access Audit Report
- Compliance investigations

---

## DICTATION_STATUS_CHANGED

Records changes to the workflow status of a dictation.

### Purpose

Supports:

- Dictation Status History Report
- User Activity Audit

---

## DICTATION_PURGED

Records permanent deletion of a dictation recording.

### Purpose

Supports:

- Compliance investigations
- User Activity Audit

---

## TRANSCRIPTION_MODIFIED

Records modification of a transcription.

### Purpose

Supports:

- Detailed Transcription Report

---

## TRANSCRIPTION_STATUS_CHANGED

Records changes to the workflow status of a transcription.

### Purpose

Supports:

- User Activity Audit
- Workflow reporting

---

## WORK_SESSION

Records a completed transcription work session.

### Purpose

Supports:

- Time Analysis Report
- True Productivity Report

---

## REPORT_EXECUTED

Records execution of a report.

### Purpose

Supports:

- User Activity Audit
- Report Usage Monitoring

---

# 7. Reporting Requirements

The platform shall provide audit reports capable of answering the customer's business and compliance questions.

---

# 8. Access Audit Report

## Objective

Determine who accessed a specific dictation.

## Questions Answered

- Who accessed the dictation?
- When was the dictation accessed?
- How many users accessed the dictation?

## Required Audit Event

- DICTATION_ACCESSED

---

# 9. Detailed Transcription Report

## Objective

Determine who modified a transcription.

## Questions Answered

- Who modified the transcription?
- When was it modified?
- How many modifications occurred?

## Required Audit Event

- TRANSCRIPTION_MODIFIED

---

# 10. Dictation Status History Report

## Objective

Provide a chronological history of dictation workflow changes.

## Questions Answered

- What status changes occurred?
- Who performed the change?
- When did the change occur?

## Required Audit Event

- DICTATION_STATUS_CHANGED

---

# 11. User Activity Audit Report

## Objective

Provide a consolidated view of user activity.

## Questions Answered

- Who logged in?
- Who logged out?
- Who changed statuses?
- Who purged recordings?
- Who executed reports?

## Required Audit Events

- LOGIN
- LOGOUT
- DICTATION_STATUS_CHANGED
- TRANSCRIPTION_STATUS_CHANGED
- DICTATION_PURGED
- REPORT_EXECUTED

---

# 12. Time Analysis Report

## Objective

Measure time spent transcribing.

## Questions Answered

- How much time was spent on a dictation?
- Which transcriptionists worked on the dictation?
- How much time did each transcriptionist contribute?

## Required Audit Event

- WORK_SESSION

---

# 13. True Productivity Report

## Objective

Measure productive transcription work.

## Questions Answered

- How many productive hours were worked?
- What productivity metrics can be calculated?
- How much work was completed during a period?

## Required Audit Event

- WORK_SESSION

---

# 14. Audit Data Requirements

Every audit event must contain sufficient information to answer reporting and investigation questions.

## Required Fields

```text
id
ts
tenantId
app
event
userId
```

---

## Optional Fields

```text
targetId
data
```

---

# 15. Multi-Tenant Requirements

The audit platform must support multiple tenants.

Requirements:

- Every event belongs to a tenant.
- Audit queries must respect tenant boundaries.
- Reports must only expose data for authorized tenants.
- Tenant data must remain logically isolated.

---

# 16. Performance Requirements

The audit solution must not negatively impact normal application workflows.

Requirements:

- Audit submission must be asynchronous.
- Audit processing must be non-blocking.
- Business transactions must not fail due to audit persistence issues.
- Temporary storage failures must not interrupt user operations.

---

# 17. Security Requirements

Audit events are security-sensitive records.

Requirements:

- Applications must authenticate to the Audit Platform.
- Direct database access is prohibited.
- Only the Audit Service may write audit events.
- Audit events are immutable once stored.

Users do not authenticate directly to the Audit Platform.

---

# 18. Reporting Requirements Summary

| Requirement | Report |
|------------|---------|
| Users who accessed a dictation | Access Audit Report |
| Users who modified transcriptions | Detailed Transcription Report |
| Dictation status history | Dictation Status History Report |
| User activity monitoring | User Activity Audit Report |
| Time spent transcribing | Time Analysis Report |
| True productivity measurement | True Productivity Report |

---

# 19. Out of Scope (Version 1)

The following capabilities are intentionally excluded from the initial release:

- Audio playback auditing
- Audio download auditing
- User administration auditing
- Role change auditing
- Permission change auditing
- AI usage auditing
- Advanced analytics
- Cross-application reporting
- Microsoft Fabric integration

These capabilities may be introduced in future phases if justified by business or compliance requirements.

---

# 20. Success Criteria

The audit platform is considered successful if it can reliably answer the following questions:

1. Who logged in?
2. Who logged out?
3. Who accessed a dictation?
4. Who modified a transcription?
5. Who changed a dictation status?
6. Who changed a transcription status?
7. How much time was spent transcribing?
8. Who purged a dictation recording?
9. Who executed a report?

If these questions can be answered accurately through the audit reports, the audit platform satisfies the current customer requirements.

---

# 21. Summary

The DigiWeb Audit Platform provides a lightweight, centralized audit solution focused on business value and compliance requirements.

Version 1 delivers:

- A minimal audit event catalog
- Multi-tenant support
- Centralized audit management
- Audit reporting capabilities
- Azure Cosmos DB as the source of truth
- Support for all customer-requested audit scenarios

The solution intentionally prioritizes simplicity, maintainability, and scalability while remaining extensible for future requirements.
