# DigiWeb Audit Coverage Matrix

## Purpose

Identify all DigiWeb business functions that require audit event generation.

This matrix is used to:

- Validate audit coverage across the application
- Identify compliance gaps
- Estimate development effort
- Prioritize implementation work
- Ensure all required audit reports can be generated
- Maintain a minimal and business-focused audit footprint

---

# Audit Coverage Status

| Business Function | Existing Audit | Required Event | Priority |
|------------------|----------------|----------------|----------|
| User Authentication | No | LOGIN | High |
| User Logout | No | LOGOUT | High |
| Dictation Access | No | DICTATION_ACCESSED | High |
| Dictation Status Change | No | DICTATION_STATUS_CHANGED | High |
| Dictation Recording Purge | No | DICTATION_PURGED | High |
| Transcription Modification | No | TRANSCRIPTION_MODIFIED | High |
| Transcription Status Change | No | TRANSCRIPTION_STATUS_CHANGED | High |
| Report Execution | No | REPORT_EXECUTED | Medium |
| Transcription Work Tracking | No | WORK_SESSION | High |

---

# Reporting Coverage

## Access Audit Report

**Business Question**

Who accessed a specific dictation?

| Event |
|---------|
| DICTATION_ACCESSED |

---

## Transcription Detailed Report

**Business Question**

Who modified a transcription?

| Event |
|---------|
| TRANSCRIPTION_MODIFIED |

---

## Dictation Status History Report

**Business Question**

How did a dictation move through its lifecycle?

| Event |
|---------|
| DICTATION_STATUS_CHANGED |

---

## User Activity Audit Report

**Business Questions**

- Who logged in?
- Who logged out?
- Who changed statuses?
- Who purged recordings?
- Who ran reports?

| Event |
|---------|
| LOGIN |
| LOGOUT |
| DICTATION_STATUS_CHANGED |
| TRANSCRIPTION_STATUS_CHANGED |
| DICTATION_PURGED |
| REPORT_EXECUTED |

---

## Time Analysis Report

**Business Question**

How much time did each transcriptionist spend working on a dictation?

| Event |
|---------|
| WORK_SESSION |

---

## True Productivity Report

**Business Question**

What productive transcription work was completed during a period?

| Event |
|---------|
| WORK_SESSION |

---

## Report Usage Audit

**Business Question**

Who executed audit or management reports?

| Event |
|---------|
| REPORT_EXECUTED |

---

# Customer Requirement Mapping

| Customer Requirement | Audit Event |
|---------------------|-------------|
| User log on | LOGIN |
| User log off | LOGOUT |
| List of users who accessed a dictation | DICTATION_ACCESSED |
| Users who modified transcriptions | TRANSCRIPTION_MODIFIED |
| Change of dictation status | DICTATION_STATUS_CHANGED |
| Change of transcription status | TRANSCRIPTION_STATUS_CHANGED |
| Dictation recording purges | DICTATION_PURGED |
| Running of a report | REPORT_EXECUTED |
| Time spent transcribing | WORK_SESSION |
| True productivity measures | WORK_SESSION |

---

# Out-of-Scope Events (Version 1)

The following event categories are intentionally excluded from the initial implementation because they do not directly support current customer audit requirements.

## User Interface Events

- Button Clicked
- Menu Opened
- Screen Navigated
- Tab Selected
- Filter Changed

## Audio Activity Events

- Playback Started
- Playback Paused
- Playback Stopped
- Audio Downloaded

## Administrative Events

- User Created
- User Disabled
- Role Assigned
- Role Removed
- Permission Changed

## Technical Events

- Session Expired
- Lock Acquired
- Lock Released
- Lock Denied

## AI-related Events

- AI Assistance Requested
- AI Assistance Completed
- AI Assistance Failed

These events may be added in future releases if justified by business, compliance, or customer requirements.

---

# Gap Analysis

## Must Have (Customer Requirements)

- [ ] LOGIN
- [ ] LOGOUT
- [ ] DICTATION_ACCESSED
- [ ] DICTATION_STATUS_CHANGED
- [ ] DICTATION_PURGED
- [ ] TRANSCRIPTION_MODIFIED
- [ ] TRANSCRIPTION_STATUS_CHANGED
- [ ] WORK_SESSION
- [ ] REPORT_EXECUTED

### Goal

Deliver all customer-requested audit and reporting capabilities with the minimum viable audit model.

---

## Should Have (Future Releases)

- [ ] Audio Access Auditing
- [ ] Administrative Activity Auditing
- [ ] Audit Report Export Tracking
- [ ] Security Administration Auditing

### Goal

Improve operational traceability and administrative oversight.

---

## Nice To Have (Future Analytics)

- [ ] AI Usage Auditing
- [ ] Cross-Application Analytics
- [ ] Microsoft Fabric Integration
- [ ] Long-Term Historical Analytics

### Goal

Extend reporting and analytical capabilities once production usage patterns and volumes are better understood.

---

# Summary

## Audit Model Scope

The Version 1 audit platform intentionally limits itself to:

- 9 audit events
- Azure Cosmos DB as the single source of truth
- Multi-tenant support
- Customer-required reporting scenarios only

## Applications Supported

- DigiWeb
- DigiConsole
- Future Applications

## Status

- [x] Functional audit coverage defined
- [x] Event catalog simplified
- [x] Customer requirements mapped
- [x] Storage model simplified (Cosmos DB)
- [x] Multi-tenant requirements addressed
- [ ] Architecture implementation ready
- [ ] Development planning complete
