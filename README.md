# DigiWeb Audit Trail

## Master Document

👉 [DigiWeb Audit Master Roadmap](./DigiWeb-Audit-Master-Roadmap.md)

# Statut du projet

**Phase actuelle : Conception**

### Avancement

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
| Analyse des risques techniques | ⏳ À faire |
| Développement | ⏳ À venir |
| Microsoft Fabric (Phase 2) | 📌 Futur |

---

## Documents de référence

### Analyse fonctionnelle

- DigiWeb-Audit-Master-Roadmap.md
- DigiWeb-Audit-Trail-and-Reporting-Requirements.md
- DigiWeb-Audit-Data-Model.md
- DigiWeb-Audit-Event-Catalog.md
- DigiWeb-Audit-Coverage-Matrix.md
- DigiWeb-Audit-Prioritization.md
- DigiWeb-Audit-Reports-Specifications.md

### Architecture et gouvernance

- DigiWeb-Audit-Trail-Architecture.md
- DigiWeb-Audit-Retention-Policy.md
- DigiWeb-Audit-Technical-Risks.md

---

## Objectif du projet

Mettre en place un système d'audit centralisé dans DigiWeb/Synnefo permettant :

- La traçabilité complète des actions utilisateur et système
- La production de rapports d'audit conformes aux exigences client
- L'investigation des incidents et des accès
- Le suivi opérationnel et la productivité
- La préparation de l'intégration Microsoft Fabric pour l'analytique avancée

---

## Couverture du RFP

### Audit Reports

| Exigence | Couverture |
|-----------|-----------|
| Liste des utilisateurs ayant accédé à une dictée | ✅ |
| Utilisateurs ayant modifié une transcription | ✅ |
| Temps passé à transcrire par tous les transcriptionnistes | ✅ |
| Mesures réelles de productivité | ✅ |
| Variables d'audit additionnelles | ✅ |

### User Activity Audit

| Exigence | Couverture |
|-----------|-----------|
| Connexion / Déconnexion utilisateur | ✅ |
| Changement de statut d'une dictée | ✅ |
| Changement de statut d'une transcription | ✅ |
| Purge des enregistrements audio | ✅ |
| Exécution de rapports | ✅ |
| Activités additionnelles | ✅ |

---

## Travaux terminés

- [x] Définir les exigences d'audit
- [x] Définir le modèle d'audit central
- [x] Créer le catalogue des événements
- [x] Cartographier la couverture fonctionnelle
- [x] Prioriser les exigences
- [x] Spécifier les rapports d'audit

---

## Travaux en cours

- [ ] Concevoir l'architecture du système d'audit

---

## Travaux à venir

- [ ] Définir la politique de rétention
- [ ] Identifier les risques techniques
- [ ] Estimer les impacts de performance
- [ ] Préparer l'implémentation
- [ ] Définir la stratégie Microsoft Fabric
