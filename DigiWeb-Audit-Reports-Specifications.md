# DigiWeb Audit Report Specifications

## Objectif

Définir précisément les rapports d'audit disponibles dans DigiWeb/Synnefo.

Chaque rapport doit documenter :

- Son objectif
- Les événements utilisés
- Les filtres disponibles
- Les colonnes affichées
- Les formats d'export
- Les permissions requises

---

# Access Audit Report

## Description

Permet d'identifier tous les utilisateurs ayant consulté une dictée.

## Événements utilisés

- DictationOpened
- DictationViewed

## Filtres

- Date début
- Date fin
- DictationId
- Utilisateur
- UserRole
- Site
- Département

## Colonnes

- Date/Heure
- DictationId
- UserId
- UserName
- UserRole
- Workstation
- IpAddress
- Action

## Export

- CSV
- Excel
- PDF (optionnel)

## Sécurité

- Supervisor
- Administrator

## Cas d'utilisation

- Enquête de confidentialité
- Audit clinique
- Vérification des accès non autorisés

---

# Transcription Detailed Report

## Description

Permet d'identifier tous les utilisateurs ayant créé, modifié, révisé ou approuvé une transcription.

## Événements utilisés

- TranscriptionCreated
- TranscriptionModified
- TranscriptionReviewed
- TranscriptionApproved
- TranscriptionSigned
- TranscriptionRejected
- TranscriptionReturned

## Filtres

- Date début
- Date fin
- TranscriptionId
- DictationId
- Utilisateur
- UserRole

## Colonnes

- Date/Heure
- TranscriptionId
- DictationId
- UserId
- UserName
- UserRole
- Action
- Version
- Outcome

## Export

- CSV
- Excel
- PDF (optionnel)

## Sécurité

- Supervisor
- Administrator

## Cas d'utilisation

- Contrôle qualité
- Historique des corrections
- Vérification des responsabilités

---

# Dictation Status History Report

## Description

Historique complet des changements de statut des dictées.

## Événements utilisés

- DictationStatusChanged

## Filtres

- Date début
- Date fin
- DictationId
- Utilisateur

## Colonnes