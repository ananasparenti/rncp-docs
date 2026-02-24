# C3 : Spécifications techniques et fonctionnelles

## 🔎 Observable 1 : Documentation des spécifications

### 1. Spécifications Fonctionnelles (SF)

#### 1.1 Contexte et objectifs
Soundwise est un **assistant DAW pédagogique et accessible**, complémentaire aux DAW existants (FL Studio, Ableton Live, Logic Pro). Il ne remplace pas ces outils mais **s'y intègre** pour guider les **débutants** et **personnes en situation de handicap** dans la production musicale.

**Fonctionnalités basées sur les contraintes observées** :
- **Contraintes techniques**: Environnements hétérogènes (multi-OS, configs variables), latence audio critique <10ms, dépendance plugins VST → Soundwise doit être **léger, modulaire, non-intrusif**.
- **Accessibilité** : DAW existants ont interfaces denses, faible compatibilité lecteurs d'écran (NVDA/JAWS), très peu sont donc accéssible et adapter au personne en situation de handicape souhaitant produire de la musique.
- **Investigation terrain** : Besoins réels de producteurs constatés (dont 1 malvoyant) → IA tuteur hybride pour courbe d'apprentissage progressive + assistance à la création
                   → Interface plus simple et plus intuitive.


#### 1.2 Périmètre fonctionnel
| Périmètre | Hors périmètre/implémentation future |
|-------------------------|--------------------------------------|
| Gestion projet basique (créer/ouvrir) | MIDI/plugins VST avancés |
| Timeline (play/stop/pistes audio) | Effets pro (EQ/compresseur) |
| IA tuteur hybride :Tutos prédéfinis (utilisation logiciel) Assistant conversationnel (aide création musicale)| Export master/mastering |
| **Navigation clavier 100%** + lecteur d'écran | Accéssibilité pour mal entendant |
| Drag & drop samples basiques | Support multi-utilisateur temps réel |
| Mixer basique (volume/pan/mute/solo) | Développement de plugins/VST internes |
| MIDI/plugins VST simples (2 max simultanés) | Intégration hardware avancée (MIDI controllers) |
| Multi-plateforme (MacOS et Windows) | Navigation par commande vocale |
| Bibliothèque samples intégrée (kicks, snares, basses) | Accès encadrés aux données internes (VST avancés) |
| Historique annuler/refaire | IA tuteur avancé (déblocage progressif fonctionnalités) |
| | Marketplace communautaire (partage samples/modéle IA) |
| | Synchronisation cloud des projets |

### 2. Spécifications Techniques (ST)

#### 2.1 Architecture globale
- **Framework** : JUCE C++ (audio temps réel, GUI, VST host, multi‑OS).

##### **Choix de l'architecture**
Architecture logicielle modulaire basée sur un ECS (Entity-Component-System) pour :

- Entités : pistes audio, clips, projets, sessions utilisateur
- Composants : état audio (buffer, position), métadonnées (nom, couleur), accessibilité (labels ARIA), progression pédagogique
- Systèmes : lecture audio, rendu UI, IA tuteur, persistance JSON, gestion accessibilité

- Séparation claire en couches : Audio Engine (JUCE), Interface Accessible, IA Tuteur, Persistance (JSON pour auto-save/backups de projets), afin de garantir maintenabilité et extensibilité.

##### **Pourquoi un ECS ?**
- **Modularité** : Permet déblocage progressif fonctionnalités (IA tuteur active/désactive systèmes/composants selon niveau utilisateur)
- **Performance** : Optimisation cache-friendly pour traitement audio temps réel
- **Évolutivité** : Ajout de nouvelles fonctionnalités (marketplace, sync cloud) sans refactoring massif
- **Intégration IA** : Contexte ECS transmis à l'API IA pour suggestions adaptatives (ex: "L'utilisateur a 3 pistes, suggérer ajout batterie")

#### 2.2 Stack technique
| Composant | Techno | Justification C2 | Infrastructure |
|-----------|--------|------------------|----------------|
|Audio Engine| JUCE RtAudio | Latence <10ms temps réel |❌ Local (0€) |
|UI Accessible | JUCE LookAndFeel + Accessibility | HandlerAccessibilité native NVDA/JAWS |❌Local (0€) |
|VST Host| JUCE VST3Wrapper | Pédagogie VST, perf optimisée |❌ Local (0€) |
|IA Tuteur - Tutos| JSON + Text-to-Speech natif OSScripts prédéfinis accessibles hors-ligne |❌ Local (0€)|
|IA Tuteur - Conversationnel |API Claude (Anthropic) via HTTPS |Guidage création musicale contextuel |✅ Cloud (API + BDD)|
| Sauvegarde projets |JSON (nlohmann/json C++)Auto-backup léger + versionning |❌ Local (0€)|
|Base de données |Supabase PostgreSQLHistorique conversations IA + progression |✅ Cloud (0€ tier gratuit)|
|Authentification |Supabase AuthComptes utilisateurs sécurisés RGPD |✅ Cloud (inclus gratuit) |


