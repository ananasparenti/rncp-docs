# C22 — Simplifier l’architecture front avec des frameworks/bibliothèques

## B4 - C22.1 — Composants tiers
- **Select Radix + Lucide** : composant réutilisable construit sur Radix UI, gère accessibilité (clavier, focus), portail, icônes et tailles sans réécrire la logique d’état.

	Pourquoi c’est probant : on délègue la gestion des states ouverts/fermés, du focus management et de l’ARIA à Radix, tout en gardant une personnalisation via `className` et les icônes Lucide. Le composant se branche partout sans recoder la mécanique.

```tsx
// src/components/ui/select.tsx (extrait)
function SelectTrigger({ className, size = "default", children, ...props }) {
	return (
		<SelectPrimitive.Trigger
			data-slot="select-trigger"
			data-size={size}
			className={cn(
				"border-input data-[placeholder]:text-muted-foreground ... data-[size=default]:h-9",
				className,
			)}
			{...props}
		>
			{children}
			<SelectPrimitive.Icon asChild>
				<ChevronDownIcon className="size-4 opacity-50" />
			</SelectPrimitive.Icon>
		</SelectPrimitive.Trigger>
	);
}
```

- **Sidebar data-driven + React Query/TRPC** : navigation alimentée par des requêtes typées et cache géré par TanStack Query, ce qui centralise l’état serveur et réduit le code boilerplate.

	Pourquoi c’est probant : `useQuery` fournit le cache, les états pending/success/error et le revalidate. Les mutations déclenchent refresh et invalidation cohérentes avec TRPC, limitant le code manuel pour la synchro UI ↔ serveur.

```tsx
// src/components/app-sidebar.tsx (extrait)
import { useMutation, useQuery } from "@tanstack/react-query";
...
const { data: impersonationStatus } = useQuery(
	trpc.admin.getImpersonationStatus.queryOptions(undefined, {
		staleTime: 30_000,
	}),
);
const stopImpersonation = useMutation(
	trpc.admin.stopImpersonation.mutationOptions({
		onSuccess: async () => {
			await authClient.getSession();
			router.refresh();
		},
	}),
);
```

## B4 - C22.2 — Gestion d’erreurs côté front
- **Toasts uniformes et thémés** : wrapper Sonner avec icônes Lucide et thème `next-themes`, utilisé globalement via `<Toaster />`.

	Pourquoi c’est probant : un seul point d’entrée pour l’affichage des notifications évite les divergences d’UI/UX et garantit l’accessibilité (icônes, couleur, thème clair/sombre cohérents).

```tsx
// src/components/ui/sonner.tsx (extrait)
const Toaster = ({ ...props }: ToasterProps) => {
	const { theme = "system" } = useTheme();
	return (
		<Sonner
			theme={theme as ToasterProps["theme"]}
			icons={{
				success: <CircleCheckIcon className="size-4" />,
				error: <OctagonXIcon className="size-4" />,
				warning: <TriangleAlertIcon className="size-4" />,
			}}
			style={{ "--normal-bg": "var(--popover)", "--border-radius": "var(--radius)" }}
			{...props}
		/>
	);
};
```

- **Feedback utilisateur sur les erreurs OAuth/REST** : la page Services affiche succès/erreurs traduits (`servicesPage.connectionError` / `disconnectError`) via `toast.error`, et nettoie l’URL après traitement.

	Pourquoi c’est probant : l’utilisateur reçoit un message localisé et contextualisé, l’URL est nettoyée pour éviter des toasts répétés au rechargement, et les erreurs réseau sont catchées pour empêcher les états silencieux.

```tsx
// src/app/(dashboard)/(rest)/services/page.tsx (extrait)
useEffect(() => {
	const connected = searchParams.get("connected");
	const error = searchParams.get("error");
	if (connected) {
		toast.success(t("servicesPage.connectedSuccess").replace("{service}", connected));
		window.history.replaceState({}, "", "/services");
	}
	if (error) {
		toast.error(t("servicesPage.connectionError").replace("{error}", error));
		window.history.replaceState({}, "", "/services");
	}
}, [searchParams, fetchConnectedServices, t]);

const handleDisconnect = async (serviceId: string) => {
	try {
		const response = await fetch(`/api/services/${serviceId}`, { method: "DELETE" });
		if (response.ok) {
			toast.success(t("servicesPage.disconnected"));
		} else {
			toast.error(t("servicesPage.disconnectError"));
		}
	} catch (error) {
		toast.error(`${t("servicesPage.disconnectError")}: ${error}`);
	}
};
```
