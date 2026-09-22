# DigiWeb Audit Trail - Master Roadmap

## Objectif

Mettre en place un système d'audit centralisé dans DigiWeb/Synnefo offrant :

- Une traçabilité complète des actions utilisateur et système
- Les rapports d'audit requis par les clients
- Les capacités de conformité et de vérification réglementaire
- Une fondation pour l'analytique avancée (Phase 2)

---

## État d'avancement

| Domaine | Statut |
|----------|----------|
| Exigences métier | ✅ Terminé |
| Modèle de données | ✅ Terminé |
| Catalogue des événements | ✅ Terminé |
| Matrice de couverture | ✅ Terminé |
| Priorisation | ✅ Terminé |
| Spécifications des rapports | ✅ Terminé |
| Architecture | 🔄 En cours |
| Politique de rétention | ⏳ À faire |
| Risques techniques | ⏳ À faire |
| Développement | ⏳ À venir |
| Microsoft Fabric | 📌 Futur |

---

## Documents de référence

### Analyse fonctionnelle

- [DigiWeb-Audit-Trail-and-Reporting-Requirements](./DigiWeb-Audit-Trail-and-Reporting-Requirements.md)
- [DigiWeb-Audit-Data-Model](./DigiWeb-Audit-Data-Model.md)
- [DigiWeb-Audit-Event-Catalog](./DigiWeb-Audit-Event-Catalog.md)
- [DigiWeb-Audit-Coverage-Matrix](./DigiWeb-Audit-Coverage-Matrix.md)
- [DigiWeb-Audit-Prioritization](./DigiWeb-Audit-Prioritization.md)
- [DigiWeb-Audit-Reports-Specifications](./DigiWeb-Audit-Reports-Specifications.md)

### Architecture

- [DigiWeb-Audit-Trail-Architecture](./DigiWeb-Audit-Trail-Architecture.md)

### Gouvernance

À définir

- Politique de rétention des données d'audit
- Analyse des risques techniques

---

# Phase 1 - Analyse et conception

## Étape 1 - Définir le modèle d'audit central

Livrable :
- DigiWeb-Audit-Data-Model.md

Statut :
✅ Terminé

---

## Étape 2 - Construire le catalogue des événements d'audit

Livrable :
- DigiWeb-Audit-Event-Catalog.md

Statut :
✅ Terminé

---

## Étape 3 - Cartographier les fonctionnalités existantes

Livrable :
- DigiWeb-Audit-Coverage-Matrix.md

Statut :
✅ Terminé

---

## Étape 4 - Prioriser les exigences

Livrable :
- DigiWeb-Audit-Prioritization.md

Statut :
✅ Terminé

---

# Phase 2 - Rapports

## Étape 5 - Spécifier les rapports

Livrable :
- DigiWeb-Audit-Reports-Specifications.md

Statut :
✅ Terminé

---

# Phase 3 - Architecture

## Étape 6 - Concevoir l'architecture d'audit

Livrable :
- DigiWeb-Audit-Trail-Architecture.md

Statut :
🔄 En cours

Objectifs :

- Audit Service
- Audit Store
- API de consultation
- Stratégie d'indexation
- Recherche
- Performance
- Sécurité
- Intégration reporting

---

# Phase 4 - Gouvernance

### Étape 7 - Définir la politique de rétention

Livrable :
À définir

Statut :
⏳ À faire

---

# Phase 5 - Analytique avancée

## Étape 8 - Intégration Microsoft Fabric

Statut :
📌 Futur

Objectifs :

- Conservation long terme
- Power BI
- KPI opérationnels
- Analyse historique
- Détection d'anomalies

---

# Definition of Done

Le projet Audit Trail est considéré prêt pour estimation lorsque :

- [x] Le modèle d'audit est défini
- [x] Tous les événements sont catalogués
- [x] Les fonctionnalités sont cartographiées
- [x] Les priorités sont validées
- [x] Tous les rapports sont spécifiés
- [ ] L'architecture est approuvée
- [ ] La politique de rétention est définie
- [ ] Les risques techniques sont documentés
- [ ] Les impacts sur la performance sont évalués
