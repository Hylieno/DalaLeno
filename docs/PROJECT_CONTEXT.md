# Contexte du projet DalaLeno

## Objectif

DalaLeno est un dépôt Dalamud personnalisé pour FINAL FANTASY XIV. Il publie des manifests et des packages installables depuis les dépôts sources de plusieurs plugins. L'URL stable consommée par Dalamud est :

`https://raw.githubusercontent.com/Hylieno/DalaLeno/main/pluginmaster.json`

Le dépôt DalaLeno n'est pas un monorepo : il agrège des ZIP, des icônes et des métadonnées, tandis que chaque plugin conserve son propre dépôt et son propre cycle de développement.

## Composants confirmés

| Composant | Dépôt source | Rôle | Distribution actuelle |
|---|---|---|---|
| DalaLeno | `Hylieno/DalaLeno` | Catalogue, manifests, icônes et copie des packages | `pluginmaster.json`, `pluginmaster-dev.json`, `plugins/*` |
| DalaFacing | `Hylieno/DalaFacing` | Flèche 3D indiquant l'orientation actuelle d'un PNJ de combat sélectionné | Stable et DEV via GitHub Releases, copiés dans DalaLeno |
| DalaUndercut | `Hylieno/DalaUndercut` | Repricing automatisé des ventes d'un servant à partir des offres live du Market Board | Stable et DEV via GitHub Releases, copiés dans DalaLeno |
| OccultWeakness | `Hylieno/OccultWeakness` | Association manuelle des monstres d'Occult Crescent à leurs faiblesses et overlay d'icônes | `dist/latest.zip`, copié dans DalaLeno |
| Logogram Helper FR+ | `Hylieno/LogogramHelperFR` | Assistant Logos bilingue pour Eureka, basé sur le plugin original d'apetih | `dist/latest.zip`; flux DEV spécifique dans son dépôt |

## Fonctionnalités principales

### DalaFacing

- Sélection explicite de la cible courante par UI ou `/dalafacing select`.
- Suivi de l'objet par `GameObjectId`, même si le joueur change ensuite de cible.
- Flèche volumétrique extrudée, orientée selon la rotation réelle de l'acteur.
- Hauteur, longueur, largeur, épaisseur, couleurs, opacité et contour configurables.
- Projection 3D vers l'écran et tri des triangles selon la profondeur caméra.
- Suppression de la sélection par UI ou `/dalafacing clear`.

Le but est de rendre le facing lisible dans les combats chargés, quand la flèche au sol ou le modèle du boss sont difficiles à distinguer. Le plugin montre l'orientation actuelle; il ne prédit pas une orientation future enregistrée par une mécanique.

### DalaUndercut

- Bouton `Undercut All` attaché à la liste des ventes du servant.
- Parcours contrôlé des 20 emplacements de vente.
- Lecture des offres live via `IMarketBoard`.
- Calcul séparé HQ/NQ, possibilité d'ignorer ses propres servants et undercut configurable.
- Prix minimum configurable, borné à au moins 1 gil.
- Machine à états avec délai, attente de fenêtres prêtes, timeout, arrêt manuel et arrêt si le contexte servant est perdu.
- Fenêtre de diagnostic via `/dlundercut`.

### OccultWeakness

- Enregistrement d'une ou plusieurs faiblesses par nom de monstre.
- Éléments actuellement supportés : feu, glace, vent et foudre; terre et eau restent dans l'enum pour relire d'anciennes configurations.
- Overlay fixe près de la barre de cible, avec taille, espacement, position et fond configurables.
- Mode placement par glisser-déposer.
- Ajout depuis la cible actuelle ou saisie manuelle; activation, édition et suppression des entrées.
- Configuration via `/ocweak`.

### Logogram Helper FR+

- Interface Logos en français ou en anglais.
- Commande `/logo` et accès aux fenêtres principales/détails.
- Filtrage des actions, recettes et calcul du nombre de créations possibles selon le dernier stock mémorisé.
- Capture et persistance du stock depuis le mélangeur Logos.
- Sources des logogrammes par éclat et enrichissement des tooltips d'objets.
- Crédit et lien Ko-fi conservés pour l'auteur original apetih.

## Publics et contraintes

- Utilisateurs de XIVLauncher/Dalamud sur Windows.
- Dépendance forte aux APIs Dalamud, aux addons UI de FFXIV et, pour certaines fonctions, à `FFXIVClientStructs`.
- Les mises à jour de FFXIV ou de Dalamud peuvent casser les noms d'addons, callbacks, structures ou projections.
- Les fonctionnalités d'automation doivent rester déclenchées volontairement, visibles, interrompables et limitées au contexte attendu.
- Les versions des manifests et celles contenues dans les ZIP doivent rester synchronisées.

## Sources de vérité

1. Code, workflows et branches actuels des dépôts GitHub.
2. Manifests et packages présents dans DalaLeno.
3. Ces documents de migration.
4. Décisions historiques des discussions, lorsqu'elles ne contredisent pas les sources ci-dessus.
