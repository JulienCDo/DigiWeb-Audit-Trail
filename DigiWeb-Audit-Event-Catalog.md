# DigiWeb Audit Event Catalog

## Objectif

Définir l'ensemble des événements d'audit qui doivent être enregistrés par DigiWeb/Synnefo.

Chaque événement doit :

- Posséder une définition claire
- Être associé à une catégorie
- Indiquer les données minimales à enregistrer
- Répondre à un besoin métier, réglementaire ou opérationnel
- Pouvoir être exploité par un ou plusieurs rapports d'audit

---

# Authentication

## Login

### Description

Authentification d'un utilisateur.

### Category

Authentication

### EntityType

User

### Outcome

Success/Failure

### Severity

Info

### Données minimales requises

- UserId
- UserName
- UserRole
- TenantId
- SessionId
- IpAddress

### Utilisé dans

- Security Audit Report
- User Activity Audit

---

## Logout

### Description

Déconnexion utilisateur.

### Category

Authentication

### EntityType

User

### Outcome

Success

### Severity

Info

### Données minimales requises

- UserId
- UserName
- SessionId

### Utilisé dans

- User Activity Audit

---

# Dictation

## DictationAccessed

### Description

Un utilisateur ouvre une dictée.

### Category

Dictation

### EntityType

Dictation

### Outcome

Success

### Severity

Info

### Données minimales requises

- DictationId
- UserId
- UserName
- UserRole

### Utilisé dans

- Access Audit Report

---

## DictationPurged

### Description

Archivage d'une dictée.

### Category

Dictation

### EntityType

Dictation

### Outcome

Success

### Severity

Info

### Données minimales requises

- DictationId
- UserId
- UserName

### Utilisé dans

- Dictation Status History Report

---

# Transcription

## TranscriptionCreated

### Description

Création d'une transcription.

### Category

Transcription

### EntityType

Transcription

### Outcome

Success

### Severity

Info

### Utilisé dans

- Transcription Detailed Report

---

## TranscriptionModified

### Description

Modification du contenu d'une transcription.

### Category

Transcription

### EntityType

Transcription

### Outcome

Success

### Severity

Info

### Details

```json
{
  "version": 3,
  "changeType": "Correction"
}
```

### Utilisé dans

- Transcription Detailed Report

---

## TranscriptionStatusChanged

### Description

Changement de statut d'une transcription.

### Category

Transcription

### EntityType

Transcription

### Outcome

Success

### Severity

Info

### Details

```json
{
  "oldStatus": "Draft",
  "newStatus": "Reviewed"
}
```

### Utilisé dans

- Transcription Detailed Report
- User Activity Audit

---

## TranscriptionReviewed

### Description

Révision effectuée sur une transcription.

### Category

Transcription

### EntityType

Transcription

### Outcome

Success

### Severity

Info

### Utilisé dans

- Transcription Detailed Report

---

## TranscriptionApproved

### Description

Transcription approuvée.

### Category

Transcription

### EntityType

Transcription

### Outcome

Success

### Severity

Info

### Utilisé dans

- Transcription Detailed Report

---

## TranscriptionSigned

### Description

Transcription signée.

### Category

Transcription

### EntityType

Transcription

### Outcome

Success

### Severity

Info

### Utilisé dans

- Transcription Detailed Report

---

## TranscriptionRejected

### Description

Transcription rejetée.

### Category

Transcription

### EntityType

Transcription

### Outcome

Success

### Severity

Warning

### Utilisé dans

- Transcription Detailed Report

---

## TranscriptionReturned

### Description

Transcription retournée pour correction.

### Category

Transcription

### EntityType

Transcription

### Outcome

Success

### Severity

Info

### Utilisé dans

- Transcription Detailed Report

---

# Productivity

## TranscriptionWorkStarted

### Description

Début d'une période active de transcription.

### Category

Transcription

### EntityType

Dictation

### Outcome

Success

### Severity

Info

### Données minimales requises

- DictationId
- UserId
- UserName
- UserRole

### Utilisé dans

- Time Analysis Report
- True Productivity Report

---

## TranscriptionWorkStopped

### Description

Fin d'une période active de transcription.

### Category

Transcription

### EntityType

Dictation

### Outcome

Success

### Severity

Info

### Details

```json
{
  "durationSeconds": 1200
}
```

### Utilisé dans

- Time Analysis Report
- True Productivity Report

---

## ProductivityCalculated

### Description

Calcul d'indicateurs de productivité.

### Category

Reporting

### EntityType

Productivity

### Outcome

