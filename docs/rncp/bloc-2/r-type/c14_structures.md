# C14 : Structures de Données

## 🔎 Observable 1 : Structures de données utilisées

Le choix des structures de données a été guidé par les besoins de performance, de maintenabilité et d’évolutivité de l’application. Différents types de structures ont été sélectionnés en fonction de leur complexité algorithmique et de leur usage dans le projet R-Type.

### 1. Synthèse des structures utilisées

- **Structures ordonnées (O(log O))**
	- `std::map` : utilisé pour stocker les scores des joueurs, permettant un accès ordonné et efficace.

- **Structures séquentielles (O(n))**
	- `std::vector` : utilisé pour la gestion des noms aléatoires des joueurs, facilitant l’itération séquentielle.

- **Structures à accès direct (O(1))**
	- `std::unordered_map` : utilisé pour le chargement rapide des textures grâce à des lookups fréquents.
	- `std::array` : utilisé pour la récupération des touches, adapté à un nombre fixe d’éléments.

<div align="center">
	<img src="../../../../assets/images/c14-structs.png" alt="Tableau des structures de données utilisées" width="70%" style="margin: 1em 0;"/>
	<br><em>Tableau des structures de données utilisées</em>
</div>

---

## 🔎 Observable 2 : Justification des choix

Chaque structure de données a été choisie pour répondre à un usage précis, en tenant compte de la complexité et des besoins du projet.

### 1. Détail des choix et justifications

- **std::map**
	- *Usage :* Gestion des scores
	- *Complexité :* O(log O)
	- *Justification :* Permet de maintenir un ordre sur les scores, essentiel pour les classements.

- **std::vector**
	- *Usage :* Stockage des noms aléatoires des joueurs
	- *Complexité :* O(n)
	- *Justification :* Idéal pour l’itération séquentielle et la gestion dynamique de la taille.

- **std::unordered_map**
	- *Usage :* Chargement des textures
	- *Complexité :* O(1)
	- *Justification :* Lookup très rapide, adapté à des accès fréquents et non ordonnés.

- **std::array**
	- *Usage :* Récupération des touches
	- *Complexité :* O(1)
	- *Justification :* Nombre de touches fixe, accès direct et rapide.

---

Ces choix de structures de données permettent d’optimiser les performances de l’application tout en assurant une bonne maintenabilité et une évolutivité du code.
