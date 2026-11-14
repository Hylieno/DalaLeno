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

## Particularités des workflows GitHub Actions

Les points suivants décrivent les 12 workflows inspectés le 14 novembre 2026. Relire les workflows avant une opération distante : ces mécanismes peuvent évoluer.

### Compilation, publication et commits automatiques

- Les quatre dépôts sources compilent sous Windows avec .NET 10, téléchargent Dalamud, puis exécutent `dotnet restore` et un build Release. DalaLeno ne compile pas les plugins.
- DalaFacing et DalaUndercut : `ci.yml` compile sur les push vers `dev`/`main` et les PR ciblant ces branches, puis conserve un artifact. `dev-release.yml` compile et crée ou met à jour la préversion GitHub `dev-latest` à chaque push sur `dev`. `release.yml` compile et publie à chaque push sur `main`, y compris documentaire, sauf si le message du commit de tête contient `[skip release]`. La publication est indépendante du workflow CI et n'attend pas son succès. Aucun de ces workflows ne commet de package dans le dépôt source.
- Leur `promote.yml`, lancé uniquement par `workflow_dispatch`, crée ou réutilise une PR `dev → main`; il ne fusionne pas et ne vérifie pas lui-même les tests en jeu. La validation et la décision de fusion restent humaines.
- LogogramHelperFR : push sur `main` → `build-package.yml` → commit de `dist/latest.zip`; push sur `dev-language-toggle` → `build-dev-package.yml` → commit de `dist/dev-latest.zip`. Le canal DEV utilise temporairement l'identité `LogogramHelperFRDev`, puis restaure les sources avant de committer le ZIP.
- OccultWeakness : push sur `main` → `build-package.yml` → commit de `dist/latest.zip`. Aucune automatisation de publication n'est définie pour `fix-icon-padding`.
- Ces workflows LogogramHelperFR/OccultWeakness acceptent aussi `workflow_dispatch` et font un checkout explicite de leur branche de publication, indépendamment de la référence choisie pour le lancement manuel. Ils valident la présence de la DLL et du manifest dans le ZIP, puis committent et poussent uniquement si le package change, avec `[skip ci]`. Un push ne touchant que le ZIP du canal est exclu par `paths-ignore`. Ils ne créent aucune GitHub Release.

### Releases existantes et traçabilité

- Pour DalaFacing et DalaUndercut, le tag stable provient de `<Version>` du `.csproj`, avec retrait du dernier suffixe `.0` (`0.2.3.0` → `v0.2.3`). Si la release existe déjà, `gh release upload --clobber` remplace ses ZIP : réutiliser une version peut donc modifier une publication existante sans créer de nouveau tag.
- Le même remplacement s'applique au ZIP de `dev-latest`. Lors des mises à jour, le workflow ne déplace pas le tag existant; son commit cible peut être ancien alors que l'asset a été renouvelé.
- L'augmentation de version et la correspondance projet/manifest/package ne sont pas imposées par ces workflows. Ne pas considérer une release existante comme immuable ni son tag DEV comme une preuve suffisante du commit compilé.

### Synchronisation du catalogue DalaLeno

- `build-packages.yml` se déclenche sur les push vers `main` touchant `pluginmaster.json`, `pluginmaster-dev.json` ou le workflow lui-même, sur `workflow_dispatch`, ainsi que sur les PR `opened`, `reopened` et `synchronize`, sans filtre de branche ni de chemin pour les PR.
- Il fait toujours un checkout de `main`, télécharge les packages publiés et tente de committer/pousser les ZIP et icônes modifiés sur `main`, avec `[skip ci]`. Aucune condition ne réserve cette écriture aux push ou lancements manuels : ouvrir ou mettre à jour une PR peut donc tenter de modifier la distribution sur `main`, sans tester le contenu de la PR. La réussite dépend des permissions du jeton et des protections de branche; les PR de forks ont normalement des droits restreints.
- Il copie les ZIP stables de LogogramHelperFR/OccultWeakness depuis leurs `main`, les ZIP stables de Facing/Undercut depuis leurs dernières releases et leurs ZIP DEV depuis `dev-latest`. Il télécharge et valide l'icône Facing et génère celle d'Undercut. Il ne modifie pas les manifests JSON.
- Aucune planification ni notification inter-dépôts n'est définie : publier un package source ne lance pas directement cette synchronisation. Vérifier qu'un événement propre à DalaLeno intervient après disponibilité des packages. Le DEV LogogramHelperFR fait exception à la copie : son manifest pointe directement vers `dev-language-toggle/dist/dev-latest.zip` dans le dépôt source.
- Les tests d'intégrité ZIP et de l'icône Facing ne vérifient pas la concordance des versions internes avec `AssemblyVersion`. Contrôler cette cohérence séparément; l'écart Undercut DEV (`0.3.5.0` au catalogue contre `0.3.5.1` dans le package inspecté) est documenté dans `docs/DECISIONS.md` et `docs/ROADMAP.md`.