#### 2.3 Architecture IA Tuteur hybride
L'IA tuteur combine deux modes complémentaires pour répondre aux contraintes d'accessibilité et de pédagogie :
Mode A : Tutos prédéfinis (local, 100% hors-ligne)
Usage : Apprentissage utilisation du logiciel (navigation, fonctionnalités de base)
Implémentation :

- Scripts JSON embarqués dans l'application
- Text-to-Speech natif OS (Windows SAPI / macOS AVSpeechSynthesizer)
- Déclenchement contextuel selon actions utilisateur

Sécurité et performance :

- Requêtes HTTPS chiffrées (TLS 1.3)
- Rate limiting : 20 requêtes/minute/utilisateur (anti-abus)
- Timeout : 5 secondes max (sinon fallback message prédéfini)
- Pas de stockage API keys côté client (proxy API Gateway)

#### 2.4 Infrastructure cloud (IA conversationnelle uniquement)
Justification : L'IA tuteur hybride sépare :

Tutos prédéfinis → local, accessibles hors-ligne
Assistant conversationnel → cloud, pour guidage personnalisé création musicale

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

### Références normatives

**Standards internationaux** :
- **WCAG 2.1** (W3C) : https://www.w3.org/TR/WCAG21/
- **ARIA 1.2** (W3C) : https://www.w3.org/TR/wai-aria-1.2/
- **RGAA 4.1** (France) : https://www.numerique.gouv.fr/publications/rgaa-accessibilite/
- **Section 508** (USA) : https://www.section508.gov/

**Guides d'implémentation** :
- JUCE Accessibility API : https://docs.juce.com/master/tutorial_accessibility.html
- Microsoft Inclusive Design : https://www.microsoft.com/design/inclusive/
- Apple Human Interface Guidelines (Accessibility) : https://developer.apple.com/design/human-interface-guidelines/accessibility

---
### Tests utilisateurs prévus

#### **Phase 1 - Alpha (mois 3-4)** : Tests fonctionnels de base

| Handicap | Testeurs | Profil | Objectif |
|----------|----------|--------|----------|
| **Aveugles** | 3 | Producteurs avec ≥2 ans expérience NVDA/JAWS/VoiceOver | Navigation 100% clavier + annonces lecteur écran |
| **Malvoyants** | 3 | Basse vision (DMLA, cataracte, albinisme) | Zoom 200%, contraste 7:1, lisibilité thèmes |
| **Daltoniens** | 2 | Deutéranopes et protanopes | Palette accessible + motifs/icônes complémentaires |
| **Sourds** | 2 | Musiciens sourds ou malentendants | Retours visuels audio (waveforms, spectrogramme) |
| **Dyslexiques** | 2 | Utilisateurs police OpenDyslexic | Simplification textes + lisibilité interface |
| **Handicaps moteurs** | 3 | Clavier seul, pédalier MIDI, contrôleur adaptatif | Navigation clavier + raccourcis personnalisables |

**Total** : 15 utilisateurs

**Protocole test (1h30/personne)** :
1. Onboarding libre (15 min) : Découverte app sans consignes
2. Tâche guidée (30 min) : "Créez un projet avec 3 pistes, ajoutez des clips, mixez et exportez"
3. Tâche libre (30 min) : "Créez ce que vous voulez, explorez les fonctionnalités"
4. Interview qualitative (15 min) : Points de friction, suggestions amélioration

**Livrables** :
- Grille bugs accessibilité (bloquant/majeur/mineur)
- Rapport SUS (System Usability Scale) par profil handicap
- Vidéos sessions anonymisées (avec consentement)
- Backlog priorisé corrections accessibilité

---

#### **Phase 2 - Beta (mois 6-7)** : Audit externe + tests élargis

| Type test | Détails | Critères succès |
|-----------|---------|-----------------|
| **Audit AccessiWeb** | Entreprise certifiée RGAA/WCAG | Score ≥80/100, 0 erreur bloquante niveau A, ≤5 erreurs mineures AA |
| **Tests automatisés** | axe-core, Pa11y (contraste), WAVE (structure) | 0 erreur critique, <10 warnings justifiés |
| **Tests élargis** | 20 utilisateurs (5 par catégorie handicap) | Utilisation réelle 2 semaines, collecte retours terrain |

---

#### **Phase 3 - Release Candidate (mois 9)** : Certification finale

| Action | Description | Critère validation |
|--------|-------------|-------------------|
| **Re-test utilisateurs** | 10 testeurs initiaux (phase 1) | Score SUS ≥75/100 (amélioration ≥10 points vs phase 1) |
| **Audit conformité final** | Vérification 100% critères WCAG 2.1 AA | Conformité totale niveau AA + 50% critères AAA |
| **Documentation publique** | Déclaration accessibilité (RGAA obligatoire) | Page web publique + PDF accessible |
| **Tests réels terrain** | 50 utilisateurs beta (inscription ouverte) | Taux satisfaction accessibilité ≥80% |


---
