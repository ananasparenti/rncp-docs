# C21 : Développement web

## 🔎 Observable 1 : Code opérationnel

## 🔎 Observable 2 : Nommage et formatage

Le code côté client a été structuré de manière claire et cohérente afin de faciliter sa compréhension et sa maintenance.
L’architecture des dossiers et des fichiers repose sur une organisation par fonctionnalités, permettant d’identifier rapidement le rôle de chaque partie de l’application.

``` bash
area-web/
├── .github/                # CI/CD, agents, workflows
├── .vscode/                # Config VS Code
├── area-mobile/            # Code mobile (probablement React Native ou similaire)
├── docker/                 # Dockerfiles, docker-compose, config nginx
├── docs/                   # Documentation (API, déploiement, mobile, web, etc.)
├── mobile/                 # APK ou builds mobiles
├── prisma/                 # ORM Prisma : schéma, migrations
├── public/                 # Fichiers statiques (robots.txt, logos, sitemap)
├── scripts/                # Scripts utilitaires (déploiement, setup, tests)
├── src/                    # Source principale de l’app web
│   ├── app/                # Entrée Next.js (pages, layout)
│   ├── components/         # Composants UI réutilisables
│   ├── config/             # Configurations diverses
│   ├── features/           # Modules fonctionnels (feature-based)
│   ├── hooks/              # Custom React hooks
│   ├── i18n/               # Internationalisation
│   ├── inngest/            # Intégration Inngest (event-driven)
│   ├── lib/                # Fonctions utilitaires, helpers
│   ├── tests/              # Tests unitaires/fonctionnels
│   ├── trpc/               # API tRPC (RPC type-safe)
│   └── __tests__/          # Dossier de tests
├── backend_middleware.ts   # Middleware backend (Next.js API ou custom)
├── next.config.ts          # Config Next.js
├── package.json            # Dépendances et scripts npm
├── tsconfig.json           # Config TypeScript
├── ...                     # Autres fichiers de config, scripts, docs
```

Les fonctions utilisent un nommage explicite et homogène, reflétant précisément leur responsabilité au sein du projet. Cette approche améliore la lisibilité du code et limite les ambiguïtés lors du développement ou de l’évolution de l’application.

| Nom de la fonction           | Fichier (dossier)                                  |
|------------------------------|----------------------------------------------------|
| getMessages                  | src/i18n/config.ts                                 |
| pollUserDropbox              | src/inngest/dropbox-polling.ts                     |
| filterByPath                 | src/inngest/dropbox-polling.ts                     |
| sanitizeNodeData             | src/features/workflows/utils/template-utils.ts      |
| extractRequiredServices      | src/features/workflows/utils/template-utils.ts      |
| createWorkflowTemplate       | src/features/workflows/utils/template-utils.ts      |
| validateTemplate             | src/features/workflows/utils/template-utils.ts      |
| autoLayoutNodes              | src/features/workflows/utils/template-utils.ts      |
| useSession                   | src/lib/auth-client.ts                             |
| sendVerificationEmail        | src/lib/email/service.ts                           |
| sendPasswordResetEmail       | src/lib/email/service.ts                           |
| register                     | src/instrumentation.ts                             |
| useIsMobile                  | src/hooks/use-mobile.ts                            |

Le formatage du code respecte les bonnes pratiques courantes : indentation régulière, commentaires ciblés et mise en forme cohérente sur l’ensemble du projet.

<div align="center">
    <img src="../../../../assets/images/c21-nomage.png" alt="Exemple de code bien indenté et commenté" width="30%" style="margin: 2em 0;"/>
    <br><em>template-utils.ts</em>
</div>

Enfin, une documentation dédiée vient compléter le code afin d’expliquer son fonctionnement global et d’accompagner sa prise en main.

<div align="center">
    <video width="80%" controls style="margin: 2em 0;">
        <source src="../../../../assets/images/c21-doc.mp4" type="video/mp4">
        Votre navigateur ne supporte pas la lecture de vidéos.
    </video>
    <br>
    <em>Démo vidéo illustrant le fonctionnement du projet frontend.</em>
</div>