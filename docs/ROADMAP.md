# État et feuille de route

## État vérifié au 14 novembre 2026

| Projet | Version/catalogue observé | État |
|---|---:|---|
| DalaLeno | quatre entrées stables | Catalogue et synchronisation opérationnels sur `main` |
| DalaFacing | 0.2.3.0 | Stable; `dev` et `main` actuellement alignées |
| DalaUndercut | 0.3.5.1 stable | Stable; `dev` et `main` actuellement alignées |
| OccultWeakness | 0.1.0.1 | Stable sur `main`; branche historique d'icône non intégrée |
| Logogram Helper FR+ | 0.1.8.8 stable | Stable sur `main`; développement bilingue spécifique conservé sur `dev-language-toggle` |

## Terminé

### Infrastructure

- Catalogue stable DalaLeno et flux DEV.
- Synchronisation automatique des packages.
- Validation des ZIP avant publication dans le catalogue.
- Structure `dev → PR → main` et publications stable/DEV pour DalaFacing et DalaUndercut.
- Suppression des branches `release` redondantes de ces deux dépôts.

### DalaFacing

- Sélection et suivi d'un PNJ de combat précis.
- Flèche 3D volumétrique configurable.
- Orientation par rotation réelle de l'acteur.
- Tri par profondeur et amélioration des faces/contours visibles.
- Commandes `select` et `clear`.

### DalaUndercut

- Bouton latéral sur la liste du servant.
- Parcours automatique des emplacements.
- Données live du Market Board.
- Filtrage HQ/NQ et exclusion optionnelle des propres servants.
- Under-cut et prix minimum configurables avec plancher absolu de 1 gil.
- Délais, timeouts, diagnostics et arrêt manuel.

### OccultWeakness

- Base de monstres configurable.
- Plusieurs faiblesses par monstre.
- Overlay repositionnable et personnalisable.
- Icônes élémentaires provenant du jeu.

### Logogram Helper FR+

- Interface bilingue FR/EN.
- Stock mémorisé et recettes possibles.
- Sources des logogrammes et enrichissement des tooltips.
- Package stable et package DEV distinct.

## Problèmes connus ou validations restantes

### Documentation et métadonnées

- Corriger ultérieurement le README de DalaFacing, qui décrit encore l'ancienne branche `release` et une ancienne baseline.
- Mettre à jour le README de DalaLeno pour inclure DalaFacing dans la liste des plugins.
- Corriger dans un commit séparé `pluginmaster-dev.json` pour DalaUndercut : il annonce 0.3.5.0, alors que le ZIP DEV inspecté contient 0.3.5.1.

### Tests en jeu

- DalaUndercut : revalider les cas Market Board vide, aucune offre de la qualité demandée, propres servants seulement, timeout réseau et interruption manuelle.
- DalaUndercut : les callbacks et IDs de composants UI sont sensibles aux patches FFXIV/Dalamud; refaire une validation après toute mise à jour majeure.
- DalaFacing : tester la lisibilité, l'ordre des faces et les contours depuis plusieurs angles de caméra et avec des acteurs de tailles variées.
- OccultWeakness : confirmer le statut désiré de l'icône avec marges avant de fermer ou supprimer la branche historique.
- Logogram Helper FR+ : vérifier si `dev-language-toggle` reste la branche active souhaitée pour les prochains changements ou si son travail doit être promu/archivé.

## Travaux prévus ou candidats

Les éléments suivants sont des suites raisonnables documentées, pas des engagements automatiques :

1. Résoudre les écarts de README et de manifests dans des commits documentaires séparés.
2. Ajouter des tests automatisés pour les calculs purs : prix d'undercut, plancher, filtrage HQ/NQ et migrations de configuration.
3. Isoler davantage les adaptateurs UI sensibles aux patches dans DalaUndercut afin de faciliter leur mise à jour.
4. Documenter une procédure de test en jeu reproductible avant chaque promotion stable.
5. Ajouter des fichiers `AGENTS.md` propres aux dépôts sources si Codex Cloud doit travailler directement dans chacun d'eux; le présent fichier couvre l'écosystème depuis le dépôt DalaLeno mais ne remplace pas des instructions locales spécialisées.

## Hors périmètre de cette migration

- Aucun changement fonctionnel.
- Aucun changement de version ou de manifest.
- Aucune fusion de branche.
- Aucune publication de plugin.
- Aucun nettoyage des branches historiques.
