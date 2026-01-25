# C13 : Persistance des données

## 🔎 Observable 1 : Choix de persistance

Plusieurs types de données doivent être conservés afin d’assurer le bon fonctionnement de l’application, la continuité de l’expérience utilisateur et le respect des besoins fonctionnels du projet.

Les données persistées dans le jeu sont les suivantes :

<div align="center">
	<img src="../../../../assets/images/c13-données-stock.png" alt="Comparatif bibliothèques graphiques" width="70%" style="margin: 1em 0;"/>
	<br><em>Données persistées du projet</em>
</div>

Cette phase d’identification permet de poser un cadre clair avant d’aborder les choix techniques liés aux solutions de persistance, qui seront détaillés dans l’observable suivant.

## 🔎 Observable 2 : Choix de persistance

Suite à l’identification des données à persister dans l’Observable 1, plusieurs solutions de stockage ont été envisagées afin de répondre aux besoins fonctionnels du projet tout en respectant les contraintes techniques, de performance et de sécurité.

Le tableau ci-dessous présente un comparatif des solutions étudiées, avec leurs avantages, inconvénients et cas d’usage typiques :

<div align="center">
	<img src="../../../../assets/images/c13-comp.png" alt="Comparatif bibliothèques graphiques" width="70%" style="margin: 1em 0;"/>
	<br><em>Comparatif des solutions de persistance</em>
</div>

Dans la section suivante, nous détaillerons les solutions effectivement retenues pour le projet R-Type, en expliquant les choix réalisés pour chaque type de donnée et les structures utilisées en mémoire.