Success

### Severity

Info

### Utilisé dans

- True Productivity Report

---

# Audio

## PlaybackStarted

### Description

Début de lecture audio.

### Category

Audio

### EntityType

AudioFile

### Outcome

Success

### Severity

Info

### Utilisé dans

- User Activity Audit

---

## PlaybackPaused

### Description

Pause de lecture audio.

### Category

Audio

### EntityType

AudioFile

### Outcome

Success

### Severity

Info

---

## PlaybackStopped

### Description

Arrêt de lecture audio.

### Category

Audio

### EntityType

AudioFile

### Outcome

Success

### Severity

Info

---

## AudioDownloaded

### Description

Téléchargement d'un fichier audio.

### Category

Audio

### EntityType

AudioFile

### Outcome

Success

### Severity

Warning

### Utilisé dans

- File Access Audit Report

---

## AudioDeleted

### Description

Suppression d'un fichier audio.

### Category

Audio

### EntityType

AudioFile

### Outcome

Success

### Severity

Warning

### Utilisé dans

- File Access Audit Report

---

## AudioPurged

### Description

Suppression définitive d'un fichier audio.

### Category

Audio

### EntityType

AudioFile

### Outcome

Success

### Severity

Critical

### Details

```json
{
  "purgeType": "Manual",
  "reason": "Retention Policy"
}
```

### Utilisé dans

- File Access Audit Report

---

# Reporting

## ReportExecuted

### Description

Exécution d'un rapport.

### Category

Reporting

### EntityType

Report

### Outcome

Success

### Severity

Info

### Details

```json
{
  "executionDurationMs": 1523,
  "parameters": {}
}
```

### Utilisé dans

- Report Usage Audit Report

---

## ReportExported

### Description

Export d'un rapport.

### Category

Reporting

### EntityType

Report

### Outcome

Success

### Severity

Info

### Utilisé dans

- Report Usage Audit Report

---

# Administration

## UserCreated

### Description

Création d'un utilisateur.

### Category

Administration

### EntityType

User

### Outcome

Success

### Severity

Info

---

## UserDisabled

### Description

Désactivation d'un utilisateur.

### Category

Administration

### EntityType

User

### Outcome

Success

### Severity

Warning

---

## RoleAssigned

### Description

Attribution d'un rôle.

### Category

Administration

### EntityType

Role

### Outcome

Success

### Severity

Warning

### Utilisé dans

- Security Audit Report

---

## RoleRemoved

### Description

Retrait d'un rôle.

### Category

Administration

### EntityType

Role

### Outcome

Success

### Severity

Warning

### Utilisé dans

- Security Audit Report

---

## PermissionChanged

### Description

Modification d'une permission.

### Category

Administration

### EntityType

Permission

### Outcome

Success

### Severity

Critical

### Utilisé dans

- Security Audit Report

---

# Lock Management

## LockAcquired

### Description

Obtention d'un verrou.

### Category

System

### EntityType

Dictation

### Outcome

Success

### Severity

Info

---

## LockReleased

### Description

Libération d'un verrou.

### Category

System

### EntityType

Dictation

### Outcome

Success

### Severity

Info

---

## LockDenied

### Description

Refus d'obtention d'un verrou.

### Category

System

### EntityType

Dictation

### Outcome

Denied

### Severity

Warning

---

# Speech Recognition

## SpeechStarted

### Description

Démarrage de la reconnaissance vocale.

### Category

AI

### EntityType

Dictation

### Outcome

Success

### Severity

Info

---

## SpeechStopped

### Description

Arrêt de la reconnaissance vocale.

### Category

AI

### EntityType

Dictation

### Outcome

Success

### Severity

Info

---

## SpeechFailed

### Description

Échec de la reconnaissance vocale.

### Category

AI

### EntityType

Dictation

### Outcome

Failed

### Severity

Warning

---

# AI

## AIAssistanceRequested

### Description

Demande d'assistance IA.

### Category

AI

### EntityType

Transcription

### Outcome

Success

### Severity

Info

### Utilisé dans

- AI Usage Audit Report

---

## AIAssistanceCompleted

### Description

Assistance IA complétée avec succès.

### Category

AI

### EntityType

Transcription

### Outcome

Success

### Severity

Info

### Utilisé dans

- AI Usage Audit Report

---

## AIAssistanceFailed

### Description

Échec d'une opération d'assistance IA.

### Category

AI

### EntityType

Transcription

### Outcome

Failed

### Severity

Warning

### Utilisé dans

- AI Usage Audit Report
