---
layout: post
title: "Quelques commandes Vim/Bash"
date: 2015-03-02 21:24:52 +0100
comments: true
categories: [Bash, Vim]
---

# Quelques commandes utiles 
Quelques commandes utiles que j'utilise souvent

## Quand je ne suis pas sudo

```bash
$ apt-get install npm
E: Could not open lock file /var/lib/dpkg/lock - open (13: Permission denied)
E: Unable to lock the administration directory (/var/lib/dpkg/), are you root?
$ sudo !!
```
`!!` est la dernière commande exécutée

### Une variante
```bash
mkdir log/
cd !$ 
```
`!$` est la dernière commande sans le premier argument ici `log/`

## Vi/Vim
Bon il y a beaucoup de commandes relativement sympa et complexes mais je donne celle que tout le monde demande

Enregistrer un fichier en lecture seule
```
:w !sudo tee %
```

Dite oui pour recharger la page. Et dite oui si vous voulez quitter la page sans enregistrer.

D'ailleurs si vous utilisez tout le temps `:wq` vous pouvez essayer

 - `:w` enregistre sans quitter
 - `:q` quitte mais avec warning si vous n'avez pas sauvegarder
 - `:q!` quitte sans warning et sans sauver
 - `:x` raccourci de `:wq`. Il existe aussi le raccourci clavier `ZZ` comme pour aller dormir. quitte et enregistre.
 - `:e fichier.txt` ouvre le fichier (l'auto complétion avec tab fonctionne..)
 - `:tabe fichier.txt` ouvre dans un nouvel onglet (vim seulement). on peux se ballader d'onglet en onglet avec les touches `gt` et `gT` `g` est le diminutif de *go* et `t` pour *tab*. ou avec `ctrl+<pageUp>` et `ctrl+<pageDown>` ou encore tout simplement cliquer avec la souris (astuce suivante)

### Active la souris
```
set mouse=a
```
une astuce est d'ajouter un .vimrc et d'ajouter cette ligne dans ce fichier de config.

### le `vimrc` 
Vous en avez besoin !! Il existe deux versions de vi: `vi` et `vim` vim démarre par défaut en mode vi compatible (ce qui n'est plus tout à fait vrai).Certaines fonctionnalités sont coupées. Dont les flèches de directions (et ca vous *voulez*les flèches haut, bas, gauche, droite) et surtout le annuler (`u` come *u*ndo) illimité (il est parfois limité à 1). Quel rapport avec le `vimrc` ? L'existence du fichier fera que votre vi démarre en mode non compatible. donc vous aurez les touches et le undo.

## l'auto-complétion sous vim

Il existe une dizaine d'auto complétion sous vim. Certainement pas aussi puissantes que IDE mais cela rend service.

En mode insertion `ctrl+p` auto complétion en cherchant dans les fichiers ouverts. Cela résout 50 % des auto complétions.

l'auto-complétion est activé via la touche `ctrl+x` +`crtl+lettre` avec :

* `ctrl+x` `ctrl+f` `f` pour `file` auto complétion suivant le fichier (marche nickel pour les hosts apache !)
* `ctrl+x` `ctrl+l` `l` pour `line` auto complétion sur la ligne entière
* `ctrl+x` `ctrl+p` c'est pareil que ctrl+p 
* `ctrl+x` `ctrl+o` `o` pour `omni-completion` auto complete php/python/bash suivant le fichier que vous éditez. (cela comprend les mots clés et peux vous donner la syntaxe)

Je montrerai dans un prochain post comment faire pareil qu'un IDE avec les fonctions. Ou se balader dans les classes et/ou auto compléter le code. Ce n'est pas encore le niveau des IDEs mais c'est souvent une fonctionnalité méconnue (pourtant c'est dans vi depuis des années)

