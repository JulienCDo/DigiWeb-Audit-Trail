# DigiWeb Audit Reports & User Activity Audit

## Contexte

Afin de répondre aux exigences de traçabilité et d'audit du client, DigiWeb doit offrir des capacités équivalentes ou supérieures à celles actuellement disponibles dans DigiConsole et Med Console.

L'objectif est de permettre aux superviseurs d'accéder à des rapports d'audit détaillés ainsi qu'à un historique complet des activités utilisateurs et système.

> Microsoft Fabric pourra être envisagé comme une phase 2 pour l'analytique avancée, les tableaux de bord et l'historisation long terme.
>
> L'audit transactionnel doit demeurer la source officielle dans la plateforme DigiWeb/Synnefo.

---

# Objectifs fonctionnels

Le système d'audit DigiWeb doit permettre :

- De retracer toutes les actions utilisateur significatives
- De reconstruire l'historique complet d'une dictée
- De reconstruire l'historique complet d'une transcription
- D'identifier les utilisateurs impliqués dans un processus
- De mesurer les performances et la productivité
- De répondre aux exigences de conformité et d'audit du client
- D'offrir des rapports d'audit exportables
- De fournir une source fiable de traçabilité pour les enquêtes et vérifications réglementaires

---

# Audit Reports

## AR-001 - Liste des utilisateurs ayant accédé à une dictée

### Description

Permet d'identifier tous les utilisateurs ayant consulté une dictée donnée.

### Données enregistrées

- DictationId
- UserId
- UserName
- UserRole
- Date/Heure
- Action (Open/View)
- Poste de travail (si disponible)
- Adresse IP (si autorisée par les politiques du client)

### Rapport DigiWeb

- Access Audit Report

### Cas d'utilisation

- Enquête de confidentialité
- Audit clinique
- Vérification des accès non autorisés

---

## AR-002 - Utilisateurs ayant modifié une transcription

### Description

Permet d'identifier tous les intervenants ayant apporté des modifications à une transcription.

### Données enregistrées

- TranscriptionId
- UserId
- UserName
- UserRole
- Date/Heure
- Type de modification
- Version

### Actions suivies

- Create
- Modify
- Correct
- Review
- Approve
- Sign
- Reject
- Return

### Rapport DigiWeb

- Transcription Detailed Report

### Cas d'utilisation

- Contrôle qualité
- Historique des corrections
- Vérification des responsabilités

---

## AR-003 - Temps passé à transcrire par tous les transcriptionnistes

### Description

Permet de mesurer le temps réellement consacré à une dictée par chacun des intervenants.

### Données enregistrées

- DictationId
- UserId
- UserName
- UserRole
- StartTime
- StopTime
- Duration

### Exemple

- Jane Doe : 18 minutes
- Bob Martin : 7 minutes
- John Smith : 4 minutes

### Rapport DigiWeb

- Time Analysis Report

### Cas d'utilisation

- Analyse de productivité
- Gestion des charges de travail
- Allocation des ressources
- Mesure des temps de traitement

### Événements requis

- TranscriptionWorkStarted
- TranscriptionWorkStopped

---

## AR-004 - True Productivity Measures

### Description

Permet de mesurer la productivité réelle des transcriptionnistes.

### Indicateurs requis

- Dictées complétées
- Documents complétés
- Temps moyen de transcription
- Temps moyen de révision
- Délai moyen de traitement
- Minutes transcrites par heure
- Volume traité par utilisateur
- Productivité par équipe
- Productivité par site

### Rapport DigiWeb

- True Productivity Report

### Cas d'utilisation

- Évaluation opérationnelle
- Optimisation des processus
- Suivi des performances
- Analyse des tendances
- Gestion des SLA

### Événements requis

- TranscriptionWorkStarted
- TranscriptionWorkStopped
- TranscriptionCreated
- TranscriptionReviewed
- TranscriptionApproved
- DictationStatusChanged

---

## AR-005 - Rapports d'audit additionnels

### Historique des statuts de dictée

#### Description

Permet de suivre toutes les transitions de statut d'une dictée.

#### Statuts suivis

- New
- Reserved
- Busy
- Dictating
- ToTranscribe
- Completed
- Archived

#### Informations enregistrées

- Ancien statut
- Nouveau statut
- Utilisateur
- Date/Heure

#### Rapport DigiWeb

- Dictation Status History Report

