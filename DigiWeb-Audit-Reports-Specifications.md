# DigiWeb Audit Platform
# Audit Reports Specifications

Phase: Architecture

---

# 1. Purpose

This document defines the audit reports supported by the DigiWeb Audit Platform.

The reports are designed to satisfy:

- Customer audit requirements
- Operational traceability needs
- Compliance investigations
- Productivity analysis
- Management reporting

All reports are generated from audit events stored in Azure Cosmos DB.

---

# 2. Reporting Principles

## Audit Reports Must Be Evidence-Based

Reports are generated exclusively from recorded audit events.

No audit report should rely on inferred or reconstructed data.

---

## Tenant Isolation

All reports must be filtered by tenant.

Users may only access reports for tenants they are authorized to view.

---

## Audit Read-Only Model

Audit reports provide visibility into audit data.

Reports must not allow modification of audit records.

---

## Consistent Filtering

All reports should support, where applicable:

- Date Range
- User
- Dictation
- Transcription
- Tenant

---

# 3. Access Audit Report

## Objective

Identify all users who accessed a specific dictation.

---

## Business Questions

- Who accessed the dictation?
- When was it accessed?
- How many users accessed it?
- How many times was it accessed?

---

## Source Event

```text
DICTATION_ACCESSED
```

---

## Available Filters

```text
Date Range
Dictation ID
User ID
```

---

## Report Columns

```text
Timestamp
User ID
Dictation ID
Application
```

---

## Sample Output

```text
2026-09-23 10:15    USR001    DICT1001    DigiWeb
2026-09-23 10:37    USR015    DICT1001    DigiWeb
2026-09-23 11:12    USR001    DICT1001    DigiWeb
```

---

# 4. Detailed Transcription Report

## Objective

Identify users who modified a transcription.

---

## Business Questions

- Who modified the transcription?
- When was the modification made?
- How many modifications occurred?

---

## Source Event

```text
TRANSCRIPTION_MODIFIED
```

---

## Available Filters

```text
Date Range
Transcription ID
User ID
```

---

## Report Columns

```text
Timestamp
User ID
Transcription ID
Application
```

---

## Sample Output

```text
2026-09-23 09:12    USR010    TR5001    DigiWeb
2026-09-23 09:18    USR010    TR5001    DigiWeb
2026-09-23 09:35    USR022    TR5001    DigiWeb
```

---

# 5. Dictation Status History Report

## Objective

Provide a complete history of dictation workflow status changes.

---

## Business Questions

- What status changes occurred?
- Who performed the change?
- When did the change occur?

---

## Source Event

```text
DICTATION_STATUS_CHANGED
```

---

## Available Filters

```text
Date Range
Dictation ID
User ID
```

---

## Report Columns

```text
Timestamp
Dictation ID
User ID
Previous Status
New Status
Application
```

---

## Sample Output

```text
2026-09-23 08:00    DICT1001    USR001    New        Assigned
2026-09-23 09:15    DICT1001    USR010    Assigned   In Progress
2026-09-23 10:42    DICT1001    USR010    In Progress Completed
```

---

# 6. User Activity Audit Report

## Objective

Provide a consolidated timeline of significant user activity.

---

## Business Questions

- Who logged in?
- Who logged out?
- Who changed statuses?
- Who purged a recording?
- Who executed a report?

---

## Source Events

```text
LOGIN
LOGOUT
DICTATION_STATUS_CHANGED
TRANSCRIPTION_STATUS_CHANGED
DICTATION_PURGED
REPORT_EXECUTED
```

---

## Available Filters

```text
Date Range
User ID
Event Type
```

---

## Report Columns

```text
Timestamp
User ID
Event
Target ID
Application
```

---

## Sample Output

```text
2026-09-23 08:01    USR001    LOGIN                      -
2026-09-23 08:15    USR001    DICTATION_STATUS_CHANGED   DICT1001
2026-09-23 09:42    USR010    REPORT_EXECUTED            Access Audit
2026-09-23 10:05    USR020    DICTATION_PURGED           DICT2100
2026-09-23 17:02    USR001    LOGOUT                     -
```

---

# 7. Time Analysis Report

## Objective

Measure transcription effort spent on dictations.

---

## Business Questions

- Who worked on a dictation?
- How much time did each user spend?
- What is the total effort for a dictation?

---

## Source Event

```text
WORK_SESSION
```

---

## Available Filters

```text
Date Range
User ID
Dictation ID
```

---

## Report Columns

```text
Dictation ID
User ID
Work Session Count
Total Minutes
Total Hours
```

---

## Sample Output

```text
DICT1001    USR010    4    72    1.20
DICT1001    USR022    2    24    0.40
DICT2001    USR015    3    48    0.80
```

---

# 8. True Productivity Report

## Objective

Measure productive transcription activity over a period.

---

## Business Questions

- How much transcription work was completed?
- Which users contributed?
- How productive was each user?

---

## Source Event

```text
WORK_SESSION
```

---

## Available Filters

```text
Date Range
User ID
```

---

## Report Columns

```text
User ID
Work Session Count
Total Minutes
Total Hours
```

---

## Sample Output

```text
USR010    52    984    16.40
USR015    43    765    12.75
USR022    39    702    11.70
```

---

# 9. Report Usage Report

## Objective

Monitor report usage within the platform.

---

## Business Questions

- Which reports are executed most frequently?
- Who executes reports?
- When are reports executed?

---

## Source Event

```text
REPORT_EXECUTED
```

---

## Available Filters

```text
Date Range
Report Name
User ID
```

---

## Report Columns

```text
Timestamp
User ID
Report Name
Application
```

---

## Sample Output

```text
2026-09-23 09:05    USR001    Access Audit Report
2026-09-23 09:35    USR010    Time Analysis Report
2026-09-23 10:22    USR015    User Activity Report
```

---

# 10. Export Capabilities

The platform should support export of report results to:

```text
CSV
Excel
PDF
```

Export activity itself is not audited in Version 1.

---

# 11. Performance Requirements

Reports should support:

```text
Date filtering
User filtering
Entity filtering
Tenant filtering
```

The reporting experience should remain responsive for normal operational use.

---

# 12. Security Requirements

Report access must respect:

```text
Tenant boundaries
Role-based authorization
Application security policies
```

Users must never be able to retrieve audit data belonging to another tenant.

---

# 13. Future Reports

The following reports may be introduced in future releases:

```text
Security Administration Report
Audio Access Report
AI Usage Report
Role Change Report
Permission Change Report
Cross-Application Audit Report
```

These reports are outside the current Version 1 scope.

---

# 14. Report to Event Mapping

| Report | Required Event(s) |
|----------|----------|
| Access Audit Report | DICTATION_ACCESSED |
| Detailed Transcription Report | TRANSCRIPTION_MODIFIED |
| Dictation Status History Report | DICTATION_STATUS_CHANGED |
| User Activity Audit Report | LOGIN, LOGOUT, DICTATION_STATUS_CHANGED, TRANSCRIPTION_STATUS_CHANGED, DICTATION_PURGED, REPORT_EXECUTED |
| Time Analysis Report | WORK_SESSION |
| True Productivity Report | WORK_SESSION |
| Report Usage Report | REPORT_EXECUTED |

---

# 15. Summary

The Version 1 reporting strategy focuses exclusively on customer-required audit capabilities.

Supported reports:

- Access Audit Report
- Detailed Transcription Report
- Dictation Status History Report
- User Activity Audit Report
- Time Analysis Report
- True Productivity Report
- Report Usage Report

These reports are fully supported by the Version 1 audit event catalog and 
