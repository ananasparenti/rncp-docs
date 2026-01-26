# Introduction au projet SupraForce

Ce projet consiste à concevoir une *plateforme d’automatisation* inspirée de services tels que *IFTTT* ou *Zapier*, permettant d’interconnecter différents services numériques entre eux à travers des règles Action → REAction.

<div align="center">
    <img src="../../../assets/images/supraforce-intro.png" alt="Bannière Action-Reaction" width="90%" style="margin: 2em 0;"/>
</div>

L’utilisateur peut créer des scénarios automatisés appelés AREA, qui déclenchent automatiquement une réaction lorsqu’un événement précis survient sur un service donné (réception d’un email, création d’un fichier, événement temporel, etc.).

Le projet repose sur une architecture client–serveur :
- un serveur applicatif centralisant toute la logique métier,
- un client web,
- un client mobile.

Les clients servant uniquement d’interface utilisateur via une API REST.

## Objectifs du projet

Créer une plateforme d’automatisation complète, modulaire et extensible, incluant :
- Une gestion complète des utilisateurs (inscription, authentification, OAuth2)
- Un système de services interconnectables (Google, Mail, Timer, etc.)
- La définition d’Actions déclenchant des événements
- La définition de Réactions exécutant des tâches automatisées
- Un moteur de hooks permettant l’exécution automatique des AREA
- Une API REST claire et documentée
- Une interface web et mobile accessibles et intuitives
- Un déploiement via Docker Compose garantissant la portabilité du projet

L’objectif n’est pas de réinventer les services existants, mais d’assembler intelligemment des briques logicielles afin de proposer une solution robuste, évolutive et maintenable, mettant l’accent sur l’architecture, l’intégration et la qualité globale du système.