---

### Rapport de sécurité

#### Événements suivis

- Échecs d'authentification
- Déconnexions
- Expiration de session
- Verrouillage de compte
- Réinitialisation de mot de passe
- Modification des permissions
- Attribution de rôle
- Retrait de rôle

#### Rapport DigiWeb

- Security Audit Report

---

### Rapport des accès aux fichiers

#### Événements suivis

- Lecture audio
- Téléchargement audio
- Export
- Suppression
- Purge

#### Rapport DigiWeb

- File Access Audit Report

---

### Rapport d'utilisation de l'assistance IA

#### Événements suivis

- Utilisateur
- Date/Heure
- Succès / échec
- Durée de traitement

#### Rapport DigiWeb

- AI Usage Audit Report

#### Contraintes

Aucune donnée sensible ou clinique ne doit être enregistrée inutilement.

---

# User Activity Audit

## Principe

Tous les événements d'audit doivent être centralisés dans un service unique afin de faciliter :

- La traçabilité
- Les rapports
- Les enquêtes
- Les vérifications réglementaires
- Les analyses opérationnelles

---

## UA-001 - Connexion / Déconnexion utilisateur

### Événements enregistrés

- LoginSucceeded
- LoginFailed
- Logout
- SessionExpired

### Informations

- Utilisateur
- UserRole
- Date/Heure
- Application
- Poste de travail
- Adresse IP (si disponible)

---

## UA-002 - Changement de statut d'une dictée

### Événements enregistrés

- DictationStatusChanged

### Informations

- Ancien statut
- Nouveau statut
- Utilisateur
- Date/Heure

### Statuts concernés

- Reserved
- Busy
- Dictating
- ToTranscribe
- Completed
- Archived

---

## UA-003 - Changement de statut d'une transcription

### Événements enregistrés

- Draft
- Editing
- Reviewed
- Approved
- Signed
- Returned
- Rejected

### Informations

- Ancien statut
- Nouveau statut
- Utilisateur
- Date/Heure

---

## UA-004 - Purge des enregistrements audio

### Événements enregistrés

- Manual Purge
- Retention Purge
- System Purge

### Informations

- DictationId
- Fichier audio
- Utilisateur
- Motif
- Date/Heure

---

## UA-005 - Exécution de rapports

### Événements enregistrés

- ReportExecuted
- ReportExported

### Informations

- Rapport exécuté
- Paramètres utilisés
- Utilisateur
- Date/Heure
- Durée d'exécution

### Rapports concernés

- Access Audit
- True Productivity Report
- Time Analysis Report
- Transcription Detailed Report
- Security Audit
- Rapports personnalisés

---

## UA-006 - Activités additionnelles recommandées

### Consultation d'une dictée

- DictationOpened
- DictationViewed

### Gestion des verrous

- LockAcquired
- LockReleased
- LockDenied

### Activités audio

- PlaybackStarted
- PlaybackPaused
- PlaybackStopped

### Reconnaissance vocale

- SpeechStarted
- SpeechStopped
- SpeechFailed

### Gestion des permissions

- RoleAssigned
- RoleRemoved
- PermissionChanged

### Activités IA

- AIAssistanceRequested
- AIAssistanceCompleted
- AIAssistanceFailed

---

# Exigences non fonctionnelles

## Performance

L'enregistrement d'un événement d'audit ne doit pas ralentir de manière perceptible les opérations courantes de DigiWeb.

## Intégrité

Les données d'audit doivent être immuables une fois enregistrées.

## Traçabilité

Chaque événement doit être horodaté et associé à un utilisateur ou à un processus système.

## Disponibilité

Les données d'audit doivent rester consultables même en cas d'évolution de l'application.

## Export

Les rapports doivent pouvoir être exportés au minimum en :

- CSV
- Excel

## Sécurité

L'accès aux rapports doit être contrôlé selon les rôles utilisateur.

## Conservation

Les données d'audit doivent respecter les politiques de rétention définies par le client.

---

# Recommandation d'architecture

## Phase 1

Implémenter un Audit Trail centralisé dans la plateforme DigiWeb/Synnefo.

### Objectifs

- Source officielle des événements
- Historique complet
- Rapports opérationnels
- Conformité et traçabilité
- Audit transactionnel unifié

---

## Phase 2 (Optionnelle)

Intégration Microsoft Fabric pour :

- Conservation historique long terme
