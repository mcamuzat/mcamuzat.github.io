---
layout: post
title: "Tig : Status"
date: 2015-10-04 21:59:59 +0200
comments: true
categories: ["git", "tig", "linux", "vim"]
---

Tig est un client git en ligne de commande

Il n'est pas compliqué à installer : 

``` bash
sudo apt-get install tig
```

Néanmoins c'est la version 1.2 dans les dépôts au moment ou j'écris ces lignes.

On peut installer la version 2 qui a plus de fonctionnalités et de raccourcis.

``` bash
git clone https://github.com/jonas/tig
make
make install
```
attention vous aurez probablement besoin d'avoir installer la librairies `libncursesw` pour l'utf-8
## La vue principale
Elle permet de voir l'historique du dépôt.

{% img center /images/tig-defaut.png 600 398 'la vue par défault' 'la vue par défaut' %}


Appuyer sur `<Enter>` pour voir la différence. (Dans la version 2, si le terminal fait plus de 160 caractères l'écran se splitte en 2 verticalement)

{% img center /images/tig-diff.png 600 399 'quand on appuie sur la touche entrée, on affiche la différence' 'l'écran de différence' %}

Screenshot de la version 2 avec les deux colonnes.

{% img center /images/tig_view_v2.png 600 366 'dans la version 2, si le terminal' 'Screenshot de la version 2 avec les deux colonnes.' %}

Il va falloir apprendre les touches Vi car on se sert beaucoup de `j` et `k` (un rappel `j` descend vers le bas et `k` va vers le haut)

De cette écran voici les différents modes (je ne les cites pas tous)

 * `S` ou `s` pour voir le stage (équivalent de git status)
 * `t` tree view affichage en explorateur de fichier
 * `r` permet de voir les différentes branches (`H` dans la version1
 * `l` voir les logs

Je vais surtout m'intéresser à la status view. 

## La vue Status

Les touches à connaitre.

 * `u` sur un noms de fichiers pour **u**se cela fait l'équivalent de `git add <nom du fichier>`

Si vous appuyer sur `u` sur les lignes `Changes to be commited`, `Changed but not updated`, `Untracked files` vous ajoutez tous les fichiers.

```
Changes to be committed:
M   fichier1
Changed but not updatedy://<---(*curseur*) 
M   fichier2
M   fichier3
M   fichier4
M   fichier5
M   fichier6
M   fichier7
Untracked files:
?   nouveau fichier
```

Cela devient 

```
Changes to be committed:
M   fichier1
M   fichier2
M   fichier3
M   fichier4
M   fichier5
M   fichier6
M   fichier7
Changed but not updatedy:
(no files)
Untracked files:
?   nouveau fichier
```

Enfin on peut aussi prendre chunk par chunck (l'équivalent de `git add -p`)

{% img center /images/tig-revert.png 600 398 'la vue par défaut' 'la vue par défaut' %}

Il suffit d'appuyer sur `Enter` puis de se déplacer dans le commit avec `j` et `k` et appuyer sur `u` pour ajouter ce chunk. Les chunks pour faire simple sont les textes séparés par des `@@ ... @@`. On se déplace de chunk en chunk grâce à la touche `@`.

Pour reverter le fichier, On utilise la touche `!`. 

Cela marche aussi sur un chunk. On peut donc reverter partiellement un fichier.

Il est possible d'ajouter ligne par ligne dans un commit grâce à la touche `1`.

Pour faire le git commit il suffit d'appuyer sur `C` comme **C**ommit.

Enfin la touche `e` comme **e**dit ouvre le fichier dans l'éditeur par défaut.

## Le fichier `.tigrc`

Le fichier `.tigrc` permet de personnaliser l'affichage et d'ajouter des raccourcis claviers.

Voici quelques exemples de ma config.

``` 
# Delete files in status view (useful for untracked files)
bind status D !@?rm %(file)

# Amend last commit with A
bind status A !git commit --amend

# Create and checkout a new branch; specify custom prompt
bind main B !git checkout -b "%(prompt Enter new branch name: )"
```
Les raccourcis claviers que j'ai rajouté

 * Dans la vue status la touche `D` efface le fichier
 * Dans la vue status la touche `A` fait un `git commit --amend`
 * Dans la vue principale la touche `B` permet de créer une branche.


## Résumé de touches

 * `s` ou `S` voir la vue status
 * `u` ajouter le commit/chunk
 * `!` revert
 * `1` ajoute une lignes au commit
 * `@` aller au chunck/diff suivant
 * `D` supprimer le fichier (*raccourcis perso*)
 * `A` git amend
 * `e` ouvre dans l'éditeur par défaut

## Des liens

 * [le site officiel](http://jonas.nitro.dk/tig/)
 * la [cheat-sheet](https://github.com/pmiossec/tig-cheat-sheet) avec les raccourcis claviers (*indispensable*)

## Conclusion 

Je vais revenir sur les autres vues bientôt.

Merci de m'avoir lu.
