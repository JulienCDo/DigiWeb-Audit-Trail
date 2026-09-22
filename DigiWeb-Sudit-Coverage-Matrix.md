# DigiWeb Audit Coverage Matrix

## Objectif

Identifier l'ensemble des fonctionnalités DigiWeb devant produire des événements d'audit.

Cette matrice permet de :

- Vérifier que toutes les fonctionnalités sont couvertes
- Identifier les écarts de conformité
- Évaluer l'effort de développement
- Prioriser les travaux d'implémentation
- Valider que chaque rapport d'audit peut être alimenté

---

# État de couverture

| Fonctionnalité | Audit existant | Événements requis | Priorité |
|---------------|---------------|------------------|----------|
| Authentification | Non | LoginSucceeded, LoginFailed, Logout, SessionExpired | Haute |
| Consultation de dictée | Non | DictationOpened, DictationViewed | Haute |
| Changement de statut de dictée | Non | DictationStatusChanged | Haute |
| Création de dictée | Non | DictationCreated | Moyenne |
| Archivage de dictée | Non | DictationArchived | Moyenne |
| Création de transcription | Non | TranscriptionCreated | Haute |
| Modification de transcription | Non | TranscriptionModified | Haute |
| Révision de transcription | Non | TranscriptionReviewed | Haute |
| Approbation de transcription | Non | TranscriptionApproved | Haute |
| Signature de transcription | Non | TranscriptionSigned | Haute |
| Rejet de transcription | Non | TranscriptionRejected | Haute |
| Retour pour correction | Non | TranscriptionReturned | Haute |
| Lecture audio | Non | PlaybackStarted, PlaybackPaused, PlaybackStopped | Moyenne |
| Téléchargement audio | Non | AudioDownloaded | Haute |
| Suppression audio | Non | AudioDeleted | Haute |
| Purge audio | Non | AudioPurged | Haute |
| Attribution de rôle | Non | RoleAssigned | Haute |
| Retrait de rôle | Non | RoleRemoved | Haute |
| Modification de permission | Non | PermissionChanged | Haute |
| Création utilisateur | Non | UserCreated | Moyenne |
| Désactivation utilisateur | Non | UserDisabled | Moyenne |
| Exécution de rapport | Non | ReportExecuted | Moyenne |
| Export de rapport | Non | ReportExported | Moyenne |
| Assistance IA | Non | AIAssistanceRequested, AIAssistanceCompleted, AIAssistanceFailed | Faible |
| Gestion des verrous | Non | LockAcquired, LockReleased, LockDenied | Moyenne |
| Temps de transcription | Non | TranscriptionWorkStarted, TranscriptionWorkStopped | Haute |
| Mesures de productivité | Non | ProductivityCalculated | Haute |


---

# Couverture des rapports

## Access Audit

| Événement |
|------------|
| DictationOpened |
| DictationViewed |

---

## Detailed Reports - Transcription

| Événement |
|------------|
| TranscriptionCreated |
| TranscriptionModified |
| TranscriptionReviewed |
| TranscriptionApproved |
| TranscriptionSigned |
| TranscriptionRejected |
| TranscriptionReturned |

---

## Dictation Status History

| Événement |
|------------|
| DictationStatusChanged |

---

## Security Audit

| Événement |
|------------|
| LoginSucceeded |
| LoginFailed |
| SessionExpired |
| RoleAssigned |
| RoleRemoved |
| PermissionChanged |
| UserDisabled |

---

## Audio Audit

| Événement |
|------------|
| AudioDownloaded |
| AudioDeleted |
| AudioPurged |

---

## AI Usage Audit

| Événement |
|------------|
| AIAssistanceRequested |
| AIAssistanceCompleted |
| AIAssistanceFailed |

---

# Analyse des écarts

## Must Have

### Conformité

- [ ] Authentification
- [ ] Consultation de dictée
- [ ] Historique des statuts
- [ ] Modifications des transcriptions
- [ ] Gestion des permissions
- [ ] Purge audio

### Objectif

Permettre l'audit réglementaire et l'investigation des accès.

---

## Should Have

- [ ] Lecture audio
- [ ] Gestion des verrous
- [ ] Exécution de rapports

### Objectif

Améliorer la traçabilité opérationnelle.

---

## Nice To Have

- [ ] Audit IA
- [ ] Exports détaillés
- [ ] Analyse avancée

### Objectif

Préparer les futures capacités analytiques.

---

# Résumé

## Nombre de fonctionnalités couvertes

- Authentication
- Dictation
- Transcription
- Audio
- Administration
- Reporting
- AI

## Statut

- [ ] Couverture fonctionnelle validée
- [ ] Événements validés avec les experts métier
- [ ] Priorités validées
- [ ] Prêt pour la conception d'architecture