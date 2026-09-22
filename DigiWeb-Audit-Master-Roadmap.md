# DigiWeb Audit Trail & Reporting - Roadmap
#### Objectif
- Mettre en place un système d'audit centralisé dans DigiWeb/Synnefo offrant :
- Une traçabilité complète des actions utilisateur et système
- Les rapports d'audit requis par les clients
- Les capacités de conformité et de vérification réglementaire
- Une fondation pour l'analytique avancée (Phase 2)

---

## Phase 1 - Conception
### Étape 1 - Définir le modèle d'audit central
#### Objectif
Créer un modèle unique représentant tous les événements d'audit de la plateforme.
#### Livrable
Avant de développer les rapports, définissez l'événement d'audit standard qui servira à tout le système.  
# Priorisation des exigences

## Must Have (MVP / V1)

### Description

Fonctionnalités requises pour répondre aux exigences du RFP, aux besoins de traçabilité client et aux audits de conformité.

L'absence d'un de ces éléments compromettrait les objectifs du projet.

### Authentication Audit

#### Événements

- LoginSucceeded
- LoginFailed
- Logout
- SessionExpired

#### Justification

Permet d'identifier qui s'est connecté au système et quand.

---

### Dictation Access Audit

#### Événements

- DictationOpened
- DictationViewed

#### Justification

Répond directement à l'exigence :

> List of users who have accessed a dictation

---

### Dictation Status History

#### Événements

- DictationStatusChanged

#### Justification

Répond directement à l'exigence :

> Change of dictation status

---

### Transcription Audit

#### Événements

- TranscriptionCreated
- TranscriptionModified
- TranscriptionReviewed
- TranscriptionApproved
- TranscriptionSigned
- TranscriptionRejected
- TranscriptionReturned
- TranscriptionStatusChanged

#### Justification

Répond directement aux exigences :

> Users that have made changes to transcriptions

et

> Change of transcription status

---

### Productivity Timing Audit

#### Événements

- TranscriptionWorkStarted
- TranscriptionWorkStopped

#### Justification

Répond directement à l'exigence :

> Time spent transcribing by all transcriptionists who’ve worked on a dictation

---

### True Productivity Measures

#### Événements

- TranscriptionWorkStarted
- TranscriptionWorkStopped
- TranscriptionCreated
- TranscriptionReviewed
- TranscriptionApproved
- DictationStatusChanged

#### Justification

Répond directement à l'exigence :

> True productivity measures

---

### Security & Permissions Audit

#### Événements

- RoleAssigned
- RoleRemoved
- PermissionChanged
- PasswordReset
- AccountLocked

#### Justification

Répond aux besoins de conformité et d'investigation.

---

### Audio Purge Audit

#### Événements

- AudioPurged

#### Justification

Répond directement à l'exigence :

> Dictation Recording Purges

---

### Report Usage Audit

#### Événements

- ReportExecuted
- ReportExported

#### Justification

Répond directement à l'exigence :

> Running of a Report

---

## Should Have (V1.1)

### Description

Fonctionnalités apportant une valeur opérationnelle importante mais non requises pour satisfaire immédiatement le RFP.

---

### File Access Audit

#### Événements

- AudioDownloaded
- AudioDeleted

#### Justification

Améliore la traçabilité des accès aux fichiers.

---

### User Administration Audit

#### Événements

- UserCreated
- UserDisabled

#### Justification

Permet un meilleur suivi administratif.

---

### Dictation Lifecycle Audit

#### Événements

- DictationCreated
- DictationArchived

#### Justification

Historique complet du cycle de vie des dictées.

---

## Nice To Have (Phase 2)

### Description

Fonctionnalités utiles pour le support, les opérations avancées et l'analytique.

---

### Lock Management Audit

#### Événements

- LockAcquired
- LockReleased
- LockDenied

#### Justification

Diagnostic des conflits utilisateurs et des verrous orphelins.

---

### Audio Playback Audit

#### Événements

- PlaybackStarted
- PlaybackPaused
- PlaybackStopped

#### Justification

Analyse détaillée de l'utilisation audio.

---

### Speech Recognition Audit

#### Événements

- SpeechStarted
- SpeechStopped
- SpeechFailed

#### Justification

Analyse opérationnelle de la reconnaissance vocale.

---

### AI Usage Audit

#### Événements

- AIAssistanceRequested
- AIAssistanceCompleted
- AIAssistanceFailed

#### Justification

Mesure de l'utilisation et de la performance des fonctions IA.

---

### Advanced Analytics

#### Solution

Microsoft Fabric

#### Cas d'utilisation

- Power BI
- KPI opérationnels
- Analyse historique
- Analyse de tendances
- Analyse de productivité avancée
- Détection d'anomalies

#### Principe

L'Audit Trail DigiWeb demeure la source officielle des événements.

Microsoft Fabric agit comme une couche analytique complémentaire.

[Audit Data Model](./DigiWeb-Audit-Data-Model.md)
#### Critères de succès
- Tous les événements peuvent être représentés dans un format unique.
- Un seul mécanisme d'audit
- Une seule table principale
- Aucun rapport n'a besoin d'une structure spéciale.
### Étape 2 - Construire le catalogue des événements d'audit
#### Objectif
Lister tous les événements devant être capturés dans DigiWeb.  
[Audit Event Catalog](./DigiWeb-Audit-Event-Catalog.md)
#### Critère de succès
- Chaque événement possède une définition claire.
- Chaque événement indique les données à enregistrer.
### Étape 3 - Cartographier les fonctionnalités existantes
#### Objectif
Identifier ce qui est déjà audité et ce qui doit être ajouté.
#### Livrable
[Audit Coverage Matrix](./DigiWeb-Sudit-Coverage-Matrix.md)
#### Critères de succès
- Toutes les fonctionnalités DigiWeb sont couvertes.
- Les écarts sont clairement identifiés.
### Étape 4 - Prioriser les exigences
#### Objectif
Permettre une livraison progressive.
#### Livrable
[Audit Prioritization](./DigiWeb-Audit-Prioritization.md)
## Phase 2 - Rapports
### Étape 5 - Spécifier chaque rapport
#### Objectif
Définir précisément les rapports à développer.
#### Livrable
[Audit Report Specifications](DigiWeb-Audit-Reports-Specifications.md)
## Phase 3 - Architecture
### Étape 6 - Concevoir l'architecture d'audit
#### Objectif
Définir le mécanisme technique de collecte des événements.
#### Livrable
[Audit Trail Architecture](./DigiWeb-Audit-Trail-Architecture.md)
## Phase 4 - Gouvernance
### Étape 7 - Définir la politique de rétention
#### Objectif
Déterminer combien de temps les données doivent être conservées.
#### Livrable
Exemple  
| Catégorie      | Conservation |
| -------------- | ------------ |
| Audit système  | 7 ans        |
| Audit clinique | 7 ans        |
| Sécurité       | 7 ans        |
| Utilisation IA | 2 ans        |
#### Critères de succès
- Validation client obtenue.
- Conforme aux politiques de rétention.
## Phase 5 - Analytique avancée (optionnelle)
### Étape 8 - Intégration Microsoft Fabric
#### Objectif
Permettre l'analyse historique avancée sans impacter l'audit transactionnel.
#### Cas d'utilisation
- KPI opérationnels
- Power BI
- Productivité
- Tendances
- Analyse historique
- Détection d'anomalies
#### Principe fondamental
- L'Audit Trail DigiWeb demeure la source officielle des événements.
- Microsoft Fabric agit comme une couche analytique complémentaire.
