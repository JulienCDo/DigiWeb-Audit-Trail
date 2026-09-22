# DigiWeb Audit Report Specifications

## Objectif

Définir précisément les rapports d'audit disponibles dans DigiWeb/Synnefo.

Chaque rapport doit documenter :

- Son objectif
- Les événements utilisés
- Les filtres disponibles
- Les colonnes affichées
- Les formats d'export
- Les permissions requises
- Des exemples de résultats

---

# Access Audit Report

## Description

Permet d'identifier tous les utilisateurs ayant consulté une dictée.

## Événements utilisés

- DictationOpened
- DictationViewed

## Filtres

- Date début
- Date fin
- DictationId
- Utilisateur
- UserRole
- Site
- Département

## Colonnes

- Date/Heure
- DictationId
- UserId
- UserName
- UserRole
- Workstation
- IpAddress
- Action

## Exemple

| Date/Heure | DictationId | Utilisateur | Rôle | Action | IP |
|------------|------------|------------|------------|------------|------------|
| 2026-09-22 09:01 | D-100345 | Jane Doe | Transcriptionist | Opened | 10.10.10.1 |
| 2026-09-22 09:02 | D-100345 | John Smith | Reviewer | Viewed | 10.10.10.2 |

## Export

- CSV
- Excel
- PDF (optionnel)

## Sécurité

- Supervisor
- Administrator

## Cas d'utilisation

- Enquête de confidentialité
- Audit clinique
- Vérification des accès non autorisés

---

# Transcription Detailed Report

## Description

Permet d'identifier tous les utilisateurs ayant créé, modifié, révisé ou approuvé une transcription.

## Événements utilisés

- TranscriptionCreated
- TranscriptionModified
- TranscriptionReviewed
- TranscriptionApproved
- TranscriptionSigned
- TranscriptionRejected
- TranscriptionReturned
- TranscriptionStatusChanged

## Filtres

- Date début
- Date fin
- TranscriptionId
- DictationId
- Utilisateur
- UserRole

## Colonnes

- Date/Heure
- TranscriptionId
- DictationId
- UserId
- UserName
- UserRole
- Action
- Version
- Outcome

## Exemple

| Date/Heure | TranscriptionId | Utilisateur | Rôle | Action | Version |
|------------|------------|------------|------------|------------|------------|
| 09:12 | T-2001 | Jane Doe | Transcriptionist | Modified | 3 |
| 09:20 | T-2001 | Bob Martin | Reviewer | Reviewed | 3 |
| 09:25 | T-2001 | Dr Smith | Physician | Signed | 3 |

## Export

- CSV
- Excel
- PDF (optionnel)

## Sécurité

- Supervisor
- Administrator

## Cas d'utilisation

- Contrôle qualité
- Historique des corrections
- Vérification des responsabilités

---

# Dictation Status History Report

## Description

Historique complet des changements de statut des dictées.

## Événements utilisés

- DictationStatusChanged

## Filtres

- Date début
- Date fin
- DictationId
- Utilisateur

## Colonnes

- Date/Heure
- DictationId
- Ancien statut
- Nouveau statut
- Utilisateur
- UserRole

## Exemple

| Date/Heure | DictationId | Ancien statut | Nouveau statut | Utilisateur |
|------------|------------|------------|------------|------------|
| 09:00 | D-100345 | Reserved | Busy | Jane Doe |
| 09:25 | D-100345 | Busy | Completed | Jane Doe |

## Export

- CSV
- Excel

## Sécurité

- Supervisor
- Administrator

## Cas d'utilisation

- Audit opérationnel
- Analyse des processus
- Vérification des SLA

---

# Security Audit Report

## Description

Historique des événements liés à la sécurité du système.

## Événements utilisés

- LoginSucceeded
- LoginFailed
- Logout
- SessionExpired
- PasswordReset
- AccountLocked
- RoleAssigned
- RoleRemoved
- PermissionChanged

## Filtres

- Date début
- Date fin
- Utilisateur
- Adresse IP
- Outcome
- Severity

## Colonnes

- Date/Heure
- Utilisateur
- UserRole
- Adresse IP
- Événement
- Outcome
- Severity

## Exemple

| Date/Heure | Utilisateur | Événement | Résultat | IP |
|------------|------------|------------|------------|------------|
| 08:00 | Jane Doe | LoginSucceeded | Success | 10.10.10.1 |
| 08:05 | John Smith | LoginFailed | Failed | 10.10.10.2 |
| 09:00 | Administrator | PermissionChanged | Success | 10.10.10.10 |

## Export

- CSV
- Excel
- PDF (optionnel)

## Sécurité

- Administrator

## Cas d'utilisation

- Conformité
- Investigation
- Audit de sécurité
- Analyse des incidents

---

# File Access Audit Report

## Description

Historique des accès et manipulations des fichiers audio.

## Événements utilisés

- PlaybackStarted
- AudioDownloaded
- AudioDeleted
- AudioPurged

## Filtres

- Date début
- Date fin
- DictationId
- Utilisateur

## Colonnes

- Date/Heure
- DictationId
- Utilisateur
- UserRole
- Action
- Outcome

## Exemple

| Date/Heure | Fichier | Utilisateur | Action |
|------------|------------|------------|------------|
| 09:00 | Audio-100.mp3 | Jane Doe | Downloaded |
| 09:10 | Audio-100.mp3 | Jane Doe | Playback |
| 09:20 | Audio-100.mp3 | Administrator | Purged |

