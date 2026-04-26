# 📋 Rapport d’analyse de sécurité Android — Lab 2 (Rooting)

**Date :** 25 Avril 2026
**Auteur :** AMAR SAAD
**Environnement :** AVD Pixel 3 XL (Android 9.0 – API 28)
**Application cible :** pizza.apk

---

## 🧭 1. Contexte de l’étude

Ce laboratoire porte sur l’analyse du rooting Android et son impact sur la sécurité des applications mobiles.
L’objectif est de comprendre comment l’élévation de privilèges (root) influence la protection du système et la visibilité des données internes d’une application.

L’ensemble des tests a été effectué dans un environnement isolé afin d’éviter toute interaction avec un système réel.

---

## 🔐 2. Compréhension du rooting Android

Le rooting correspond à l’obtention des droits administrateur sur un appareil Android.
Cela permet un accès complet au système, incluant les fichiers protégés, les configurations internes et les processus système.

Dans un contexte d’analyse sécurité, cela permet :

* d’examiner le comportement réel des applications,
* d’accéder aux zones normalement protégées,
* d’analyser les mécanismes de sécurité internes.

Cependant, ce niveau d’accès casse les protections natives d’Android et doit être utilisé uniquement dans un cadre de test contrôlé.

---

## 🔗 3. Mécanisme de sécurité Android (chaîne de confiance)

Android repose sur un mécanisme de validation en plusieurs étapes garantissant l’intégrité du système :

```text id="x8c0lm"
Matériel sécurisé (Root of Trust)
        ↓
Bootloader
        ↓
Kernel (partition boot)
        ↓
Système Android
```

Chaque étape vérifie la signature de la suivante afin d’empêcher toute modification non autorisée.

### 🔒 Android Verified Boot (AVB)

AVB ajoute une couche de sécurité supplémentaire :

* vérification cryptographique des partitions,
* protection contre les versions modifiées,
* blocage du downgrade (anti-rollback).

---

## ⚠️ 4. Analyse des risques identifiés

Lors de l’étude du contexte rooté, plusieurs risques ont été identifiés :

### 🔴 Risques critiques

* perte d’intégrité du système d’analyse,
* exposition possible de données sensibles,
* résultats non représentatifs d’un environnement réel.

### 🟠 Risques moyens

* augmentation de la surface d’attaque,
* contournement des protections applicatives,
* accès facilité aux fichiers internes.

### 🟢 Mesures de sécurité appliquées

* utilisation exclusive de données fictives,
* environnement totalement isolé,
* suppression des comptes personnels,
* réinitialisation de l’émulateur après test,
* restriction des applications installées.

---

## 📚 5. Référentiels de sécurité utilisés

### OWASP MASVS

* **STORAGE-1 :** les données sensibles doivent être chiffrées et jamais stockées en clair.
* **NETWORK-1 :** toutes les communications doivent utiliser TLS avec validation stricte.

### OWASP MASTG

* inspection des fichiers internes via accès root (`/data/data/...`)
* surveillance des logs via `adb logcat` pour détecter des fuites d’informations

---

## 🧪 6. Résultats des tests

L’application a été testée dans plusieurs scénarios :

### ▶️ Démarrage de l’application

L’application Pizza recipes se lance correctement sans détection du root.

### ▶️ Navigation dans les recettes

Les images et contenus se chargent normalement sans erreur.

### ▶️ Interaction utilisateur

Les actions (navigation, partage, affichage détail) fonctionnent sans interruption.

👉 Conclusion des tests : aucune protection active contre l’environnement root n’a été détectée.

---

## 🖥️ 7. Informations d’environnement

* **Android version :** 9.0 (API 28)
* **Émulateur :** emulator-5554
* **Statut root :** actif (uid=0)
* **État Verified Boot :** orange (bootloader déverrouillé)


---
<img width="1723" height="645" alt="image" src="https://github.com/user-attachments/assets/04e1e12b-7b37-4792-9c7d-002807b68a8f" />

