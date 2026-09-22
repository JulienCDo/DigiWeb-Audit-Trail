# DigiWeb Audit Trail - Audit Storage Strategy

## Statut

🔄 Proposition à valider

Dernière mise à jour : 2026-09-22

## Objectif

Définir une stratégie de stockage des événements d'audit permettant :

- La conservation de plusieurs années d'historique
- Une consultation efficace des données récentes
- Un coût d'exploitation raisonnable
- La conformité aux exigences de rétention
- L'intégration future avec Microsoft Fabric

---

# Contexte

Les événements d'audit représentent un volume de données qui augmente continuellement.

Contrairement aux données transactionnelles, les événements d'audit sont généralement :

- Créés une seule fois
- Rarement modifiés
- Consultés occasionnellement
- Conservés pendant plusieurs années

Exemples :

- Connexions utilisateur
- Consultation de dictées
- Modifications de transcriptions
- Changements de statut
- Exécution de rapports
- Purge d'enregistrements audio

---

# Problématique

Une approche consistant à conserver l'ensemble des événements d'audit dans une base de données SQL pendant plusieurs années présente certains inconvénients :

- Augmentation continue du volume de données
- Coût de stockage plus élevé
- Augmentation potentielle des coûts d'exploitation
- Risque d'impact sur les performances des rapports

Il est donc souhaitable d'utiliser une approche mieux adaptée aux données d'audit à long terme.

---

# Architecture proposée

## Principe général

L'architecture proposée repose sur deux mécanismes complémentaires :

### 1. Stockage principal des événements

Tous les événements d'audit sont conservés dans Azure Blob Storage.

Azure Blob Storage devient la source officielle de conservation des événements.

Avantages :

- Coût de stockage très faible
- Excellente durabilité
- Conservation de plusieurs années
- Évolutivité pratiquement illimitée

---

### 2. Index de recherche

Un index léger est conservé dans SQL.

L'index contient uniquement les informations nécessaires aux recherches rapides :

- Date de l'événement
- Type d'événement
- Utilisateur
- Entité concernée
- Résultat
- Référence vers l'événement complet

L'index ne contient pas l'événement complet.

---

# Architecture logique

```
Audit Service
    │
    ├── Audit Index (SQL)
    │       │
    │       └── AuditEventId
    │
    └── Audit Archive (Blob)
            │
            └── AuditEventId
```

## Cohérence des données

Chaque événement d'audit possède :

- Un enregistrement complet dans Azure Blob Storage
- Un enregistrement correspondant dans l'index SQL

La relation est de type 1:1.

L'Audit Service est le seul composant autorisé à créer les artefacts d'audit.

Les événements d'audit ne peuvent pas être modifiés après leur création.

Les opérations de rétention, d'archivage ou de suppression doivent également être orchestrées par l'Audit Service.

Cette approche permet :

- De préserver l'intégrité des données
- D'éviter les incohérences entre l'index et les événements archivés
- De centraliser la logique de rétention et d'archivage

## Identifiant unique

Chaque événement reçoit un AuditEventId unique.

Cet identifiant est utilisé comme clé de référence entre :

- L'enregistrement SQL
- L'événement complet stocké dans Azure Blob Storage

Exemple :

AuditEventId = 3e2d6c6a-45f0-4d7e-b4d3-91a58f1d2e4c

## Risque critique : Désynchronisation Index / Blob

Le principal risque de cette architecture est la perte de cohérence entre :

- L'enregistrement de l'index SQL
- L'événement complet stocké dans Azure Blob Storage

Exemples d'incohérence :

### Cas 1

L'index SQL existe mais le Blob est manquant.

Conséquence :

- Les recherches fonctionnent
- Les détails complets de l'événement deviennent inaccessibles

### Cas 2

Le Blob existe mais l'index SQL est manquant.

Conséquence :

- L'événement existe toujours
- Les rapports et recherches opérationnelles ne peuvent plus le retrouver

### Cas 3

Le Blob et l'index pointent vers des versions différentes.

Conséquence :

- Résultats incohérents
- Risque d'erreur lors d'un audit ou d'une enquête

### Mesures d'atténuation

Afin de réduire ce risque :

- Seul l'Audit Service peut écrire dans SQL ou dans Azure Blob Storage
- Les applications ne peuvent jamais accéder directement aux mécanismes de stockage
- La création de l'index et du Blob doit être réalisée comme une opération unique contrôlée par l'Audit Service
- Des vérifications périodiques d'intégrité peuvent être exécutées afin de détecter les incohérences
- AuditEventId demeure la référence unique entre les deux systèmes

### Hypothèse clé

Cette stratégie demeure viable tant que l'Audit Service reste l'unique propriétaire du cycle de vie des événements d'audit.