### Précautions avant une opération distante

- Avant tout push, fusion de PR, création/mise à jour de PR ou lancement manuel, identifier les workflows applicables et leurs effets sur les releases, les ZIP et le catalogue. Un changement documentaire peut aussi publier ou synchroniser des packages.
- Avant une opération sur une branche stable, vérifier les versions, tags et assets existants, les packages attendus, le succès de la CI et les validations en jeu requises. Pour Facing/Undercut, vérifier le message final du commit sur `main` si une publication doit être évitée; `[skip release]` ne désactive que le job stable concerné, pas la CI ni la publication DEV.
- Les protections de branches, approbations, permissions Actions et autorisations de création de PR ne sont pas garanties par les fichiers YAML. Les vérifier sur GitHub au besoin; une consigne de validation n'est pas une barrière technique.
- Les README de Facing/Undercut mentionnent encore l'ancienne branche `release`; suivre les workflows actuels et les conventions `dev/main` définies ici, sans recréer `release`.

## Modèles de référence pour les nouveaux plugins Dalamud

DalaFacing et DalaUndercut sont les modèles de référence pour créer et gérer les futurs plugins. Sauf justification technique particulière, documentée pour le nouveau plugin, reprendre leur organisation :

- `dev` pour le développement et les tests; `main` réservée aux versions stables.
- Compilation et validation automatiques avec GitHub Actions, notamment sur les push et les pull requests concernées.
- Publication automatique d'une préversion `dev-latest` après les modifications poussées sur `dev`.
- Après validation, notamment en jeu si nécessaire, promotion contrôlée par pull request `dev → main`, sans fusion automatique.
- Publication automatique d'une GitHub Release stable après validation et fusion dans `main`.
- Gestion cohérente de la version du projet, des tags, des manifests et des ZIP; tenir compte des risques de remplacement et de traçabilité décrits ci-dessus plutôt que les reproduire sans examen.
- Intégration au catalogue DalaLeno lorsque nécessaire, en prévoyant la cohérence des versions et la synchronisation effective des packages.

Cette règle concerne uniquement les nouveaux plugins. Elle n'autorise ni la migration automatique des plugins existants utilisant un autre fonctionnement, ni une publication sans demande explicite. Préserver notamment les flux actuels de LogogramHelperFR et OccultWeakness.

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

### Environnement de compilation Codex Cloud

Cette consigne concerne les quatre dépôts de plugins : `LogogramHelperFR`, `OccultWeakness`, `DalaUndercut` et `DalaFacing`.

Dans Codex Cloud, avant toute commande `dotnet` (`restore`, `build`, `test`, etc.), activer l'environnement existant dans le shell qui exécutera la commande :

```bash
source /workspace/.cloud-setup/activate.sh
```

Cette activation est nécessaire parce que .NET 10 n'est pas directement disponible dans le shell par défaut. Après activation, utiliser les SDK et dépendances Dalamud déjà installés. Pour compiler sous Linux, conserver les paramètres de compatibilité nécessaires, notamment `-p:EnableWindowsTargeting=true`.

Ne pas réinstaller .NET ou Dalamud inutilement : vérifier d'abord les outils disponibles et respecter les configurations propres à chaque projet.

## Limites de ce dépôt

Les documents de ce dépôt décrivent l'écosystème, mais le dépôt source de chaque plugin reste la référence pour son implémentation. Si le code contredit un document historique, corriger la documentation ou signaler l'écart avant de changer le comportement.
