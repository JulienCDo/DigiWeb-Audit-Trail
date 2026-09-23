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

## Sécurité

Principes :

- Les événements d'audit sont immuables
- Les événements ne peuvent pas être modifiés par les utilisateurs
- Seuls les rôles autorisés peuvent consulter les rapports
- Toutes les communications entre applications et Audit API doivent être authentifiées

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
