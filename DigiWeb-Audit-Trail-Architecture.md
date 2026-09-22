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

```
DigiWeb
    │
    │ Emit Audit Event
    ▼
Audit Service
    │
    ▼
Audit Repository
    │
    ▼
AuditEvent Table
    │
    ├── Reports
    ├── Audit Search
    └── Future Fabric Export
```

## Audit Service

Responsable de :

- Recevoir les événements
- Valider les données
- Enrichir les métadonnées
- Persister les événements

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

## Audit Repository

Responsable du stockage des événements.

Fonctions :

- Insert
- Search
- Reporting

### Interface
```
IAuditRepository
{
    Task InsertAsync(AuditEvent auditEvent);

    Task<SearchResult<AuditEvent>> SearchAsync(...);
}
```
