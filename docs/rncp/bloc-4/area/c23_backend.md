# C23 — Développement back-end avec frameworks/bibliothèques

## B4 - C23.1 — Code serveur opérationnel
- **CRUD Credentials via tRPC + Prisma + Zod** : routes `create/update/remove/getMany` sécurisées, validation d’entrée et accès scellé à `ctx.auth.user.id`.

	Pourquoi c’est probant : Zod bloque les payloads malformés, `protectedProcedure` impose l’auth, Prisma applique le filtrage `userId` côté requête pour garantir l’isolement des données. Le chiffrement est appliqué à l’enregistrement.

```ts
// src/features/credentials/server/routers.ts (extrait create/update)
create: protectedProcedure
	.input(z.object({ name: z.string().min(1), type: z.enum(CredentialType), value: z.string().min(1) }))
	.mutation(({ ctx, input }) =>
		prisma.credential.create({
			data: { name: input.name, userId: ctx.auth.user.id, type: input.type, value: encrypt(input.value) },
		}),
	),
update: protectedProcedure
	.input(z.object({ id: z.string(), name: z.string().min(1), type: z.enum(CredentialType), value: z.string().min(1) }))
	.mutation(({ ctx, input }) =>
		prisma.credential.update({
			where: { id: input.id, userId: ctx.auth.user.id },
			data: { name: input.name, type: input.type, value: encrypt(input.value) },
		}),
	),
```

- **Orchestration métiers Workflows** : activation/deactivation déclenche Inngest et webhooks GitHub/Dropbox, stocke l’état en base.

	Pourquoi c’est probant : l’activation pilote les intégrations externes (webhooks GitHub, triggers Dropbox) et la persistance Prisma assure la traçabilité de l’état des triggers par utilisateur.

```ts
// src/features/workflows/server/routers.ts (extrait)
if (githubTriggerNodes.length > 0 && input.isActive) {
	setupGitHubWebhooksForWorkflow(input.id, ctx.auth.user.id, githubTriggerNodes)
		.then(({ success, errors }) => { if (!success) console.error("[GitHub] Webhook setup errors:", errors); });
}
const dropboxTriggerNode = workflow.nodes.find((node) => isDropboxTrigger(node.type));
if (dropboxTriggerNode && input.isActive) {
	await prisma.dropboxWorkflowTrigger.upsert({
		where: { workflowId: input.id },
		create: { workflowId: input.id, userId: ctx.auth.user.id, triggerType, isActive: true, folderPath: nodeData.folderPath ?? null },
		update: { triggerType, isActive: true, folderPath: nodeData.folderPath ?? null },
	});
}
```

## B4 - C23.2 — Gestion des ressources
- **Connexion unique Prisma** : singleton via `globalThis`, adapter Postgres, évite la création multiple de clients.

	Pourquoi c’est probant : en environnement Next.js/SSR, éviter la multiplication de clients limite la pression sur le pool de connexions et améliore la stabilité.

```ts
// src/lib/db.ts (extrait)
import { PrismaPg } from "@prisma/adapter-pg";
import { PrismaClient } from "@prisma/client";
const adapter = new PrismaPg({ connectionString: `${process.env.DATABASE_URL}` });
const globalForPrisma = globalThis as unknown as { prisma: PrismaClient };
export const prisma = globalForPrisma.prisma || new PrismaClient({ adapter });
if (process.env.NODE_ENV !== "production") {
	globalForPrisma.prisma = prisma;
}
```

- **Pagination + sélection minimale** : `skip/take`, `count` en parallèle, et omission du champ sensible.

	Pourquoi c’est probant : réduit l’IO, garde les secrets hors des réponses, et fournit les méta-infos de pagination pour un front efficace.

```ts
// src/features/credentials/server/routers.ts (extrait getMany)
const [items, totalCount] = await Promise.all([
	prisma.credential.findMany({
		skip: (page - 1) * pageSize,
		take: pageSize,
		where: { userId: ctx.auth.user.id, name: { contains: search, mode: "insensitive" } },
		orderBy: { updatedAt: "desc" },
		select: { id: true, name: true, type: true, createdAt: true, updatedAt: true },
	}),
	prisma.credential.count({ where: { userId: ctx.auth.user.id, name: { contains: search, mode: "insensitive" } } }),
]);
```

- **Logs maîtrisés** : insertion + purge contrôlée (`LOG_RETAIN_LIMIT`, batch delete) dans une transaction.

	Pourquoi c’est probant : la table de log ne grossit pas indéfiniment, on conserve un historique récent sans compromettre le stockage, et l’écriture est atomique.

```ts
// src/lib/request-logger.ts (extrait)
await prisma.$transaction(async (tx) => {
	await tx.apiRequestLog.create({ data: { method: payload.method, path: payload.path, statusCode: payload.statusCode, responseBody: responsePreview, durationMs: payload.durationMs } });
	const stale = await tx.apiRequestLog.findMany({ select: { id: true }, orderBy: { createdAt: "desc" }, skip: LOG_RETAIN_LIMIT, take: LOG_PRUNE_BATCH });
	if (stale.length) {
		await tx.apiRequestLog.deleteMany({ where: { id: { in: stale.map((s) => s.id) } } });
	}
});
```

## B4 - C23.3 — Nommage et formatage
- **Conventions TS claires** : noms explicites (`getMany`, `getByType`), enums Prisma (`CredentialType`), schémas Zod nommés, destructuration lisible.

	Pourquoi c’est probant : améliore la lisibilité, réduit l’ambiguïté fonctionnelle et facilite la relecture/revue.

```ts
// src/features/credentials/server/routers.ts (extrait en-tête)
import { CredentialType } from "@prisma/client";
import z from "zod";
import { createTRPCRouter, protectedProcedure } from "@/trpc/init";

export const credentialsRouter = createTRPCRouter({
	getByType: protectedProcedure
		.input(z.object({ type: z.enum(CredentialType) }))
		.query(({ input, ctx }) =>
			prisma.credential.findMany({ where: { type: input.type, userId: ctx.auth.user.id }, orderBy: { updatedAt: "desc" } }),
		),
});
```

- **Constantes et types dédiés** : SCREAMING_SNAKE pour seuils, `LogPayload`, wrappers (`withApiLogging`) conformes aux conventions.

	Pourquoi c’est probant : clarifie l’intention (limites, types d’entrée), évite la duplication magique des valeurs et prépare les extensions futures.

```ts
// src/lib/request-logger.ts (extrait)
const RESPONSE_PREVIEW_LIMIT = 2000;
const LOG_RETAIN_LIMIT = 100;
type LogPayload = {
	method: string;
	path: string;
	statusCode: number;
	responseBody?: string | null;
	durationMs?: number;
};
export const withApiLogging = (handler) => async (req, context) => { /* ... */ };
```
