# C11 : Segmentation du code (Décomposition en sous-problèmes)

## 🎯 Objectif
Segmenter chaque problème complexe en sous-problèmes pour obtenir des tâches atomiques, optimisées pour la performance, l’adaptabilité et la maintenabilité.

---

## 🌐 Architecture modulaire du projet
- **ECS** : données (components) séparées de la logique (systems) dans `engine/` et `game/`.
- **Couche réseau** : serveur dédié (`server/`) isolant rooms, protocole, state broadcast.
- **Couche client/UI** : états et navigation dans `states/`, rendu/audio dans `graphics/`.
- **Ressources** : chargement résolu via `engine/core` et `graphics`.

Schéma mental (simple) :
- Data → Components
- Logic → Systems
- Orchestration → Managers/States
- I/O → Network/Graphics

---

## 🔎 Observables (avec exemples concrets)

### 1) Mouvement isolé (ECS, O(E))
- Fichier : MovementSystem.cpp
- Points clés :
  - Récupère uniquement `Transform` + `Velocity`.
  - Mise à jour position `O(E)` ; clamp optionnel activable (`enable_bounds_checking`).
  - Séparation data/logique → ajout d’autres comportements sans toucher aux composants.

### 2) IA ennemie segmentée par comportement
- Fichier : EnemyAISystem.cpp
- Points clés :
  - Cache joueurs, sélection du plus proche, switch de comportements (passive/aggressive/defensive/hunting).
  - Callbacks de tir injectables (découplage).
  - Complexité contrôlée : recherche puis update unitaire par ennemi.

### 3) Gestion de rooms côté serveur (concurrence et règles métier)
- Fichier : RoomManager.cpp
- Points clés :
  - Mutex pour sûreté, mapping player→room, règles (solo privé, room pleine).
  - Suppression automatique si room vide (invariant local).
  - API claire (`findOrCreateRoom`, `findAvailableRoom`) → adaptation facile.

### 4) Navigation d’états UI (séparation UI / réseau)
- Fichier : StateManager.cpp
- Points clés :
  - Changement d’état centralisé (`changeState`), callbacks réseau injectés selon l’état.
  - Précondition explicite (impossible de lancer GAME sans connexion).
  - Ajout d’un nouvel état = implémentation locale + enregistrement dans le switch.

### 5) Rendu fond défilant (isolement I/O)
- Fichier : BackgroundRenderer.cpp
- Points clés :
  - Chargement via `AssetResolver`, textures gérées localement.
  - Mode scrolling optionnel ; double draw pour tiling.
  - Aucune dépendance gameplay → remplaçable sans impacter la logique.

---

## 🧩 Stratégies de segmentation employées
- **Séparation data / logique (ECS)** : composants purs, systèmes spécialisés.
- **Variantes activables** : ex. bounds check toggle, modes IA, callbacks réseau/UI.
- **Responsabilités locales** : RoomManager encapsule règles d’adhésion/suppression ; StateManager encapsule transitions.
- **Orchestration par couches** : gameplay dans `game/`, moteur générique dans `engine/`, transport dans `server/`, rendu dans `graphics/`.
- **Points d’extension clairs** : ajouter un composant ou un système n’impacte pas les autres (contrats minimalistes).

---

## 📈 Bénéfices (performance, adaptabilité, maintenabilité)
- **Performance** : passes linéaires `O(E)` sur des vues de composants ; options activables sans surcoût global.
- **Adaptabilité** : comportements IA interchangeables, états UI pluggables, assets/rendu remplaçables.
- **Maintenabilité** : contraintes et invariants localisés (rooms, états, mouvement) ; couplage réduit entre couches.

---

## 🛠️ Comment réutiliser le pattern
1. **Identifier la donnée** → créer un component minimal (ex. Health, Velocity).
2. **Isoler la logique** → système dédié qui ne dépend que des composants ciblés.
3. **Encapsuler les variantes** → flags, callbacks, stratégies (ex. `enable_bounds_checking`, behaviors IA).
4. **Garder des points d’entrée clairs** → managers (rooms, states) avec règles explicites.
5. **Mesurer la complexité** → viser des passes linéaires, éviter les doubles boucles inutiles.

---

## 📄 Snippet prêt à montrer (mouvement)
Extrait court et autoportant : MovementSystem.cpp
- Met en avant : séparation data/logique, O(E), clamp optionnel, effet localisé.

---

## 🔚 Conclusion
La segmentation dans ce projet s’appuie sur :
- L’ECS pour découpler données et comportements.
- Des modules dédiés par responsabilité (mouvement, IA, rooms, états, rendu).
- Des variantes activables et des callbacks pour adapter sans réécrire.
Cette approche répond aux exigences de performance, d’adaptabilité et de maintenabilité fixées par la compétence C11.