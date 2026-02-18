# C1 : Recenser les besoins du client et des utilisateurs

### Contexte de l'analyse

Dans le cadre du développement de SoundWise, nous avons mené une phase d'analyse des besoins auprès de professionnels du secteur musical afin d'identifier :

- Les usages réels
- Les problématiques actuelles
- Les attentes fonctionnelles
- Les besoins spécifiques liés à l'accessibilité

Cette démarche s’inscrit dans une logique de conception centrée utilisateur, avec une attention particulière portée aux personnes en situation de handicap.

### Parties prenantes interrogées

- Plusieurs producteurs de musique indépendants
- Un ingénieur du son professionnel
- Un producteur malvoyant utilisant des logiciels de MAO

Les échanges ont été réalisés sous forme d’entretiens semi-directifs et d’un questionnaire structuré diffusé en ligne. Les retours ont permis de couvrir l’ensemble du scope fonctionnel envisagé pour SoundWise (interface, pédagogie, accessibilité, assistance intelligente).

TODO : mettre image du nombre de participants au forms

## 🔎 Observable 1 : Analyse des besoins clients

### Usages identifiés

Les professionnels interrogés utilisent quotidiennement des logiciels de MAO (DAW), des plugins de mixage ainsi que des contrôleurs MIDI dans un cadre professionnel ou semi-professionnel.

Leur usage principal concerne :

TODO : mettre photo de question 2

### Problématiques identifiées

Les échanges ont permis de faire ressortir plusieurs difficultés récurrentes :

TODO : mettre photo de question 6 et 12

### Attentes fonctionnelles exprimées

Les parties prenantes souhaitent :

TODO : mettre photo de question 11

### Synthèse de l’analyse

L’analyse des échanges et du questionnaire a permis de définir les priorités fonctionnelles de SoundWise :
- Concevoir une interface intuitive
- Intégrer un accompagnement pédagogique en temps réel
- Réduire la complexité perçue des outils de production

Cette phase a couvert l’ensemble du périmètre fonctionnel du projet et constitue la base des choix de conception réalisés.

## 🔎 Observable 2 : Analyse des besoins - accessibilité

### Identification des besoins spécifiques

Les échanges menés, notamment avec un producteur malvoyant, ainsi que les réponses au questionnaire ont permis d’identifier plusieurs limites dans les logiciels de MAO actuels :
- Interfaces très visuelles et peu adaptées aux déficiences visuelles
- Compatibilité partielle avec les lecteurs d’écran
- Navigation clavier incomplète
- Manque de hiérarchisation claire des informations
- Surcharge cognitive liée à la densité des interfaces

Ces constats démontrent que l’accessibilité est encore insuffisamment prise en compte dans les outils de production musicale.

### Prise en compte des normes en vigueur

Les besoins identifiés ont été analysés au regard des principes d’accessibilité numérique (notamment les recommandations WCAG), afin de garantir :
- Une navigation alternative (clavier, assistance vocale)
- Une structuration claire et logique des contenus
- Un contraste visuel adapté
- Une simplification des parcours utilisateurs

### Impacts sur la conception de SoundWise

L’analyse a conduit à intégrer dès la phase de conception :
- Une navigation entièrement accessible au clavier
- Une compatibilité avec les lecteurs d’écran
- Une interface épurée et hiérarchisée
- Un accompagnement pédagogique progressif réduisant la charge cognitive

Ainsi, les besoins des personnes en situation de handicap ont été intégrés en amont du développement, conformément aux exigences de la compétence C1 (C01.2).