Aucune application ne doit accéder directement :

- À l'index SQL
- Aux blobs d'archivage

Toutes les opérations doivent transiter par l'Audit Service afin de garantir la cohérence des données.

### Importance du risque

Cette désynchronisation représente le principal risque architectural de la stratégie de stockage proposée.

Sans mécanismes de contrôle adéquats, il devient impossible de garantir qu'un événement retrouvé via l'index SQL possède toujours son équivalent complet dans Azure Blob Storage.

La cohérence entre les deux systèmes doit être considérée comme une exigence critique de la plateforme d'audit.

## Question : Que faire de l'index lorsqu'un blob pass Cool ou Archive ?
### Option A Conserver l'index SQL indéfiniment
#### Avantage
Rapport rapides.
#### Inconvénient
Le SQL ne fait que grossir. Les coûts et la lourdeur des requêtes aussi.

### Option B - Supprimer l'index en même temps que l'archivage
#### Avantage
Coût minimal.
#### Inconvénient
Les rapports historiques deviennent très difficiles.

### Option C - Index SQL avec rétention propre (Recommandée)

| Données | Conservation |
|----------|----------|
| Blob | 7 ans |
| SQL Index | 2 ans |

Après 2 ans :

- Index SQL supprimé
- Blob conservé
- Fabric conserve la capacité analytique

#### Avantages

- Contrôle de la croissance du stockage SQL
- Coût d'exploitation prévisible
- Rapports opérationnels rapides sur les données récentes
- Conservation long terme des événements complets
- Compatible avec Microsoft Fabric
- Réduction de la maintenance SQL à long terme

#### Inconvénients

- Les recherches opérationnelles ne couvrent que la période conservée dans l'index SQL
- Les analyses historiques nécessitent Azure Blob Storage ou Microsoft Fabric
- Processus d'archivage et de purge plus complexes
- Nécessite une gouvernance claire de la rétention

## Décision recommandée

La stratégie recommandée pour DigiWeb Audit Trail est l'Option C.

Justification :

- Contrôle des coûts de stockage SQL
- Conservation long terme des événements
- Compatibilité avec Microsoft Fabric
- Bon équilibre entre performance et coût
- Réduction de la croissance des données opérationnelles
  
---

# Consultation des rapports

## Rapports opérationnels

Les recherches quotidiennes utilisent principalement l'index SQL.

Exemples :

- Qui a consulté cette dictée ?
- Qui a modifié cette transcription ?
- Quels utilisateurs se sont connectés aujourd'hui ?

Les résultats sont obtenus rapidement grâce à l'index.

---

## Consultation détaillée

Lorsqu'une information détaillée est requise, le système récupère l'événement complet depuis Azure Blob Storage.

Cette approche permet :

- Des recherches rapides
- Une conservation économique
- Une consultation complète au besoin

---

# Gestion de la rétention

Azure Blob Storage permet l'application de politiques de cycle de vie.

Exemple :

| Âge des données | Niveau de stockage |
|-----------------|-------------------|
| 0 à 12 mois | Hot |
| 12 à 36 mois | Cool |
| Plus de 36 mois | Archive |

Cette approche réduit les coûts sans supprimer les données.

---

# Intégration Microsoft Fabric

L'architecture proposée est compatible avec une future intégration Microsoft Fabric.

Architecture cible :

```text
DigiWeb
    │
    ▼
Audit Service
    │
    ├── Audit Index (SQL)
    │
    └── Azure Blob Storage
            │
            ▼
    Microsoft Fabric
            │
            ▼
         Power BI
```

Microsoft Fabric pourra être utilisé pour :

- Les analyses historiques
- Les KPI opérationnels
- Les tableaux de bord
- L'analyse de productivité
- La détection d'anomalies

---

# Avantages

## Coût

Réduction significative du coût de conservation des données d'audit à long terme.

---

## Évolutivité

Le volume d'événements peut augmenter sans nécessiter une croissance importante de l'infrastructure SQL.

---

## Performance

Les recherches courantes demeurent rapides grâce à l'index SQL.

---

## Conformité

Permet de conserver plusieurs années d'historique conformément aux exigences réglementaires et contractuelles.

---

## Évolution future

Prépare naturellement l'intégration avec Microsoft Fabric et les futures capacités analytiques.

---

# Recommandation

Pour DigiWeb Audit Trail, il est recommandé d'utiliser :

- Azure Blob Storage comme source officielle de conservation des événements
- Une base SQL légère comme index de recherche
- Microsoft Fabric comme couche analytique future

Cette approche offre le meilleur compromis entre :

- Coût
- Performance
- Scalabilité
- Conformité
- Évolutivité
