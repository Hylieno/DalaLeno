# Décisions et historique technique

## Statut des informations

- **Confirmé** : vérifié dans le code, les branches ou les workflows actuels.
- **Historique** : décision issue des discussions et cohérente avec le dépôt, mais pouvant dater d'une version antérieure.
- **Incertain** : nécessite une validation du propriétaire ou un test en jeu.

## Branches et publication

### `main` remplace l'ancienne branche `release` — Confirmé

DalaFacing et DalaUndercut ont utilisé une structure à trois branches. La branche `release` était redondante avec `main`; elle a été supprimée. La convention actuelle est :

- `dev` pour développer et tester;
- PR manuelle `dev → main` pour promouvoir;
- `main` pour la version stable et les GitHub Releases.

Les workflows `release.yml` restent valides : « release » y désigne la publication GitHub, pas une branche.

### Ne pas uniformiser les autres dépôts — Confirmé

OccultWeakness n'a pas de branche `dev` générale. LogogramHelperFR possède une branche spécifique `dev-language-toggle`, pas une convention générique réutilisable sans vérification. DalaLeno publie directement son catalogue depuis `main`.

## DalaLeno comme dépôt de distribution — Confirmé

Les plugins ne sont pas contenus directement dans DalaLeno. Le dépôt centralise les manifests, icônes et packages nécessaires à l'installation. Les dépôts sources restent propriétaires de leur code et de leurs builds.

Les entrées stables pointent vers `DalaLeno/main/plugins/.../latest.zip`. DalaFacing et DalaUndercut publient d'abord leurs GitHub Releases; le workflow DalaLeno en copie les assets. OccultWeakness et LogogramHelperFR publient actuellement des ZIP committés dans leurs propres dépôts.

## Versioning — Confirmé

- `AssemblyVersion` du manifest doit correspondre à la version contenue dans le ZIP.
- Une modification visible par les joueurs doit augmenter la version pour que Dalamud propose la mise à jour.
- DalaFacing et DalaUndercut lisent la version depuis leur `.csproj` lors de la publication stable.
- Les workflows retirent le suffixe `.0` final pour construire certains tags (`0.2.3.0` → `v0.2.3`).

## DalaFacing

### Rotation réelle plutôt que modèle animé — Confirmé

La flèche utilise `actor.Rotation`. Cette orientation est plus fiable que la posture visuelle d'un modèle tordu ou masqué par les joueurs. Elle ne représente toutefois que le facing actuel et ne prédit pas une mécanique future.

### Overlay projeté plutôt qu'effet injecté — Historique et confirmé dans l'implémentation

Le choix a été de construire une géométrie 3D en coordonnées monde, de la projeter et de la dessiner via ImGui. Cela évite d'injecter un effet 3D dans le moteur du jeu et réduit la fragilité aux patches.

### Volume, profondeur et faces visibles — Confirmé

La flèche initialement pensée comme indicateur simple est devenue un volume extrudé. Le tri utilise la profondeur issue de la matrice vue-projection afin d'éviter les erreurs d'ordre de dessin. Les contours excluent les arêtes internes et suivent les faces visibles.

### Sélection persistante par objet — Confirmé

Le plugin mémorise le `GameObjectId` de la cible choisie. Il continue donc à suivre le même acteur lorsque la cible du joueur change. La sélection cesse naturellement de produire un overlay lorsque l'objet quitte la table d'objets.

## DalaUndercut

### Données live du Market Board — Confirmé

Le calcul utilise `IMarketBoard.OfferingsReceived`; il ne dépend pas d'une API web externe. Le plugin n'agit que dans le contexte UI du servant et du Market Board.

### HQ/NQ et servants personnels — Confirmé

Les offres sont filtrées selon la qualité détectée. Par défaut, les propres servants sont ignorés pour éviter de s'undercut soi-même. Si aucun concurrent correspondant n'existe, le prix reste inchangé.

### Prix plancher — Confirmé

Le minimum configurable est borné à 1 et le calcul final applique à nouveau un plancher absolu de 1 gil. Cette contrainte ne doit pas être supprimée.

### Automation par machine à états — Confirmé

L'automation ne doit pas enchaîner des clics aveuglément. Elle attend la visibilité et l'état prêt des addons, applique un délai configurable, possède un timeout et s'arrête lorsque le contexte est perdu. Le bouton latéral `Undercut All` et l'arrêt manuel font partie du comportement attendu.

## OccultWeakness

### Association par nom — Confirmé

Les faiblesses sont enregistrées par nom de monstre afin d'éviter des IDs jugés instables. La casse peut être prise en compte ou ignorée selon la configuration.

### Overlay fixe configurable — Confirmé

L'overlay est placé près de la barre de cible, pas directement dans le monde 3D. Le mode placement active les entrées et permet de déplacer la rangée; hors de ce mode, la fenêtre ne capture pas les entrées.

### Branche d'icône inachevée — Incertain

`fix-icon-padding` contient des fichiers temporaires et diverge de `main`. Une variante d'icône avec marges a été préparée historiquement, mais son statut final n'est pas fiable. Ne pas fusionner cette branche sans inspection et confirmation.

## Logogram Helper FR+

### Respect du projet original — Confirmé

Le fork conserve le crédit à apetih, le lien Ko-fi et un avis tiers. Les ajouts FR+ incluent la localisation bilingue, `/logo`, le stock mémorisé et les sources des logogrammes.

### Identité DEV distincte — Confirmé

Le workflow DEV transforme temporairement l'assembly et le manifest vers `LogogramHelperFRDev` afin de produire un package distinct, puis restaure les sources avant de committer uniquement le ZIP généré.

## Écarts documentaires observés

- Le README actuel de DalaFacing mentionne encore `release` et une baseline v0.2.0, alors que les branches/workflows et le `.csproj` confirment `dev/main` et v0.2.3.0.
- `pluginmaster-dev.json` annonce DalaUndercut DEV 0.3.5.0, mais l'inspection de `plugins/DalaUndercut/dev-latest.zip` confirme que son assembly et son manifest internes sont en 0.3.5.1. Le catalogue DEV est donc désynchronisé; cette migration le documente sans modifier le manifest.
- Le README DalaLeno liste trois plugins dans sa section dédiée, alors que le manifest stable en contient quatre avec DalaFacing.

Ces écarts sont documentés ici; cette migration n'altère aucune fonctionnalité ni métadonnée existante.
