# 📋 Rapport d’Analyse Statique — OWASP UnCrackable Level 1

**Date :** 26 Avril 2026
**Analyste :** AMAR SAAD
**Contexte :** Rooting Android

---

# A. Informations générales

| Champ            | Détail                                                           |
| ---------------- | ---------------------------------------------------------------- |
| Application      | UnCrackable Level 1                                              |
| Version          | v1.0 (code 1)                                                    |
| Package          | owasp.mstg.uncrackable1                                          |
| SDK min / target | API 19 / API 28                                                  |
| Origine          | APK pédagogique OWASP MAS Crackmes                               |
| Hash SHA-256     | 1DA8BF57D266109F9A07C01BF7111A1975CE01F190B9D914BCD3AE3DBEF96F21 |
| Outils           | JADX GUI, dex2jar, JD-GUI                                        |

---

# B. Résumé global

L’étude statique de l’application **UnCrackable Level 1** a permis d’identifier plusieurs problèmes de sécurité liés principalement à :

* une implémentation cryptographique faible,
* une logique de validation exposée côté client,
* et des mécanismes de protection anti-root facilement contournables.

Ces éléments rendent l’application vulnérable à une analyse et une manipulation via des outils de reverse engineering.

### 📊 Niveau de risque global : **ÉLEVÉ 🔴**

### 🎯 Recommandations prioritaires :

* Abandon du mode AES/ECB au profit de AES/GCM
* Externalisation de la vérification du secret côté serveur
* Adoption de Play Integrity API pour les contrôles d’environnement

---

# C. Analyse des vulnérabilités

---

## 🔴 Constat 1 — Chiffrement AES en mode ECB

| Élément      | Détail                                          |
| ------------ | ----------------------------------------------- |
| Gravité      | Élevée                                          |
| Problème     | Utilisation d’AES en mode ECB sans IV           |
| Impact       | Répétition de motifs dans les données chiffrées |
| Localisation | classe cryptographique interne                  |

### 📌 Analyse

Le mode ECB applique un chiffrement déterministe, ce qui signifie qu’un même message produit toujours le même résultat. Cela facilite grandement l’analyse cryptographique.

### 🛠️ Correction recommandée

* Utiliser **AES/GCM/NoPadding**
* Ajouter un IV généré aléatoirement

```java id="aes_fix"
Cipher cipher = Cipher.getInstance("AES/GCM/NoPadding");
```

---

## 🔴 Constat 2 — Validation côté client

| Élément  | Détail                                     |
| -------- | ------------------------------------------ |
| Gravité  | Élevée                                     |
| Problème | Secret vérifié localement dans l’APK       |
| Impact   | Extraction directe via reverse engineering |

### 📌 Analyse

La logique de validation étant embarquée dans l’application, elle peut être facilement reconstruite sans exécution.

### 🛠️ Correction recommandée

* Déplacer la vérification vers un **serveur sécurisé**
* Utiliser une API HTTPS avec authentification

---

## 🟡 Constat 3 — Détection de root contournable

| Élément  | Détail                                 |
| -------- | -------------------------------------- |
| Gravité  | Moyenne                                |
| Problème | Détection basée sur fichiers système   |
| Impact   | Facilement bypass avec instrumentation |

### 📌 Analyse

L’application vérifie la présence de certains fichiers liés au root. Ces contrôles sont statiques et donc contournables.

### 📌 Fichiers surveillés :

```
/system/app/Superuser.apk
/system/xbin/daemonsu
/system/etc/init.d/99SuperSUDaemon
/system/bin/.ext/.su
/dev/com.koushikdutta.superuser.daemon/
```

### 🛠️ Amélioration

* Utiliser **Play Integrity API**
* Ajouter vérification serveur

---

## 🟡 Constat 4 — Backup activé

| Élément  | Détail                         |
| -------- | ------------------------------ |
| Gravité  | Moyenne                        |
| Problème | allowBackup = true             |
| Impact   | Extraction des données via ADB |

### 🛠️ Correction

```xml id="backup_fix"
android:allowBackup="false"
```

---

## 🟢 Constat 5 — Version SDK obsolète

| Élément  | Détail                                        |
| -------- | --------------------------------------------- |
| Gravité  | Faible                                        |
| Problème | minSdk trop ancien (API 19)                   |
| Impact   | Exposition à vulnérabilités système anciennes |

### 🛠️ Correction

* minSdkVersion ≥ 26
* targetSdkVersion ≥ 34

---

# D. Plan de correction

| Priorité | Action                               |
| -------- | ------------------------------------ |
| 🔴       | Remplacer AES/ECB par AES/GCM        |
| 🔴       | Externaliser validation côté serveur |
| 🟡       | Ajouter Play Integrity API           |
| 🟡       | Désactiver backup Android            |
| 🟢       | Mettre à jour SDK                    |

---

# E. Analyse technique APK

## 🔐 Permissions

Aucune permission sensible déclarée → surface limitée.

---

## 📦 Composants

| Composant    | État                |
| ------------ | ------------------- |
| MainActivity | Exportée (launcher) |

---

## 📁 Structure APK

* AndroidManifest.xml
* classes.dex
* resources.arsc
* res/layout
* META-INF (signature)

---

# F. Comparaison outils

| Critère              | JADX | JD-GUI  |
| -------------------- | ---- | ------- |
| XML Android          | ✔    | ✖       |
| Lisibilité code      | ✔    | Moyen   |
| Analyse complète APK | ✔    | Limitée |
<img width="453" height="37" alt="image" src="https://github.com/user-attachments/assets/820ceb58-d13c-428e-9d51-7f52f740cd32" />
<img width="605" height="49" alt="image" src="https://github.com/user-attachments/assets/e9376e71-3f6c-4371-ba6a-51f14b10092c" />
<img width="596" height="76" alt="image" src="https://github.com/user-attachments/assets/60b18914-a8cd-4014-95f5-91bf645bd37e" />
<img width="770" height="47" alt="image" src="https://github.com/user-attachments/assets/e14bb870-c769-42f4-aaff-764d62593e77" />

---

# ⚠️ Conclusion

L’application étudiée présente des faiblesses classiques des applications pédagogiques en sécurité mobile :

* cryptographie faible,
* logique client exposée,
* protections anti-root basiques.

Ces éléments en font un excellent support pour apprendre les techniques de reverse engineering et d’analyse statique.

---

# ⚠️ Note finale

Analyse réalisée dans un cadre **strictement académique et contrôlé (OWASP MAS Crackmes)**.
Aucune exploitation malveillante n’a été effectuée.
