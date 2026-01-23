

# 🕵️‍♂️ C6 : Veille Technologique & Accessibilité

---

## 🔎 Observable 1 : Étude comparative

### 📝 Objectif

Choisir la technologie la plus adaptée pour l’interface utilisateur du projet **R-Type**, en tenant compte de l’accessibilité pour les personnes en situation de handicap.

> **Besoins principaux :**
> - Interface graphique claire et lisible
> - Navigation efficace pour différents types de handicaps (visuel, moteur, etc.)

Une étude comparative est nécessaire pour :
- Identifier la solution la plus appropriée
- Éviter la dette technique
- Garantir le respect des obligations légales en matière d’accessibilité numérique (**RGAA** en France)

---

### 🎯 Critères retenus

| Critère                | Détail                                                                 |
|------------------------|------------------------------------------------------------------------|
| **Accessibilité**      | Navigation clavier, couleurs adaptées, compatibilité lecteurs d’écran   |
| **Performance**        | Fluidité et réactivité pour un jeu shoot’em up                         |
| **Portabilité**        | Compatibilité Windows / Linux / MacOS                                  |
| **Intégration**        | Utilisation avec C++ et code existant                                  |
| **Communauté/support** | Documentation et forums pour résoudre les problèmes                    |
| **Évolutivité**        | Ajout de fonctionnalités sans tout réécrire                            |

---

## ⚖️ Synthèse comparative

<div align="center">

| Technologie | Accessibilité 🦯 | Intégration 🔗 | Communauté 🤝 | Performance 🚀 | Points forts / Points faibles |
|:-----------:|:----------------:|:--------------:|:-------------:|:-------------:|:-----------------------------|
| **SDL2**    | ⚠️ Moyenne (adaptable via code) | ⭐⭐⭐⭐ (C++) | ⭐⭐⭐⭐ Large | ⭐⭐⭐⭐ Très bonne | Rapide, portable, flexible.<br>❗ Limité pour le support natif des aides techniques (screen reader…). |
| **SFML**    | ⚠️ Moyenne (exige adaptations)  | ⭐⭐⭐⭐ (C++) | ⭐⭐⭐ Moyenne | ⭐⭐⭐ Bonne      | Simple à utiliser.<br>❗ Moins flexible pour l’accessibilité. |
| **Qt**      | ✅ Excellente (ARIA, screen reader) | ⭐⭐⭐ Moyenne | ⭐⭐⭐⭐ Très large | ⭐⭐⭐ Moyenne | Très complet et accessible.<br>❗ Plus lourd et complexe pour un jeu. |

</div>

---

## Analyse qualitative

Après comparaison :

- **SDL2** offre un excellent compromis entre performance, portabilité et flexibilité, ce qui est crucial pour un jeu comme R-Type.
- Bien qu’elle ne propose pas un support natif complet pour l’accessibilité, des adaptations sont possibles :
	- Raccourcis clavier
	- Contrastes de couleurs
	- Ajustements des contrôles
- Les autres technologies présentent des avantages spécifiques :
	- **Qt** : très accessible mais lourd
	- **SFML** : simple mais limité pour certains besoins
	- **ImGui** : idéal pour des prototypes rapides mais peu adapté aux PSH

---

## Conclusion

La technologie **SDL2** a été retenue pour le projet R-Type, car elle :

- Répond aux critères de performance et de flexibilité
- Permet des adaptations pour l’accessibilité
- S’intègre facilement avec le C++ existant

> La veille réglementaire sur l’accessibilité numérique sera poursuivie afin de continuer à améliorer l’expérience des PSH et rester conforme aux normes en vigueur (**RGAA**).