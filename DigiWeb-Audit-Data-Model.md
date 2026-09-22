# DigiWeb Audit Data Model

## Objectif

Fournir un modèle unique permettant de stocker tous les événements d'audit de la plateforme DigiWeb/Synnefo.

---

# AuditEvent

## Champs communs

| Champ 			| Type 		| Description 						| Precision
|---				|:---:		|---								|---
| AuditEventId 		| Guid 		| Identifiant unique 				| Généré au moment de la création de l'audit
| EventVersion 		| Int 		| Version du contrat d'événement 	|
| TimestampUtc 		| DateTime 	| Date de l'événement 				| TimeStamp à la seconde près
| EventType 		| String 	| Type d'événement 					| [Type d'évènement](#type-dévènement)
| Category 			| String 	| Domaine fonctionnel 				| [Catégories](#catégories-recommandées)
| EntityType 		| String 	| Type d'objet 						| [Type d'entité](#types-dentités)
| EntityId 			| String 	| Identifiant d'objet 				|
| UserId 			| Guid 		| Utilisateur concerné 				| 
| UserName 			| String 	| Nom d'utilisateur 				|
| UserRole 			| String 	| Rôle principal 					| [User Role](#user-role)
| Application 		| String 	| Source de l'événement 			| DigiWeb, DigiConsole, etc...
| Workstation 		| String 	| Poste de travail 					| Questionnable ...
| IpAddress 		| String 	| Adresse IP 						| A-t-on le droit ?
| TenantId 			| Guid 		| Organisation 						|
| SessionId 		| Guid 		| Session utilisateur 				|
| CorrelationId 	| Guid 		| Trace d'une transaction 			| Identifiant permettant de relier plusieurs événements appartenant à la même opération métier
| CreatedBySystem 	| String 	| Module source 					| AuthenticationService, DictationService, TranscriptionService, ReportingService, etc...
| Outcome 			| String 	| Succès ou échec 					| [Outcome](#outcome)
| Severity 			| String 	| Niveau d'importance 				| [Severity](#severity)
| Details 			| Json 		| Données additionnelles 			|

---

# Catégories recommandées
- Authentication
- Dictation
- Transcription
- Audio
- Reporting
- Administration
- Security
- AI
- System
- ...

---

# Types d'entités
- User
- Dictation
- Transcription
- AudioFile
- Report
- Role
- Permission
- ...

---

# Type d'évènement
- LoginSucceeded
- LoginFailed
- DictationOpened
- DictationViewed
- TranscriptionApproved
- ReportExecuted
- AudioPurged
- ...

---

# User Role
- Transcriptionist
- Reviewer
- Author
- Physician
- Supervisor
- Administrator
- Support
- System
- ...

---

# Outcome
- Success
- Failed
- PartialSuccess
- Denied

---

# Severity
- Info
- Warning
- Error
- Critical

---

# Exemple

## Login réussi

```json
{
  "eventType": "LoginSucceeded",
  "category": "Authentication",
  "userId": "123",
  "userName": "jsmith",
  "timestampUtc": "2026-09-21T18:00:00Z",
  "ipAddress": "10.10.10.10"
}
```

## Changement de status
```json
{
  "eventType": "DictationStatusChanged",
  "category": "Dictation",
  "entityType": "Dictation",
  "entityId": "D-100345",
  "details": {
    "oldStatus": "Reserved",
    "newStatus": "Completed"
  }
}
```
