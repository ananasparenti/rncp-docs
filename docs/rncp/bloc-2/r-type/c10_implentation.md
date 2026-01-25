## 🚀 SLIDE 10 : C10 Observable 1 – Implémentation

### Clean Code & RAII

**Engine = Modèle RAII:**
```cpp
class Engine {
 public:
  Engine(const Engine&) = delete;           // Pas de copie
  Engine& operator=(const Engine&) = delete;
  ~Engine() { shutdown(); }                 // Cleanup auto
  
  bool initialize();
  void shutdown();
  void update();
};
```

**Bénéfices:**
- ✅ Pas de fuite mémoire (destructeur garanti)
- ✅ Pas de double-free
- ✅ Lifecycle explicite et prévisible

---

## 🕹️ SLIDE 11 : C10 Observable 1 – Exemple: Mouvement Joueur

### PlayerMovementSystem – La vraie complexité

```cpp
void PlayerMovementSystem::update_entity(EntityId e, float dt) {
  // 1️⃣ Récupère les composants
  auto* tr = get_component<Transform>(e);
  auto* vel = get_component<Velocity>(e);
  auto* in  = get_component<PlayerInput>(e);
  auto* mv  = get_component<MovementStats>(e);
  if (!tr || !vel || !in || !mv) return;
  
  // 2️⃣ Smoothing input (anti-jitter)
  in->update_smooth_input(dt);
  
  // 3️⃣ Accélération / Décélération
  float target_vx = in->input_x * mv->max_speed;
  vel->vx = move_towards(vel->vx, target_vx, mv->acceleration * dt);
  
  // 4️⃣ Physique
  tr->x += vel->vx * dt;
  
  // 5️⃣ Clamp écran
  apply_boundary_constraints(tr, vel);
}
```

**Concepts:**
- Composition (Transform + Velocity + Input + Stats)
- Interpolation physique (smooth acceleration)
- Synchronisation réseau optionnelle

---

## 🤖 SLIDE 12 : C10 Observable 1 – Exemple: IA Ennemis

### EnemyAISystem – Strategy Pattern

```cpp
auto nearest = find_nearest_player(entity, transform);
if (nearest && distance <= config_.detection_range) {
  // Calcule offset formation (évite stacking)
  float angle = fmod(entity * 37.0F, 360.0F) * PI/180.0F;
  Transform target{
    player->x + cos(angle) * config_.formation_radius,
    player->y + sin(angle) * config_.formation_radius
  };
  
  // Poursuit la cible
  move_towards_target(velocity, transform, &target, enemy->current_speed);
}

// Tir sur cooldown
if (shoot_timers_[entity] <= 0.0f && shoot_callback_) {
  shoot_callback_(entity);
  shoot_timers_[entity] = config_.shoot_cooldown;
}
```

**Comportements (Strategy):**
- 🟢 PASSIVE: se déplace selon pattern
- 🔴 AGGRESSIVE: chasse + tir
- 🛡️ DEFENSIVE: fuit si joueur trop proche
- 🎯 HUNTING: poursuit jusqu'à destruction

---

## 📡 SLIDE 13 : C10 Observable 2 – Design Patterns

### Registry, Service Locator, Strategy

| Pattern | Usage | Fichier |
|---------|-------|---------|
| **Registry** | Auto-allocation TypeID composants | `Component.hpp` |
| **Service Locator** | Injection managers (Component/Entity) | `System.hpp` |
| **Strategy** | Comportements IA (Aggressive/Defensive) | `EnemyAISystem.cpp` |
| **Observer** | Network sync déclenché par timer | `PlayerMovementSystem.cpp` |
| **Factory** | Création entités via `Engine::create_entity()` | `Engine.hpp` |

---

## 🌐 SLIDE 14 : C10 Observable 2 – Réseau Asynchrone

### UDP Async + Validation

```cpp
socket_.async_receive_from(buf, sender, 
  [this](error_code ec, size_t n) {
    // 1️⃣ Vérifie pas d'erreur et taille min
    if (!ec && n >= sizeof(PacketHeader)) {
      // 2️⃣ Parse header + payload
      if (protocol_.parsePacket(buf.data(), n, header, payload, err)) {
        // 3️⃣ Dispatch handler (HELLO/INPUT/etc.)
        handlePacket(header, payload, sender);
      }
    }
    // 4️⃣ Réarme la boucle (rearm pattern)
    receivePackets();
  }
);
```

**Qualité:**
- ✅ Validation stricte payloads
- ✅ Pas de blocking (async)
- ✅ Rearm automatique (pas d'oubli)
