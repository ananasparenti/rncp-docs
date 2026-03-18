# C3 : Spécifications techniques et fonctionnelles

## 🔎 Observable 1 : Documentation des spécifications

### Fonctionnel

- Création / gestion de projets audio (pistes, timeline)
- Lecture & contrôle (play, stop, mixer : volume/pan/mute)
- IA tuteur hybride :
    - tutoriels guidés (prise en main)
    - assistant de création musicale
- Navigation 100% clavier + compatibilité lecteurs d’écran
- Interface simplifiée adaptée aux débutants

### Technique
- JUCE (C++) : audio temps réel, UI, VST host
- Architecture modulaire (ECS) avec séparation :
    - Audio Engine / UI / IA / Persistance
- Multi-plateforme : Windows / macOS / Linux
- Persistance : JSON + auto-save
- Plugins VST3 simples (max 2 simultanés)

### IA hybride
- Local (offline) :
    - scripts JSON + TTS natif
    - apprentissage des fonctionnalités
- Cloud (online) :
    - assistant conversationnel contextuel
    - requêtes sécurisées (HTTPS)

### Contraintes

- Latence audio < 10 ms
- Temps de réponse IA < 5 s
- Environnement hétérogène → app légère & modulaire
- Fonctionnement partiel hors-ligne
- Sécurité : TLS 1.3, pas de clé API côté client

### Interfaces & données

- API IA (cloud)
- Plugins VST3 (audio)
- Base de données (Supabase – historique & progression)
- Sauvegarde projets : JSON (local)

## 🔎 Observable 2 : Accessibilité

Standards implémentés

 WCAG 2.1 niveau AA (minimum légal) :

