# C2 : Audit technique, fonctionnel et de sécurité de l'environnement

## 🔎 Observable 1 : Audit technique

L'audit technique de SoundWise vise à analyser l'environnement technologique dans lequel le projet s'inscrit afin d'identifier les **contraintes structurelles** du secteur musical, les **limites des solutions existantes** et les **opportunités d'intégration**.

L'objectif est de garantir que la solution proposée soit réaliste, compatible avec les usages actuels et adaptée aux contraintes matérielles et logicielles des producteurs.

---

### 1️⃣ Environnement technologique du secteur musical

Les producteurs de musique évoluent majoritairement dans un écosystème structuré autour de **DAW (Digital Audio Workstations)** :

| DAW | Caractéristiques |
|-----|------------------|
| **Ableton Live** | Synthèse, séquençage, performance live |
| **FL Studio** | Production urbaine et EDM |
| **Logic Pro** | Production généraliste, mixing professionnel |

**Puissance technique** :
- Traitement audio temps réel haute performance
- Gestion de centaines de multipistes
- Intégration de plugins tiers variés

**Complexité structurelle** :
- Architecture complexe et couplée
- Dépendance forte à l'environnement matériel (CPU, RAM, carte son, pilotes audio)
- Configuration hétérogène selon les utilisateurs

---

### 2️⃣ Contraintes techniques identifiées

#### 🎵 Traitement audio en temps réel
- **Latence critique** : < 10ms pour une expérience musicale acceptable
- **Consommation système** : importante en CPU et mémoire
- **Implication** : Toute solution complémentaire doit être **légère, optimisée** et **sans dégradation des performances**

#### 🔌 Dépendance aux plugins tiers

Les producteurs utilisent massivement des **plugins (VST, AU, etc.)** développés par des éditeurs tiers.

**Problématiques identifiées** :
- Standards variés et non standardisés
- Accès limité aux données internes du logiciel hôte
- Niveaux d'accessibilité très hétérogènes
- **Résultat** : Environnement fragmenté et difficilement maîtrisable

#### 💻 Hétérogénéité des environnements

**Configurations utilisateurs** :
- Systèmes d'exploitation : macOS ou Windows
- Matériel : configurations très variables (processeurs, mémoire, cartes son)
- Installations personnalisées avec multiples plugins

**Exigences de conception** :
- ✓ Multiplateforme
- ✓ Peu intrusive
- ✓ Compatible avec les workflows variés

---

### 3️⃣ Enjeux d'interopérabilité

**Point stratégique majeur identifié** :

> SoundWise ne doit pas remplacer les DAW existants mais **s'y intégrer**.

**Principes d'intégration** :

```
Architecture modulaire
      ↓
Complémentarité (pas de remplacement)
      ↓
Interopérabilité maximale
      ↓
Respect des workflows établis
```

**La solution doit pouvoir** :
- S'intégrer à des environnements existants variés
- Communiquer avec des logiciels tiers
- S'adapter à des workflows déjà établis
- Fonctionner sans modifications massives du système existant

---

### 4️⃣ Contraintes d'accessibilité technique

**Constat des logiciels existants** : L'accessibilité est **rarement intégrée nativement**.

**Problèmes observés** :
- Interfaces très denses visuellement
- Éléments graphiques non structurés sémantiquement
- Faible compatibilité avec les lecteurs d'écran
- Navigation au clavier limitée ou absente

**Standards à implémenter dans SoundWise** :
- 🎯 Structure d'interface sémantique claire
- ⌨️ Gestion optimale du focus clavier
- 📢 Retours contextuels et accessibles
- 🔤 Textes alternatifs appropriés

---

### 5️⃣ Synthèse des contraintes techniques

**Éléments structurants identifiés** :

| Contrainte | Impact |
|-----------|--------|
| 🎵 Exigence en performance audio | Ressources système limitées |
| 🌐 Environnement fragmenté | Dépendance aux plugins tiers |
| 💾 Hétérogénéité des configurations | Multiplateforme indispensable |
| 🔗 Besoin d'interopérabilité | Intégration, pas remplacement |
| ♿ Accessibilité insuffisante | Standards dès la conception |

**Orientations de conception pour SoundWise** :

```
Légère et optimisée (impact minimal sur la performance)
        ↓
Modulaire et complémentaire (s'intègre aux DAW)
        ↓
Compatible avec environnements existants (multiplateforme)
        ↓
Pensée dès l'origine pour l'accessibilité (standards du web)
```

---

## 🔎 Observable 2 : Approche méthodologique

L'audit du projet SoundWise a été conduit selon une **démarche structurée** combinant :

- Investigation terrain
- Analyse comparative des solutions existantes
- Étude des enjeux techniques et sécuritaires du secteur musical

L'objectif n'était pas uniquement de recueillir des impressions, mais de **formaliser une analyse exploitable** pour la rédaction des spécifications.

---

### 1️⃣ Investigation terrain

La première phase a consisté en une **investigation qualitative** auprès de professionnels du secteur musical :

- **Entretiens semi-directifs** menés avec plusieurs producteurs de musique
- Compréhension des **usages réels**, contraintes quotidiennes et difficultés rencontrées
- Échange spécifique avec un **producteur malvoyant** pour identifier les problématiques d'accessibilité numérique dans un contexte professionnel concret

> **Avantage méthodologique** : Le format semi-directif permet de laisser émerger les besoins implicites, frustrations non exprimées et usages détournés des outils existants.

---

### 2️⃣ Analyse comparative des solutions existantes

Une analyse comparative des **principaux logiciels de production** du marché a été réalisée :

| Logiciel | Focus |
|----------|-------|
| **Ableton Live** | Production électronique |
| **FL Studio** | Production hip-hop/pop |
| **Logic Pro** | Production généraliste |

**Critères d'analyse** :
- Ergonomie générale et structure de navigation
- Complexité des interfaces
- Présence d'assistance intégrée
- Niveau d'accessibilité numérique

**Résultat clé** : Identification d'un **écart significatif** entre la puissance technique des logiciels et leur dimension pédagogique ou inclusive.

---

### 3️⃣ Analyse des contraintes techniques et sécuritaires

#### Aspects techniques
- **Traitement audio en temps réel** : consommation importante de ressources système
- **Plugins tiers** : créent une forte dépendance à des environnements hétérogènes et n'ont pas accès à toutes les données d'un logiciel
- **Interopérabilité avec les DAW existants** : enjeu stratégique majeur

> SoundWise n'a pas vocation à remplacer ces outils mais à **s'y intégrer de manière complémentaire**.

#### Aspects sécuritaires
L'analyse a porté sur la nature des données manipulées :

- Comptes utilisateurs
- Projets musicaux
- Contenus partagés via marketplace communautaire

**Risques prioritaires identifiés** :
- ⚖️ Protection de la propriété intellectuelle
- 🔒 Confidentialité des données personnelles
- 📜 Conformité réglementaire (RGPD)

---

### 4️⃣ Synthèse et structuration des résultats

**Processus de synthèse** :

```
Collecte d'informations
    ↓
Classification par catégorie (fonctionnelle, technique, sécurité, accessibilité)
    ↓
Croisement pour identifier les problématiques récurrentes
    ↓
Hiérarchisation selon impact utilisateur et faisabilité technique
    ↓
Transformation en axes stratégiques pour les spécifications (C3)
```