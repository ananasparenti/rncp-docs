# C3 : Spécifications techniques et fonctionnelles

## 🔎 Observable 1 : Documentation des spécifications

### 1. Spécifications Fonctionnelles (SF)

#### 1.1 Contexte et objectifs
Soundwise est un **assistant DAW pédagogique et accessible**, complémentaire aux DAW existants (FL Studio, Ableton Live, Logic Pro). Il ne remplace pas ces outils mais **s'y intègre** pour guider les **débutants** et **personnes en situation de handicap** dans la production musicale.

**Liens directs avec l'audit C2** :
- **Contraintes techniques** (C2.2) : Environnements hétérogènes (multi-OS, configs variables), latence audio critique <10ms, dépendance plugins VST → Soundwise doit être **léger, modulaire, non-intrusif**.
- **Accessibilité** (C2.4) : DAW existants ont interfaces denses, faible compatibilité lecteurs d'écran (NVDA/JAWS), navigation clavier limitée → Soundwise intègre **standards dès la conception** (focus clavier, retours sémantiques).
- **Investigation terrain** (C2 Observable 2) : Besoins réels de producteurs (dont 1 malvoyant) → IA tuteur pour courbe d'apprentissage progressive.

**Objectifs V1** :
- Production beats simples via **guidage IA** (3 niveaux).
- **Interface épurée** évolutive (features débloquées).
- Accessibilité **complète** (clavier only, annonces vocales).

#### 1.2 Périmètre fonctionnel V1
| Inclus V1 (focus IA/UI) | Hors périmètre V1 (contraintes C2.2) |
|-------------------------|--------------------------------------|
| Gestion projet basique (créer/ouvrir) | MIDI/plugins VST avancés |
| Timeline simple (play/stop/loop sur 4 pistes audio) | Effets pro (EQ/compresseur) |
| **IA tuteur** (tutos vocaux, déblocage progressif) | Export master/mastering |
| **Navigation clavier 100%** + lecteur d'écran | Support multi-utilisateur |
| Drag & drop samples basiques | Intégration hardware avancée (MIDI controllers) |

#### Tableau fonctionnalités mis à jour
| Module         | Description                        | Critères d'acceptation                   |
| -------------- | ---------------------------------- | ---------------------------------------- |
| Gestion projet | Créer/ouvrir/sauvegarder           | Format JSON custom, auto-save 30s [C2.2] |
| Pistes audio   | Ajouter/mute/solo/volume (4 max)   | WAV/MP3 drag&drop, latence <10ms [C2.2]  |
| Timeline       | Play/stop/loop/zoom                | Curseur clavier, annonces vocales [C2.4] |
| IA Tuteur      | Tutos vocaux, déblocage progressif | 3 niveaux, intégration NVDA [C2 Obs2]    |
| VST Host | Host complet | Scan auto, 10 plugins max, apprentissage usage | [C2.2] |
| Accessibilité  | Clavier only, lecteur d'écran      | WCAG AA, focus visible partout [C2.4]    |


### 2. Spécifications Techniques (ST) - Soundwise V1.0

#### 2.1 Architecture globale
- **Framework** : JUCE C++ (audio cross-platform, VST host natif) [C2.2 multi-OS]
- Schéma : UI (JUCE Components) → Audio Engine (AudioProcessor) → Persistance (JSON)
- Multiplateforme : Windows/macOS/Linux

#### 2.2 Stack technique
| Composant | Techno | Justification C2 |
|-----------|--------|------------------|
| Audio | JUCE RtAudio | Latence <10ms temps réel [C2.2] |
| UI | JUCE LookAndFeel | Accessibilité native (focus, ARIA) [C2.4] |
| VST Host | JUCE VST3Wrapper | Pédagogie VST + perf optimisée[C2.2] |
| IA | Local LLM (ex: simple GPT-like) | Pas de cloud, perf faible |
| Persistance | nlohmann/json + ZIP | Projets <10MB rapides |

#### 2.3 Détails techniques par module
| Module | Implémentation | Contraintes perf/access |
|--------|----------------|-------------------------|
| Audio Engine | AudioDeviceManager, buffer 256 samples | CPU <15%, latence <10ms [C2.2] |
| UI Access | AccessibilityHandler JUCE | NVDA/JAWS : annonces "Piste 1 mute" [C2.4] |
| VST | AudioPluginInstance (2 max) | Pas de GUI VST (audio only pour access) [C2.2] |
| IA Tuteur | Text-to-Speech (JUCE?) | Voix synthé locale, 3 scripts prédéfinis |

#### 2.4 Non-fonctionnel
- **Perf** : <20% CPU idle, testé i5/8GB [C2.2]
- **Access** : WCAG AA desktop (contrast 4.5:1, clavier nav) [C2.4]
- **Sécurité** : Pas de réseau V1, projets locaux [C2 Obs2]
- **Déploiement** : Standalone EXE/DMG, deps minimales

## 🔎 Observable 2 : Accessibilité

## 🔎 Observable 3 : Documentation des spécifications sur l'accessibilité