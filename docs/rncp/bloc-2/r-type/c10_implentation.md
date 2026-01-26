# 📚 Documentation RNCP: Implémentation de l’Architecture

## 🔎 Architecture Clean Code & RAII

### Modèle RAII de l'Engine
Le modèle RAII (Resource Acquisition Is Initialization) garantit que les ressources sont correctement libérées lorsque l'objet sort de portée. Dans cette classe `Engine`, la copie et l'assignation sont désactivées pour éviter des comportements indésirables, et le destructeur assure un nettoyage automatique.

```cpp
class Engine {
 public:
  Engine(const Engine&) = delete;            // Pas de copie
  Engine& operator=(const Engine&) = delete; // Pas d'assignation
  ~Engine() { shutdown(); }                  // Cleanup automatique
  
  bool initialize();
  void shutdown();
  void update();
};
```

### Bénéfices de RAII
- ✅ **Pas de fuite mémoire** : Destructeur garanti
- ✅ **Pas de double-free** : Gestion sécurisée de la mémoire
- ✅ **Lifecycle explicite** : Prévisibilité des opérations

---

## 🔎 Exemples de Systèmes

### Système de Mouvement du Joueur
Ce système gère le mouvement du joueur en récupérant les composants nécessaires et en appliquant des transformations basées sur l'entrée du joueur. Il inclut également un lissage de l'entrée pour éviter les mouvements saccadés.

```cpp
void PlayerMovementSystem::update_entity(EntityId e, float dt) {
  // 1️⃣ Récupération des composants
  auto* tr = get_component<Transform>(e);
  auto* vel = get_component<Velocity>(e);
  auto* in  = get_component<PlayerInput>(e);
  auto* mv  = get_component<MovementStats>(e);
  if (!tr || !vel || !in || !mv) return;
  
  // 2️⃣ Lissage de l'input (anti-jitter)
  in->update_smooth_input(dt);
  
  // 3️⃣ Accélération et Décélération
  float target_vx = in->input_x * mv->max_speed;
  vel->vx = move_towards(vel->vx, target_vx, mv->acceleration * dt);
  
  // 4️⃣ Mise à jour de la position
  tr->x += vel->vx * dt;
  
  // 5️⃣ Application des contraintes de bord
  apply_boundary_constraints(tr, vel);
}
```

### Système d'IA des Ennemis
Ce système permet aux ennemis de détecter le joueur et de se déplacer vers lui tout en évitant le chevauchement avec d'autres ennemis. Il gère également le tir en fonction d'un cooldown.

```cpp
auto nearest = find_nearest_player(entity, transform);
if (nearest && distance <= config_.detection_range) {
  // Calcul de l'offset de formation (évite le stacking)
  float angle = fmod(entity * 37.0F, 360.0F) * PI/180.0F;
  Transform target{
    player->x + cos(angle) * config_.formation_radius,
    player->y + sin(angle) * config_.formation_radius
  };
  
  // Poursuite de la cible
  move_towards_target(velocity, transform, &target, enemy->current_speed);
}

// Tir sur cooldown
if (shoot_timers_[entity] <= 0.0f && shoot_callback_) {
  shoot_callback_(entity);
  shoot_timers_[entity] = config_.shoot_cooldown;
}
```

### Comportements de l'IA (Pattern Strategy)
Les comportements des ennemis sont définis par un pattern de stratégie, permettant une flexibilité dans leur comportement en fonction de la situation.

- 🟢 **PASSIF** : Se déplace selon un pattern
- 🔴 **AGRESSIF** : Chasse et tire
- 🛡️ **DÉFENSIF** : Fuit si le joueur est trop proche
- 🎯 **CHASSE** : Poursuit jusqu'à destruction

---

## 🔎 Design Patterns et Réseau Asynchrone

### Utilisation des Design Patterns
Les design patterns sont utilisés pour structurer le code de manière efficace, facilitant la gestion des composants et des comportements.

| **Pattern** | **Usage** | **Fichier** |
|-------------|-----------|--------------|
| **Registry** | Auto-allocation des TypeID des composants | `Component.hpp` |
| **Service Locator** | Injection des managers (Component/Entity) | `System.hpp` |
| **Strategy** | Comportements IA (Agressif/Défensif) | `EnemyAISystem.cpp` |
| **Observer** | Synchronisation réseau déclenchée par timer | `PlayerMovementSystem.cpp` |
| **Factory** | Création d'entités via `Engine::create_entity()` | `Engine.hpp` |

### UDP Asynchrone et Validation
Ce code gère la réception asynchrone de paquets UDP, en vérifiant les erreurs et en analysant les données reçues pour dispatcher les handlers appropriés.

```cpp
socket_.async_receive_from(buf, sender, 
  [this](error_code ec, size_t n) {
    // 1️⃣ Vérification d'erreur et taille minimale
    if (!ec && n >= sizeof(PacketHeader)) {
      // 2️⃣ Analyse de l'en-tête et du payload
      if (protocol_.parsePacket(buf.data(), n, header, payload, err)) {
        // 3️⃣ Dispatch du handler (HELLO/INPUT/etc.)
        handlePacket(header, payload, sender);
      }
    }
    // 4️⃣ Réarmement de la boucle (pattern de réarmement)
    receivePackets();
  }
);
```

### Qualité du Réseau
Les caractéristiques de qualité du réseau garantissent une communication efficace et fiable entre les clients et le serveur.

- ✅ **Validation stricte** des payloads
- ✅ **Pas de blocage** : Traitement asynchrone
- ✅ **Réarmement automatique** : Pas d'oubli dans le traitement
