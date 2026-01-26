# C7 - SÉCURITÉ INFORMATIQUE R-TYPE

---

R-Type utilise UDP pour la communication réseau temps réel. Dans ce cadre, une attention particulière est portée à la sécurité des échanges et à la robustesse du serveur face à des clients potentiellement malveillants.

Une partie de cette démarche repose sur l’analyse de **CVE (Common Vulnerabilities and Exposures)**.

> 🔎 **Qu’est-ce qu’une CVE ?**
> Une CVE est une vulnérabilité de sécurité publique, documentée et référencée de manière unique, affectant un logiciel, une bibliothèque ou un outil. Chaque CVE décrit la nature de la faille, les versions concernées, son niveau de sévérité et les correctifs associés.

Dans ce projet, seules les CVE **pertinentes pour l’architecture R-Type** ont été retenues afin de rester cohérent avec le périmètre du jeu.

---

## 🔎 Observable 1 : ÉTUDE DES FAILLES DE SÉCURITÉ

### Démarche

Afin de rester lisible et pertinent, l’étude se concentre volontairement sur **trois cas représentatifs** :

* une vulnérabilité de bibliothèque graphique,
* une vulnérabilité réseau liée aux dépendances,
* une limitation inhérente au protocole UDP.

---

### 1️⃣ CVE-2022-4743 – SDL2 Buffer Overflow

**Technologie** : SDL2 (Simple DirectMedia Layer)
**Sévérité** : Élevée (7.8)
**Versions affectées** : SDL2 < 2.0.22
**Version utilisée** : 2.28.3 ✅

**Description** :
Cette CVE décrit un dépassement de mémoire tampon lors du chargement d’images XPM via `SDL_Image`. Une image spécialement forgée peut provoquer un crash du client, voire une exécution de code arbitraire.

**Mesure appliquée** :

* Mise à jour vers une version corrigée de SDL2
* Gestion stricte des dépendances via Conan

**Statut** : ✅ Corrigée

---

### 2️⃣ CVE-2020-13616 – Boost.Asio TLS Hostname Bypass

**Technologie** : Boost.Asio
**Sévérité** : Moyenne (5.9)
**Versions affectées** : Boost < 1.73.0
**Version utilisée** : 1.82.0 ✅

**Description** :
Cette vulnérabilité concerne une validation incorrecte du nom d’hôte lors d’une connexion TLS, pouvant permettre une attaque de type Man-in-the-Middle.

**Analyse dans R-Type** :
R-Type utilise exclusivement **UDP brut**, sans chiffrement TLS, afin de garantir une latence minimale compatible avec un jeu temps réel.

**Décision technique** :

* TLS non utilisé volontairement (trade-off performance / sécurité)
* Sécurité assurée par un serveur autoritaire et une validation stricte des paquets

**Statut** : ⚠️ Non applicable

---

### 3️⃣ Limitation UDP – Fragmentation des paquets (RFC 768)

**Technologie** : UDP
**Type** : Limitation inhérente au protocole

**Description** :
Les paquets UDP dépassant la MTU réseau (~1500 octets) sont fragmentés au niveau IP. Cette fragmentation augmente les risques de pertes et peut être exploitée pour des attaques de type déni de service.

**Mesure appliquée** :

* Taille maximale des paquets fixée à **1400 octets**
* Rejet des paquets fragmentés

**Bénéfices** :

* Réduction du risque de DoS
* Comportement réseau prédictible
* Stabilité accrue du serveur

**Statut** : ✅ Mitigé par conception

---

## Bonnes pratiques de sécurité implémentées

* Validation serveur systématique des actions client
* Serveur autoritaire (le client n’est jamais considéré comme fiable)
* Contrôle des dimensions et valeurs reçues (clamping)
* Isolation stricte des parties (rooms)

---

## Résumé C7.1

* **3 risques de sécurité analysés**, dont 1 CVE critique
* **Décisions techniques justifiées** selon le contexte temps réel
* **Mesures concrètes implémentées et vérifiables dans le code**

---

## 🔎 Observable 2 : VEILLE SÉCURITÉ INFORMATIQUE

### Démarche de veille

La veille sécurité repose sur des sources reconnues :

* Base CVE (NVD – NIST)
* OWASP (principes de sécurité applicative)
* RFC réseau (UDP – RFC 768)
* Retours d’expérience du domaine du jeu vidéo multijoueur

### Résultats

* Identification de vulnérabilités pertinentes pour le projet
* Mise à jour proactive des dépendances
* Intégration de bonnes pratiques adaptées au contexte jeu réseau

### Pistes d’amélioration

* Signature légère des paquets (HMAC)
* Numérotation des paquets (anti-replay)
* Rate limiting côté serveur

---

## Message clé

> « Le serveur doit rester autoritaire. Le client ne doit jamais être digne de confiance. »
