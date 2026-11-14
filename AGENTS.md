# Instructions permanentes pour Codex

Ce dépôt est le **catalogue et dépôt de distribution** de la famille de plugins Dalamud DalaLeno. Il ne contient pas le code source des plugins. Les sources vivent dans des dépôts GitHub séparés; ne déplacer ni dupliquer leur code ici.

## Vérifications obligatoires avant toute modification

1. Lire ce fichier, le `README.md`, les fichiers `docs/` et les manifests concernés.
2. Inspecter les branches et les workflows du dépôt réellement modifié. Ne jamais déduire le rôle d'une branche uniquement de son nom.
3. Vérifier l'état actuel du code, des versions et des packages; les discussions historiques sont du contexte, pas une source plus fiable que GitHub.
4. Si la branche active ou la cible de publication est ambiguë, demander confirmation avant tout commit, PR ou changement de référence.
5. Ne jamais inclure de secret, jeton, donnée de compte ou identifiant privé.

## Dépôts et branches confirmés

### `Hylieno/DalaLeno`

- `main` est la branche active, stable et publique du catalogue.
- Il n'existe pas de branche `dev` générale pour ce dépôt.
- `trigger-package-build` est une ancienne branche technique contenant seulement un déclencheur; ce n'est pas une branche de développement. Ne pas l'utiliser sans instruction explicite.
- Les changements documentaires et de catalogue vont sur `main` après inspection.
- Le workflow `build-packages.yml` surveille sur `main` les manifests et son propre fichier. Il synchronise les ZIP publiés par les dépôts sources et peut committer les packages sur `main`.

### `Hylieno/DalaFacing`

- `dev` : développement et tests en jeu.
- `main` : stable et publication.
- Développer sur `dev`; CI et le package `dev-latest` y sont produits.
- Après validation en jeu, utiliser le workflow de promotion pour ouvrir une PR `dev` vers `main`.
- Ne pas fusionner automatiquement. La fusion dans `main` déclenche la publication stable, sauf commit marqué `[skip release]`.
- La branche `release` a été supprimée et ne doit pas être recréée.

### `Hylieno/DalaUndercut`

- `dev` : développement et tests en jeu.
- `main` : stable et publication.
- Développer sur `dev`; CI et le package `dev-latest` y sont produits.
- Après validation en jeu, utiliser le workflow de promotion pour ouvrir une PR `dev` vers `main`.
- Ne pas fusionner automatiquement. La fusion dans `main` déclenche la publication stable, sauf commit marqué `[skip release]`.
- La branche `release` a été supprimée et ne doit pas être recréée.

### `Hylieno/OccultWeakness`

- `main` est la seule branche confirmée pour le code actif et le package stable.
- `fix-icon-padding` est une branche historique divergente contenant des fichiers temporaires; elle n'est pas une branche de développement générale.
- En l'absence d'une convention `dev` confirmée, demander confirmation avant une modification fonctionnelle. Ne pas créer de branche ou fusionner `fix-icon-padding` par supposition.
- Le workflow stable construit puis commet `dist/latest.zip` sur `main`.

### `Hylieno/LogogramHelperFR`

- `main` est la branche stable et produit `dist/latest.zip`.
- `dev-language-toggle` est une branche de développement spécifique à la variante bilingue et produit `dist/dev-latest.zip`.
- `build-package-trigger` et `stable-build-trigger` sont des branches techniques/historiques, pas des branches générales de développement.
- Avant tout nouveau travail, vérifier si le changement appartient encore à `dev-language-toggle` ou s'il faut une autre branche; demander confirmation si ce n'est pas évident.
- Ne pas fusionner automatiquement dans `main`.

## Règles Git et publication

- Préserver le flux existant de chaque dépôt; ne pas normaliser tous les dépôts vers une structure unique.
- Une GitHub Release n'est pas une branche. Les noms `release.yml` et `dev-release.yml` désignent des workflows/packages.
- Avant une publication, mettre à jour la version du projet source et vérifier qu'elle correspond à `AssemblyVersion` dans le manifest DalaLeno.
- Les changements visibles par les joueurs doivent augmenter la version pour que Dalamud détecte la mise à jour.
- Pour DalaFacing et DalaUndercut, tester d'abord sur `dev`, puis promouvoir par PR vers `main`.
- Pour les plugins qui committent leurs ZIP générés, ne pas modifier manuellement un ZIP sans comprendre le workflow qui le régénère.
- Ne jamais pousser directement une modification fonctionnelle sur une branche stable lorsqu'une branche de développement confirmée existe.
- Ne jamais fusionner, supprimer une branche, créer une release ou déployer automatiquement sans demande explicite.

## Conventions C#/.NET et Dalamud

- Cibler le SDK et l'API Dalamud déjà déclarés par le projet; actuellement `Dalamud.NET.Sdk/15.0.0`, `DalamudPackager 15.0.0` et .NET 10 sont utilisés.
- Respecter les namespaces de fichier, les types scellés, la nullabilité et les usings implicites lorsqu'ils sont déjà activés.
- Conserver les services Dalamud injectés avec `[PluginService]` et désabonner tous les événements/handlers dans `Dispose()`.
- Encapsuler et commenter le code `unsafe`, les callbacks d'addons et les accès `FFXIVClientStructs`; ils sont sensibles aux mises à jour du jeu et de Dalamud.
- Ne pas bloquer le thread de rendu ou `IFramework.Update`. Les automations doivent être des machines à états avec délais, timeouts, possibilité d'arrêt et vérification de contexte.
- Sauvegarder la configuration via `IPluginConfiguration`/`SavePluginConfig`; borner les valeurs entrées par l'utilisateur.
- Journaliser les erreurs utiles avec `IPluginLog`, sans données sensibles ni spam par frame.
- Conserver les identités (`InternalName`, nom d'assembly et manifest) stables sauf migration volontaire et documentée.

## UI, overlays et contraintes FFXIV

- Les overlays sont rendus avec ImGui et doivent rester lisibles sans bloquer les entrées du jeu lorsqu'ils ne sont pas en mode édition.
- Toute géométrie ancrée dans le monde doit être recalculée par frame et projetée avec `WorldToScreen`; gérer les objets disparus et les projections invalides.
- Ne pas prétendre prédire une mécanique : DalaFacing affiche la rotation actuelle de l'acteur.
- Pour les automations d'interface, vérifier que l'addon attendu est visible et prêt avant chaque callback. Arrêter proprement si le contexte servant/Market Board est perdu.
- Respecter les différences HQ/NQ, les propres servants et le prix plancher. Aucun prix ne doit descendre sous 1 gil.
- Les plugins sont des outils tiers non officiels; éviter toute fonction qui dépend d'une exploitation serveur ou d'informations non accessibles au client.

## Validation minimale

- Documentation/catalogue : valider le JSON et les liens/versions touchés.
- Code C# : `dotnet restore`, puis `dotnet build -c Release` avec l'environnement Dalamud attendu.
- Workflow : vérifier la syntaxe YAML et les branches/paths de déclenchement.
- Overlay ou automation : un build réussi ne remplace pas un test en jeu. Signaler clairement ce qui n'a pas été testé dans FFXIV.

## Limites de ce dépôt

Les documents de ce dépôt décrivent l'écosystème, mais le dépôt source de chaque plugin reste la référence pour son implémentation. Si le code contredit un document historique, corriger la documentation ou signaler l'écart avant de changer le comportement.
