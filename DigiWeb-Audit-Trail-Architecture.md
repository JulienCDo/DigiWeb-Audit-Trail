# DigiWeb Audit Trail Architecture

## Objectif

Définir l'architecture technique permettant :

- La capture des événements d'audit
- Le stockage centralisé des événements
- La consultation des événements
- La génération des rapports
- La conformité et la traçabilité
- L'intégration future avec Microsoft Fabric

## Principes

### Source unique de vérité

Tous les événements sont enregistrés dans un système d'audit centralisé.

### Modèle unifié

Tous les événements utilisent la structure AuditEvent.

### Immutabilité

Les événements d'audit ne peuvent pas être modifiés après leur création.

### Faible impact

L'audit ne doit pas ralentir significativement les opérations DigiWeb.

### Extensibilité

Le système doit supporter de nouveaux événements sans modification majeure.

## Architecture cible

```text
Applications

 ├── DigiWeb
 ├── DigiConsole
 └── Future Apps

          │

          ▼

     Audit gRPC API

          ▼

      Audit Service

          ▼

    Internal Queue

          ▼

     Storage Layer

       ├── SQL Index
       └── Blob Storage

                ▼

          Microsoft Fabric
```

## Audit Platform

La plateforme d'audit centralisée est composée des éléments suivants :

- Audit gRPC API
- Audit Service
- Audit Repository
- Audit Index
- Audit Archive
- Reporting Layer

### Interface
```
IAuditService
{
    Task LogAsync(AuditEvent auditEvent);
}
```

### Utilisation
```
await auditService.LogAsync(
    AuditEventFactory.DictationViewed(...)
);
```
## Audit gRPC API

La plateforme d'audit expose un service gRPC utilisé par les applications consommatrices.

Applications supportées :

- DigiWeb
- DigiConsole
- Applications futures

Responsabilités :

- Authentifier les applications
- Valider les événements reçus
- Transmettre les événements au pipeline de traitement

L'utilisation de gRPC assure une cohérence avec l'architecture actuelle des services backend DigiWeb.

## Audit Repository

Responsable de la persistance des événements d'audit.

Fonctions :

- Insert
- GetById
- Search

### Interface
```
IAuditRepository
{
    Task InsertAsync(AuditEvent auditEvent);

    Task<SearchResult<AuditEvent>> SearchAsync(...);
}
```

## Reporting Layer

Responsable de :

- Générer les rapports d'audit
- Exécuter les recherches
- Fournir les données aux interfaces utilisateur
- Préparer les futures intégrations analytiques

Exemples :

- Access Audit Report
- Security Audit Report
- Audio Access Audit Report
- Time Analysis Report
- True Productivity Report


## Storage 
Voir:
[À valider](./DigiWeb-Audit-Storage-Strategy.md)

Principe actuel :

- Azure Blob Storage comme source officielle de conservation
- Index SQL léger pour les recherches opérationnelles
- Préparation pour Microsoft Fabric

## Security Model

### Publication

Seules les applications approuvées peuvent publier des événements.

Applications autorisées :

- DigiWeb
- DigiConsole
- Applications futures

L'authentification s'effectue au niveau applicatif.

### Consultation

Rôles autorisés :

- Administrator
- Supervisor

### Modification

Les événements d'audit sont immuables.

Aucune modification manuelle n'est autorisée.

### Suppression

Seules les politiques de rétention approuvées peuvent supprimer des événements.

## Audit Event Processing Flow

### Objectif

Définir le cycle de vie complet d'un événement d'audit depuis sa création par une application jusqu'à sa conservation finale.

Cette architecture vise à :

- Minimiser l'impact sur les applications consommatrices
- Garantir la cohérence des données
- Assurer la résilience du système
- Préparer l'évolutivité future de la plateforme

---

### Flux logique

```text
Utilisateur
      │
      ▼
Application
(DigiWeb / DigiConsole)
      │
      ▼
Audit gRPC API
      │
      ▼
Audit Service
      │
      ▼
Internal Queue
      │
      ▼
Audit Processing Worker
      │
      ├── Azure Blob Storage
      │
      └── SQL Index
```

---

### Étape 1 - Génération de l'événement

Une action métier se produit dans une application.

Exemples :

- Connexion utilisateur
- Consultation d'une dictée
- Modification d'une transcription
- Exécution d'un rapport
- Purge d'un enregistrement audio

L'application construit un objet AuditEvent conforme au modèle d'audit standard.

Exemple :

```json
{
  "eventType": "DictationViewed",
  "category": "Dictation",
  "userId": "123",
  "entityType": "Dictation",
  "entityId": "D-100345"
}
```

---

### Étape 2 - Publication via Audit gRPC API

L'application transmet l'événement à la plateforme d'audit via l'API gRPC.

Responsabilités :

