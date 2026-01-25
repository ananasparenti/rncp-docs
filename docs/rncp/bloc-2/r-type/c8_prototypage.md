# C8 : Prototypage & Contraintes du Projet

## 🔎 Observable 1 : Démarche de prototypage

Le prototypage a été une étape clé dans le développement du projet **R-Type**. Il a permis de valider les choix techniques, d’anticiper les difficultés et d’optimiser l’architecture du jeu avant la mise en production.

---

### 1. Évolution des prototypes

Le projet s’est articulé autour de plusieurs prototypes successifs, chacun répondant à des objectifs précis :

- **Prototype 1 :** Mise en place d’un ECS minimal et développement d’un serveur simple.
- **Prototype 2 :** Ajout d’une fenêtre graphique en SFML, mise en place d’un serveur plus complexe et utilisation partielle de l’ECS.
- **Problèmes identifiés :** L’ECS n’est pas exploité pleinement, faible modularité et forte dépendance aux bibliothèques externes.

<div align="center">
	<img src="../../../../assets/images/c8-schema-prototypage.png" alt="Schéma d'évolution des prototypes" width="70%" style="margin: 1em 0;"/>
	<br><em>Schéma d’évolution des prototypes</em>
</div>

---

### 2. Prototype final

Suite à l’analyse des prototypes précédents, le prototype final a intégré plusieurs améliorations majeures :

- Utilisation complète de l’ECS pour une meilleure modularité et évolutivité.
- Abstraction des bibliothèques réseau et graphiques afin de limiter la dépendance et faciliter la maintenance.
- Remplacement de la SFML par SDL2 pour le rendu graphique, conformément aux choix techniques validés lors de la veille.

Ce processus itératif a permis d’aboutir à une architecture robuste, adaptée aux besoins du projet et facilement extensible.

---

## 🔎 Observable 2 : Comparatif des prototypes

Le développement du projet R-Type a été guidé par plusieurs contraintes majeures, qui ont influencé les choix techniques et organisationnels.

---

### 1. Synthèse des contraintes

<div align="center">
	<img src="../../../../assets/images/c8-tableau-contraintes.png" alt="Tableau des contraintes du projet" width="70%" style="margin: 1em 0;"/>
	<br><em>Tableau des contraintes du projet</em>
</div>

| Contraintes                | Description                        | Impact                        |
|----------------------------|------------------------------------|-------------------------------|
| Multijoueur en temps réel  | 4 joueurs en simultané             | Latences très faibles         |
| Performance réseau         | Bande passante limitée             | Compression nécessaire        |
| Accessibilité              | Changement de touche, daltonisme   | Paramétrage de l’utilisateur  |
| Maintenabilité             | Projet long terme                  | Architecture propre           |
| Temps de développement     | Délais étudiants                   | Réutilisation maximale        |

---

### 2. Impact sur le prototypage