- **Guideline 1.1**  : Alternatives textuelles pour tout contenu non-textuel (descriptions audio/visuelles, labels ARIA)
- **Guideline 1.4**  : Distinguabilité (contraste 7:1 pour thèmes accessibles, redimensionnement 200%, pas d'info par couleur seule)
-  **Guideline 2.1**  : Clavier (100% fonctionnalités accessibles sans souris, pas de piège clavier)
- **Guideline 2.4**  : Navigation (focus visible, ordre logique, skip links, titres descriptifs)
- **Guideline 2.5**  : Modalités d'entrée (taille cible 44x44px minimum, annulation gestes)
- **Guideline 3.1**  : Lisible (langue déclarée, vocabulaire simplifié pour dyslexiques)
- **Guideline 3.2**  : Prévisible (pas de changement contexte automatique, navigation cohérente)
- **Guideline 3.3**  : Assistance saisie (identification erreurs, labels explicites)
- **Guideline 4.1**  : Compatible technologies assistives (JUCE AccessibilityHandler, rôles ARIA corrects)

Conformité réglementaire :

🇪🇺 Directive européenne 2016/2102 (accessibilité sites web et applications)
🇫🇷 RGAA 4.1 (Référentiel Général d'Amélioration de l'Accessibilité)
🇺🇸 Section 508 (compatibilité internationale)

### Au-delà du standard

#### **Pour les aveugles**
- TTS intégré natif OS pour tutos audio (indépendant des lecteurs d'écran)
- Raccourcis clavier personnalisables avec profils pré-configurés ("Une main", "Pieds uniquement")
- Annonces contextuelles enrichies ("Piste 1 Batterie, volume -6dB, 3 clips, durée 8 mesures")
- Support NVDA (Windows), JAWS (Windows), VoiceOver (macOS)

#### **Pour les malvoyants**
- Zoom UI 100% à 200% sans perte de fonctionnalité (Ctrl + molette)
- Thèmes contraste élevé 7:1 (dépasse standard AA 4.5:1) : clair/sombre/dyslexie
- Épaisseur traits personnalisable (1px/2px/4px) pour waveforms et contrôles
- Espacement augmenté entre éléments cliquables (44x44px min, dépasse WCAG AA)

#### **Pour les daltoniens**
- Palette optimisée deutéranopie/protanopie/tritanopie (rouge→orange, vert→cyan)
- Motifs/icônes/textures complètent systématiquement les couleurs
- Simulation daltonisme en temps réel (mode développeur)
- États multiples : couleur + icône + texte (ex: mute = orange + ❌ + "MUTE")

#### **Pour les sourds et malentendants**
- Retours visuels audio : waveforms temps réel, spectrogramme animé, VU-mètres colorés
- Métronome visuel : flash écran + curseur timeline synchronisé BPM
- Alertes visuelles clipping : flash rouge + bordure piste en surcharge
- Sous-titres automatiques pour toutes annonces vocales IA tuteur
- Support futur retour haptique (manettes vibration, MIDI controllers)

#### **Pour les dyslexiques**
- Police OpenDyslexic intégrée (empattements alourdis, taille 18px min, interligne 1.5)
- Textes simplifiés : phrases <20 mots, vocabulaire sans jargon
- Icônes accompagnant tous textes (double encodage visuel)
- Mode lecture : surlignage ligne active, masquage lignes adjacentes

#### **Pour les TDAH**
- Mode Focus : masque éléments non-essentiels (barre outils secondaire)
- Réduction distractions : pas de pop-ups intrusives, notifications discrètes
- Pas d'animations inutiles (transitions rapides <200ms)
- Structuration visuelle claire : zones délimitées, code couleur limité (max 5 couleurs)

#### **Pour les troubles autistiques (TSA)**
- Prévisibilité absolue : shortcuts constants, disposition fixe, pas de réorganisation auto
- Confirmations avant actions destructives ("Supprimer piste ? Oui/Non")
- Mode Simplicité : interface monochrome, 0 animation, luminosité réduite
- Désactivation sons UI (bips, clics) en option

#### **Pour les handicaps moteurs**
- Navigation 100% clavier (aucune souris nécessaire)
- Support contrôleurs adaptatifs : Xbox Adaptive Controller, pédaliers MIDI, joysticks bouche
- Macro-commandes (1 raccourci = séquence actions complexes)
- Zones cibles élargies : tous boutons ≥44x44px, sliders zone cliquable ±20px
- Assistance pointage : snap-to-grid fort, magnétisme curseur, drag & drop tolérant ±10px

---

## 🔎 Observable 3 : Documentation des spécifications sur l'accessibilité

Et pour finir sur cet observable voilà ce qu'on a mit en place comme règles pour l'accéssibilité :

### 3.1 Règles d’implémentation technique

* Utilisation de `AccessibilityHandler` (JUCE) pour tous les composants
* Définition d’un rôle et d’un nom accessible pour chaque élément interactif
* Notification des changements d’état (focus, valeur, activation)
* Ordre de navigation cohérent avec l’interface

---

### 3.2 Contraintes d’accessibilité

* Navigation 100% clavier (Tab, Shift+Tab, Entrée)
* Focus toujours visible
* Contraste minimum : **7:1**
* Zones interactives ≥ **44x44 px**
* Aucune information uniquement par la couleur

---

### 3.3 Méthodes de validation

* Tests avec lecteurs d’écran (NVDA, VoiceOver)
* Vérification navigation clavier complète
* Tests contraste et zoom (jusqu’à 200%)
* Tests utilisateurs (si applicable)

---

### 3.4 Accessibilité de la documentation

* Structure claire (titres hiérarchisés)
* Formats lisibles (Markdown / PDF)
* Texte simple et compréhensible
* Descriptions pour les éléments visuels

---

### 3.5 Référentiel technique utilisé

Les règles définies s’appuient sur les standards suivants :
* WCAG 2.1 (niveau AA minimum)
* RGAA 4.1 (référentiel français)
* WAI-ARIA (rôles et attributs)

Bonnes pratiques d’accessibilité des systèmes Windows et macOS
---
