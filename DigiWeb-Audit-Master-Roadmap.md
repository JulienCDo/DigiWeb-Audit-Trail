# DigiWeb Audit Trail & Reporting - Roadmap
#### Objectif
- Mettre en place un système d'audit centralisé dans DigiWeb/Synnefo offrant :
- Une traçabilité complète des actions utilisateur et système
- Les rapports d'audit requis par les clients
- Les capacités de conformité et de vérification réglementaire
- Une fondation pour l'analytique avancée (Phase 2)

---

## Prochaine étape

### DigiWeb-Audit-Trail-Architecture.md

L'objectif est de définir :

- Le service d'audit (Audit Service)
- La stratégie de stockage
- La structure de la base de données
- Les mécanismes d'écriture des événements
- Les index et la recherche
- Les 
## Phase 1 - Conception
### Étape 1 - Définir le modèle d'audit central
#### Objectif
Créer un modèle unique représentant tous les événements d'audit de la plateforme.
#### Livrable
Avant de développer les rapports, définissez l'événement d'audit standard qui servira à tout le système.  
#### Must Have
- Authentification
- Consultation des dictées
- Modifications des transcriptions
- Historique des statuts
- Gestion des permissions
- Purge des fichiers audio
#### Should Have
- Temps de transcription
- Historique des verrous
- Exécution des rapports
#### Nice To Have
- Audit IA
- Playback détaillé
- Microsoft Fabric
- KPI avancés  

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
