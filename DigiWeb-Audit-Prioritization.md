# DigiWeb Audit Prioritization

## Objectif

Définir les priorités d'implémentation du système d'audit DigiWeb.

Cette priorisation vise à :

- Répondre aux exigences réglementaires et contractuelles
- Livrer rapidement de la valeur métier
- Réduire les risques projet
- Fournir une base solide pour les évolutions futures

---

# Must Have (v1)

## Description

Fonctionnalités requises pour la conformité, la traçabilité et les audits clients.

L'absence d'un de ces éléments compromettrait les objectifs du projet.

---

## Authentication

### Événements

- LoginSucceeded
- LoginFailed
- Logout
- SessionExpired

### Justification

Traçabilité des accès à la plateforme.

---

## Dictation Access Audit

### Événements

- DictationOpened
- DictationViewed

### Justification

Répond directement au besoin :

> Qui a consulté cette dictée ?

---

## Dictation Status History

### Événements

- DictationStatusChanged

### Justification

Historique complet des transitions.

---

## Transcription Audit

### Événements

- TranscriptionCreated
- TranscriptionModified
- TranscriptionReviewed
- TranscriptionApproved
- TranscriptionSigned
- TranscriptionRejected
- TranscriptionReturned

### Justification

Traçabilité complète des interventions sur une transcription.

---

## Advanced Productivity

### Événements

- TranscriptionTimeTracked
- ProductivityCalculated

### Justification

KPI de performance.

---
## Security & Permissions

### Événements

- RoleAssigned
- RoleRemoved
- PermissionChanged

### Justification

Audit des droits d'accès.

---

## Audio Retention

### Événements

- AudioPurged

### Justification

Audit des suppressions permanentes.

---

# Should Have (v1.1)

## Description

Fonctionnalités à forte valeur ajoutée mais non bloquantes pour la conformité.

---

## Audio Activity

### Événements

- PlaybackStarted
- PlaybackPaused
- PlaybackStopped

### Justification

Analyse comportementale et opérationnelle.

---

## Report Audit

### Événements

- ReportExecuted
- ReportExported

### Justification

Traçabilité de l'utilisation des rapports.

---

## Lock Management

### Événements

- LockAcquired
- LockReleased
- LockDenied

### Justification

Diagnostic et analyse des conflits utilisateurs.

---

## Audio Access

### Événements

- AudioDownloaded
- AudioDeleted

### Justification

Meilleur contrôle des accès aux fichiers.

---

# Nice To Have (Phase 2)

## Description

Fonctionnalités pouvant être ajoutées après la mise en production du système d'audit principal.

---

## Artificial Intelligence

### Événements

- AIAssistanceRequested
- AIAssistanceCompleted
- AIAssistanceFailed

### Justification

Statistiques d'utilisation IA.

---

## Advanced Analytics

### Solution

Microsoft Fabric

### Cas d'utilisation

- Power BI
- KPI opérationnels
- Analyse historique
- Tendances
- Détection d'anomalies

---

# MVP Scope

## Inclus dans la première version

### Audit Trail

- Authentication
- Dictation Access
- Dictation Status History
- Transcription Audit
- Permission Audit
- Audio Purge Audit

### Rapports

- Access Audit
- Detailed Transcription Audit
- Status History Audit
- Security Audit

---

# Hors périmètre MVP

- Microsoft Fabric
- Productivité avancée
- Analyse IA
- Playback détaillé
- Analytics avancés

---

# Validation

Cette priorisation est considérée valide lorsque :

- [ ] Les besoins métier sont confirmés
- [ ] Les besoins de conformité sont validés
- [ ] Les rapports MVP sont approuvés
- [ ] Le périmètre V1 est approuvé

---

# Décision

## MVP Audit DigiWeb

La première livraison doit permettre de répondre aux questions suivantes :

1. Qui s'est connecté ?
2. Qui a consulté une dictée ?
3. Qui a modifié une transcription ?
4. Quelle est la durée de la transcription.
4. Qui a changé un statut ?
5. Qui a modifié des permissions ?
6. Qui a supprimé un fichier audio ?

Si ces six questions peuvent être répondues de manière fiable via les rapports d'audit, le MVP est considéré comme réussi.