- Authentifier l'application émettrice
- Recevoir l'événement
- Valider le format minimal
- Retourner rapidement une confirmation de réception

L'appel doit être rapide et ne pas dépendre des mécanismes de stockage.

---

### Étape 3 - Validation et enrichissement

L'Audit Service enrichit l'événement avec les métadonnées requises.

Exemples :

- AuditEventId
- TimestampUtc
- Application
- CorrelationId
- SessionId
- CreatedBySystem

L'événement devient alors prêt à être persisté.

---

### Étape 4 - Mise en file d'attente

L'événement est placé dans une file interne de traitement.

Objectifs :

- Découpler les applications du stockage
- Éviter qu'un ralentissement du stockage impacte les utilisateurs
- Permettre les mécanismes de reprise et de réessai

Une fois l'événement placé dans la file, l'application peut poursuivre son exécution normalement.

---

### Étape 5 - Traitement asynchrone

Un ou plusieurs Audit Processing Workers récupèrent les événements depuis la file.

Responsabilités :

- Générer l'identifiant unique AuditEventId
- Écrire l'événement complet dans Azure Blob Storage
- Créer l'entrée correspondante dans l'index SQL
- Vérifier la cohérence de la relation entre les deux mécanismes de stockage

---

### Étape 6 - Persistance

Chaque événement produit deux artefacts :

#### Événement complet

Stocké dans Azure Blob Storage.

Contient :

- Toutes les données de l'événement
- Les détails complets
- Les métadonnées enrichies

Azure Blob Storage constitue la source officielle de conservation.

---

#### Index opérationnel

Stocké dans SQL.

Contient uniquement les données nécessaires aux :

- Recherches
- Rapports
- Filtres

Exemples :

- TimestampUtc
- Category
- EventType
- UserId
- EntityType
- EntityId
- Outcome
- Severity
- BlobPath

---

### Gestion des erreurs

#### Blob indisponible

L'événement demeure dans la file de traitement.

Une nouvelle tentative est effectuée ultérieurement.

---

#### SQL indisponible

L'événement demeure dans la file de traitement.

Une nouvelle tentative est effectuée ultérieurement.

---

#### Échec de cohérence

Si l'un des deux artefacts est créé mais pas l'autre :

- L'événement est considéré comme incomplet
- Une alerte est générée
- Une procédure de reprise est exécutée

---

### Principe fondamental

Un événement d'audit est considéré valide uniquement lorsque :

- Le Blob existe
- L'index SQL existe
- Les deux partagent le même AuditEventId

La cohérence entre ces deux systèmes constitue une exigence critique de l'architecture.

---

### Disponibilité des applications

L'indisponibilité de la plateforme d'audit ne doit jamais empêcher une opération métier.

Exemples :

- Ouverture d'une dictée
- Modification d'une transcription
- Connexion utilisateur

La plateforme d'audit doit être conçue pour minimiser tout impact sur les applications consommatrices.

---

### Monitoring

Les indicateurs suivants doivent être surveillés :

- Nombre d'événements reçus
- Nombre d'événements archivés
- Nombre d'événements en erreur
- Événements sans Blob
- Événements sans index SQL
- Temps moyen de traitement
- Taille de la file d'attente

Des alertes doivent être générées lorsqu'une incohérence est détectée.

## Recovery Strategy

Azure Blob Storage constitue la source officielle des événements.

En cas de perte ou corruption de l'index SQL, celui-ci peut être reconstruit à partir des événements archivés.

Cette approche réduit la dépendance à l'index SQL et améliore la résilience de la plateforme.

## Non Functional Requirements

### Scalability

La plateforme doit supporter l'ajout de nouvelles applications sans modification majeure de l'architecture.

### Availability

L'indisponibilité temporaire de la plateforme d'audit ne doit pas empêcher les opérations métier.

### Maintainability

Toutes les applications utilisent le modèle AuditEvent standard.

### Performance

La publication d'un événement d'audit ne doit pas avoir d'impact perceptible sur l'expérience utilisateur.

## Vision long terme

Le système d'audit doit être conçu comme une plateforme centralisée pouvant être utilisée par plusieurs applications.

Applications ciblées :

- DigiWeb
- DigiConsole
- Applications futures

Chaque application publie des événements conformes au modèle AuditEvent.

L'Audit Platform assure :

- La réception des événements
- La validation
- L'archivage
- L'indexation
- L'exposition aux rapports
- L'alimentation future de Microsoft Fabric

Microsoft Fabric n'est pas requis pour la phase 1.

La plateforme d'audit doit toutefois être conçue de manière à permettre une future exploitation des événements d'audit stockés dans Azure Blob Storage.

Azure Blob Storage constitue ainsi la source officielle des événements et la future source de données analytiques utilisée par Microsoft Fabric.
