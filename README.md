# DalaLeno

Dépôt personnalisé regroupant mes plugins Dalamud pour FINAL FANTASY XIV.

## URL du dépôt

Dans le jeu : `/xlsettings` → **Experimental** → **Custom Plugin Repositories** puis ajoutez :

`https://raw.githubusercontent.com/Hylieno/DalaLeno/main/pluginmaster.json`

Activez le dépôt, sauvegardez, puis ouvrez `/xlplugins`.

## Plugins

- **Logogram Helper FR+** — assistant français pour les actions Logos d'Eureka.
- **Occult Weakness** — affiche la faiblesse élémentaire enregistrée de la cible.

## Mise à jour

Chaque entrée de `pluginmaster.json` pointe vers un `latest.zip` stocké dans ce dépôt, sous `plugins/<NomDuPlugin>/latest.zip`. La valeur `AssemblyVersion` doit correspondre à la version du plugin contenu dans le ZIP.