## Export

- CSV
- Excel

## Sécurité

- Supervisor
- Administrator

## Cas d'utilisation

- Audit de confidentialité
- Vérification des suppressions
- Investigation des accès aux fichiers

---

# Time Analysis Report

## Description

Permet de mesurer le temps réellement consacré à une dictée par chacun des intervenants.

## Événements utilisés

- TranscriptionWorkStarted
- TranscriptionWorkStopped

## Filtres

- Date début
- Date fin
- DictationId
- Utilisateur
- UserRole
- Site
- Département

## Colonnes

- DictationId
- UserId
- UserName
- UserRole
- StartTime
- StopTime
- Duration

## Exemple

| Utilisateur | Début | Fin | Temps |
|------------|------------|------------|------------|
| Jane Doe | 09:00 | 09:18 | 18 min |
| Bob Martin | 09:20 | 09:27 | 7 min |
| John Smith | 09:30 | 09:34 | 4 min |

## Résumé

| DictationId | Temps total |
|------------|------------|
| D-100345 | 29 min |

## Export

- CSV
- Excel

## Sécurité

- Supervisor
- Administrator

## Cas d'utilisation

- Analyse de productivité
- Répartition de charge de travail
- Vérification des temps de traitement

---

# True Productivity Report

## Description

Mesure la productivité réelle des transcriptionnistes.

## Événements utilisés

- TranscriptionCreated
- TranscriptionModified
- TranscriptionReviewed
- TranscriptionApproved
- TranscriptionSigned
- TranscriptionWorkStarted
- TranscriptionWorkStopped
- DictationStatusChanged

## Filtres

- Période
- Utilisateur
- Équipe
- Site
- Département

## Indicateurs

- Dictées complétées
- Documents complétés
- Temps moyen de transcription
- Temps moyen de révision
- Temps moyen de traitement
- Minutes transcrites par heure
- Volume traité par utilisateur
- Productivité par équipe
- Productivité par site

## Exemple

| Utilisateur | Dictées complétées | Temps total | Temps moyen | Productivité |
|------------|------------|------------|------------|------------|
| Jane Doe | 42 | 8h15 | 11.8 min | 5.1 dictées/h |
| Bob Martin | 31 | 7h50 | 15.2 min | 3.9 dictées/h |

## Export

- CSV
- Excel
- PDF (optionnel)

## Sécurité

- Supervisor
- Administrator

## Cas d'utilisation

- Gestion des opérations
- Analyse des performances
- Optimisation des processus
- Suivi des SLA

---

# AI Usage Audit Report

## Description

Historique de l'utilisation des fonctionnalités d'assistance IA.

## Événements utilisés

- AIAssistanceRequested
- AIAssistanceCompleted
- AIAssistanceFailed

## Filtres

- Date début
- Date fin
- Utilisateur
- Outcome

## Colonnes

- Date/Heure
- Utilisateur
- UserRole
- Outcome
- Durée du traitement

## Exemple

| Date/Heure | Utilisateur | Résultat | Durée |
|------------|------------|------------|------------|
| 10:00 | Jane Doe | Success | 4 sec |
| 10:10 | Bob Martin | Failed | 2 sec |

## Export

- CSV
- Excel

## Sécurité

- Supervisor
- Administrator

## Cas d'utilisation

- Adoption des fonctionnalités IA
- Analyse des performances
- Contrôle des coûts

## Note

Aucune donnée clinique ou sensible ne doit être enregistrée dans les événements d'audit IA.

---

# Report Usage Audit Report

## Description

Permet d'identifier quels rapports sont exécutés dans le système et par quels utilisateurs.

## Événements utilisés

- ReportExecuted
- ReportExported

## Filtres

- Date début
- Date fin
- Utilisateur
- Nom du rapport

## Colonnes

- Date/Heure
- Utilisateur
- UserRole
- Nom du rapport
- Paramètres utilisés
- Durée d'exécution
- Action

## Exemple

| Date/Heure | Utilisateur | Rapport | Durée | Export |
|------------|------------|------------|------------|------------|
| 10:00 | Supervisor | Access Audit | 1.2 sec | CSV |
| 10:05 | Administrator | Productivity Report | 3.1 sec | Excel |

## Export

- CSV
- Excel

## Sécurité

- Administrator

## Cas d'utilisation

- Audit des rapports
- Analyse d'utilisation
- Optimisation des performances

---

# Mapping des exigences RFP

| Exigence | Rapport |
|-----------|----------|
| List of users who have accessed a dictation | Access Audit Report |
| Users that have made changes to transcriptions | Transcription Detailed Report |
| Time spent transcribing by all transcriptionists | Time Analysis Report |
| True Productivity Measures | True Productivity Report |
| User log on/off | Security Audit Report |
| Change of dictation status | Dictation Status History Report |
| Change of transcription status | Transcription Detailed Report |
| Dictation recording purges | File Access Audit Report |
| Running of a report | Report Usage Audit Report |
| Additional audit report variables | Security Audit Report, File Access Audit Report, AI Usage Audit Report |

---

# Critères de succès

Le document est considéré complet lorsque :

- Chaque rapport possède une définition claire
- Chaque rapport indique les événements utilisés
- Les filtres sont définis
- Les colonnes sont définies
- Les formats d'export sont définis
- Les permissions de consultation sont définies
- Toutes les exigences du RFP sont couvertes
- Les développeurs peuvent implémenter directement les rapports
