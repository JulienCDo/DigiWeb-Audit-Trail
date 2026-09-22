# DigiWeb Audit Trail - Audit Storage Strategy

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

L'Audit Service est le seul composant autorisé à créer, mettre à jour ou supprimer ces artefacts.

Les applications consommatrices ne peuvent jamais accéder directement au stockage.

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

### Option C - Index SQL avec rétention propre
| Données   | Conservation |
| --------- | ------------ |
| Blob      | 7 ans        |
| SQL Index | 2 ans        |

Après 2 ans :
SQL purge
Blob conservé
Fabric conserve la capacité analytique

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
