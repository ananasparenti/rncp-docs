## 🔎 Observable 1 : Choix d'Architecture

### Architecture Client/Serveur + ECS

**Pourquoi ECS ?**
- ✅ Découplage complet entre données (Composants) et logique (Systèmes)
- ✅ Extensibilité: ajouter une feature = créer un Système + des Composants
- ✅ Performance: data-oriented design, cache-friendly
- ✅ Testabilité: chaque système indépendant

**Structure logique:**

```
┌─────────────────────────────────────────────────────────┐
│                   CLIENT (Rendu)                        │
│                                                         │
│  InputManager ←→ UDP ←→ Renderer                        │
└─────────────────────────────────────────────────────────┘
                       ↕
┌─────────────────────────────────────────────────────────┐
│              SERVER (Autorité Logique)                  │
│                                                         │
│  UDP Protocol ←→ Game Loop ←→ ECS Engine               │
│                              ├─ SystemManager          │
│                              ├─ EntityManager          │
│                              └─ ComponentManager       │
└─────────────────────────────────────────────────────────┘
```

---

## 🔎 Observable 1 : Pipeline Moteur

### Engine::update() – Le cœur du système

```cpp
void Engine::update() {
  if (!initialized_) return;
  
  // 1️⃣ Mise à jour timing
  time_manager_.update();
  float delta_time = time_manager_.get_delta_time();
  
  // 2️⃣ Update tous les systèmes
  system_manager_.update_all_systems(delta_time);
  
  // 3️⃣ Cleanup automatique
}
```

**Systèmes registrés (dans l'ordre):**
1. InputSystem
2. MovementSystem
3. EnemyAISystem
4. PhysicsSystem
5. NetworkSyncSystem
6. RenderSystem (côté client)

---

## 🔎 Observable 1 : Pattern ECS

### Template System<ComponentTypes...>

```cpp
template <typename... ComponentTypes>
class System : public ISystem {
 public:
  void update(float dt) override {
    for (EntityId e : entity_manager_->get_entities())
      if (has_required_components(e))
        update_entity(e, dt);  // Traite seulement les entités valides
  }
};
```

**Exemple concret – PlayerMovementSystem:**
- Cherche entités ayant: `Transform`, `Velocity`, `PlayerInput`, `MovementStats`
- Applique la physique du mouvement
- Clamp aux limites écran
- Déclenche sync réseau si nécessaire

---

## 🔎 Observable 2 : Intégration Technique

### Gestion du Lifecycle & Injection de Dépendances

**SystemManager = Service Locator léger**

```cpp
template <typename T, typename... Args>
T* SystemManager::register_system(Args&&... args) {
  auto system = std::make_unique<T>(...);
  system->set_component_manager(&component_manager_);
  system->set_entity_manager(&entity_manager_);
  system->initialize();  // ← Hook d'initialisation
  systems_.push_back(std::move(system));
}
```

**Avantages:**
- ✅ Systèmes ne dépendent que des interfaces (ComponentManager, EntityManager)
- ✅ Pas de couplage croisé entre systèmes
- ✅ Lifecycle garanti: init → update → shutdown

---

## 🔎 Observable 2 : Registry Composants

### Allocation Auto des Type IDs

```cpp
template <typename T>
class ComponentTypeRegistry {
 public:
  static ComponentTypeId get_type_id() {
    // 🔐 Thread-safe: static local variable
    static ComponentTypeId type_id = allocate_component_type_id();
    return type_id;
  }
};

inline ComponentTypeId allocate_component_type_id() {
  static ComponentTypeId next_id = 0;
  return ++next_id;
}
```

**Bénéfice: Extensibilité zéro-coupling**
- Ajouter un composant = créer une classe
- Pas de modification du core ECS
- IDs générés automatiquement et uniques

---

## 🔎 Observable 2 : – Serveur et Intégration Réseau

### Initialisation Serveur

```cpp
GameServer::GameServer(Asamio::IoContext& io, uint16_t port)
  : socket_(io, UdpEndpoint(UdpProtocol::v4(), port)) {
  
  engine_.initialize();
  
  // Enregistre les systèmes
  movement_system_ = engine_.register_system<MovementSystem>();
  enemy_ai_system_ = engine_.register_system<EnemyAISystem>();
  // ... autres systèmes
  
  startGameLoop();  // ← Boucle async UDP
}
```

**Architecture:**
- UDP Protocol Handler ← reçoit packets
- Valide headers, parse payloads
- Dispatch vers systèmes (Input, Spawn, etc.)
- Broadcast state updates aux clients

---

## 🔎 Observable 2 : – Organisation Dossiers

### Modularité par Domaine

```
src/
├── engine/         ← Core ECS (infrastructure)
│   └── core/       Engine, System, Component, Entity
├── game/           ← Domaine applicatif
│   ├── components/ Enemy, Player, Health, etc.
│   └── systems/    EnemyAI, Movement, Projectile, etc.
├── client/         ← Rendu + Input
│   └── Graphics/   Renderer, InputManager
├── server/         ← Réseau autoritaire
│   └── Server.cpp  GameLoop, Protocol, RoomManager
└── common/         ← Partagé client/server
```

**Avantage: Dépendances unidirectionnelles**
- `engine/` ne dépend de rien
- `game/` dépend de `engine/`
- `client/` et `server/` dépendent de `engine/` + `game/`

---

## 🔎 Observable 2 : Pérennité & Extensibilité

### Comme ajouter une nouvelle feature?

**Scénario: Ajouter un système de "Shield"**

1. **Créer les composants:**
   ```cpp
   struct Shield { float health; float recharge_rate; };
   struct ShieldRenderer { /* ... */ };
   ```

2. **Créer le système:**
   ```cpp
   class ShieldSystem : public System<Shield, Health> {
     void update_entity(EntityId e, float dt) override {
       auto* shield = get_component<Shield>(e);
       shield->health = std::min(shield->health + shield->recharge_rate * dt, 100.f);
     }
   };
   ```

3. **Enregistrer:**
   ```cpp
   engine_.register_system<ShieldSystem>();
   ```

**✅ Zéro modification du code existant!**
